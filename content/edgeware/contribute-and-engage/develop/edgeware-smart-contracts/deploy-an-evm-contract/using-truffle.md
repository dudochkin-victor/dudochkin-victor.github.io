+++
title = "Использование Truffle"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

## Взаимодействие с Edgeware EVM с помощью Truffle <a id="interacting-with-edgeware-evm-using-truffle"></a>

### Введение <a id="introduction"></a>

В этом руководстве рассматривается процесс развертывания смарт-контракта на основе Solidity на узле Edgeware с использованием [Truffle](https://www.trufflesuite.com/docs). Truffle — один из наиболее часто используемых инструментов разработки смарт-контрактов на Ethereum. Учитывая возможности совместимости Edgeware с Ethereum, Truffle можно использовать напрямую с узлом Edgeware.

В этом руководстве предполагается, что у вас есть [работающий локальный узел Edgeware EVM, работающий в режиме --dev]. контракт-evm/настройка-узла-edgeware-evm).

### Условия среды <a id="environment-prerequisites"></a>

Установлен [Nodejs](https://nodejs.org/en/) и конкретный менеджер пакетов, например [yarn](https://classic.yarnpkg.com/en/docs/install/#mac-stable) или [npm](https://www.npmjs.com/get-npm), остальные батареи включены в это руководство. Когда вы будете готовы, клонируйте наш учебный репозиторий с подготовленным стеком для вас.

```bash
git clone https://github.com/edgeware-builders/tutorials tutorials;cd tutorials/truffle;yarn
```

Он переместится в ваш клонированный репозиторий, установит необходимые пакеты, и вы готовы к работе!

Давайте рассмотрим `truffle-config.js` в каталоге `truffle/`

```rust
const HDWalletProvider = require("@truffle/hdwallet-provider");
const privKey = '1111111111111111111111111111111111111111111111111111111111111111';

module.exports = {
  compilers: {
    solc: {
      version: "^0.6.0",
    }
  },
  networks: {
    development: {
      provider: () => new HDWalletProvider({
        privateKeys: [ privKey ],
        providerOrUrl: "http://localhost:9933/",
      }),
      network_id: 2021,
    },
  } 
}
```

Вы заметили несколько фактов отсюда, наш chainId — «2021», и мы используем версию solc выше «^0.6.0».

> **Примечание** Мы используем тот же закрытый ключ, который мы использовали в других руководствах, который предварительно финансируется токенами `tEDG` через конфигурацию генезиса узла Edgeware EVM, работающего в режиме `--dev`. Открытый ключ для этой учетной записи: `0x19e7e376e7c213b7e7e7e46cc70a5dd086daff2a`.

Контракт, который мы будем развертывать с Truffle, — это простой контракт ERC-20. Вы можете найти этот контракт в `truffle/contracts/HedgeToken.sol`, его содержание показано здесь.

### [Контракт ERC-20]

```javascript
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract HedgeToken is ERC20 {
    constructor(uint256 initialSupply) public ERC20("HedgeToken", "HEDGE") {
        _mint(msg.sender, initialSupply);
    }
}
```

Это простой контракт ERC-20, основанный на контракте OpenZeplin ERC20, который создает «HedgeToken» и присваивает созданный первоначальный запас токенов создателю контракта.

Вы можете заметить начальную поставку в `migrations/2_deploy.contracts.js`, она содержит следующее:

```javascript
var HedgeToken = artifacts.require("HedgeToken");

module.exports = function (deployer) {
  deployer.deploy(HedgeToken, "21000000000000000000000000");
};
```

`21000000000000000000000000` — это количество токенов, которое будет первоначально отчеканено с контрактом, то есть 21 миллион с 18 знаками после запятой. [Начиная с OpenZeplin v3.0+, по умолчанию для SimpleToken.sol используется десятичное число 18](https://docs.openzeppelin.com/contracts/3.x/api/token/erc20#ERC20-_setupDecimals-uint8-)

Теперь давайте перейдем к основной части! После того, как вы установили необходимые пакеты, продолжите работу в терминале с помощью следующей команды

### Компиляция контракта ERC-20 <a id="compile-erc-20-contract"></a>

```
npx truffle compile
```

![](https://user-images.githubusercontent.com/32852637/122429916-24bbd900-cf61-11eb-98bd-faa07d223e68.PNG)

Что делает id, он берет токен OpenZepplin ERC20.sol, компилирует его с другим кодом, на который ссылаются, в другом коде OpenZeplin, создает артефакт \(байт-код\) и ABI \(контрактный интерфейс\)

### Развертывание контракта в Edgeware EVM с помощью Truffle <a id="deploying-a-contract-to-edgeware-evm-using-truffle"></a>

Теперь давайте перейдем к горячим вещам, развернем их на нашу EVM Edgeware.

```
npx truffle --network development migrate
```

![Truffle_2](https://user-images.githubusercontent.com/32852637/122431469-7ca70f80-cf62-11eb-8684-114f0323ff83.PNG)

Как видите, мы используем нашу сеть разработки из truffle-config.js. Из миграции вы заметите, какой у нас контрактный адрес нашего контракта.

### Свяжитесь с нами, чтобы узнать больше <a id="reach-us-for-more-engagement"></a>

Рад, что вы прошли через это! 🥰 Мы стремимся помочь вам в ваших исследованиях с помощью функции расчесывания Edgeware Ethereum. Мы **будем рады узнать о вашем опыте и предложениях, которые вы можете нам предложить.**. Вы можете свободно [общаться с нами в каналах Edgeware, таких как Discord, Element и Telegram](https://linktr.ee/edg_developers), мы можем помочь вам с проблемами, которые могут у вас возникнуть, или с проектом, который вы хотите финансировать. через нашу [программу казначейства](https://docs.edgewa.re/edgeware-runtime/treasury). Не стесняйтесь делиться своими отзывами на наших каналах, всегда есть место для улучшения! 🙌
