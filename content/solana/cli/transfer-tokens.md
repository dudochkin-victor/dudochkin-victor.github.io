+++
title = "Отправка и получение токенов"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

На этой странице описывается, как получать и отправлять токены SOL с помощью инструментов командной строки с кошельком командной строки, например [бумажным кошельком](../wallet-guide/paper-wallet/), [кошельком файловой системы](../wallet-guide/file-system-wallet/) или [аппаратный кошелек](../wallet-guide/hardware-wallets/). Прежде чем начать, убедитесь, что вы создали кошелек и имеете доступ к его адресу (pubkey) и паре ключей для подписи. Ознакомьтесь с нашими [условиями ввода пар ключей для разных типов кошельков](../cli/conventions.md#keypair-conventions).

## Проверка вашего кошелька

Прежде чем делиться своим открытым ключом с другими, вы можете сначала убедиться, что ключ действителен и что вы действительно владеете соответствующим закрытым ключом.

В этом примере мы создадим второй кошелек в дополнение к вашему первому кошельку, а затем переведем на него несколько токенов. Это подтвердит, что вы можете отправлять и получать токены с помощью выбранного типа кошелька.

В этом тестовом примере используется наша тестовая сеть для разработчиков, которая называется devnet. Токены, выпущенные в devnet, не имеют ценности, поэтому не беспокойтесь, если вы их потеряете.

#### Раздайте несколько токенов, чтобы начать

Во-первых, _airdrop_ себе несколько игровых жетонов в devnet.

```bash
solana airdrop 1 <RECIPIENT_ACCOUNT_ADDRESS> --url https://api.devnet.solana.com
```

где вы заменяете текст `<RECIPIENT_ACCOUNT_ADDRESS>` своим открытым ключом/адресом кошелька в кодировке base58.

#### Проверьте свой баланс

Подтвердите, что аирдроп прошел успешно, проверив баланс аккаунта. Он должен вывести `1 SOL`:

```bash
solana balance <ACCOUNT_ADDRESS> --url https://api.devnet.solana.com
```

#### Создайте второй адрес кошелька

Нам понадобится новый адрес для получения наших токенов. Создайте вторую пару ключей и запишите ее открытый ключ:

```bash
solana-keygen new --no-passphrase --no-outfile
```

Вывод будет содержать адрес после текста `pubkey:`. Скопируйте адрес. Мы будем использовать его на следующем шаге.

```
pubkey: GKvqsuNcnwWqPzzuhLmGi4rzzh55FhJtGizkhHaEJqiV
```

Вы также можете создать второй (или несколько) кошелек любого типа: [бумажный](../wallet-guide/paper-wallet#creating-multiple-paper-wallet-address), [файловая система](../wallet -guide/file-system-wallet.md#creating-multiple-file-system-wallet-addresss), или [аппаратное обеспечение](../wallet-guide/hardware-wallets.md#multiple-addresses-on-a- единый аппаратный кошелек).

#### Перевести токены с первого кошелька на второй адрес

Затем докажите, что вы владеете токенами, разосланными по воздуху, передав их. Кластер Solana примет передачу только в том случае, если вы подпишите транзакцию парой закрытых ключей, соответствующей открытому ключу отправителя в транзакции.

```bash
solana transfer --from <KEYPAIR> <RECIPIENT_ACCOUNT_ADDRESS> 0.5 --allow-unfunded-recipient --url https://api.devnet.solana.com --fee-payer <KEYPAIR>
```

где вы заменяете `<KEYPAIR>` на путь к паре ключей в вашем первом кошельке и заменяете `<RECIPIENT_ACCOUNT_ADDRESS>` на адрес вашего второго кошелька.

Подтвердите обновленные балансы с помощью `solana balance`:

```bash
solana balance <ACCOUNT_ADDRESS> --url http://api.devnet.solana.com
```

где `<ACCOUNT_ADDRESS>` — это либо открытый ключ из вашей пары ключей, либо открытый ключ получателя.

#### Полный пример тестового переноса

```bash
$ solana-keygen new --outfile my_solana_wallet.json   # Creating my first wallet, a file system wallet
Generating a new keypair
For added security, enter a passphrase (empty for no passphrase):
Wrote new keypair to my_solana_wallet.json
==========================================================================
pubkey: DYw8jCTfwHNRJhhmFcbXvVDTqWMEVFBX6ZKUmG5CNSKK                          # Here is the address of the first wallet
==========================================================================
Save this seed phrase to recover your new keypair:
width enhance concert vacant ketchup eternal spy craft spy guard tag punch    # If this was a real wallet, never share these words on the internet like this!
==========================================================================

$ solana airdrop 1 DYw8jCTfwHNRJhhmFcbXvVDTqWMEVFBX6ZKUmG5CNSKK --url https://api.devnet.solana.com  # Airdropping 1 SOL to my wallet's address/pubkey
Requesting airdrop of 1 SOL from 35.233.193.70:9900
1 SOL

$ solana balance DYw8jCTfwHNRJhhmFcbXvVDTqWMEVFBX6ZKUmG5CNSKK --url https://api.devnet.solana.com # Check the address's balance
1 SOL

$ solana-keygen new --no-outfile  # Creating a second wallet, a paper wallet
Generating a new keypair
For added security, enter a passphrase (empty for no passphrase):
====================================================================
pubkey: 7S3P4HxJpyyigGzodYwHtCxZyUQe9JiBMHyRWXArAaKv                   # Here is the address of the second, paper, wallet.
====================================================================
Save this seed phrase to recover your new keypair:
clump panic cousin hurt coast charge engage fall eager urge win love   # If this was a real wallet, never share these words on the internet like this!
====================================================================

$ solana transfer --from my_solana_wallet.json 7S3P4HxJpyyigGzodYwHtCxZyUQe9JiBMHyRWXArAaKv 0.5 --allow-unfunded-recipient --url https://api.devnet.solana.com --fee-payer my_solana_wallet.json  # Transferring tokens to the public address of the paper wallet
3gmXvykAd1nCQQ7MjosaHLf69Xyaqyq1qw2eu1mgPyYXd5G4v1rihhg1CiRw35b9fHzcftGKKEu4mbUeXY2pEX2z  # This is the transaction signature

$ solana balance DYw8jCTfwHNRJhhmFcbXvVDTqWMEVFBX6ZKUmG5CNSKK --url https://api.devnet.solana.com
0.499995 SOL  # The sending account has slightly less than 0.5 SOL remaining due to the 0.000005 SOL transaction fee payment

$ solana balance 7S3P4HxJpyyigGzodYwHtCxZyUQe9JiBMHyRWXArAaKv --url https://api.devnet.solana.com
0.5 SOL  # The second wallet has now received the 0.5 SOL transfer from the first wallet
```

## Получить токены

Чтобы получить токены, вам понадобится адрес, на который другие могут отправлять токены. В Solana адрес кошелька является открытым ключом пары ключей. Существует множество методов генерации пар ключей. Выбранный вами метод будет зависеть от того, как вы решите хранить пары ключей. Пары ключей хранятся в кошельках. Перед получением токенов вам необходимо [создать кошелек](../wallet-guide/cli/). После завершения у вас должен быть открытый ключ для каждой сгенерированной вами пары ключей. Открытый ключ представляет собой длинную строку символов base58. Его длина варьируется от 32 до 44 символов.

## Отправить токены

Если у вас уже есть SOL и вы хотите отправить кому-то токены, вам потребуется путь к вашей паре ключей, их открытый ключ в кодировке base58 и количество токенов для передачи. После того, как вы соберете это, вы можете передать токены с помощью команды `solana transfer`:

```bash
solana transfer --from <KEYPAIR> <RECIPIENT_ACCOUNT_ADDRESS> <AMOUNT> --fee-payer <KEYPAIR>
```

Подтвердите обновленные балансы с помощью `solana balance`:

```bash
solana balance <ACCOUNT_ADDRESS>
```
