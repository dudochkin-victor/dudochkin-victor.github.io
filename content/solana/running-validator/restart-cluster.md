+++
title = "Перезапуск кластера"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++


### Шаг 1. Определите слот, в котором будет перезапущен кластер

Самый высокий оптимистически подтвержденный слот — это лучший слот для начала, который можно найти, выполнив поиск [это] (https://github.com/solana-labs/solana/blob/0264147d42d506fb888f5c4c021a998e231a3e74/core/src/optimistic_confirmation_verifier.rs# L71) точка данных метрик. В противном случае используйте последний корень.

Назовите этот слот SLOT_X.

### Шаг 2. Остановить валидатор(ы)

### Шаг 3. При желании установите новую версию solana

### Шаг 4. Создайте новый снимок для слота `SLOT_X` с хард-форком в слоте `SLOT_X`

```bash
$ solana-ledger-tool -l ledger create-snapshot SLOT_X ledger --hard-fork SLOT_X
```

Каталог бухгалтерской книги теперь должен содержать новый снимок.
`solana-ledger-tool create-snapshot` также выведет новую версию уничтожения и значение хэша банка, назовите это NEW_SHRED_VERSION и NEW_BANK_HASH соответственно.

Настройте аргументы вашего валидатора:

```bash
 --wait-for-supermajority SLOT_X
 --expected-bank-hash NEW_BANK_HASH
```

Затем перезапустите валидатор.

Подтвердите с помощью журнала, что валидатор загрузился и теперь находится в шаблоне ожидания в `SLOT_X`, ожидая супербольшинства.

Как только NEW_SHRED_VERSION будет определена, подтолкните операторов точки входа фонда к обновлению точек входа.

### Шаг 5. Объявите о перезапуске в Discord:

Разместите что-то вроде следующего в #announcements (при необходимости изменив текст):

> Привет @Validators,
> 
> Мы выпустили версию 1.1.12 и готовы снова запустить тестовую сеть.
> 
> Шаги:
> 
> 1. Установите выпуск v1.1.12: https://github.com/solana-labs/solana/releases/tag/v1.1.12.
> 2. а. Предпочтительный метод, начните с вашей локальной книги с помощью:
> 
> ```bash
> solana-validator
>   --wait-for-supermajority SLOT_X     # <-- NEW! IMPORTANT! REMOVE AFTER THIS RESTART
>   --expected-bank-hash NEW_BANK_HASH  # <-- NEW! IMPORTANT! REMOVE AFTER THIS RESTART
>   --hard-fork SLOT_X                  # <-- NEW! IMPORTANT! REMOVE AFTER THIS RESTART
>   --no-snapshot-fetch                 # <-- NEW! IMPORTANT! REMOVE AFTER THIS RESTART
>   --entrypoint entrypoint.testnet.solana.com:8001
>   --known-validator 5D1fNXzvv5NjV1ysLjirC4WY92RNsVH18vjmcszZd8on
>   --expected-genesis-hash 4uhcVJyU9pJkvQyS88uRDiswHXSCkY3zQawwpjk2NsNY
>   --only-known-rpc
>   --limit-ledger-size
>   ...                                # <-- your other --identity/--vote-account/etc arguments
> ```
> 
> b. Если у вашего валидатора нет реестра до слота SLOT_X или если вы удалили свой реестр, вместо этого загрузите снимок с помощью:
> 
> ```bash
> solana-validator
>   --wait-for-supermajority SLOT_X     # <-- NEW! IMPORTANT! REMOVE AFTER THIS RESTART
>   --expected-bank-hash NEW_BANK_HASH  # <-- NEW! IMPORTANT! REMOVE AFTER THIS RESTART
>   --entrypoint entrypoint.testnet.solana.com:8001
>   --known-validator 5D1fNXzvv5NjV1ysLjirC4WY92RNsVH18vjmcszZd8on
>   --expected-genesis-hash 4uhcVJyU9pJkvQyS88uRDiswHXSCkY3zQawwpjk2NsNY
>   --only-known-rpc
>   --limit-ledger-size
>   ...                                # <-- your other --identity/--vote-account/etc arguments
> ```
> 
>      You can check for which slots your ledger has with: `solana-ledger-tool -l path/to/ledger bounds`
> 
> 3. Подождите, пока 80% ставки не перейдет в онлайн.
> 
> Чтобы подтвердить, что ваш перезапущенный валидатор правильно ожидает 80%:
> а. Ищите «N% активной доли, видимой в сообщениях журнала сплетен».
> б. Спросите его через RPC, в каком слоте он находится: `solana --url http://127.0.0.1:8899 slot`. Он должен возвращать `SLOT_X`, пока мы не достигнем доли 80%.
> 
> Спасибо!

### Шаг 7. Подождите и слушайте

Следите за валидаторами по мере их перезапуска. Отвечайте на вопросы, помогайте людям,

## Исправление проблем

### 80% доли не участвовали в перезапуске, что теперь?

Если менее 80% ставки присоединится к перезапуску через разумное время, необходимо будет повторить попытку перезапуска с удалением ставки от не отвечающих валидаторов.

Сообщество должно определить и прийти к общественному консенсусу в отношении набора неотзывчивых валидаторов. Затем все участвующие валидаторы возвращаются к шагу 4 и создают новый снимок с дополнительными аргументами `--detake-vote-account <PUBKEY>` для каждого адреса учетной записи для голосования не отвечающего валидатора.

```bash
$ solana-ledger-tool -l ledger create-snapshot SLOT_X ledger --hard-fork SLOT_X \
    --destake-vote-account <VOTE_ACCOUNT_1> \
    --destake-vote-account <VOTE_ACCOUNT_2> \
    .
    .
    --destake-vote-account <VOTE_ACCOUNT_N> \
```

Это приведет к немедленной деактивации всех ставок, связанных с не отвечающими на запросы валидаторами. Все их участники должны будут повторно делегировать свои доли после успешного перезапуска кластера.
