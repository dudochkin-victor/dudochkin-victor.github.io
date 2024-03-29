+++
title = "Ledger Nano"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

На этой странице описывается, как использовать Ledger Nano S или Nano X для взаимодействия с Solana с помощью инструментов командной строки. Чтобы увидеть другие решения для взаимодействия с Solana с вашим Nano, [нажмите здесь](../ledger-live.md#interact-with-the-solana-network).

## Прежде чем вы начнете

- [Настройте Nano с помощью приложения Solana](../ledger-live/)
- [Установить инструменты командной строки Solana](../../cli/install-solana-cli-tools/)

## Используйте Ledger Nano с Solana CLI

1. Убедитесь, что приложение Ledger Live закрыто.
2. Подключите Nano к USB-порту вашего компьютера.
3. Введите свой пин-код и запустите приложение Solana на Nano.
4. Убедитесь, что на экране отображается сообщение «Приложение готово».

### Просмотр идентификатора кошелька

На вашем компьютере запустите:

```bash
solana-keygen pubkey usb://ledger
```

Это подтверждает, что ваше устройство Ledger правильно подключено и находится в правильном состоянии для взаимодействия с интерфейсом командной строки Solana. Команда возвращает уникальный _идентификатор кошелька_ вашего Ledger. Если у вас есть несколько устройств Nano, подключенных к одному компьютеру, вы можете использовать свой идентификатор кошелька, чтобы указать, какой аппаратный кошелек Ledger вы хотите использовать. Если вы планируете использовать только один Nano на своем компьютере одновременно, вам не нужно указывать идентификатор кошелька. Информацию об использовании идентификатора кошелька для использования определенного Ledger см. в разделе Управление несколькими аппаратными кошельками.

### Просмотр адресов вашего кошелька

Ваш Nano поддерживает произвольное количество действительных адресов кошельков и подписантов.
Чтобы просмотреть любой адрес, используйте команду `solana-keygen pubkey`, как показано ниже, за которой следует действительный [URL-адрес пары ключей](../hardware-wallets.md#specify-a-keypair-url).

Несколько адресов кошелька могут быть полезны, если вы хотите передавать токены между своими учетными записями для разных целей или использовать разные пары ключей на устройстве в качестве полномочий для подписи, например, для учетной записи доли.

Все следующие команды будут отображать разные адреса, связанные с заданным путем пары ключей. Попробуйте их!

```bash
solana-keygen pubkey usb://ledger
solana-keygen pubkey usb://ledger?key=0
solana-keygen pubkey usb://ledger?key=1
solana-keygen pubkey usb://ledger?key=2
```

- ПРИМЕЧАНИЕ: параметры URL пары ключей игнорируются в **zsh**
  &nbsp;см. раздел «Устранение неполадок» для получения дополнительной информации

Вы также можете использовать другие значения для числа после `key=`.
Любой из адресов, отображаемых этими командами, является действительным адресом кошелька Solana. Частная часть, связанная с каждым адресом, надежно хранится в Nano и используется для подписи транзакций с этого адреса.
Просто запишите, какой URL пары ключей вы использовали для получения любого адреса, который вы будете использовать для получения токенов.

Если вы планируете использовать только один адрес/пару ключей на своем устройстве, хорошим легко запоминающимся путем может быть использование адреса `key=0`. Посмотреть этот адрес с помощью:

```bash
solana-keygen pubkey usb://ledger?key=0
```

Теперь у вас есть адрес кошелька (или несколько адресов), вы можете опубликовать любой из этих адресов, чтобы он действовал как адрес получателя, и вы можете использовать связанный URL-адрес пары ключей в качестве подписывающей стороны для транзакций с этого адреса.

### Просмотр вашего баланса

Для просмотра баланса любого аккаунта, независимо от того, какой кошелек он использует, используйте команду `solana balance`:

```bash
solana balance SOME_WALLET_ADDRESS
```

Например, если ваш адрес `7cvkjYAkUYs4W8XcXsca7cBrEGFeSUjeZmKoNBvEwyri`, введите следующую команду для просмотра баланса:

```bash
solana balance 7cvkjYAkUYs4W8XcXsca7cBrEGFeSUjeZmKoNBvEwyri
```

Вы также можете просмотреть баланс любого адреса учетной записи на вкладке «Учетные записи» в [Проводнике](https://explorer.solana.com/accounts) и вставить адрес в поле для просмотра баланса в своем веб-браузере.

Примечание. Любой адрес с балансом 0 SOL, например, только что созданный в вашем Ledger, будет отображаться в проводнике как «Не найден». Пустые учетные записи и несуществующие учетные записи обрабатываются в Solana одинаково. Это изменится, когда в адресе вашей учетной записи будет указано SOL.

### Отправить SOL с Nano

Чтобы отправить несколько токенов с адреса, контролируемого вашим Nano, вам нужно будет использовать устройство для подписи транзакции, используя тот же URL-адрес пары ключей, который вы использовали для получения адреса. Для этого убедитесь, что ваш Nano подключен, разблокирован с помощью PIN-кода, Ledger Live не запущен, а приложение Solana открыто на устройстве с надписью «Приложение готово».

Команда solana transfer используется, чтобы указать, на какой адрес отправлять токены, сколько токенов отправлять, и использует аргумент `--keypair`, чтобы указать, какая пара ключей отправляет токены, которая будет подписывать транзакцию, и баланс. с ассоциированного адреса будет уменьшаться.

```bash
solana transfer RECIPIENT_ADDRESS AMOUNT --keypair KEYPAIR_URL_OF_SENDER
```

Ниже приведен полный пример. Сначала просматривается адрес по определенной паре ключей URL.
Во-вторых, проверяется баланс этого адреса. Наконец, вводится транзакция перевода для отправки «1» SOL на адрес получателя «7cvkjYAkUYs4W8XcXsca7cBrEGFeSUjeZmKoNBvEwyri».
Когда вы нажмете Enter для команды перевода, вам будет предложено подтвердить детали транзакции на вашем устройстве Ledger. На устройстве используйте правую и левую кнопки для просмотра сведений о транзакции. Если они выглядят правильно, нажмите обе кнопки на экране «Утвердить», в противном случае нажмите обе кнопки на экране «Отклонить».

```bash
~$ solana-keygen pubkey usb://ledger?key=42
CjeqzArkZt6xwdnZ9NZSf8D1CNJN1rjeFiyd8q7iLWAV

~$ solana balance CjeqzArkZt6xwdnZ9NZSf8D1CNJN1rjeFiyd8q7iLWAV
1.000005 SOL

~$ solana transfer 7cvkjYAkUYs4W8XcXsca7cBrEGFeSUjeZmKoNBvEwyri 1 --keypair usb://ledger?key=42
Waiting for your approval on Ledger hardware wallet usb://ledger/2JT2Xvy6T8hSmT8g6WdeDbHUgoeGdj6bE2VueCZUJmyN
✅ Approved

Signature: kemu9jDEuPirKNRKiHan7ycybYsZp7pFefAdvWZRq5VRHCLgXTXaFVw3pfh87MQcWX4kQY4TjSBmESrwMApom1V
```

После одобрения транзакции на вашем устройстве программа отобразит подпись транзакции и дождется максимального количества подтверждений (32) перед возвратом. Это занимает всего несколько секунд, а затем транзакция завершается в сети Solana. Вы можете просмотреть подробности этой или любой другой транзакции, перейдя на вкладку «Транзакция» в [Проводнике](https://explorer.solana.com/transactions) и вставив подпись транзакции.

## Расширенные операции

### Управление несколькими аппаратными кошельками

Иногда бывает полезно подписать транзакцию ключами от нескольких аппаратных кошельков. Для подписи с использованием нескольких кошельков требуются _полные URL-адреса пары ключей_.
Если URL-адрес не является полным, интерфейс командной строки Solana предложит вам указать полные URL-адреса всех подключенных аппаратных кошельков и попросит вас выбрать, какой кошелек использовать для каждой подписи.

Вместо использования интерактивных подсказок вы можете генерировать полные URL-адреса с помощью команды Solana CLI `resolve-signer`. Например, попробуйте подключить Nano к USB, разблокировать его с помощью пин-кода и выполнить следующую команду:

```
solana resolve-signer usb://ledger?key=0/0
```

Вы увидите вывод, похожий на:

```
usb://ledger/BsNsvfXqQTtJnagwFWdBS7FBXgnsK8VZ5CmuznN85swK?key=0/0
```

но где `BsNsvfXqQTtJnagwFWdBS7FBXgnsK8VZ5CmuznN85swK` — это ваш `WALLET_ID`.

С вашим полным URL-адресом вы можете подключить несколько аппаратных кошельков к одному компьютеру и однозначно идентифицировать пару ключей от любого из них.
Используйте вывод команды `resolve-signer` везде, где команда `solana` ожидает, что запись `<KEYPAIR>` будет использовать разрешенный путь в качестве подписавшего для этой части данной транзакции.

## Исправление проблем

### Параметры URL пары ключей игнорируются в zsh

Знак вопроса — это специальный символ в zsh. Если вы не используете эту функцию, добавьте следующую строку в ваш файл `~/.zshrc`, чтобы рассматривать его как обычный символ:

```bash
unsetopt nomatch
```

Затем либо перезапустите окно оболочки, либо запустите `~/.zshrc`:

```bash
source ~/.zshrc
```

Если вы предпочитаете не отключать специальную обработку zsh символа вопросительного знака, вы можете явно отключить ее с помощью обратной косой черты в URL-адресах вашей пары ключей.
Например:

```bash
solana-keygen pubkey usb://ledger\?key=0
```

## Служба поддержки

Посетите нашу [Страницу поддержки Кошелька](../support/), чтобы узнать, как получить помощь.

Подробнее об [отправке и получении токенов](../../cli/transfer-tokens/) и [делегировании доли](../../cli/delegate-stake/). Вы можете использовать URL-адрес пары ключей Ledger везде, где вы видите параметр или аргумент, который принимает `<KEYPAIR>`.
