+++
title = "Использование Remix — Ethereum IDE"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

## Введение

В этом руководстве рассматривается процесс создания и развертывания смарт-контракта на основе Solidity на узле разработки Edgeware с помощью [Remix IDE](https://remix.ethereum.org/). Remix — одна из наиболее часто используемых сред разработки смарт-контрактов на Ethereum. Учитывая возможности совместимости Edgeware с Ethereum, Remix можно использовать напрямую с узлом Edgeware.

В этом руководстве предполагается, что у вас есть работающий локальный узел Edgeware, работающий в режиме `--dev`, и что у вас есть установка MetaMask, настроенная на использование этого локального узла. [Вы можете найти инструкции по запуску локального узла Edgeware EVM](https://github.com/hicommonwealth/edgeware-documentation/blob/master/docs/quickstart/evm-introduction/setting-up-a-local-node. md) и [для настройки MetaMask для Edgeware](https://github.com/hicommonwealth/edgeware-documentation/blob/master/docs/quickstart/evm-introduction/interacting-with-a-edgeware-node-using-metamask .мд).

## Взаимодействие с Edgeware с помощью Remix

[Откройте ремикс](https://remix.ethereum.org/) и нажмите «Новый файл».

![Remix-New-File](../../.gitbook/assets/remix-new-file.png)

Назовите свой файл, в нашем случае мы назвали его `erc20.sol` - да, это [известный стандарт токена](https://eips.ethereum.org/EIPS/eip-20)

![Remix-Name-File](../../.gitbook/assets/remix-name-file.png)

Теперь добавьте код. Здесь мы используем простой контракт ERC-20, основанный на текущем шаблоне Open Zeppelin ERC-20. Он создает `MyFirstToken` с символом `HEDGE` и отчеканивает всю исходную поставку создателю контракта.

```javascript
pragma solidity ^0.6.0;

import 'https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v3.1.0/contracts/token/ERC20/ERC20.sol';

// This ERC-20 contract mints the specified amount of tokens to the contract creator.
contract MyFirstToken is ERC20 {
  constructor(uint256 initialSupply) ERC20("MyFirstToken", "HEDGE") public {
    _mint(msg.sender, initialSupply);
  }
}
```

![Remix-Add-Code](../../.gitbook/assets/remix-add-code.png)

На левой боковой панели нажмите Компилятор Solidity и Скомпилируйте erc20.sol.

```
Solidity Compiler >>> Compile Contract
```

![Remix-Compile-Contract](../../.gitbook/assets/remix-compile-contract.png)

Теперь нажмите «Развернуть и запустить транзакции» слева на боковой панели и откройте Metamask, чтобы проверить, подключена ли наша учетная запись. Если он подключен, вы можете перейти к следующим шагам.

![Remix-Open-MM](../../.gitbook/assets/remix-open-mm.png)

Выберите нашу учетную запись, в данном случае это «Учетная запись 4», и нажмите «Подключиться».

![Remix-Connect-MM](../../.gitbook/assets/remix-connect-mm.png)

Теперь вы отправитесь к развертыванию контракта. Непосредственно перед этим убедитесь, что вы установили для **ENVIRONMENT** значение «Injected Web3» и учетную запись, которую мы импортировали. Подсказка, у него должен быть какой-то Eth. На вход следующий деплой input `initialSupply`, в нашем случае это 21M. Поскольку в этом контракте по умолчанию используется 18 знаков после запятой, значение, которое вы укажете, будет «210000000000000000000000000».

![Remix-Click-Deploy](../../.gitbook/assets/remix-click-deploy.png)

Вы нажмете **подтвердить**!

![Remix-MM-confirm](../../.gitbook/assets/remix-mm-confirm.png)

Вы увидите, что ваш контракт был успешно развернут.

![Remix-deployed-contract](../../.gitbook/assets/remix-deployed-contract.png)

Вы можете просмотреть сведения о развертывании вашего контракта, который был успешно развернут на Edgeware EVM.

![Remix-contrac-deployment](../../.gitbook/assets/remix-contract-deployment.png)

Теперь вы можете щелкнуть, чтобы вызвать такие функции, как `decimals`, `name`, `symbol`, `totalSupply`

![Remix-call-functions](../../.gitbook/assets/remix-call-functions.png)

## Что дальше?

Вы можете скопировать адрес своего контракта и добавить его в Metamask, чтобы играть! Веселитесь, берегите себя!

## Свяжитесь с нами, чтобы узнать больше

Рад, что вы прошли через это! 🥰 Мы стремимся помочь вам в ваших исследованиях с помощью функции совместимости Edgeware Ethereum. Мы **будем рады услышать ваш опыт и предложения, которые вы можете нам предложить.**. Вы можете свободно [общаться с нами в каналах Edgeware, таких как Discord, Element и Telegram](https://linktr.ee/edg_developers), мы можем помочь вам с проблемами, которые могут у вас возникнуть, или с проектом, который вы хотите финансировать. через нашу [программу казначейства](https://docs.edgewa.re/edgeware-runtime/treasury). Не стесняйтесь делиться своими отзывами на наших каналах, всегда есть место для улучшения! 🙌
