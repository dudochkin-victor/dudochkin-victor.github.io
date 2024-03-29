+++
title = "Вызов методов контракта"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Теперь, когда ваш контракт полностью развернут, мы можем начать с ним взаимодействовать! У Flipper есть только две функции, поэтому мы покажем вам, каково это — играть с ними обеими.

## get\(\)

Если вы вернетесь к функции `on_deploy()` нашего контракта, мы установим начальное значение контракта Flipper в `false`. Проверим, так ли это.

![An image of the Contracts call page](../../.gitbook/assets/flipper-get.png)

## flip\(\)

Итак, давайте сделаем значение равным true прямо сейчас!

Альтернативное _сообщение для отправки_, которое мы можем создать с помощью пользовательского интерфейса, — это `flip()`. Снова установите _максимально допустимый газ_ на «1 000 000».

![An image of the Contracts extrinsic page](../../.gitbook/assets/flipper-flip.png)

Вы заметите, что этот вызов фактически отправляет транзакцию. Если транзакция прошла успешно, мы сможем вернуться к функции `get()` и увидеть наше обновленное хранилище:

![An image of Flipper RPC call with true](../../.gitbook/assets/flipper-flip-true.png)

Ууууу! Вы развернули свой первый смарт-контракт!

## Движение вперед

Мы не будем повторять эти шаги по настройке и развертыванию, но будем использовать их на протяжении всего руководства. Вы всегда можете вернуться к этой главе, если вам нужно вспомнить, как выполнять определенный процесс.

В остальной части руководства будет **шаблон кода**, который вы будете использовать для прохождения различных этапов разработки контракта. Каждый шаблон поставляется с полностью разработанным набором тестов, которые должны пройти, если вы правильно запрограммировали свой контракт. Прежде чем перейти к разделу, убедитесь, что вы запустили:

```bash
cargo +nightly test
```

и что все тесты выполняются успешно, без каких-либо предупреждений.

Вам не нужно развертывать свой контракт между каждым разделом, но если мы попросим вас развернуть ваш контракт, вам нужно будет выполнить те же шаги, которые вы сделали с контрактом Flipper.
