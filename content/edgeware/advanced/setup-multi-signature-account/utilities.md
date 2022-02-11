+++
title = "Утилиты"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

## Генерация адресов учетных записей с мультиподписью

> ПРИМЕЧАНИЕ. Адреса, предоставляемые кошелькам с мультиподписью, должны быть отсортированы. Приведенные ниже методы создания сортируют учетные записи для вас, но если вы реализуете свою собственную сортировку, имейте в виду, что открытые ключи сравниваются побайтно и сортируются по возрастанию перед вставкой в хешируемую полезную нагрузку.

Адреса детерминировано генерируются из подписывающих лиц и порога мультиподписного кошелька. [w3f/msig-util](https://www.npmjs.com/package/@w3f/msig-util) — это небольшой инструмент командной строки, который может определить адрес мультиподписи на основе ваших входных данных.

Вы можете видеть здесь, мы взяли адреса учетных записей разработчиков ALICE, BOB и CHARLIE, и у нас есть тот же адрес, что и в наших приложениях.

```
❯ npx @w3f/msig-util derive --addresses 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY,5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty,5FLSigC9HGRKVhB9FiEo4Y3koPsNmBmLJbpXg2mp1hXcS59Y --threshold 2 --ss58Prefix 42
--------------------------------
Addresses: 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY 5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty 5FLSigC9HGRKVhB9FiEo4Y3koPsNmBmLJbpXg2mp1hXcS59Y
Threshold: 2
Multisig Address (SS58: 42): 5DjYJStmdZ2rcqXbXGX7TW85JsrW6uG4y9MUcLq2BoPMpRA7
--------------------------------
```

* [w3f/msig-util](https://www.npmjs.com/package/@w3f/msig-util) - [internal code](https://github.com/lsaether/msig-util/blob/master/src/actions/deriveAddress.ts)
