+++
title = "API Розетты"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

## [https://github.com/edgeware-network/substrate-rosetta-api](https://github.com/edgeware-network/substrate-rosetta-api)

## Введение

[Rosetta API](https://www.rosetta-api.org/) — это инструмент разработки, созданный [Coinbase](https://coinbase.com/), который позволяет блокчейнам быть более совместимыми с различными третьими стороннее программное обеспечение, включая биржи (например, Coinbase), кошельки, внебиржевые столы и приложения.

В сочетании с [Substrate,](https://substrate.dev/), который является [гибким,](https://substrate.io/technology/flexible/) [открытым,](https://substrate.io /technology/open/) [совместимая,](https://substrate.io/technology/interoperable/) и [защищенная от будущего](https://substrate.io/technology/future-proof/) фреймворк для разработки блокчейнов , у нас есть два ведущих в отрасли способа разработки и взаимодействия с платформами на основе блокчейна. Преимущества этой комбинации позволяют любой цепочке Substrate тривиально реализовать совместимые инструменты, такие как этот репозиторий Rosetta API.

## Предпосылки

**Доступ к узлу архива**

Вам потребуется доступ к **[архивному узлу](https://wiki.polkadot.network/docs/maintain-sync)** [](https://wiki.polkadot.network/docs/maintain-sync) чтобы иметь возможность запрашивать всю цепочку блоков. Вы можете использовать общедоступные узлы или запускать их локально, используя флаг `--pruning archive` при запуске узла. Вы также можете использовать флаг `--wasm-execution Compiled`, чтобы значительно ускорить время синхронизации, однако это использует больше ЦП и ОЗУ, поэтому вы должны отключить его после завершения синхронизации.

**Настройка Rosetta-api**

1. `git clone https://github.com/edgeware-network/substrate-rosetta-api`
2. Отредактируйте файлы конфигурации в соответствии с вашей цепочкой. Они находятся в папке `networks`. Информацию можно найти в самом узле и в [subscan](https://polkadot.subscan.io/) или в предпочитаемом обозревателе блоков. Вы также можете использовать [приложения Polkadot.](https://polkadot.js.org/apps/#/explorer)
3. Добавьте метаданные вашей цепочки в шестнадцатеричном формате в `networks/metadata`. Вы можете получить это с помощью [инструмента subxt CLI] (https://github.com/paritytech/subxt), по умолчанию он работает на локальном узле, или вы можете использовать другой узел, указав --url.
   - 1. Вы можете получить метаданные, выполнив команду `subxt metadata --format hex`
4. Добавьте свои типы цепочек. Это можно сделать одним из двух способов:
   - 1. Добавьте типы вручную в файл `polkadot-types.json`.
        2. Если ваша цепочка использует пакет узла, такой как Edgeware, вы добавляете спецификацию цепочки в `src/helpers/connections.js` с типами в `class SubstrateNetworkConnection`
5. После завершения запустите yarn install и yarn start.

**Настройка Rosetta-cli**

1. В отдельном тесте терминала с [rosetta-cli] (https://github.com/coinbase/rosetta-cli). После загрузки вам, скорее всего, придется установить свой путь для rosetta-cli с помощью этой команды `export PATH= Например, $PATH:/Users/bin.

2. После установки `Используйте "rosetta-cli [команда] --help" для получения дополнительной информации о команде.` Это список примеров команд rosetta-cli. Обратите внимание, что вы должны указывать, какой файл конфигурации вы хотите нам каждый раз.

3. `rosetta-cli view:balance '{"address":"ADDRESS-GOES-HERE"}' BLOCK-NUMBER-GOES-HERE --configuration-file ./rosetta-api/rosetta-cli/mainnet/config. json`

4. `rosetta-cli view:block BLOCK-NUMBER-GOES-HERE --configuration-file ./Depth-rosetta-api/rosetta-cli/mainnet/config.json`

5. `rosetta-cli view:networks --configuration-file ./rosetta-api/rosetta-cli/mainnet/config.json`

6. `rosetta-cli check:data --configuration-file ./rosetta-api/rosetta-cli/mainnet/config.json`

Попробуйте все возможные тесты с rosetta-cli.

- Теперь у вас есть рабочий экземпляр rosetta-api для вашей цепочки субстратов. Пожалуйста, не стесняйтесь, дайте нам знать, как мы можем улучшить его в будущем. Наш API — это ваш API.

## Связаться с нами:

Discord: [https://discord.gg/wFbystdrHC](https://discord.gg/wFbystdrHC)

Telegram: [https://t.me/heyedgeware](https://t.me/heyedgeware)

## Дополнительные ресурсы:

1. [https://www.rosetta-api.org/](https://www.rosetta-api.org/)
2. [https://www.rosetta-api.org/docs/welcome.html](https://www.rosetta-api.org/docs/welcome.html)
3. [https://community.rosetta-api.org/](https://community.rosetta-api.org/)
4. [https://docs.substrate.io/](https://docs.substrate.io/)
5. [https://polkadot.js.org/apps/#/explorer](https://polkadot.js.org/apps/#/explorer)
6. [https://www.subscan.io/](https://www.subscan.io/)