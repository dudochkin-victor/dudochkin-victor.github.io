+++
title = "Настройка EVM узла Edgeware"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

## Настройка узла Edgeware для разработки Ethereum/EVM

Это руководство поможет вам настроить узел Edgeware с совместимостью с Ethereum/EVM.

> **Примечание** Это быстрый способ запуска узла. [Вы всегда можете скомпилировать из исходного кода](https://github.com/hicommonwealth/edgeware-node/tree/edgeware-frontier). Мы рекомендуем использовать ваши собственные скомпилированные двоичные файлы для рабочей сети.
> 
> **Примечание** Если у вас не установлен [Docker, вы можете быстро установить его отсюда](https://docs.docker.com/get-docker/)

Вы можете клонировать наш репозиторий с помощью docker-compose, чтобы сразу приступить к работе:

```
git clone https://github.com/yangwao/substrate_playground; cd substrate_playground;
docker-compose -f edgeware_frontier.yml up
```

> **Примечание** Если вы хотите сбросить или очистить локальную цепочку, удалите контейнер докеров, запустив `docker-compose -f edgeware_frontier.yml rm`

Вы увидите что-то вроде этого:

![Running-Edgeware-EVM-node](../../.gitbook/assets/node-setup-run.png)

После этого вы можете зайти в [Polkadot Apps](https://polkadot.js.org/apps/?rpc=ws%3A%2F%2F127.0.0.1%3A9944#/explorer) и подключиться к 127.0.0.1:9944. , и вы должны увидеть создаваемые блоки.

![Edgeware-EVM-producing-blocks](../../.gitbook/assets/frontier-explorer.png)

Теперь вы можете продолжить подключение [Metamask](https://github.com/hicommonwealth/edgeware-documentation/blob/master/docs/quickstart/evm-introduction/interacting-with-a-edgeware-node-using-metamask. md), Remix и Web3.js, чтобы получить отличный опыт.

## Свяжитесь с нами для большего вовлечения

Рад, что вы прошли через это! 🥰 Мы стремимся помочь вам в ваших исследованиях с помощью функции совместимости Edgeware Ethereum. Мы **будем рады услышать ваш опыт и предложения, которые вы можете нам предложить.**. Вы можете свободно [общаться с нами в каналах Edgeware, таких как Discord, Element и Telegram](https://linktr.ee/edg_developers), мы можем помочь вам с проблемами, которые могут у вас возникнуть, или с проектом, который вы хотите финансировать. через нашу [программу казначейства](https://docs.edgewa.re/edgeware-runtime/treasury). Не стесняйтесь делиться своими отзывами на наших каналах, всегда есть место для улучшения! 🙌
