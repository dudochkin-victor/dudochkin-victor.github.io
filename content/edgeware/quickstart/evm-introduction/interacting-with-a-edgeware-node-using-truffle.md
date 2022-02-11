+++
title = "Использование Truffle"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

## Введение

В этом руководстве рассматривается процесс развертывания смарт-контракта на основе Solidity на узле Edgeware с использованием [Truffle] (https://www.trufflesuite.com/docs). [Truffle](https://www.trufflesuite.com/docs) — один из часто используемых инструментов разработки смарт-контрактов на Ethereum. Учитывая возможности совместимости Edgeware с Ethereum, Truffle можно использовать напрямую с узлом Edgeware.

В этом руководстве предполагается, что у вас есть [работающий локальный узел Edgeware EVM, работающий в режиме --dev].(https://github.com/hicommonwealth/edgeware-documentation/blob/master/docs/quickstart/evm-introduction/настройка локального узла.md).

## Условия среды

Установлен [Nodejs](https://nodejs.org/en/) и конкретный менеджер пакетов, например [yarn](https://classic.yarnpkg.com/en/docs/install/#mac-stable) или [npm] (https://www.npmjs.com/get-npm), остальные батареи включены в это руководство. Когда вы будете готовы, клонируйте наш учебный репозиторий с подготовленным стеком для вас.

```
git clone https://github.com/edgeware-builders/tutorials tutorials;cd tutorials/truffle;yarn
```

Он переместится в ваш клонированный репозиторий, установит необходимые пакеты, и вы готовы к работе!

Давайте рассмотрим `truffle-config.js` в каталоге `truffle/`

```javascript
const EdgewarePrivateKeyProvider = require('./private-provider')

var edgewarePrivateKey = "99B3C12287537E38C90A9219D4CB074A89A16E9CDB20BF85728EBD97C343E342";

module.exports = {
  compilers: {
    solc: {
      version: "^0.6.0",
    }
  },
  networks: {
    development: {
      provider: () => new EdgewarePrivateKeyProvider(edgewarePrivateKey, "http://localhost:9933/", 42),
      network_id: 42,
      skipDryRun: true,
      gasPrice: 1000000000
    },
  } 
}
```

Вы заметили несколько фактов отсюда, наш chainId равен «42», и мы используем версию solc выше «^0.6.0».

> **Примечание** Мы используем `PrivateKeyProvider` в качестве нашего провайдера Web3 \ (экземпляр включен в `private-provider.js`\). Этот провайдер настраивается очень специфическим образом, чтобы обойти некоторые проблемы, над которыми мы в настоящее время работаем, связанные с `chainid`, `обработкой одноразового номера` и настройкой `skipCache: true` при использовании поставщика Web3, предоставляемого Truffle по умолчанию, с Узлы Edgeware EVM. Мы также используем тот же закрытый ключ, который мы использовали в других руководствах, который предварительно финансируется токенами «tEDG» через конфигурацию генезиса узла Edgeware EVM, работающего в режиме «--dev». Открытый ключ для этой учетной записи: `0x6Be02d1d3665660d22FF9624b7BE0551ee1Ac91b`.

Контракт, который мы будем развертывать с Truffle, — это простой контракт ERC-20. Вы можете найти этот контракт в `truffle/contracts/HedgeToken.sol`, его содержимое показано здесь.

## Контракт ERC-20

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

Это простой контракт ERC-20, основанный на контракте OpenZeplin ERC20, который создает `HedgeToken` и присваивает созданный первоначальный запас токенов создателю контракта.

Вы можете заметить начальную поставку в `migrations/2_deploy.contracts.js`, она содержит следующее:

```javascript
var HedgeToken = artifacts.require("HedgeToken");

module.exports = function (deployer) {
  deployer.deploy(HedgeToken, "21000000000000000000000000", { gas: 2100000000 });
};
```

`21000000000000000000000000` — это количество токенов, которое будет первоначально отчеканено с контрактом, то есть 21 миллион с 18 знаками после запятой. [Начиная с OpenZeplin v3.0+, по умолчанию для SimpleToken.sol используется десятичное число 18](https://docs.openzeppelin.com/contracts/3.x/api/token/erc20#ERC20-_setupDecimals-uint8-)

Теперь давайте перейдем к основной части! После того, как вы установили необходимые пакеты, продолжите работу в терминале с помощью следующей команды

## Компиляция контракта ERC-20

```
npx truffle compile
```

![truffle-compile](../../.gitbook/assets/truffle-compile.png)

Что делает идентификатор, он берет токен OpenZeplin ERC20.sol, компилирует его с другим кодом, на который ссылаются, в другом коде OpenZepplin, создает артефакт \(байт-код\) и ABI \(контрактный интерфейс\)

## Развертывание контракта в Edgeware EVM с помощью Truffle

Теперь давайте перейдем к горячим вещам, развернем их на нашу EVM Edgeware.

```
npx truffle --network development migrate
```

![truffle-migrate](../../.gitbook/assets/truffle-migrate.png)

Как видите, мы используем нашу сеть разработки из truffle-config.js. Из миграции вы заметите, что это наш контрактный адрес нашего контракта.

## Свяжитесь с нами, чтобы узнать больше

Рад, что вы прошли через это! 🥰 Мы стремимся помочь вам в ваших исследованиях с помощью функции совместимости Edgeware Ethereum. Мы **будем рады услышать ваш опыт и предложения, которые вы можете нам предложить.**. Вы можете свободно [общаться с нами в каналах Edgeware, таких как Discord, Element и Telegram](https://linktr.ee/edg_developers), мы можем помочь вам с проблемами, которые могут у вас возникнуть, или с проектом, который вы хотите финансировать. через нашу [программу казначейства](https://docs.edgewa.re/edgeware-runtime/treasury). Не стесняйтесь делиться своими отзывами на наших каналах, всегда есть место для улучшения! 🙌
