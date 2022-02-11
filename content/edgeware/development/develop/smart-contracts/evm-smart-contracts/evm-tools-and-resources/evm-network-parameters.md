+++
title = "EVM: Сетевые параметры "
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

## Тесты EVM

1 единица газа EVM = 3000-4000 пикосекунд времени выполнения, что означает количество газа EVM, которое мы можем обработать в секунду = 250 000 000.

Мы консервативно настроили газ в секунду = 8 000 000, что означает, что наш лимит газа в блоке составляет 12 млн газа (у Эфириума — 15 млн газа). Контрольный показатель предполагает, что мы можем значительно увеличить эту сумму до 250 000 000, что будет означать, что наш лимит газа на блоке составляет 375 млн.

На Beresheet лимит газа установлен на 225 м, мы рекомендуем запускать развертывание в основной сети с лимитом газа 12 м.

## Конечные точки RPC

### Beresheet testnet

* Имя сети: Beresheet
* RPC URL: [https://beresheet2.edgewa.re/evm](https://beresheet2.edgewa.re/evm) \(В качестве альтернативы можно использовать [https://beresheetX.edgewa.re/evm](https://beresheetX.edgewa.re/evm) где X может быть любым числом от 1 до 8.\)
* Chain ID: 2022

### Local Edgeware узел разработки

* Имя сети: dev
* RPC URL: [http://localhost:9933/](http://localhost:9933/)
* Chain ID: 2021

### Edgeware mainnet

* Имя сети: Edgeware
* RPC URL: [https://mainnet2.edgewa.re/evm](https://mainnet2.edgewa.re/evm) \(В качестве альтернативы можно использовать [https://mainnetX.edgewa.re/evm](https://mainnetX.edgewa.re/evm) где X может быть любым числом от 1 до 20.\)
* Chain ID: 2021

### EDG EVM Tools

testnet обозреватель блоков [https://beresheet.edgscan.com/](https://beresheet.edgscan.com/)

mainnet обозреватель блоков [https://mainnet.edgscan.com/](https://mainnet.edgscan.com/)

Edgeware Key Generator [https://edgewa.re/keygen](https://edgewa.re/keygen)

### EDG EVM Учебники

Youtube [https://www.youtube.com/playlist?list=PLJK4WO5y\_eJFMGZGDmNOWRZI2dAV4GyU6](https://www.youtube.com/playlist?list=PLJK4WO5y_eJFMGZGDmNOWRZI2dAV4GyU6)

Документация [https://main.edgeware.wiki/development/develop/smart-contracts/evm-smart-contracts/tutorials](https://main.edgeware.wiki/development/develop/smart-contracts/evm-smart-contracts/tutorials)
