+++
title = "Кошельки командной строки"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Solana поддерживает несколько различных типов кошельков, которые можно использовать для прямого взаимодействия с инструментами командной строки Solana.

**Если вы не знакомы с использованием программ командной строки и просто хотите иметь возможность отправлять и получать токены SOL, мы рекомендуем настроить сторонний [App Wallet](apps/)**.

Чтобы использовать кошелек командной строки, вы должны сначала [установить инструменты Solana CLI](../cli/install-solana-cli-tools/)

## Кошелек файловой системы

_Кошелек файловой системы_, также известный как кошелек FS, представляет собой каталог в файловой системе вашего компьютера. Каждый файл в каталоге содержит пару ключей.

### Безопасность кошелька файловой системы

Кошелек с файловой системой является наиболее удобной и наименее безопасной формой кошелька. Это удобно тем, что ключевая пара хранится в простом файле. Вы можете сгенерировать столько ключей, сколько захотите, и тривиально создать их резервную копию, скопировав файлы. Это небезопасно, потому что файлы пары ключей **не зашифрованы**. Если вы единственный пользователь своего компьютера и уверены, что на нем нет вредоносных программ, кошелек FS — отличное решение для небольших сумм криптовалюты. Однако если ваш компьютер содержит вредоносное ПО и подключен к Интернету, это вредоносное ПО может загрузить ваши ключи и использовать их для получения ваших токенов. Аналогичным образом, поскольку пары ключей хранятся на вашем компьютере в виде файлов, опытный хакер, имеющий физический доступ к вашему компьютеру, может получить к нему доступ. Использование зашифрованного жесткого диска, такого как FileVault в MacOS, минимизирует этот риск.

[Кошелек файловой системы](file-system-wallet/)

## Бумажный кошелек

_Бумажный кошелек_ представляет собой набор _начальных фраз_, написанных на бумаге. Исходная фраза — это некоторое количество слов (обычно 12 или 24), которые можно использовать для регенерации пары ключей по запросу.

### Безопасность бумажного кошелька

С точки зрения удобства и безопасности бумажный кошелек находится на противоположной стороне спектра от кошелька FS. Это ужасно неудобно в использовании, но предлагает отличную безопасность. Эта высокая безопасность еще более усиливается, когда бумажные кошельки используются в сочетании с [автономной подписью](../offline-signing/). Депозитарные службы, такие как [Coinbase Custody](https://custody.coinbase.com/), используют эту комбинацию.
Бумажные кошельки и депозитарные услуги — отличный способ обезопасить большое количество токенов на длительный период времени.

[Бумажные кошельки](paper-wallet/)

## Аппаратный кошелек

Аппаратный кошелек — это небольшое портативное устройство, которое хранит пары ключей и предоставляет некоторый интерфейс для подписания транзакций.

### Безопасность аппаратного кошелька

Аппаратный кошелек, например
[Аппаратный кошелек Ledger](https://www.ledger.com/) предлагает отличное сочетание безопасности и удобства для криптовалют. Он эффективно автоматизирует процесс автономной подписи, сохраняя при этом почти все удобства кошелька файловой системы.

[Аппаратные кошельки](hardware-wallets/)
