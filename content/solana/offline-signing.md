+++
title = "Подписание офлайн-транзакций"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Некоторые модели безопасности требуют хранения ключей подписи и, следовательно, процесса подписи отдельно от создания транзакций и широковещательной рассылки по сети. Примеры включают:

- Сбор подписей от географически разрозненных подписантов в [схеме с несколькими подписями](https://spl.solana.com/token#multisig-usage)
- Подписание транзакций с помощью устройства для подписи [airgapped](<https://en.wikipedia.org/wiki/Air_gap_(networking)>)

В этом документе описывается использование интерфейса командной строки Solana для отдельной подписи и отправки транзакции.

## Команды, поддерживающие автономную подпись

В настоящее время автономную подпись поддерживают следующие команды:

- [`create-stake-account`](cli/usage.md#solana-create-stake-account)

- [`create-stake-account-checked`](cli/usage.md#solana-create-stake-account-checked)

- [`deactivate-stake`](cli/usage.md#solana-deactivate-stake)

- [`delegate-stake`](cli/usage.md#solana-delegate-stake)

- [`split-stake`](cli/usage.md#solana-split-stake)

- [`stake-authorize`](cli/usage.md#solana-stake-authorize)

- [`stake-authorize-checked`](cli/usage.md#solana-stake-authorize-checked)

- [`stake-set-lockup`](cli/usage.md#solana-stake-set-lockup)

- [`stake-set-lockup-checked`](cli/usage.md#solana-stake-set-lockup-checked)

- [`transfer`](cli/usage.md#solana-transfer)

- [`withdraw-stake`](cli/usage.md#solana-withdraw-stake)

- [`create-vote-account`](cli/usage.md#solana-create-vote-account)

- [`vote-authorize-voter`](cli/usage.md#solana-vote-authorize-voter)

- [`vote-authorize-voter-checked`](cli/usage.md#solana-vote-authorize-voter-checked)

- [`vote-authorize-withdrawer`](cli/usage.md#solana-vote-authorize-withdrawer)

- [`vote-authorize-withdrawer-checked`](cli/usage.md#solana-vote-authorize-withdrawer-checked)

- [`vote-update-commission`](cli/usage.md#solana-vote-update-commission)

- [`vote-update-validator`](cli/usage.md#solana-vote-update-validator)

- [`withdraw-from-vote-account`](cli/usage.md#solana-withdraw-from-vote-account)

## Подписание транзакций в автономном режиме

Чтобы подписать транзакцию в автономном режиме, передайте следующие аргументы в командной строке.

1. `--sign-only` запрещает клиенту отправлять подписанную транзакцию в сеть. Вместо этого пары открытый ключ/подпись выводятся на стандартный вывод.
2. `--blockhash BASE58_HASH` позволяет вызывающей стороне указать значение, используемое для заполнения поля транзакции `recent_blockhash`. Это служит ряду
   целей, а именно: _ Устраняет необходимость подключения к сети и запроса последнего хэша блока через RPC _ Позволяет подписывающим сторонам координировать хеш блока в схеме с несколькими подписями

### Пример: автономная подпись платежа

Команда

```bash
solana@offline$ solana pay --sign-only --blockhash 5Tx8F3jgSHx21CbtjwmdaKPLM5tWmreWAnPrbqHomSJF \
    recipient-keypair.json 1
```

Вывод

```
Blockhash: 5Tx8F3jgSHx21CbtjwmdaKPLM5tWmreWAnPrbqHomSJF
Signers (Pubkey=Signature):
  FhtzLVsmcV7S5XqGD79ErgoseCLhZYmEZnz9kQg1Rp7j=4vC38p4bz7XyiXrk6HtaooUqwxTWKocf45cstASGtmrD398biNJnmTcUCVEojE7wVQvgdYbjHJqRFZPpzfCQpmUN

{"blockhash":"5Tx8F3jgSHx21CbtjwmdaKPLM5tWmreWAnPrbqHomSJF","signers":["FhtzLVsmcV7S5XqGD79ErgoseCLhZYmEZnz9kQg1Rp7j=4vC38p4bz7XyiXrk6HtaooUqwxTWKocf45cstASGtmrD398biNJnmTcUCVEojE7wVQvgdYbjHJqRFZPpzfCQpmUN"]}'
```

## Отправка автономных подписанных транзакций в сеть

Чтобы отправить транзакцию, подписанную в автономном режиме, в сеть, передайте следующие аргументы в командной строке.

1. `--blockhash BASE58_HASH` должен быть тем же хэшем, который использовался для подписи.
2. `--signer BASE58_PUBKEY=BASE58_SIGNATURE`, по одному для каждого подписавшего в автономном режиме. Это включает в себя пары открытого ключа/подписи непосредственно в транзакции, а не ее подписание какой-либо локальной парой ключей.

### Пример: отправка платежа с подписью в автономном режиме

Команда

```bash
solana@online$ solana pay --blockhash 5Tx8F3jgSHx21CbtjwmdaKPLM5tWmreWAnPrbqHomSJF \
    --signer FhtzLVsmcV7S5XqGD79ErgoseCLhZYmEZnz9kQg1Rp7j=4vC38p4bz7XyiXrk6HtaooUqwxTWKocf45cstASGtmrD398biNJnmTcUCVEojE7wVQvgdYbjHJqRFZPpzfCQpmUN
    recipient-keypair.json 1
```

Вывод

```
4vC38p4bz7XyiXrk6HtaooUqwxTWKocf45cstASGtmrD398biNJnmTcUCVEojE7wVQvgdYbjHJqRFZPpzfCQpmUN
```

## Автономный вход в течение нескольких сеансов

Подписание в автономном режиме также может выполняться в течение нескольких сеансов. В этом случае передайте открытый ключ отсутствующей подписывающей стороны для каждой роли. Все публичные ключи, которые были указаны, но для которых не была сгенерирована подпись, будут указаны как отсутствующие в выходных данных автономной подписи.

### Пример: передача с двумя автономными сеансами подписи

Команда (оффлайн сеанс № 1)

```
solana@offline1$ solana transfer Fdri24WUGtrCXZ55nXiewAj6RM18hRHPGAjZk3o6vBut 10 \
    --blockhash 7ALDjLv56a8f6sH6upAZALQKkXyjAwwENH9GomyM8Dbc \
    --sign-only \
    --keypair fee_payer.json \
    --from 674RgFMgdqdRoVtMqSBg7mHFbrrNm1h1r721H1ZMquHL
```

Вывод (оффлайн сеанс № 1)

```
Blockhash: 7ALDjLv56a8f6sH6upAZALQKkXyjAwwENH9GomyM8Dbc
Signers (Pubkey=Signature):
  3bo5YiRagwmRikuH6H1d2gkKef5nFZXE3gJeoHxJbPjy=ohGKvpRC46jAduwU9NW8tP91JkCT5r8Mo67Ysnid4zc76tiiV1Ho6jv3BKFSbBcr2NcPPCarmfTLSkTHsJCtdYi
Absent Signers (Pubkey):
  674RgFMgdqdRoVtMqSBg7mHFbrrNm1h1r721H1ZMquHL
```

Команда (оффлайн сеанс № 2)

```
solana@offline2$ solana transfer Fdri24WUGtrCXZ55nXiewAj6RM18hRHPGAjZk3o6vBut 10 \
    --blockhash 7ALDjLv56a8f6sH6upAZALQKkXyjAwwENH9GomyM8Dbc \
    --sign-only \
    --keypair from.json \
    --fee-payer 3bo5YiRagwmRikuH6H1d2gkKef5nFZXE3gJeoHxJbPjy
```

Вывод (оффлайн сеанс № 2)

```
Blockhash: 7ALDjLv56a8f6sH6upAZALQKkXyjAwwENH9GomyM8Dbc
Signers (Pubkey=Signature):
  674RgFMgdqdRoVtMqSBg7mHFbrrNm1h1r721H1ZMquHL=3vJtnba4dKQmEAieAekC1rJnPUndBcpvqRPRMoPWqhLEMCty2SdUxt2yvC1wQW6wVUa5putZMt6kdwCaTv8gk7sQ
Absent Signers (Pubkey):
  3bo5YiRagwmRikuH6H1d2gkKef5nFZXE3gJeoHxJbPjy
```

Команда (онлайн сеанс)

```
solana@online$ solana transfer Fdri24WUGtrCXZ55nXiewAj6RM18hRHPGAjZk3o6vBut 10 \
    --blockhash 7ALDjLv56a8f6sH6upAZALQKkXyjAwwENH9GomyM8Dbc \
    --from 674RgFMgdqdRoVtMqSBg7mHFbrrNm1h1r721H1ZMquHL \
    --signer 674RgFMgdqdRoVtMqSBg7mHFbrrNm1h1r721H1ZMquHL=3vJtnba4dKQmEAieAekC1rJnPUndBcpvqRPRMoPWqhLEMCty2SdUxt2yvC1wQW6wVUa5putZMt6kdwCaTv8gk7sQ \
    --fee-payer 3bo5YiRagwmRikuH6H1d2gkKef5nFZXE3gJeoHxJbPjy \
    --signer 3bo5YiRagwmRikuH6H1d2gkKef5nFZXE3gJeoHxJbPjy=ohGKvpRC46jAduwU9NW8tP91JkCT5r8Mo67Ysnid4zc76tiiV1Ho6jv3BKFSbBcr2NcPPCarmfTLSkTHsJCtdYi
```

Вывод (онлайн сеанс)

```
ohGKvpRC46jAduwU9NW8tP91JkCT5r8Mo67Ysnid4zc76tiiV1Ho6jv3BKFSbBcr2NcPPCarmfTLSkTHsJCtdYi
```

## Выиграйте больше времени для подписания

Обычно транзакция Solana должна быть подписана и принята сетью в пределах нескольких слотов от хэша блока в его поле `recent_blockhash` (~ 1 минута на момент написания этой статьи). Если ваша процедура подписания занимает больше времени, [Durable Transaction Nonce](offline-signing/durable-nonce/) может дать вам необходимое дополнительное время.
