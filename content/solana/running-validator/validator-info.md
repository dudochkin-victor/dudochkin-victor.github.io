+++
title = "Публикация информации о валидаторе"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Вы можете опубликовать информацию о своем валидаторе в цепочке, чтобы она была общедоступна для других пользователей.

## Запустить solana validator-info

Запустите CLI solana, чтобы заполнить информационную учетную запись валидатора:

```bash
solana validator-info publish --keypair ~/validator-keypair.json <VALIDATOR_INFO_ARGS> <VALIDATOR_NAME>
```

Подробнее о необязательных полях для VALIDATOR_INFO_ARGS:

```bash
solana validator-info publish --help
```

## Примеры команд

Пример команды публикации:

```bash
solana validator-info publish "Elvis Validator" -n elvis -w "https://elvis-validates.com"
```

Пример команды запроса:

```bash
solana validator-info get
```

который выводит

```
Validator info from 8WdJvDz6obhADdxpGCiJKZsDYwTLNEDFizayqziDc9ah
  Validator pubkey: 6dMH3u76qZ7XG4bVboVRnBHR2FfrxEqTTTyj4xmyDMWo
  Info: {"keybaseUsername":"elvis","name":"Elvis Validator","website":"https://elvis-validates.com"}
```

## База ключей

Добавление имени пользователя Keybase позволяет клиентским приложениям \(таким как Solana Network Explorer\) автоматически извлекать ваш общедоступный профиль валидатора, включая криптографические доказательства, фирменный стиль и т. д. Чтобы связать ваш публичный ключ валидатора с Keybase:

1. Присоединяйтесь к [https://keybase.io/](https://keybase.io/) и заполните профиль для вашего валидатора.

2. Добавьте свой валидатор **identity pubkey** в Keybase:
   
   - Создайте на локальном компьютере пустой файл с именем `validator-<PUBKEY>`
   - В Keybase перейдите в раздел «Файлы» и загрузите файл открытого ключа в подкаталог solana в вашей общей папке: `/keybase/public/<KEYBASE_USERNAME>/solana`
   - Чтобы проверить свой публичный ключ, убедитесь, что вы можете успешно перейти к `https://keybase.pub/<KEYBASE_USERNAME>/solana/validator-<PUBKEY>`

3. Добавьте или обновите `solana validator-info`, указав свое имя пользователя Keybase. CLI проверит файл `validator-<PUBKEY>`
