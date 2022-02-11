+++
title = "Узел Edgeware"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Блокчейн-сети состоят из отдельных **узлов**, соединенных одноранговой (P2P) сетью. Узлы — это отдельные компьютеры в сети, на которых работает программное обеспечение блокчейна, благодаря которому все работает. Подраздел «Узел Edgeware» содержит все, что относится к узлам, работающим в Edgeware.

## Для операторов узлов, валидаторов и других пользователей

Руководство по началу работы можно найти на нашей [Github Wiki](https://github.com/hicommonwealth/edgeware-node/wiki), включая руководства по запуску узла, проверке и настройке основных инструментов мониторинга, чтобы сохранить ваш узел онлайн.

## Чтобы запустить узел и подключиться к Wako v3.3.3

Репозиторий GitHub: [https://github.com/edgeware-network/edgeware-node](https://github.com/edgeware-network/edgeware-node)

Для основной сети запустите:

```
docker run --rm -it decentration/edgeware:v3.3.3 edgeware --chain=edgeware --name <INSERT-NAME> --wasm-execution Compiled
```

Для тестовой сети Beresheet запустите:

```
docker run --rm -it decentration/edgeware:v3.3.3 edgeware --chain=beresheet --name <INSERT_NAME>
```

Если у вас есть дополнительные вопросы или информация, обратитесь к нашему каналу `builders-general` в [Edgeware Discord](https://discord.gg/zdFJm4gA5M).

{% content-ref url="../../quickstart/set-up-a-full-node.md" %}
[set-up-a-full-node.md](../../quickstart/set-up-a-full-node/)
{% endcontent-ref %}

{% content-ref url="../../development/develop/smart-contracts/evm-smart-contracts/tutorials/deploy-an-evm-contract/setting-up-a-edgeware-evm-node.md" %}
[setting-up-a-edgeware-evm-node.md](../../development/develop/smart-contracts/evm-smart-contracts/tutorials/deploy-an-evm-contract/setting-up-a-edgeware-evm-node/)
{% endcontent-ref %}

{% content-ref url="../../development/develop/smart-contracts/wasm-smart-contracts/tutorials/deploy-a-wasm-contract/running-an-edgeware-node.md" %}
[running-an-edgeware-node.md](../../development/develop/smart-contracts/wasm-smart-contracts/tutorials/deploy-a-wasm-contract/running-an-edgeware-node/)
{% endcontent-ref %}
