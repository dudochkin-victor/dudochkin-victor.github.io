+++
title = "Переход от лидера к лидеру"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Этот дизайн описывает, как лидеры передают производство реестра PoH друг другу, поскольку каждый лидер создает свой собственный слот.

## Проблемы

Текущий лидер и следующий лидер соревнуются, чтобы сгенерировать последний тик для текущего слота. Следующий лидер может появиться в этом слоте, все еще обрабатывая записи текущего лидера.

Идеальным сценарием было бы то, что следующий лидер сгенерировал свой собственный слот сразу после того, как смог проголосовать за текущего лидера. Весьма вероятно, что следующий лидер достигнет высоты своего слота PoH до того, как текущий лидер закончит трансляцию всего блока.

Следующий лидер должен принять решение о присоединении своего блока к последнему завершенному блоку или дождаться завершения ожидающего блока. Вполне возможно, что следующий лидер создаст блок, который предполагает, что текущий лидер потерпел неудачу, даже если остальная часть сети наблюдает, что этот блок успешен.

У нынешнего лидера есть стимулы начать свой слот как можно раньше, чтобы получить экономическую выгоду. Эти стимулы должны быть уравновешены потребностью лидера присоединить свой блок к блоку, который имеет наибольшую приверженность от остальной части сети.

## Тайм-аут лидера

Пока лидер активно получает записи для предыдущего слота, лидер может задержать трансляцию начала своего блока в реальном времени. Задержка настраивается локально каждым лидером и может быть динамически основана на поведении предыдущего лидера. Если блок предыдущего лидера подтверждается TVU лидера до истечения времени ожидания, PoH сбрасывается на начало слота, и этот лидер немедленно создает свой блок.

Недостатки:

- Лидер задерживает свой собственный слот, потенциально давая следующему лидеру больше времени, чтобы наверстать упущенное.

Плюсы по сравнению с гвардией:

- Все пространство в блоке используется для записей.
- Таймаут не фиксирован.
- Тайм-аут является локальным для лидера, и поэтому может быть умным. Эвристика лидера может учитывать характеристики турбины.
- Этот дизайн не требует обновления хардфорка леджера.
- Предыдущий лидер может избыточно передать последнюю запись в блоке следующему лидеру, а следующий лидер может спекулятивно решить доверить ему создание своего блока без проверки предыдущего блока.
- Лидер может спекулятивно сгенерировать последний тик из последней полученной записи.
- Лидер может спекулятивно обрабатывать транзакции и угадывать, какие из них не будут закодированы предыдущим лидером. Это тоже вектор атаки цензуры. Текущий лидер может удерживать транзакции, которые он получает от клиентов, чтобы он мог закодировать их в свой собственный слот. После обработки записи можно быстро воспроизвести в PoH.

## Альтернативные варианты дизайна

### Защитная галочка в конце слота

Лидер не создает записи в своем блоке после _предпоследнего тика_, который является последним тиком перед первым тиком следующего слота. Сеть голосует за _последний тик_, поэтому разница во времени между _предпоследним тиком_ и _последним тиком_ является принудительной задержкой для всей сети, а также для следующего лидера, прежде чем может быть сгенерирован новый слот. Сеть может создать _последний тик_ из _предпоследнего тика_.

Если следующий лидер получает _предпоследний тик_ до того, как он произведет свой собственный _первый тик_, он сбросит свой PoH и создаст _первый тик_ из _предпоследнего тика_ предыдущего лидера. Остальная часть сети также сбросит свой PoH, чтобы создать _последний тик_ в качестве идентификатора для голосования.

Недостатки:

- Каждое голосование и, следовательно, подтверждение задерживается на фиксированный тайм-аут. 1 тик или около 100 мс.
- Среднее время подтверждения кейса для транзакции будет как минимум на 50 мс хуже.
- Это часть определения леджера, поэтому для изменения этого поведения потребуется хард-форк.
- Не все доступное пространство используется для записей.

Преимущества по сравнению с тайм-аутом лидера:

- Следующий лидер получил все предыдущие записи, поэтому он может начать обработку транзакций, не записывая их в PoH.
- Предыдущий лидер может избыточно передать последнюю запись, содержащую _предпоследний тик_, следующему лидеру. Следующий лидер может предположительно сгенерировать _последний тик_, как только он получит _предпоследний тик_, даже до его проверки.
