+++
title = "Одноразовые номера устойчивых транзакций"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Устойчивые одноразовые номера транзакций — это механизм, позволяющий обойти типичный короткий жизненный цикл транзакции [`recent_blockhash`](developing/programming-model/transactions.md#recent-blockhash). Они реализованы в виде программы Solana, о механике которой можно прочитать в [предложении](../implemented-proposals/durable-tx-nonces/).

## Примеры использования

Полную информацию об использовании CLI-команд с устойчивым одноразовым номером можно найти в [справке по CLI](../cli/usage/).

### Полномочия одноразового номера

Полномочия над одноразовой учетной записью могут быть дополнительно назначены другой учетной записи. При этом новый орган наследует полный контроль над одноразовой учетной записью от предыдущего органа, включая создателя учетной записи. Эта функция позволяет создавать более сложные механизмы владения учетными записями и производные адреса учетных записей, не связанные с парой ключей. Аргумент `--nonce-authority <AUTHORITY_KEYPAIR>` используется для указания этой учетной записи и поддерживается следующими командами.

- `создать одноразовый аккаунт`
- `новый одноразовый номер`
- `снятие с одноразового счета`
- `авторизовать одноразовый аккаунт`

### Создание одноразовой учетной записи

Функция одноразового номера устойчивой транзакции использует учетную запись для хранения следующего значения одноразового номера. Аккаунты с постоянным одноразовым номером должны быть [освобождены от арендной платы](../implemented-proposals/rent.md#two-tiered-rent-regime), поэтому для достижения этого необходимо иметь минимальный баланс.

Учетная запись nonce создается путем создания новой пары ключей, а затем создания учетной записи в цепочке.

- Команда

```bash
solana-keygen new -o nonce-keypair.json
solana create-nonce-account nonce-keypair.json 1
```

- Вывод

```
2SymGjGV4ksPdpbaqWFiDoBz8okvtiik4KE9cnMQgRHrRLySSdZ6jrEcpPifW4xUpp4z66XM9d9wM48sA7peG2XL
```

> Чтобы пара ключей оставалась полностью автономной, используйте [инструкции] генерации пары ключей [Paper Wallet](wallet-guide/paper-wallet/)(wallet-guide/paper-wallet.md#seed-phrase-generation)

> [Полная документация по использованию](../cli/usage.md#solana-create-nonce-account)

### Запрос сохраненного значения одноразового номера

Создание надежной транзакции одноразового номера требует передачи сохраненного значения одноразового номера в качестве значения аргумента `--blockhash` при подписании и отправке. Получите текущее сохраненное значение nonce с помощью

- Команда

```bash
solana nonce nonce-keypair.json
```

- Вывод

```
8GRipryfxcsxN8mAGjy8zbFo9ezaUsh47TsPzmZbuytU
```

> [Полная документация по использованию](../cli/usage.md#solana-get-nonce)

### Расширение значения сохраненного одноразового номера

Хотя обычно это не требуется за пределами более полезной транзакции, сохраненное значение одноразового номера может быть расширено с помощью

- Команда

```bash
solana new-nonce nonce-keypair.json
```

- Вывод

```
44jYe1yPKrjuYDmoFTdgPjg8LFpYyh1PFKJqm5SC1PiSyAL8iw1bhadcAX1SL7KDmREEkmHpYvreKoNv6fZgfvUK
```

> [Полная документация по использованию](../cli/usage.md#solana-new-nonce)

### Показать учетную запись одноразового номера

Проверьте учетную запись nonce в более удобном для человека формате с помощью

- Команда

```bash
solana nonce-account nonce-keypair.json
```

- Вывод

```
balance: 0.5 SOL
minimum balance required: 0.00136416 SOL
nonce: DZar6t2EaCFQTbUP4DHKwZ1wT8gCPW2aRfkVWhydkBvS
```

> [Полная документация по использованию](../cli/usage.md#solana-nonce-account)

### Вывод средств с одноразового счета

Вывод средств с одноразового счета с помощью

- Команда

```bash
solana withdraw-from-nonce-account nonce-keypair.json ~/.config/solana/id.json 0.5
```

- Вывод

```
3foNy1SBqwXSsfSfTdmYKDuhnVheRnKXpoPySiUDBVeDEs6iMVokgqm7AqfTjbk7QBE8mqomvMUMNQhtdMvFLide
```

> Закройте учетную запись nonce, сняв весь баланс

> [Полная документация по использованию](../cli/usage.md#solana-withdraw-from-nonce-account)

### Назначение новых полномочий учетной записи Nonce

Переназначить полномочия одноразовой учетной записи после создания с помощью

- Команда

```bash
solana authorize-nonce-account nonce-keypair.json nonce-authority.json
```

- Вывод

```
3F9cg4zN9wHxLGx4c3cUKmqpej4oa67QbALmChsJbfxTgTffRiL3iUehVhR9wQmWgPua66jPuAYeL1K2pYYjbNoT
```

> [Полная документация по использованию](../cli/usage.md#solana-authorize-nonce-account)

## Другие команды, поддерживающие устойчивые одноразовые номера

Чтобы использовать устойчивые одноразовые номера с другими подкомандами CLI, необходимо поддерживать два аргумента.

- `--nonce`, указывает учетную запись, хранящую значение nonce
- `--nonce-authority`, указывает необязательный nonce полномочия

Следующие подкоманды получили эту обработку до сих пор

- [`pay`](../cli/usage.md#solana-pay)
- [`delegate-stake`](../cli/usage.md#solana-delegate-stake)
- [`deactivate-stake`](../cli/usage.md#solana-deactivate-stake)

### Пример оплаты с использованием прочного одноразового номера

Здесь мы демонстрируем, как Алиса платит Бобу 1 SOL, используя устойчивый одноразовый номер. Процедура одинакова для всех подкоманд, поддерживающих устойчивые одноразовые номера.

#### - Создание учетных записей

Сначала нам нужны учетные записи для Алисы, одноразового номера Алисы и Боба.

```bash
$ solana-keygen new -o alice.json
$ solana-keygen new -o nonce.json
$ solana-keygen new -o bob.json
```

#### - Пополнить счет Алисы

Алисе потребуются средства, чтобы создать одноразовую учетную запись и отправить ее Бобу. Раздайте ей немного SOL

```bash
$ solana airdrop -k alice.json 1
1 SOL
```

#### - Создать одноразовый аккаунт Алисы

Теперь Алисе нужна одноразовая учетная запись. Создай

> Здесь не используется отдельный nonce полномочие, поэтому alice.json имеет полные права доступа к учетной записи nonce

```bash
$ solana create-nonce-account -k alice.json nonce.json 0.1
3KPZr96BTsL3hqera9up82KAU462Gz31xjqJ6eHUAjF935Yf8i1kmfEbo6SVbNaACKE5z6gySrNjVRvmS8DcPuwV
```

#### - Неудачная первая попытка заплатить Бобу

Алиса пытается заплатить Бобу, но подписывает слишком долго. Срок действия указанного хэша истекает, и транзакция не выполняется.

```bash
$ solana pay -k alice.json --blockhash expiredDTaxfagttWjQweib42b6ZHADSx94Tw8gHx3W7 bob.json 0.01
[2020-01-02T18:48:28.462911000Z ERROR solana_cli::cli] Io(Custom { kind: Other, error: "Transaction \"33gQQaoPc9jWePMvDAeyJpcnSPiGUAdtVg8zREWv4GiKjkcGNufgpcbFyRKRrA25NkgjZySEeKue5rawyeH5TzsV\" failed: None" })
Error: Io(Custom { kind: Other, error: "Transaction \"33gQQaoPc9jWePMvDAeyJpcnSPiGUAdtVg8zREWv4GiKjkcGNufgpcbFyRKRrA25NkgjZySEeKue5rawyeH5TzsV\" failed: None" })
```

#### - Нонсенс на помощь!

Алиса повторяет транзакцию, на этот раз указывая свою учетную запись nonce и хранящийся там блокхеш.

> Помните, что в этом примере `alice.json` — это nonce Authority

```bash
$ solana nonce-account nonce.json
balance: 0.1 SOL
minimum balance required: 0.00136416 SOL
nonce: F7vmkY3DTaxfagttWjQweib42b6ZHADSx94Tw8gHx3W7
```

```bash
$ solana pay -k alice.json --blockhash F7vmkY3DTaxfagttWjQweib42b6ZHADSx94Tw8gHx3W7 --nonce nonce.json bob.json 0.01
HR1368UKHVZyenmH7yVz5sBAijV6XAPeWbEiXEGVYQorRMcoijeNAbzZqEZiH8cDB8tk65ckqeegFjK8dHwNFgQ
```

#### - Успех!

Сделка прошла успешно! Боб получает 0,01 SOL от Алисы, и сохраненный одноразовый номер Алисы изменяется на новое значение.

```bash
$ solana balance -k bob.json
0.01 SOL
```

```bash
$ solana nonce-account nonce.json
balance: 0.1 SOL
minimum balance required: 0.00136416 SOL
nonce: 6bjroqDcZgTv6Vavhqf81oBHTv3aMnX19UTB51YhAZnN
```
