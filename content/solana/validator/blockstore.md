+++
title = "Blockstore"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

После того, как блок достигает окончательности, все блоки от этого до блока генезиса образуют линейную цепочку со знакомым названием блокчейн. Однако до этого момента валидатор должен поддерживать все потенциально действительные цепочки, называемые _forks_. Процесс естественного формирования вилок в результате ротации лидеров описан в [генерация вилки](../cluster/fork-generation/). Описанная здесь структура данных _blockstore_ показывает, как валидатор справляется с этими ответвлениями, пока блоки не будут завершены.

Хранилище блоков позволяет валидатору записывать каждый клочок, который он наблюдает в сети, в любом порядке, если клочок подписан ожидаемым лидером для данного слота.

Фрагменты перемещаются в ключевое пространство с возможностью разветвления, состоящее из `ведущего слота` + `индекса фрагмента` (внутри слота). Это позволяет сохранять структуру списка пропусков протокола Solana полностью, без априорного выбора, за какой веткой следовать, какие записи сохранять или когда их сохранять.

Запросы на восстановление недавних фрагментов обслуживаются из ОЗУ или последних файлов, а также из более глубокого хранилища для менее свежих фрагментов, как это реализовано в хранилище, поддерживающем Blockstore.

## Функциональность Blockstore

1. Постоянство: Blockstore находится перед конвейером проверки узлов, сразу за сетевым получением и проверкой подписи. Если полученный клок соответствует расписанию лидера \(т.е. был подписан лидером для указанного слота\), он сразу же сохраняется.

2. Ремонт: ремонт аналогичен описанному выше ремонту окон, но способен обслужить любой полученный клочок. Blockstore хранит фрагменты с подписями, сохраняя цепочку происхождения.

3. Вилки: Blockstore поддерживает произвольный доступ к фрагментам, поэтому может удовлетворить потребность валидатора в откате и воспроизведении с контрольной точки банка.

4. Перезапуск: при правильном сокращении/отборе Blockstore можно воспроизвести путем упорядоченного перечисления записей из слота 0. Логика этапа воспроизведения \(т.е. работа с вилками\) должна будет использоваться для самых последних записей в Блокмаркет.

## Дизайн блочного магазина

1. Записи в Blockstore хранятся в виде пар ключ-значение, где ключ — это конкатенированный индекс слота и индекс уничтожения для записи, а значение — это данные записи. Обратите внимание, что индексы уничтожения отсчитываются от нуля для каждого слота \ (т. е. они относятся к слоту \).

2. Blockstore поддерживает метаданные для каждого слота в структуре SlotMeta, содержащей:
   
   - `slot_index` - Индекс этого слота
   
   - `num_blocks` - количество блоков в слоте \(используется для привязки к предыдущему слоту\)
   
   - `consumed` - наивысший индекс порчи `n`, такой, что для всех `m < n` в этом слоте существует порча с индексом порчи, равным `n` \ (т. е. самый высокий последовательный индекс порции).
   
   - `received` - наивысший полученный индекс уничтожения для слота.
   
   - `next_slots` - список будущих слотов, к которым может быть привязан этот слот. Используется при перестроении леджера для поиска возможных точек разветвления.
   
   - `last_index` - индекс фрагмента, помеченного как последний фрагмент для данного слота. Этот флаг на фрагменте будет установлен лидером для слота, когда он передает последний фрагмент для слота.
   
   - `is_connected` - Истинно, если и только если каждый блок от 0 до слота образует полную последовательность без каких-либо пробелов. Мы можем вывести is_connected для каждого слота со следующими правилами. Пусть slot\(n\) будет слотом с индексом `n`, а slot\(n\).is_full\(\) будет истинным, если слот с индексом `n` имеет все тики, ожидаемые для этого слота. Пусть is_connected\(n\) будет утверждением, что "слот\(n\).is_connected является истинным". Затем: is_connected\(0\) is_connected\(n+1\) iff \(is_connected\(n\) и slot\(n\).is_full\(\)

3. Цепочка. Когда приходит фрагмент для нового слота `x`, мы проверяем количество блоков \(`num_blocks`\) для этого нового слота \(эта информация закодирована в фрагменте\). Затем мы знаем, что этот новый слот связан со слотом `x - num_blocks`.

4. Подписки — Blockstore записывает набор слотов, на которые была «подписана». Это означает, что записи, связанные с этими слотами, будут отправлены по каналу Blockstore для использования ReplayStage. Подробнее см. в разделе «API Blockstore».

5. Уведомления об обновлениях. Blockstore уведомляет слушателей, когда slot\(n\).is_connected переключается с false на true для любого `n`.

## API-интерфейсы блочных магазинов

Blockstore предлагает API на основе подписки, который ReplayStage использует для запроса интересующих его записей. Записи будут отправляться по каналу, открытому Blockstore. Эти API подписки следующие: 1. `fn get_slots_since(slot_indexes: &[u64]) -> Vec<SlotMeta>`: возвращает новые слоты, соединяющиеся с любым элементом списка `slot_indexes`.

1. `fn get_slot_entries(slot_index: u64, entry_start_index: usize, max_entries: Option<u64>) -> Vec<Entry>`: возвращает вектор входа для слота, начинающийся с `entry_start_index`, ограничивая результат значением `max`, если `max_entries == Some(max)`, в противном случае верхний предел длины возвращаемого вектора не налагается.

Примечание. В совокупности это означает, что этап воспроизведения теперь должен будет знать, когда слот будет завершен, и подписаться на следующий интересующий его слот, чтобы получить следующий набор записей. Раньше бремя объединения слотов ложилось на Blockstore.

## Взаимодействие с банком

Банк выставляет на повтор этап:

1. `prev_hash`: с какой цепочкой PoH он работает, на что указывает хэш последней обработанной записи.

2. `tick_height`: тики в цепочке PoH, которые в настоящее время проверяются этим банком.

3. `votes`: стек записей, который содержит: 1. `prev_hashes`: все, что после этого голосования должно быть связано в PoH 2. `tick_height`: высота тика, на которой было подано это голосование 3. `период блокировки` : как долго должна наблюдаться цепочка в реестре, чтобы ее можно было привязать ниже этого голоса

Этап воспроизведения использует API-интерфейсы Blockstore, чтобы найти самую длинную цепочку записей, которая может быть привязана к предыдущему голосованию. Если эта цепочка записей не зависает от последнего голосования, этап воспроизведения откатывает банк к этому голосованию и воспроизводит цепочку оттуда.

## Удаление Blockstore

Как только записи Blockstore становятся достаточно старыми, представление всех возможных ответвлений становится менее полезным, возможно, даже проблематичным для воспроизведения после перезапуска. Однако после того, как количество голосов валидатора достигнет максимальной блокировки, любое содержимое Blockstore, которое не находится в цепочке PoH для этого голосования, может быть удалено, удалено.