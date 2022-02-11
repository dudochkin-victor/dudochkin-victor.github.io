+++
title = "Стэйкинг"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Чтобы получить общее представление о ставках, сначала прочитайте [Часто задаваемые вопросы о ставках и инфляции].(https://solana.com/staking).

После того, как вы [получили SOL](transfer-tokens/), вы можете рассмотреть возможность его использования, делегировав _stake_ валидатору. Ставка — это то, что мы называем токенами в _аккаунте доли_. Солана взвешивает голоса валидаторов в соответствии с делегированной им долей, что дает этим валидаторам больше влияния при определении следующего действительного блока транзакций в блокчейне. Затем Солана периодически генерирует новый SOL для вознаграждения стейкеров и валидаторов. Вы зарабатываете тем больше вознаграждений, чем больше делегируете доли.

## Создать учетную запись доли

Чтобы делегировать долю, вам нужно будет перевести несколько токенов на счет доли. Для создания учетной записи вам понадобится пара ключей. Его открытый ключ будет использоваться как [адрес стейк-аккаунта](../staking/stake-accounts.md#account-address).
Здесь не нужен пароль или шифрование; эта пара ключей будет удалена сразу после создания учетной записи доли.

```bash
solana-keygen new --no-passphrase -o stake-account.json
```

Вывод будет содержать открытый ключ после текста `pubkey:`

```
pubkey: GKvqsuNcnwWqPzzuhLmGi4rzzh55FhJtGizkhHaEJqiV
```

Скопируйте открытый ключ и сохраните его для сохранности. Он понадобится вам в любое время, когда вы захотите выполнить действие с учетной записью доли, которую вы создадите в следующий раз.

Теперь создайте учетную запись:

```bash
solana create-stake-account --from <KEYPAIR> stake-account.json <AMOUNT> \
    --stake-authority <KEYPAIR> --withdraw-authority <KEYPAIR> \
    --fee-payer <KEYPAIR>
```

Токены в количестве `<AMOUNT>` переносятся с учетной записи "от" `<KEYPAIR>` на новую учетную запись доли с открытым ключом Stain-account.json.

Файл Staking-account.json теперь можно удалить. Чтобы авторизовать дополнительные действия, вы будете использовать пару ключей `--stake-authority` или `--withdraw-authority`, а не стейк-аккаунт.json.

Просмотрите новую учетную запись доли с помощью команды `solana Staking-Account`:

```bash
solana stake-account <STAKE_ACCOUNT_ADDRESS>
```

Вывод будет выглядеть примерно так:

```
Total Stake: 5000 SOL
Stake account is undelegated
Stake Authority: EXU95vqs93yPeCeAU7mPPu6HbRUmTFPEiGug9oCdvQ5F
Withdraw Authority: EXU95vqs93yPeCeAU7mPPu6HbRUmTFPEiGug9oCdvQ5F
```

### Установить ставку и отозвать полномочия

[Полномочия на ставку и отзыв](../staking/stake-accounts.md#understanding-account-authorities) можно установить при создании учетной записи с помощью параметров `--stake-authority` и `--withdraw-authority`, или после этого с помощью команды `solana_stake-authorize`. Например, чтобы установить новый орган управления колом, запустите:

```bash
solana stake-authorize <STAKE_ACCOUNT_ADDRESS> \
    --stake-authority <KEYPAIR> --new-stake-authority <PUBKEY> \
    --fee-payer <KEYPAIR>
```

При этом будет использоваться существующий орган `<KEYPAIR>` для авторизации нового органа `<PUBKEY>` в учетной записи стейка `<STAKE_ACCOUNT_ADDRESS>`.

### Дополнительно: получение адресов счетов ставок

Когда вы делегируете долю, вы делегируете все токены в учетной записи доли одному валидатору. Чтобы делегировать полномочия нескольким валидаторам, вам потребуется несколько учетных записей ставок. Создание новой пары ключей для каждой учетной записи и управление этими адресами может быть обременительным. К счастью, вы можете получить адреса ставок, используя опцию --seed:

```bash
solana create-stake-account --from <KEYPAIR> <STAKE_ACCOUNT_KEYPAIR> --seed <STRING> <AMOUNT> \
    --stake-authority <PUBKEY> --withdraw-authority <PUBKEY> --fee-payer <KEYPAIR>
```

`<STRING>` — это произвольная строка длиной до 32 байт, но обычно это число, соответствующее производной учетной записи. Первая учетная запись может быть «0», затем «1» и так далее. Открытый ключ `<STAKE_ACCOUNT_KEYPAIR>` действует как базовый адрес. Команда извлекает новый адрес из базового адреса и исходной строки. Чтобы увидеть, какой адрес стейка получит команда, используйте `solana create-address-with-seed`:

```bash
solana create-address-with-seed --from <PUBKEY> <SEED_STRING> STAKE
```

`<PUBKEY>` — это открытый ключ `<STAKE_ACCOUNT_KEYPAIR>`, переданный в `solana create-stake-account`.

Команда выведет производный адрес, который можно использовать в качестве аргумента `<STAKE_ACCOUNT_ADDRESS>` в операциях стейкинга.

## Делегировать долю

Чтобы делегировать свою ставку валидатору, вам понадобится адрес его счета для голосования. Найдите его, запросив у кластера список всех валидаторов и их учетных записей для голосования с помощью команды solana validators:

```bash
solana validators
```

Первый столбец каждой строки содержит идентификатор валидатора, а второй — адрес учетной записи для голосования. Выберите валидатор и используйте адрес его счета для голосования в solana delegate-stake:

```bash
solana delegate-stake --stake-authority <KEYPAIR> <STAKE_ACCOUNT_ADDRESS> <VOTE_ACCOUNT_ADDRESS> \
    --fee-payer <KEYPAIR>
```

Авторитет доли `<KEYPAIR>` разрешает операцию с учетной записью с адресом `<STAKE_ACCOUNT_ADDRESS>`. Ставка делегируется на счет для голосования с адресом `<VOTE_ACCOUNT_ADDRESS>`.

После делегирования ставки используйте `солана стейк-аккаунт`, чтобы наблюдать за изменениями в стейк-счете:

```bash
solana stake-account <STAKE_ACCOUNT_ADDRESS>
```

В выводе вы увидите новые поля «Делегированная доля» и «Адрес делегированной учетной записи для голосования». Вывод будет выглядеть примерно так:

```
Total Stake: 5000 SOL
Credits Observed: 147462
Delegated Stake: 4999.99771712 SOL
Delegated Vote Account Address: CcaHc2L43ZWjwCHART3oZoJvHLAe9hzT2DJNUpBzoTN1
Stake activates starting from epoch: 42
Stake Authority: EXU95vqs93yPeCeAU7mPPu6HbRUmTFPEiGug9oCdvQ5F
Withdraw Authority: EXU95vqs93yPeCeAU7mPPu6HbRUmTFPEiGug9oCdvQ5F
```

## Деактивировать ставку

После делегирования вы можете отменить делегирование ставки с помощью команды `solana deactivate-stake`:

```bash
solana deactivate-stake --stake-authority <KEYPAIR> <STAKE_ACCOUNT_ADDRESS> \
    --fee-payer <KEYPAIR>
```

Авторитет доли `<KEYPAIR>` разрешает операцию с учетной записью с адресом `<STAKE_ACCOUNT_ADDRESS>`.

Обратите внимание, что ставка требует несколько эпох, чтобы «остыть». Попытки делегировать ставку в период охлаждения потерпят неудачу.

## Снять ставку

Переведите токены со стейк-аккаунта с помощью команды solana remove-stake:

```bash
solana withdraw-stake --withdraw-authority <KEYPAIR> <STAKE_ACCOUNT_ADDRESS> <RECIPIENT_ADDRESS> <AMOUNT> \
    --fee-payer <KEYPAIR>
```

`<STAKE_ACCOUNT_ADDRESS>` – существующая учетная запись для ставок, орган управления ставками `<KEYPAIR>` – это полномочия для вывода средств, а `<AMOUNT>` – количество токенов для перевода на `<RECIPIENT_ADDRESS>`.

## Разделить ставку

Вы можете делегировать ставку дополнительным валидаторам, в то время как ваша существующая ставка не подлежит снятию. Он может не соответствовать требованиям, потому что в настоящее время он поставлен на карту, остывает или заблокирован. Чтобы перевести токены с существующей учетной записи доли на новую, используйте команду «solana split-stake»:

```bash
solana split-stake --stake-authority <KEYPAIR> <STAKE_ACCOUNT_ADDRESS> <NEW_STAKE_ACCOUNT_KEYPAIR> <AMOUNT> \
    --fee-payer <KEYPAIR>
```

`<STAKE_ACCOUNT_ADDRESS>` — это существующая учетная запись доли, полномочия доли `<KEYPAIR>` — полномочия доли, `<NEW_STAKE_ACCOUNT_KEYPAIR>` — пара ключей для новой учетной записи, а `<AMOUNT>` — количество токенов для передачи
на новый аккаунт.

Чтобы разделить учетную запись доли на производный адрес учетной записи, используйте параметр `--seed`. Подробности см. в Адреса учетных записей для получения ставок.
