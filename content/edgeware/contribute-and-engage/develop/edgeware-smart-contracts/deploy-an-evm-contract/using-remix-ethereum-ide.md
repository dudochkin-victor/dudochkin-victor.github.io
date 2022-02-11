+++
title = "Использование Remix — Ethereum IDE"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

## Введение <a id="introduction"></a>

В этом руководстве рассматривается процесс создания и развертывания смарт-контракта на основе Solidity на узле разработки Edgeware с помощью [Remix IDE] (https://remix.ethereum.org/). Remix — одна из наиболее часто используемых сред разработки смарт-контрактов на Ethereum. Учитывая возможности совместимости Edgeware с Ethereum, Remix можно использовать напрямую с узлом Edgeware.

В этом руководстве предполагается, что у вас есть работающий локальный узел Edgeware, работающий в режиме `--dev`, и что у вас есть установка MetaMask, настроенная на использование этого локального узла. [Вы можете найти инструкции по запуску локального узла Edgeware EVM](https://main.edgeware.wiki/contribute-and-engage/develop/edgeware-smart-contracts/deploy-an-evm-contract/setting-up- a-edgeware-evm-node) и [настроить MetaMask для Edgeware](https://main.edgeware.wiki/contribute-and-engage/develop/edgeware-smart-contracts/deploy-an-evm-contract/using -метамаска).

## Взаимодействие с Edgeware с помощью Remix <a id="interacting-with-edgeware-using-remix"></a>

[Откройте ремикс](https://remix.ethereum.org/) и нажмите «Новый файл».

Назовите свой файл, в нашем случае мы назвали его `ERC20.sol` - да, это [известный стандарт токена](https://eips.ethereum.org/EIPS/eip-20)

![](https://user-images.githubusercontent.com/44712760/122647263-91a9ad00-d0e0-11eb-93fb-1516d5c70509.png)

Теперь добавьте код. Здесь мы используем простой контракт ERC-20, основанный на текущем шаблоне Open Zeppelin ERC-20. Он создает `MyFirstToken` с символом `HEDGE` и отчеканивает всю исходную поставку создателю контракта.

```
pragma solidity ^0.6.0;

import 'https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v3.1.0/contracts/token/ERC20/ERC20.sol';

// This ERC-20 contract mints the specified amount of tokens to the contract creator.
contract MyFirstToken is ERC20 {
  constructor(uint256 initialSupply) ERC20("MyFirstToken", "HEDGE") public {
    _mint(msg.sender, initialSupply);
  }
}
```

![](https://user-images.githubusercontent.com/44712760/122647352-1eed0180-d0e1-11eb-83f2-652e5d071d28.png)

На левой боковой панели вы нажмете на компилятор Solidity и скомпилируете ERC20.sol.

```
Solidity Compiler >>> Compile Contract
```

![](https://user-images.githubusercontent.com/44712760/122647422-683d5100-d0e1-11eb-8ec4-d987d7ed8365.png)

Теперь нажмите «Развернуть и запустить транзакции» слева на боковой панели и откройте Metamask, чтобы проверить, подключена ли наша учетная запись. Если он подключен, вы можете перейти к следующим шагам.

![](https://user-images.githubusercontent.com/44712760/122647516-cb2ee800-d0e1-11eb-91c8-78da242aca5b.png)

Выберите нашу учетную запись, в данном случае это `Edgeware Dev`, и нажмите **подключить**, если вы не видите эту опцию, нажмите **Не подключено**, появится новое окно.

![](https://user-images.githubusercontent.com/44712760/122647653-8ce5f880-d0e2-11eb-8775-1c0a8040e6db.png)
![](https://user-images.githubusercontent.com/44712760/122647679-a71fd680-d0e2-11eb-890d-18a72131a25d.png)

Теперь вы отправитесь к развертыванию контракта. Непосредственно перед этим убедитесь, что вы установили для **ENVIRONMENT** значение «Injected Web3» и учетную запись, которую мы импортировали. Подсказка, у него должен быть какой-то Eth. На вход следующий деплой input `initialSupply`, в нашем случае это 21M. Поскольку в этом контракте по умолчанию используется 18 знаков после запятой, значение, которое вы укажете, будет «210000000000000000000000000».

![](https://user-images.githubusercontent.com/44712760/122647765-0aaa0400-d0e3-11eb-8163-a3bf7778d3d0.png)

Вы нажмете **подтвердить**!

![Remix-MM-confirm](https://user-images.githubusercontent.com/44712760/122647786-27ded280-d0e3-11eb-8e16-46ca0bda1982.png)

Вы увидите, что ваш контракт был успешно развернут.

![Remix-deployed-contract](https://user-images.githubusercontent.com/44712760/122647859-886e0f80-d0e3-11eb-9b90-3da70fefe94a.png)

Вы можете просмотреть сведения о развертывании вашего контракта, который был успешно развернут на Edgeware EVM.

![](https://user-images.githubusercontent.com/44712760/122647914-db47c700-d0e3-11eb-98ad-747ff2befd04.png)

Теперь вы можете щелкнуть, чтобы вызвать такие функции, как «десятичные числа», «имя», «символ», «общее количество».

![](https://user-images.githubusercontent.com/44712760/122647936-f61a3b80-d0e3-11eb-9657-ae4a4914dc4b.png)

## Что дальше? <a id="what39s-next"></a>

Вы можете скопировать адрес своего контракта и добавить его в Metamask, чтобы играть! Веселитесь, берегите себя!

## Свяжитесь с нами, чтобы узнать больше <a id="reach-us-for-more-engagement"></a>

Рад, что вы прошли через это! 🥰 Мы стремимся помочь вам в ваших исследованиях с помощью функции расчесывания Edgeware Ethereum. Мы **будем рады узнать о вашем опыте и предложениях, которые вы можете нам предложить.**. Вы можете свободно [общаться с нами в каналах Edgeware, таких как Discord, Element и Telegram](https://linktr.ee/edg_developers), мы можем помочь вам с проблемами, которые могут у вас возникнуть, или с проектом, который вы хотите финансировать. через нашу [программу казначейства](https://docs.edgewa.re/edgeware-runtime/treasury). Не стесняйтесь делиться своими отзывами на наших каналах, всегда есть место для улучшения! 🙌
