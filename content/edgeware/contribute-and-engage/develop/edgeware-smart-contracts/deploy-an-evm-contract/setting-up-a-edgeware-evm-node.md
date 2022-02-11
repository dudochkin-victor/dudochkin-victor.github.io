+++
title = "Настройка узла Edgeware EVM"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

## Настройка узла Edgeware для разработки Ethereum/EVM <a id="setting-up-a-edgeware-node-for-ethereumevm-development"></a>

Это руководство поможет вам настроить узел Edgeware с совместимостью с Ethereum/EVM.

>**Примечание** Это быстрый способ запуска узла. [Вы всегда можете скомпилировать из исходного кода] (https://github.com/hicommonwealth/edgeware-node/tree/v3.2.0). Мы рекомендуем использовать ваши собственные скомпилированные двоичные файлы для рабочей сети.

>**Примечание** Если у вас не установлен [Docker, вы можете быстро установить его отсюда](https://docs.docker.com/get-docker/)

Вы можете клонировать наш репозиторий с помощью docker-compose, чтобы сразу приступить к работе:

```
git clone https://github.com/hicommonwealth/edgeware-node; cd edgeware-node/docker;
docker-compose up
```

>**Примечание** Если вы хотите сбросить или очистить локальную цепочку, удалите контейнер докеров, запустив **`docker-compose rm`**

You will see something like this:

![](https://user-images.githubusercontent.com/32852637/121593861-5afdd380-ca0a-11eb-80dd-8922a0b7cfc7.PNG)

После этого вы можете зайти в [Polkadot Apps](https://polkadot.js.org/apps/?rpc=ws%3A%2F%2F127.0.0.1%3A9944#/explorer) и подключиться к 127.0.0.1:9944. , и вы должны увидеть создаваемые блоки.

![](https://user-images.githubusercontent.com/32852637/121594313-e9725500-ca0a-11eb-8f5f-36e5184d6b46.PNG)

Теперь вы можете продолжать подключать [Metamask](https://main.edgeware.wiki/contribute-and-engage/develop/edgeware-smart-contracts/deploy-an-evm-contract/using-metamask), Remix и Web3.js, чтобы иметь большой опыт.

## Свяжитесь с нами, чтобы узнать больше

Рад, что вы прошли через это! 🥰 Мы стремимся помочь вам в ваших исследованиях с помощью функции совместимости Edgeware Ethereum. Мы **будем рады узнать о вашем опыте и предложениях, которые вы можете нам предложить.**. Вы можете свободно [общаться с нами в каналах Edgeware, таких как Discord, Element и Telegram](https://linktr.ee/edg_developers), мы можем помочь вам с проблемами, которые могут у вас возникнуть, или с проектом, который вы хотите финансировать. через нашу [программу казначейства](https://docs.edgewa.re/edgeware-runtime/treasury). Не стесняйтесь делиться своими отзывами на наших каналах, всегда есть место для улучшения! 🙌
