+++
title = "Использование аппаратных кошельков в Solana CLI"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Для подписания транзакции требуется закрытый ключ, но хранение закрытого ключа на вашем персональном компьютере или телефоне делает его уязвимым для кражи.
Добавление пароля к вашему ключу повышает безопасность, но многие люди предпочитают сделать еще один шаг и переместить свои закрытые ключи на отдельное физическое устройство, называемое _аппаратным кошельком_. Аппаратный кошелек — это небольшое портативное устройство, которое хранит закрытые ключи и предоставляет некоторый интерфейс для подписания транзакций.

Solana CLI имеет первоклассную поддержку аппаратных кошельков. Везде, где вы используете путь к файлу пары ключей (обозначается как `<KEYPAIR>` в документации по использованию), вы можете передать _URL-адрес пары ключей_, который однозначно идентифицирует пару ключей в аппаратном кошельке.

## Поддерживаемые аппаратные кошельки

Solana CLI поддерживает следующие аппаратные кошельки:

- [Ledger Nano S и Ledger Nano X](hardware-wallets/ledger/)

## Укажите URL пары ключей

Solana определяет формат URL-адреса пары ключей, чтобы однозначно находить любую пару ключей Solana в аппаратном кошельке, подключенном к вашему компьютеру.

URL пары ключей имеет следующую форму, где квадратные скобки обозначают необязательные поля:

```
usb://<MANUFACTURER>[/<WALLET_ID>][?key=<DERIVATION_PATH>]
```

`WALLET_ID` — это глобально уникальный ключ, используемый для устранения неоднозначности нескольких устройств.

DERVIATION_PATH используется для перехода к ключам Solana в вашем аппаратном кошельке. Путь имеет форму `<ACCOUNT>[/<CHANGE>]`, где каждый `ACCOUNT` и `CHANGE` представляют собой положительные целые числа.

Например, полный URL-адрес для устройства Ledger может быть следующим:

```
usb://ledger/BsNsvfXqQTtJnagwFWdBS7FBXgnsK8VZ5CmuznN85swK?key=0/0
```

Все пути производных неявно включают префикс «44»/501, который указывает, что путь следует [спецификациям BIP44](https://github.com/bitcoin/bips/blob/master/bip-0044.mediawiki) и что любые производные ключи являются ключами Solana (тип монеты 501). Одинарная кавычка указывает на «закаленный» вывод. Поскольку Solana использует пары ключей Ed25519, все производные защищены, поэтому добавление кавычек является необязательным и ненужным.
