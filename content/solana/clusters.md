+++
title = "Кластеры Solana"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Solana поддерживает несколько разных кластеров с разными целями.

Прежде чем начать, убедитесь, что вы сначала [установили инструменты командной строки Solana](cli/install-solana-cli-tools/)

Исследователи:

- [http://explorer.solana.com/](https://explorer.solana.com/).
- [http://solanabeach.io/](http://solanabeach.io/).

## Девнет

- Devnet служит игровой площадкой для всех, кто хочет протестировать Солану в качестве пользователя, владельца токена, разработчика приложения или валидатора.
- Разработчики приложений должны ориентироваться на Devnet.
- Потенциальные валидаторы должны сначала ориентироваться на Devnet.
- Ключевые различия между Devnet и Mainnet Beta:
  - Токены Devnet **не настоящие**
  - Devnet включает сборщик токенов для аирдропов для тестирования приложений.
  - Devnet может подлежать сбросу реестра.
  - Devnet обычно использует более новую версию программного обеспечения, чем бета-версия Mainnet.
- Точка входа сплетен для Devnet: `entrypoint.devnet.solana.com:8001`
- Переменная среды Metrics для Devnet:

```bash
export SOLANA_METRICS_CONFIG="host=https://metrics.solana.com:8086,db=devnet,u=scratch_writer,p=topsecret"
```

- RPC URL для Devnet: `https://api.devnet.solana.com`

##### Пример конфигурации командной строки solana

```bash
solana config set --url https://api.devnet.solana.com
```

##### Пример командной строки `solana-validator`

```bash
$ solana-validator \
    --identity validator-keypair.json \
    --vote-account vote-account-keypair.json \
    --known-validator dv1ZAGvdsz5hHLwWXsVnM94hWf1pjbKVau1QVkaMJ92 \
    --known-validator dv2eQHeP4RFrJZ6UeiZWoc3XTtmtZCUKxxCApCDcRNV \
    --known-validator dv4ACNkpYPcE3aKmYDqZm9G5EB3J4MRoeE7WNDRBVJB \
    --known-validator dv3qDFk1DTF36Z62bNvrCXe9sKATA6xvVy6A798xxAS \
    --only-known-rpc \
    --ledger ledger \
    --rpc-port 8899 \
    --dynamic-port-range 8000-8020 \
    --entrypoint entrypoint.devnet.solana.com:8001 \
    --entrypoint entrypoint2.devnet.solana.com:8001 \
    --entrypoint entrypoint3.devnet.solana.com:8001 \
    --entrypoint entrypoint4.devnet.solana.com:8001 \
    --entrypoint entrypoint5.devnet.solana.com:8001 \
    --expected-genesis-hash EtWTRABZaYq6iMfeYKouRu166VU2xqa1wcaWoxPkrZBG \
    --wal-recovery-mode skip_any_corrupted_record \
    --limit-ledger-size
```

[`--known-validator`s](running-validator/validator-start.md#known-validators) управляются Solana Labs.

## Тестовая сеть

- Тестовая сеть — это место, где мы проводим стресс-тестирование функций последних выпусков на действующем кластере, уделяя особое внимание производительности сети, стабильности и поведению валидатора.
- Токены тестовой сети **не настоящие**
- Testnet может подлежать сбросу реестра.
- Testnet включает сборщик токенов для аирдропов для тестирования приложений.
- Testnet обычно использует более новую версию программного обеспечения, чем бета-версия Devnet и Mainnet.
- Точка входа для сплетен для Testnet: `entrypoint.testnet.solana.com:8001`
- Переменная среды Metrics для Testnet:

```bash
export SOLANA_METRICS_CONFIG="host=https://metrics.solana.com:8086,db=tds,u=testnet_write,p=c4fa841aa918bf8274e3e2a44d77568d9861b3ea"
```

- RPC URL для Testnet: `https://api.testnet.solana.com`

##### Пример конфигурации командной строки solana

```bash
solana config set --url https://api.testnet.solana.com
```

##### Пример командной строки `solana-validator`

```bash
$ solana-validator \
    --identity validator-keypair.json \
    --vote-account vote-account-keypair.json \
    --known-validator 5D1fNXzvv5NjV1ysLjirC4WY92RNsVH18vjmcszZd8on \
    --known-validator 7XSY3MrYnK8vq693Rju17bbPkCN3Z7KvvfvJx4kdrsSY \
    --known-validator Ft5fbkqNa76vnsjYNwjDZUXoTWpP7VYm3mtsaQckQADN \
    --known-validator 9QxCLckBiJc783jnMvXZubK4wH86Eqqvashtrwvcsgkv \
    --only-known-rpc \
    --ledger ledger \
    --rpc-port 8899 \
    --dynamic-port-range 8000-8020 \
    --entrypoint entrypoint.testnet.solana.com:8001 \
    --entrypoint entrypoint2.testnet.solana.com:8001 \
    --entrypoint entrypoint3.testnet.solana.com:8001 \
    --expected-genesis-hash 4uhcVJyU9pJkvQyS88uRDiswHXSCkY3zQawwpjk2NsNY \
    --wal-recovery-mode skip_any_corrupted_record \
    --limit-ledger-size
```

Личности
[`--known-validator`s](running-validator/validator-start.md#known-validators):

- `5D1fNXzvv5NjV1ysLjirC4WY92RNsVH18vjmcszZd8on` - Solana Labs (testnet.solana.com)
- `Ft5fbkqNa76vnsjYNwjDZUXoTWpP7VYm3mtsaQckQADN` - Certus One
- `9QxCLckBiJc783jnMvXZubK4wH86Eqqvashtrwvcsgkv` - Algo|Stake

## Бета-версия основной сети

Постоянный кластер без разрешений для первых держателей токенов и партнеров по запуску.

- Токены, выпущенные в бета-версии основной сети, являются **настоящими** SOL.
- Если вы заплатили деньги за покупку/выпуск токенов, например, через наш аукцион CoinList, эти токены будут переведены в бета-версию основной сети.
  - Примечание. Если вы используете кошелек без командной строки, такой как [Solflare](wallet-guide/solflare/), кошелек всегда будет подключаться к бета-версии основной сети.
- Точка входа сплетен для бета-версии основной сети: `entrypoint.mainnet-beta.solana.com:8001`
- Переменная среды Metrics для бета-версии основной сети:

```bash
export SOLANA_METRICS_CONFIG="host=https://metrics.solana.com:8086,db=mainnet-beta,u=mainnet-beta_write,p=password"
```

- RPC URL для Mainnet Beta: `https://api.mainnet-beta.solana.com`

##### Пример конфигурации командной строки solana

```bash
solana config set --url https://api.mainnet-beta.solana.com
```

##### Пример командной строки `solana-validator`

```bash
$ solana-validator \
    --identity ~/validator-keypair.json \
    --vote-account ~/vote-account-keypair.json \
    --known-validator 7Np41oeYqPefeNQEHSv1UDhYrehxin3NStELsSKCT4K2 \
    --known-validator GdnSyH3YtwcxFvQrVVJMm1JhTS4QVX7MFsX56uJLUfiZ \
    --known-validator DE1bawNcRJB9rVm3buyMVfr8mBEoyyu73NBovf2oXJsJ \
    --known-validator CakcnaRDHka2gXyfbEd2d3xsvkJkqsLw2akB3zsN1D2S \
    --only-known-rpc \
    --ledger ledger \
    --rpc-port 8899 \
    --private-rpc \
    --dynamic-port-range 8000-8020 \
    --entrypoint entrypoint.mainnet-beta.solana.com:8001 \
    --entrypoint entrypoint2.mainnet-beta.solana.com:8001 \
    --entrypoint entrypoint3.mainnet-beta.solana.com:8001 \
    --entrypoint entrypoint4.mainnet-beta.solana.com:8001 \
    --entrypoint entrypoint5.mainnet-beta.solana.com:8001 \
    --expected-genesis-hash 5eykt4UsFv8P8NJdTREpY1vzqKqZKvdpKuc147dw2N9d \
    --wal-recovery-mode skip_any_corrupted_record \
    --limit-ledger-size
```

Все четыре [`--known-validator`s](running-validator/validator-start.md#known-validators) управляются Solana Labs.
