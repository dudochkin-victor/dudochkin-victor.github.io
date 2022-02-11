+++
title = "EVM: Параметры сети"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

## Тесты EVM

1 единица газа EVM = 3000-4000 пикосекунд времени выполнения, что означает количество газа EVM, которое мы можем обработать в секунду = 250 000 000.

Мы консервативно настроили газ в секунду = 8 000 000, что означает, что наш лимит газа в блоке составляет 12 млн газа (у Эфириума — 15 млн газа). Контрольный показатель предполагает, что мы можем значительно увеличить эту сумму до 250 000 000, что будет означать, что наш лимит газа на блоке составляет 375 млн.

На Beresheet лимит газа установлен на 225 м, мы рекомендуем запускать развертывание в основной сети с лимитом газа 12 м.

## Конечные точки RPC

### Тестовая сеть Beresheet

- Сетевое имя: Берешит
- URL-адрес RPC: [https://beresheet2.edgewa.re/evm](https://beresheet2.edgewa.re/evm) \(В качестве альтернативы можно использовать [https://beresheetX.edgewa.re/evm] (https://beresheetX.edgewa.re/evm), где X может быть любым числом от 1 до 8.\)
- ID цепи: 2022

### Локальный узел разработки Edgeware

- Сетевое имя: dev
- URL-адрес RPC: [http://localhost:9933/](http://localhost:9933/)
- ID цепи: 2021

### Основная сеть Edgeware

- Сетевое имя: Edgeware
- URL-адрес RPC: [https://mainnet2.edgewa.re/evm](https://mainnet2.edgewa.re/evm) \(В качестве альтернативы можно использовать [https://mainnetX.edgewa.re/evm] (https://mainnetX.edgewa.re/evm), где X может быть любым числом от 1 до 20.\)
- ID цепи: 2021

### Инструменты EDG EVM

обозреватель блоков testnet [https://beresheet.edgscan.com/](https://beresheet.edgscan.com/)

обозреватель блоков основной сети [https://mainnet.edgscan.com/] (https://mainnet.edgscan.com/)

Генератор ключей Edgeware [https://edgewa.re/keygen](https://edgewa.re/keygen)

### Учебники по EDG EVM

Youtube [https://www.youtube.com/playlist?list=PLJK4WO5y\_eJFMGZGDmNOWRZI2dAV4GyU6](https://www.youtube.com/playlist?list=PLJK4WO5y_eJFMGZGDmNOWRZI2dAV4GyU6)

Документы [https://main.edgeware.wiki/development/develop/smart-contracts/evm-smart-contracts/tutorials](https://main.edgeware.wiki/development/develop/smart-contracts/evm-smart -контракты/учебники)
