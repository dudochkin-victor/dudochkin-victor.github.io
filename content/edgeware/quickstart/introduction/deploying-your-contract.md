+++
title = "Развертывание контракта"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Теперь, когда мы сгенерировали двоичный файл Wasm из нашего исходного кода и запустили узел Substrate, мы хотим развернуть этот контракт в нашей цепочке блоков Substrate.

Развертывание смарт-контрактов на Substrate немного отличается от традиционных блокчейнов смарт-контрактов.

В то время как совершенно новый блок исходного кода смарт-контракта развертывается каждый раз, когда вы отправляете контракт на другие платформы, Substrate предпочитает оптимизировать это поведение. Например, стандартный токен ERC20 развертывался в Ethereum тысячи раз, иногда только с изменениями начальной конфигурации \(через функцию «конструктора» Solidity). Каждый из этих экземпляров занимает место в блокчейне, эквивалентное размеру исходного кода контракта, хотя на самом деле код не был изменен.

В Substrate процесс развертывания контракта разделен на две части:

1. Размещение вашего кода в блокчейне
2. Создание экземпляра вашего контракта

С помощью этого шаблона код контракта, такой как стандарт ERC20, может быть помещен в блокчейн один раз, но инстанцировано любое количество раз. Нет необходимости постоянно загружать один и тот же исходный код и тратить место в блокчейне.

## Быстрый трек

Если вы пропустили предыдущие шаги и просто хотите увидеть взаимодействие с чернилами! смарт-контракт, загрузите [flipper.wasm](https://contracts.edgewa.re/0/assets/flipper.wasm) и [metadata.json](https://contracts.edgewa.re/0/assets/flipper .json)

## Размещение вашего кода в блокчейне

Когда ваш узел разработки Substrate запущен, вы можете вернуться в [пользовательский интерфейс Polkadot](https://polkadot.js.org/apps/), где вы сможете взаимодействовать со своим блокчейном.

Откройте специально разработанный раздел [**Контракты**](https://polkadot.js.org/apps/?rpc=ws%3A%2F%2F127.0.0.1%3A9944#/contracts) пользовательского интерфейса.

В разделе **Код** выберите **"Загрузить Wasm"**.

![Upload Wasm](../../.gitbook/assets/upload-wasm.png)

Во всплывающем окне выберите _учетную запись развертывания_ с некоторым балансом учетной записи, например «Алиса». В _скомпилированном контракте WASM_ выберите сгенерированный нами файл `flipper.wasm`. В качестве _метаданных_ контракта_ выберите файл JSON.

![Contracts code page for deploying Flipper](../../.gitbook/assets/upload-wasm-dialog.png)

После того, как вы нажмете **Загрузить** и **Подписать и отправить** внешнюю форму, будет сформирован новый блок, и будет отправлено системное событие с `contracts.PutCode`. Если транзакция пройдет успешно, вы получите событие `system.ExtrinsicSuccess`, и ваш контракт WASM будет сохранен в вашей цепочке блоков Substrate!

![An image of events from Flipper code upload](../../.gitbook/assets/upload-wasm-ok.png)

> **Примечание.** Если вы получаете сообщение об ошибке `system.ExtrinsicFailed`, возможно, у вас недостаточно газа для выполнения вызова. Вы можете убедиться, что это является причиной, просмотрев журналы в терминале. Это может произойти в этом или любом последующем экземпляре или вызове контракта.

## Создание экземпляра вашего контракта

Смарт-контракты существуют как расширение системы учетных записей в блокчейне. Таким образом, создание экземпляра этого контракта создаст новый «AccountId», который будет хранить любой баланс, управляемый смарт-контрактом, и позволит нам взаимодействовать с контрактом.

Вы заметите, что на вкладке **Код** появился новый объект, представляющий наш смарт-контракт. Теперь нам нужно развернуть наш смарт-контракт, чтобы создать **экземпляр**. Нажмите кнопку **"Развернуть"** на флиппер-контракте.

Чтобы создать экземпляр нашего контракта, нам просто нужно предоставить этому контрактному аккаунту _Endowment_ в размере 10 единиц, чтобы заплатить арендную плату за хранение, и снова установить _максимально разрешенный газ_ на value\(`1,000,000`\):

![An image of the Contracts Instance Page](../../.gitbook/assets/flipper-init.png)

> **Примечание.** Как упоминалось ранее, создание контракта предполагает создание новой учетной записи. Таким образом, вы должны быть уверены, что предоставили контрактному счету как минимум экзистенциальный депозит, определенный вашей цепочкой блоков. Мы также должны быть в состоянии оплатить арендную плату по контракту \(_`пожертвование`_\). Если мы израсходуем весь этот депозит, контракт станет недействительным. Мы всегда можем пополнить баланс контракта и сохранить его в цепочке.

Когда вы нажимаете **Deploy**, вы должны увидеть ряд событий, включая создание новой учетной записи \(`system.NewAccount`\) и создание экземпляра контракта \(`contracts.instantiate`\):

![An image of events from instantiation of Flipper](../../.gitbook/assets/flipper-init-ok.png)