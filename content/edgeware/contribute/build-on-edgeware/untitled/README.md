+++
title = "Интерфейс командной строки Edgeware (Устаревший)"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

>Интерфейс командной строки Edgeware работает, однако он устарел. Пожалуйста, используйте интерфейсы polkadot.js. CLI [можно найти на Github.](https://github.com/hicommonwealth/edgeware-cli)

Интерфейс командной строки Edgeware позволяет легко взаимодействовать с локальным или удаленным узлом Edgeware или любым общим узлом субстрата. API требует, чтобы вы создали файл `.env` с информацией о вашей паре ключей, которая будет описана ниже.

## Конфигурация

Создайте файл `.env` со следующей информацией:

```
MNEMONIC_PHRASE=...
DERIVATION_PATH=...
```

Примеры некоторых значений:

```
MNEMONIC_PHRASE="bottom drive obey lake curtain smoke basket hold race lonely fit walk"
DERIVATION_PATH=//Alice
```

## Сборка

1. Требуется: typescript версии 3.2 или выше, Node версии 12.3 или выше.
2. Клонируйте репозиторий git.
3. Соберите, запустив `tsc` в клонированном репозитории.
4. Запустите с `./bin/edge`.

## Использование

1. Установите пакет с помощью `yarn` или `npm`
2. Если вы столкнулись с ошибками, запустите `tsc` в репозитории, чтобы скомпилировать машинописный текст.
3. Если вы хотите отправлять транзакции, подготовьте ключ в шестнадцатеричном формате.
4. Вызовите `edge <module> <func> [...args]` с желаемой функцией модуля и аргументами.

## Опции

```
Options:
  -V, --version           output the version number
  -A, --argfile <file>    A JSON-formatted file containing an array of args
  -s, --seed <key>        Public/private keypair seed
  -r, --remoteNode <url>  Remote node url (default: "localhost:9944").
  -T, --types             Print types instead of performing action.
  -t, --tail              Tail output rather than exiting immediately.
  -h, --help              output usage information
```

## Примеры

- Перевод токенов со своего баланса

```
edge balances transfer 5FmE1Adpwp1bT1oY95w59RiSPVu9QwzBGjKsE2hxemD2AFs8 1000
```

* Получение баланса учетной записи из тестовой сети Edgeware

```
edge -r ws://testnet1.edgewa.re:9944 balances freeBalance 5FTyhanHHymJ2FQ2ZWMwhoKcUXuNcBfNaqufrFvwDR8Mf1mG
```

* Регистрация личности

```
edge identity register github drewstone
```
