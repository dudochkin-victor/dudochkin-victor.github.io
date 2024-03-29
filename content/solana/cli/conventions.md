+++
title = "Использование Solana CLI"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Прежде чем запускать какие-либо команды Solana CLI, давайте рассмотрим некоторые соглашения, которые вы увидите во всех командах. Во-первых, Solana CLI на самом деле представляет собой набор различных команд для каждого действия, которое вы можете предпринять. Вы можете просмотреть список всех возможных команд, выполнив:

```bash
solana --help
```

Чтобы узнать, как использовать конкретную команду, запустите:

```bash
solana <COMMAND> --help
```

где вы заменяете текст `<COMMAND>` именем команды, о которой хотите узнать больше.

Сообщение об использовании команды обычно содержит такие слова, как `<AMOUNT>`, `<ACCOUNT_ADDRESS>` или `<KEYPAIR>`. Каждое слово является заполнителем для _типа_ текста, с которым вы можете выполнить команду. Например, вы можете заменить «<СУММА>» числом, таким как «42» или «100,42». Вы можете заменить `<ACCOUNT_ADDRESS>` кодировкой base58 вашего открытого ключа, например, `9grmKMwTiZwUHSExjtbFzHLPTdWoXgcg1bZkhvwTrTww`.

## Соглашения о ключевой паре

Многие команды, использующие инструменты CLI, требуют значения для `<KEYPAIR>`. Значение, которое вы должны использовать для пары ключей, зависит от того, какой тип [кошелька командной строки вы создали](../wallet-guide/cli/).

Например, справка CLI показывает, что способ отобразить адрес любого кошелька (также известный как pubkey пары ключей):

```bash
solana-keygen pubkey <KEYPAIR>
```

Ниже мы покажем, как решить, что вы должны поместить в `<KEYPAIR>` в зависимости от типа вашего кошелька.

#### Бумажный кошелек

В бумажном кошельке пара ключей надежно создается из исходных слов и необязательной парольной фразы, которую вы ввели при создании кошелька. Чтобы использовать пару ключей бумажного кошелька везде, где в примерах или справочных документах показан текст `<KEYPAIR>`, введите схему uri `prompt://`, и программа предложит вам ввести исходные слова при выполнении команды.

Чтобы отобразить адрес кошелька Paper Wallet:

```bash
solana-keygen pubkey prompt://
```

#### Кошелек файловой системы

В кошельке с файловой системой пара ключей хранится в файле на вашем компьютере. Замените `<KEYPAIR>` полным путем к файлу пары ключей.

Например, если расположение файла пары ключей файловой системы — `/home/solana/my_wallet.json`, чтобы отобразить адрес, выполните:

```bash
solana-keygen pubkey /home/solana/my_wallet.json
```

#### Аппаратный кошелек

Если вы выбрали аппаратный кошелек, используйте свой [URL-адрес пары ключей](../wallet-guide/hardware-wallets.md#specify-a-hardware-wallet-key), например `usb://ledger?key=0`.

```bash
solana-keygen pubkey usb://ledger?key=0
```
