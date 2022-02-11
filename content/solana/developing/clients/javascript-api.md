+++
title = "Web3 JavaScript API"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

## Что такое Solana-Web3.js?

Библиотека Solana-Web3.js призвана обеспечить полное покрытие Solana. Библиотека была создана на основе [JSON RPC API Solana](https://docs.solana.com/developing/clients/jsonrpc-api).

## Общая терминология

| Term        | Definition                                                                                                                                                                             |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Program     | Исполняемый код без сохранения состояния, написанный для интерпретации инструкций. Программы способны выполнять действия на основе предоставленных инструкций.                         |
| Instruction | Наименьшая единица программы, которую клиент может включить в транзакцию. В своем коде обработки инструкция может содержать один или несколько межпрограммных вызовов.                 |
| Transaction | Одна или несколько инструкций, подписанных клиентом с использованием одной или нескольких пар ключей и выполняемых атомарно только с двумя возможными результатами: успех или неудача. |

Полный список терминов см. в разделе [Терминология Solana](https://docs.solana.com/terminology#cross-program-invocation).

## Начиная

### Установка

#### yarn

```bash
$ yarn add @solana/web3.js
```

#### npm

```bash
$ npm install --save @solana/web3.js
```

#### Бандл

```html
<!-- Development (un-minified) -->
<script src="https://unpkg.com/@solana/web3.js@latest/lib/index.iife.js"></script>

<!-- Production (minified) -->
<script src="https://unpkg.com/@solana/web3.js@latest/lib/index.iife.min.js"></script>
```

### Использование

#### Javascript

```javascript
const solanaWeb3 = require('@solana/web3.js');
console.log(solanaWeb3);
```

#### ES6

```javascript
import * as solanaWeb3 from '@solana/web3.js';
console.log(solanaWeb3);
```

#### Бандл для браузера

```javascript
// solanaWeb3 is provided in the global namespace by the bundle script
console.log(solanaWeb3);
```

## Быстрый старт

### Подключение к кошельку

Чтобы пользователи могли использовать ваше dApp или приложение на Solana, им необходимо получить доступ к своей паре ключей. Keypair — это закрытый ключ с соответствующим открытым ключом, используемый для подписи транзакций.

Есть два способа получить пару ключей:

1. Создайте новую пару ключей
2. Получите пару ключей, используя секретный ключ

Вы можете получить новую пару ключей следующим образом:

```javascript
const {Keypair} = require("@solana/web3.js");

let keypair = Keypair.generate();
```

Это создаст новую пару ключей, которую пользователь сможет финансировать и использовать в вашем приложении.

Вы можете разрешить ввод секретного ключа с помощью текстового поля и получить пару ключей с помощью `Keypair.fromSecretKey(secretKey)`.

```javascript
const {Keypair} = require("@solana/web3.js");

let secretKey = Uint8Array.from([
  202, 171, 192, 129, 150, 189, 204, 241, 142,  71, 205,
  2,  81,  97,   2, 176,  48,  81,  45,   1,  96, 138,
  220, 132, 231, 131, 120,  77,  66,  40,  97, 172,  91,
  245,  84, 221, 157, 190,   9, 145, 176, 130,  25,  43,
  72, 107, 190, 229,  75,  88, 191, 136,   7, 167, 109,
  91, 170, 164, 186,  15, 142,  36,  12,  23
]);

let keypair = Keypair.fromSecretKey(secretKey);
```

Сегодня многие кошельки позволяют пользователям использовать свою пару ключей с помощью различных расширений или веб-кошельков. Общая рекомендация — использовать для подписи транзакций кошельки, а не пары ключей. Кошелек создает слой разделения между dApp и парой ключей, гарантируя, что dApp никогда не получит доступ к секретному ключу. Вы можете найти способы подключения к внешним кошелькам с помощью библиотеки [wallet-adapter](https://github.com/solana-labs/wallet-adapter).

### Создание и отправка транзакций

Чтобы взаимодействовать с программами на Solana, вы создаете, подписываете и отправляете транзакции в сеть. Транзакции представляют собой наборы инструкций с подписями. Порядок, в котором инструкции существуют в транзакции, определяет порядок их выполнения.

Транзакция в Solana-Web3.js создается с использованием объекта [`Transaction`](javascript-api.md#Transaction) и добавления желаемых сообщений, адресов или инструкций.

Возьмем пример транзакции перевода:

```javascript
const {Keypair, Transaction, SystemProgram, LAMPORTS_PER_SOL} = require("@solana/web3.js");

let fromKeypair = Keypair.generate();
let toKeypair = Keypair.generate();
let transaction = new Transaction();

transaction.add(
  SystemProgram.transfer({
    fromPubkey: fromKeypair.publicKey,
    toPubkey: toKeypair.publicKey,
    lamports: LAMPORTS_PER_SOL
  })
);
```

Приведенный выше код позволяет создать транзакцию, готовую к подписанию и передаче в сеть. В транзакцию была добавлена инструкция SystemProgram.transfer, содержащая количество отправляемых ламппортов, а также открытые ключи «куда» и «от».

Остается только подписать транзакцию парой ключей и отправить ее по сети. Вы можете выполнить отправку транзакции, используя `sendAndConfirmTransaction`, если вы хотите предупредить пользователя или сделать что-то после завершения транзакции, или использовать `sendTransaction`, если вам не нужно ждать подтверждения транзакции.

```javascript
const {sendAndConfirmTransaction, clusterApiUrl, Connection} = require("@solana/web3.js");

let keypair = Keypair.generate();
let connection = new Connection(clusterApiUrl('testnet'));

sendAndConfirmTransaction(
  connection,
  transaction,
  [keypair]
);
```

Приведенный выше код принимает TransactionInstruction с помощью SystemProgram, создает Transaction и отправляет ее по сети. Вы используете «Соединение», чтобы определить сеть Solana, к которой вы подключаетесь, а именно «mainnet-beta», «testnet» или «devnet».

### Взаимодействие с пользовательскими программами

В предыдущем разделе рассматривается отправка основных транзакций. В Solana все, что вы делаете, взаимодействует с различными программами, включая транзакцию перевода из предыдущего раздела. На момент написания программы на Solana написаны либо на Rust, либо на C.

Давайте посмотрим на `SystemProgram`. Сигнатура метода для выделения места в вашем аккаунте на Solana в Rust выглядит так:

```rust
pub fn allocate(
    pubkey: &Pubkey,
    space: u64
) -> Instruction
```

В Solana, когда вы хотите взаимодействовать с программой, вы должны сначала знать все учетные записи, с которыми вы будете взаимодействовать.

Вы должны всегда предоставлять каждую учетную запись, с которой программа будет взаимодействовать в инструкции. Кроме того, вы должны указать, является ли учетная запись isSigner или isWritable.

В приведенном выше методе «выделения» требуется один «публичный ключ» учетной записи, а также объем «пространства» для выделения. Мы знаем, что метод allocate записывает в учетную запись, выделяя пространство внутри нее, делая pubkey обязательным для записи isWritable. `isSigner` требуется, когда вы назначаете учетную запись, которая выполняет инструкцию. В этом случае подписывающая сторона — это учетная запись, призывающая выделить пространство внутри себя.

Давайте посмотрим, как вызвать эту инструкцию с помощью solana-web3.js:

```javascript
let keypair = web3.Keypair.generate();
let payer = web3.Keypair.generate();
let connection = new web3.Connection(web3.clusterApiUrl('testnet'));

let airdropSignature = await connection.requestAirdrop(
  payer.publicKey,
  web3.LAMPORTS_PER_SOL,
);

await connection.confirmTransaction(airdropSignature);
```

Во-первых, мы настраиваем пару ключей учетной записи и соединение, чтобы у нас была учетная запись для выделения в тестовой сети. Мы также создаем пару ключей плательщика и раздаем несколько солей, чтобы мы могли оплатить транзакцию распределения.

```javascript
let allocateTransaction = new web3.Transaction({
    feePayer: payer.publicKey
  });
let keys = [{pubkey: keypair.publicKey, isSigner: true, isWritable: true}];
let params = { space: 100 };
```

Мы создаем транзакцию allocateTransaction, объекты ключей и параметров. `feePayer` — это необязательное поле при создании транзакции, в котором указывается, кто платит за транзакцию, по умолчанию это публичный ключ первого лица, подписавшего транзакцию. `keys` представляет все учетные записи, с которыми будет взаимодействовать функция `allocate` программы. Так как функция allocate также требует места, мы создали params, которые будут использоваться позже при вызове функции allocate.

```javascript
let allocateStruct = {
  index: 8,
  layout: struct([
    u32('instruction'),
    ns64('space'),
  ])
};
```

Вышеприведенное создано с использованием u32 и ns64 из `@solana/buffer-layout` для облегчения создания полезной нагрузки. Функция «allocate» принимает параметр «пробел». Чтобы взаимодействовать с функцией, мы должны предоставить данные в формате буфера. Библиотека `buffer-layout` помогает выделить буфер и правильно закодировать его для интерпретации программами Rust на Solana.

Давайте разберем эту структуру.

```javascript
{
  index: 8, /* <-- */
  layout: struct([
    u32('instruction'),
    ns64('space'),
  ])
}
```

`index` установлен на 8, потому что функция `allocate` находится на 8-й позиции в перечислении инструкций для `SystemProgram`.

```rust
/* https://github.com/solana-labs/solana/blob/21bc43ed58c63c827ba4db30426965ef3e807180/sdk/program/src/system_instruction.rs#L142-L305 */
pub enum SystemInstruction {
    /** 0 **/CreateAccount {/**/},
    /** 1 **/Assign {/**/},
    /** 2 **/Transfer {/**/},
    /** 3 **/CreateAccountWithSeed {/**/},
    /** 4 **/AdvanceNonceAccount,
    /** 5 **/WithdrawNonceAccount(u64),
    /** 6 **/InitializeNonceAccount(Pubkey),
    /** 7 **/AuthorizeNonceAccount(Pubkey),
    /** 8 **/Allocate {/**/},
    /** 9 **/AllocateWithSeed {/**/},
    /** 10 **/AssignWithSeed {/**/},
    /** 11 **/TransferWithSeed {/**/},
}
```

Далее идет `u32('instruction')`.

```javascript
{
  index: 8,
  layout: struct([
    u32('instruction'), /* <-- */
    ns64('space'),
  ])
}
```

`layout` в структуре распределения всегда должен иметь `u32('instruction')` первым, когда вы используете его для вызова инструкции.

```javascript
{
  index: 8,
  layout: struct([
    u32('instruction'),
    ns64('space'), /* <-- */
  ])
}
```

`ns64('space')` - это аргумент для функции `allocate`. Вы можете видеть, что в оригинальной функции `allocate` в Rust пространство имеет тип `u64`. `u64` - это 64-битное целое число без знака. Javascript по умолчанию предоставляет только до 53-битных целых чисел. `ns64` происходит от `@solana/buffer-layout`, чтобы помочь с преобразованием типов между Rust и Javascript. Вы можете найти больше преобразований типов между Rust и Javascript по адресу [solana-labs/buffer-layout](https://github.com/solana-labs/buffer-layout).

```javascript
let data = Buffer.alloc(allocateStruct.layout.span);
let layoutFields = Object.assign({instruction: allocateStruct.index}, params);
allocateStruct.layout.encode(layoutFields, data);
```

С помощью созданного ранее bufferLayout мы можем выделить буфер данных. Затем мы назначаем нашим параметрам `{space: 100}`, чтобы они правильно отображались в макете, и кодируем их в буфер данных. Теперь данные готовы для отправки в программу.

```javascript
allocateTransaction.add(new web3.TransactionInstruction({
  keys,
  programId: web3.SystemProgram.programId,
  data,
}));

await web3.sendAndConfirmTransaction(connection, allocateTransaction, [payer, keypair]);
```

Наконец, мы добавляем инструкцию транзакции со всеми ключами учетной записи, плательщиком, данными и идентификатором программы и транслируем транзакцию в сеть.

Полный код можно найти ниже.

```javascript
const {struct, u32, ns64} = require("@solana/buffer-layout");
const {Buffer} = require('buffer');
const web3 = require("@solana/web3.js");

let keypair = web3.Keypair.generate();
let payer = web3.Keypair.generate();

let connection = new web3.Connection(web3.clusterApiUrl('testnet'));

let airdropSignature = await connection.requestAirdrop(
  payer.publicKey,
  web3.LAMPORTS_PER_SOL,
);

await connection.confirmTransaction(airdropSignature);

let allocateTransaction = new web3.Transaction({
  feePayer: payer.publicKey
});
let keys = [{pubkey: keypair.publicKey, isSigner: true, isWritable: true}];
let params = { space: 100 };

let allocateStruct = {
  index: 8,
  layout: struct([
    u32('instruction'),
    ns64('space'),
  ])
};

let data = Buffer.alloc(allocateStruct.layout.span);
let layoutFields = Object.assign({instruction: allocateStruct.index}, params);
allocateStruct.layout.encode(layoutFields, data);

allocateTransaction.add(new web3.TransactionInstruction({
  keys,
  programId: web3.SystemProgram.programId,
  data,
}));

await web3.sendAndConfirmTransaction(connection, allocateTransaction, [payer, keypair]);
```
