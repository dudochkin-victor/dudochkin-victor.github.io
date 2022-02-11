+++
title = "Справочник по Web3 API"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

## Справочное руководство по API Web3

## Общий

### Связь

[Исходная документация](https://solana-labs.github.io/solana-web3.js/classes/Connection.html)

Соединение используется для взаимодействия с [Solana JSON RPC](https://docs.solana.com/developing/clients/jsonrpc-api). Вы можете использовать Connection для подтверждения транзакций, получения информации об учетной записи и многого другого.

Вы создаете соединение, определяя конечную точку кластера JSON RPC и требуемое обязательство. Как только это будет завершено, вы можете использовать этот объект подключения для взаимодействия с любым из Solana JSON RPC API.

#### Пример использования

```javascript
const web3 = require("@solana/web3.js");

let connection = new web3.Connection(web3.clusterApiUrl('devnet'), 'confirmed');

let slot = await connection.getSlot();
console.log(slot);
// 93186439

let blockTime = await connection.getBlockTime(slot);
console.log(blockTime);
// 1630747045

let block = await connection.getBlock(slot);
console.log(block);

/*
{
    blockHeight: null,
    blockTime: 1630747045,
    blockhash: 'AsFv1aV5DGip9YJHHqVjrGg6EKk55xuyxn2HeiN9xQyn',
    parentSlot: 93186438,
    previousBlockhash: '11111111111111111111111111111111',
    rewards: [],
    transactions: []
}
*/

let slotLeader = await connection.getSlotLeader();
console.log(slotLeader);
//49AqLYbpJYc2DrzGUAH1fhWJy62yxBxpLEkfJwjKy2jr
```

В приведенном выше примере показаны лишь некоторые из методов Connection. Полный список см. в [документах, сгенерированных исходным кодом](https://solana-labs.github.io/solana-web3.js/classes/Connection.html).

### Транзакция

[Исходная документация](https://solana-labs.github.io/solana-web3.js/classes/Transaction.html)

Транзакция используется для взаимодействия с программами на блокчейне Solana. Эти транзакции создаются с помощью инструкций TransactionInstructions, содержащих все учетные записи, с которыми можно взаимодействовать, а также любые необходимые данные или адреса программ. Каждая TransactionInstruction состоит из ключей, данных и идентификатора программы. Вы можете выполнять несколько инструкций в одной транзакции, взаимодействуя с несколькими программами одновременно.

#### Пример использования

```javascript
const web3 = require('@solana/web3.js');
const nacl = require('tweetnacl');

// Airdrop SOL for paying transactions
let payer = web3.Keypair.generate();
let connection = new web3.Connection(web3.clusterApiUrl('devnet'), 'confirmed');

let airdropSignature = await connection.requestAirdrop(
    payer.publicKey,
    web3.LAMPORTS_PER_SOL,
);

await connection.confirmTransaction(airdropSignature);

let toAccount = web3.Keypair.generate();

// Create Simple Transaction
let transaction = new web3.Transaction();

// Add an instruction to execute
transaction.add(web3.SystemProgram.transfer({
    fromPubkey: payer.publicKey,
    toPubkey: toAccount.publicKey,
    lamports: 1000,
}));

// Send and confirm transaction
// Note: feePayer is by default the first signer, or payer, if the parameter is not set
await web3.sendAndConfirmTransaction(connection, transaction, [payer])

// Alternatively, manually construct the transaction
let recentBlockhash = await connection.getRecentBlockhash();
let manualTransaction = new web3.Transaction({
    recentBlockhash: recentBlockhash.blockhash,
    feePayer: payer.publicKey
});
manualTransaction.add(web3.SystemProgram.transfer({
    fromPubkey: payer.publicKey,
    toPubkey: toAccount.publicKey,
    lamports: 1000,
}));

let transactionBuffer = manualTransaction.serializeMessage();
let signature = nacl.sign.detached(transactionBuffer, payer.secretKey);

manualTransaction.addSignature(payer.publicKey, signature);

let isVerifiedSignature = manualTransaction.verifySignatures();
console.log(`The signatures were verifed: ${isVerifiedSignature}`)

// The signatures were verified: true

let rawTransaction = manualTransaction.serialize();

await web3.sendAndConfirmRawTransaction(connection, rawTransaction);
```

### Ключевая пара

[Исходная документация](https://solana-labs.github.io/solana-web3.js/classes/Keypair.html)

Пара ключей используется для создания учетной записи с открытым ключом и секретным ключом в Solana. Вы можете сгенерировать, сгенерировать из семени или создать из секретного ключа.

#### Пример использования

```javascript
const {Keypair} = require("@solana/web3.js")

let account = Keypair.generate();

console.log(account.publicKey.toBase58());
console.log(account.secretKey);

// 2DVaHtcdTf7cm18Zm9VV8rKK4oSnjmTkKE6MiXe18Qsb
// Uint8Array(64) [
//   152,  43, 116, 211, 207,  41, 220,  33, 193, 168, 118,
//    24, 176,  83, 206, 132,  47, 194,   2, 203, 186, 131,
//   197, 228, 156, 170, 154,  41,  56,  76, 159, 124,  18,
//    14, 247,  32, 210,  51, 102,  41,  43,  21,  12, 170,
//   166, 210, 195, 188,  60, 220, 210,  96, 136, 158,   6,
//   205, 189, 165, 112,  32, 200, 116, 164, 234
// ]


let seed = Uint8Array.from([70,60,102,100,70,60,102,100,70,60,102,100,70,60,102,100,70,60,102,100,70,60,102,100,70,60,102,100,70,60,102,100]);
let accountFromSeed = Keypair.fromSeed(seed);

console.log(accountFromSeed.publicKey.toBase58());
console.log(accountFromSeed.secretKey);

// 3LDverZtSC9Duw2wyGC1C38atMG49toPNW9jtGJiw9Ar
// Uint8Array(64) [
//    70,  60, 102, 100,  70,  60, 102, 100,  70,  60, 102,
//   100,  70,  60, 102, 100,  70,  60, 102, 100,  70,  60,
//   102, 100,  70,  60, 102, 100,  70,  60, 102, 100,  34,
//   164,   6,  12,   9, 193, 196,  30, 148, 122, 175,  11,
//    28, 243, 209,  82, 240, 184,  30,  31,  56, 223, 236,
//   227,  60,  72, 215,  47, 208, 209, 162,  59
// ]


let accountFromSecret = Keypair.fromSecretKey(account.secretKey);

console.log(accountFromSecret.publicKey.toBase58());
console.log(accountFromSecret.secretKey);

// 2DVaHtcdTf7cm18Zm9VV8rKK4oSnjmTkKE6MiXe18Qsb
// Uint8Array(64) [
//   152,  43, 116, 211, 207,  41, 220,  33, 193, 168, 118,
//    24, 176,  83, 206, 132,  47, 194,   2, 203, 186, 131,
//   197, 228, 156, 170, 154,  41,  56,  76, 159, 124,  18,
//    14, 247,  32, 210,  51, 102,  41,  43,  21,  12, 170,
//   166, 210, 195, 188,  60, 220, 210,  96, 136, 158,   6,
//   205, 189, 165, 112,  32, 200, 116, 164, 234
// ]
```

Использование `generate` генерирует случайную пару ключей для использования в качестве учетной записи на Solana. Используя `fromSeed`, вы можете сгенерировать пару ключей с помощью детерминированного конструктора. `fromSecret` создает пару ключей из секретного массива Uint8. Вы можете видеть, что publicKey для пары ключей «сгенерировать» и пары ключей «fromSecret» одинаковы, потому что секрет из пары ключей «сгенерировать» используется в «fromSecret».

**Предупреждение**: не используйте fromSeed, если только вы не создаете семя с высокой энтропией. Не делитесь своим семенем. Относитесь к семени так же, как к закрытому ключу.

### Публичный ключ

[Исходная документация](https://solana-labs.github.io/solana-web3.js/classes/PublicKey.html)

PublicKey используется в `@solana/web3.js` в транзакциях, парах ключей и программах. Публичный ключ требуется при перечислении каждой учетной записи в транзакции и в качестве общего идентификатора в Solana.

PublicKey может быть создан с помощью строки в кодировке base58, буфера, Uint8Array, числа и массива чисел.

#### Пример использования

```javascript
const {Buffer} = require('buffer');
const web3 = require('@solana/web3.js');
const crypto = require('crypto');

// Create a PublicKey with a base58 encoded string
let base58publicKey = new web3.PublicKey('5xot9PVkphiX2adznghwrAuxGs2zeWisNSxMW6hU6Hkj');
console.log(base58publicKey.toBase58());

// 5xot9PVkphiX2adznghwrAuxGs2zeWisNSxMW6hU6Hkj

// Create a Program Address
let highEntropyBuffer = crypto.randomBytes(31);
let programAddressFromKey = await web3.PublicKey.createProgramAddress([highEntropyBuffer.slice(0, 31)], base58publicKey);
console.log(`Generated Program Address: ${programAddressFromKey.toBase58()}`);

// Generated Program Address: 3thxPEEz4EDWHNxo1LpEpsAxZryPAHyvNVXJEJWgBgwJ

// Find Program address given a PublicKey
let validProgramAddress = await web3.PublicKey.findProgramAddress([Buffer.from('', 'utf8')], programAddressFromKey);
console.log(`Valid Program Address: ${validProgramAddress}`);

// Valid Program Address: C14Gs3oyeXbASzwUpqSymCKpEyccfEuSe8VRar9vJQRE,253
```

### SystemProgram

[Исходная документация](https://solana-labs.github.io/solana-web3.js/classes/SystemProgram.html)

SystemProgram предоставляет возможность создавать учетные записи, распределять данные учетных записей, назначать учетную запись программам, работать с одноразовыми учетными записями и передавать лампы. Вы можете использовать класс SystemInstruction, чтобы помочь с расшифровкой и чтением отдельных инструкций.

#### Пример использования

```javascript
const web3 = require("@solana/web3.js");

// Airdrop SOL for paying transactions
let payer = web3.Keypair.generate();
let connection = new web3.Connection(web3.clusterApiUrl('devnet'), 'confirmed');

let airdropSignature = await connection.requestAirdrop(
    payer.publicKey,
    web3.LAMPORTS_PER_SOL,
);

await connection.confirmTransaction(airdropSignature);

// Allocate Account Data
let allocatedAccount = web3.Keypair.generate();
let allocateInstruction = web3.SystemProgram.allocate({
    accountPubkey: allocatedAccount.publicKey,
    space: 100,
})
let transaction = new web3.Transaction().add(allocateInstruction);

await web3.sendAndConfirmTransaction(connection, transaction, [payer, allocatedAccount])

// Create Nonce Account
let nonceAccount = web3.Keypair.generate();
let minimumAmountForNonceAccount = await connection.getMinimumBalanceForRentExemption(
    web3.NONCE_ACCOUNT_LENGTH,
);
let createNonceAccountTransaction = new web3.Transaction().add(
web3.SystemProgram.createNonceAccount({
    fromPubkey: payer.publicKey,
    noncePubkey: nonceAccount.publicKey,
    authorizedPubkey: payer.publicKey,
    lamports: minimumAmountForNonceAccount,
}),
);

await web3.sendAndConfirmTransaction(connection, createNonceAccountTransaction, [payer, nonceAccount])

// Advance nonce - Used to create transactions as an account custodian
let advanceNonceTransaction = new web3.Transaction().add(
    web3.SystemProgram.nonceAdvance({
        noncePubkey: nonceAccount.publicKey,
        authorizedPubkey: payer.publicKey,
    }),
);

await web3.sendAndConfirmTransaction(connection, advanceNonceTransaction, [payer])

// Transfer lamports between accounts
let toAccount = web3.Keypair.generate();

let transferTransaction = new web3.Transaction().add(
web3.SystemProgram.transfer({
    fromPubkey: payer.publicKey,
    toPubkey: toAccount.publicKey,
    lamports: 1000,
}),
);
await web3.sendAndConfirmTransaction(connection, transferTransaction, [payer])

// Assign a new account to a program
let programId = web3.Keypair.generate();
let assignedAccount = web3.Keypair.generate();

let assignTransaction = new web3.Transaction().add(
web3.SystemProgram.assign({
    accountPubkey: assignedAccount.publicKey,
    programId: programId.publicKey,
}),
);

await web3.sendAndConfirmTransaction(connection, assignTransaction, [payer, assignedAccount]);
```

### Secp256k1Program

[Исходная документация](https://solana-labs.github.io/solana-web3.js/classes/Secp256k1Program.html)

Secp256k1Program используется для проверки подписей Secp256k1, которые используются как Биткойном, так и Эфириумом.

#### Пример использования

```javascript
const {keccak_256} = require('js-sha3');
const web3 = require("@solana/web3.js");
const secp256k1 = require('secp256k1');

// Create a Ethereum Address from secp256k1
let secp256k1PrivateKey;
do {
    secp256k1PrivateKey = web3.Keypair.generate().secretKey.slice(0, 32);
} while (!secp256k1.privateKeyVerify(secp256k1PrivateKey));

let secp256k1PublicKey = secp256k1.publicKeyCreate(secp256k1PrivateKey, false).slice(1);

let ethAddress = web3.Secp256k1Program.publicKeyToEthAddress(secp256k1PublicKey);
console.log(`Ethereum Address: 0x${ethAddress.toString('hex')}`);

// Ethereum Address: 0xadbf43eec40694eacf36e34bb5337fba6a2aa8ee

// Fund a keypair to create instructions
let fromPublicKey = web3.Keypair.generate();
let connection = new web3.Connection(web3.clusterApiUrl('devnet'), 'confirmed');

let airdropSignature = await connection.requestAirdrop(
    fromPublicKey.publicKey,
    web3.LAMPORTS_PER_SOL,
);
await connection.confirmTransaction(airdropSignature);

// Sign Message with Ethereum Key
let plaintext = Buffer.from('string address');
let plaintextHash = Buffer.from(keccak_256.update(plaintext).digest());
let {signature, recid: recoveryId} = secp256k1.ecdsaSign(
    plaintextHash,
    secp256k1PrivateKey
);

// Create transaction to verify the signature
let transaction = new Transaction().add(
    web3.Secp256k1Program.createInstructionWithEthAddress({
        ethAddress: ethAddress.toString('hex'),
        plaintext,
        signature,
        recoveryId,
    }),
);

// Transaction will succeed if the message is verified to be signed by the address
await web3.sendAndConfirmTransaction(connection, transaction, [fromPublicKey]);
```

### Message

[Исходная документация](https://solana-labs.github.io/solana-web3.js/classes/Message.html)

Сообщение используется как еще один способ построения транзакций. Вы можете создать сообщение, используя учетные записи, заголовок, инструкции и недавний блокхеш, которые являются частью транзакции. [Транзакция](javascript-api.md#Transaction) — это сообщение и список подписей, необходимых для выполнения транзакции.

#### Пример использования

```javascript
const {Buffer} = require("buffer");
const bs58 = require('bs58');
const web3 = require('@solana/web3.js');

let toPublicKey = web3.Keypair.generate().publicKey;
let fromPublicKey = web3.Keypair.generate();

let connection = new web3.Connection(
    web3.clusterApiUrl('devnet'),
    'confirmed'
);

let airdropSignature = await connection.requestAirdrop(
    fromPublicKey.publicKey,
    web3.LAMPORTS_PER_SOL,
);

await connection.confirmTransaction(airdropSignature);

let type = web3.SYSTEM_INSTRUCTION_LAYOUTS.Transfer;
let data = Buffer.alloc(type.layout.span);
let layoutFields = Object.assign({instruction: type.index});
type.layout.encode(layoutFields, data);

let recentBlockhash = await connection.getRecentBlockhash();

let messageParams = {
    accountKeys: [
        fromPublicKey.publicKey.toString(),
        toPublicKey.toString(),
        web3.SystemProgram.programId.toString()
    ],
    header: {
        numReadonlySignedAccounts: 0,
        numReadonlyUnsignedAccounts: 1,
        numRequiredSignatures: 1,
    },
    instructions: [
        {
        accounts: [0, 1],
        data: bs58.encode(data),
        programIdIndex: 2,
        },
    ],
    recentBlockhash,
};

let message = new web3.Message(messageParams);

let transaction = web3.Transaction.populate(
    message,
    [fromPublicKey.publicKey.toString()]
);

await web3.sendAndConfirmTransaction(connection, transaction, [fromPublicKey])
```

### Struct

[Исходная документация](https://solana-labs.github.io/solana-web3.js/classes/Struct.html)

Класс struct используется для создания совместимых с Rust структур в javascript. Этот класс совместим только со структурами Rust, закодированными Borsh.

#### Пример использования

Структура в Rust:

```rust
pub struct Fee {
    pub denominator: u64,
    pub numerator: u64,
}
```

Использование web3:

```javascript
import BN from 'bn.js';
import {Struct} from '@solana/web3.js';

export class Fee extends Struct {
  denominator: BN;
  numerator: BN;
}
```

### Enum

[Исходная документация](https://solana-labs.github.io/solana-web3.js/classes/Enum.html)

Класс Enum используется для представления Enum, совместимого с Rust, в javascript. Перечисление будет просто строковым представлением, если оно зарегистрировано, но может быть правильно закодировано/декодировано при использовании в сочетании с [Struct](javascript-api.md#Struct). Этот класс совместим только с перечислениями Rust, закодированными Borsh.

#### Пример использования

Rust:

```rust
pub enum AccountType {
    Uninitialized,
    StakePool,
    ValidatorList,
}
```

Web3:

```javascript
import {Enum} from '@solana/web3.js';

export class AccountType extends Enum {}
```

### NonceAccount

[Исходная документация](https://solana-labs.github.io/solana-web3.js/classes/NonceAccount.html)

Обычно транзакция отклоняется, если поле `recentBlockhash` транзакции слишком старое. Для предоставления определенных кастодиальных услуг используются одноразовые учетные записи. Срок действия транзакций, в которых используется `recentBlockhash`, захваченный в цепочке учетной записью Nonce, не истекает до тех пор, пока учетная запись Nonce не будет продвинута.

Вы можете создать одноразовую учетную запись, сначала создав обычную учетную запись, а затем используя `SystemProgram`, чтобы сделать учетную запись одноразовой учетной записью.

#### Пример использования

```javascript
const web3 = require('@solana/web3.js');

// Create connection
let connection = new web3.Connection(
    web3.clusterApiUrl('devnet'),
    'confirmed',
);

// Generate accounts
let account = web3.Keypair.generate();
let nonceAccount = web3.Keypair.generate();

// Fund account
let airdropSignature = await connection.requestAirdrop(
    account.publicKey,
    web3.LAMPORTS_PER_SOL,
);

await connection.confirmTransaction(airdropSignature);

// Get Minimum amount for rent exemption
let minimumAmount = await connection.getMinimumBalanceForRentExemption(
    web3.NONCE_ACCOUNT_LENGTH,
);

// Form CreateNonceAccount transaction
let transaction = new web3.Transaction().add(
web3.SystemProgram.createNonceAccount({
    fromPubkey: account.publicKey,
    noncePubkey: nonceAccount.publicKey,
    authorizedPubkey: account.publicKey,
    lamports: minimumAmount,
}),
);
// Create Nonce Account
await web3.sendAndConfirmTransaction(
    connection,
    transaction,
    [account, nonceAccount]
);

let nonceAccountData = await connection.getNonce(
    nonceAccount.publicKey,
    'confirmed',
);

console.log(nonceAccountData);
// NonceAccount {
//   authorizedPubkey: PublicKey {
//     _bn: <BN: 919981a5497e8f85c805547439ae59f607ea625b86b1138ea6e41a68ab8ee038>
//   },
//   nonce: '93zGZbhMmReyz4YHXjt2gHsvu5tjARsyukxD4xnaWaBq',
//   feeCalculator: { lamportsPerSignature: 5000 }
// }

let nonceAccountInfo = await connection.getAccountInfo(
    nonceAccount.publicKey,
    'confirmed'
);

let nonceAccountFromInfo = web3.NonceAccount.fromAccountData(
    nonceAccountInfo.data
);

console.log(nonceAccountFromInfo);
// NonceAccount {
//   authorizedPubkey: PublicKey {
//     _bn: <BN: 919981a5497e8f85c805547439ae59f607ea625b86b1138ea6e41a68ab8ee038>
//   },
//   nonce: '93zGZbhMmReyz4YHXjt2gHsvu5tjARsyukxD4xnaWaBq',
//   feeCalculator: { lamportsPerSignature: 5000 }
// }
```

В приведенном выше примере показано, как создать `NonceAccount` с помощью `SystemProgram.createNonceAccount`, а также как получить `NonceAccount` из accountInfo. Используя одноразовый номер, вы можете создавать транзакции в автономном режиме с одноразовым номером вместо `recentBlockhash`.

### VoteAccount

[Исходная документация](https://solana-labs.github.io/solana-web3.js/classes/VoteAccount.html)

Учетная запись для голосования — это объект, предоставляющий возможность декодирования учетных записей для голосования из собственной программы учетной записи для голосования в сети.

#### Пример использования

```javascript
const web3 = require('@solana/web3.js');

let voteAccountInfo = await connection.getProgramAccounts(web3.VOTE_PROGRAM_ID);
let voteAccountFromData = web3.VoteAccount.fromAccountData(voteAccountInfo[0].account.data);
console.log(voteAccountFromData);
/*
VoteAccount {
  nodePubkey: PublicKey {
    _bn: <BN: cf1c635246d4a2ebce7b96bf9f44cacd7feed5552be3c714d8813c46c7e5ec02>
  },
  authorizedWithdrawer: PublicKey {
    _bn: <BN: b76ae0caa56f2b9906a37f1b2d4f8c9d2a74c1420cd9eebe99920b364d5cde54>
  },
  commission: 10,
  rootSlot: 104570885,
  votes: [
    { slot: 104570886, confirmationCount: 31 },
    { slot: 104570887, confirmationCount: 30 },
    { slot: 104570888, confirmationCount: 29 },
    { slot: 104570889, confirmationCount: 28 },
    { slot: 104570890, confirmationCount: 27 },
    { slot: 104570891, confirmationCount: 26 },
    { slot: 104570892, confirmationCount: 25 },
    { slot: 104570893, confirmationCount: 24 },
    { slot: 104570894, confirmationCount: 23 },
    ...
  ],
  authorizedVoters: [ { epoch: 242, authorizedVoter: [PublicKey] } ],
  priorVoters: [
      [Object], [Object], [Object],
      [Object], [Object], [Object],
      [Object], [Object], [Object],
      [Object], [Object], [Object],
      [Object], [Object], [Object],
      [Object], [Object], [Object],
      [Object], [Object], [Object],
      [Object], [Object], [Object],
      [Object], [Object], [Object],
      [Object], [Object], [Object],
      [Object], [Object]
   ],
  epochCredits: [
    { epoch: 179, credits: 33723163, prevCredits: 33431259 },
    { epoch: 180, credits: 34022643, prevCredits: 33723163 },
    { epoch: 181, credits: 34331103, prevCredits: 34022643 },
    { epoch: 182, credits: 34619348, prevCredits: 34331103 },
    { epoch: 183, credits: 34880375, prevCredits: 34619348 },
    { epoch: 184, credits: 35074055, prevCredits: 34880375 },
    { epoch: 185, credits: 35254965, prevCredits: 35074055 },
    { epoch: 186, credits: 35437863, prevCredits: 35254965 },
    { epoch: 187, credits: 35672671, prevCredits: 35437863 },
    { epoch: 188, credits: 35950286, prevCredits: 35672671 },
    { epoch: 189, credits: 36228439, prevCredits: 35950286 },
    ...
  ],
  lastTimestamp: { slot: 104570916, timestamp: 1635730116 }
}
*/
```

## Стэйкинг

### StakeProgram

[Исходная документация](https://solana-labs.github.io/solana-web3.js/classes/StakeProgram.html)

StakeProgram упрощает размещение SOL и делегирование их любым валидаторам в сети. Вы можете использовать StakeProgram для создания стейк-аккаунта, ставки SOL, авторизации аккаунтов для снятия вашей ставки, деактивации вашей ставки и вывода средств. Класс StakeInstruction используется для декодирования и чтения дополнительных инструкций из транзакций, вызывающих StakeProgram.

#### Пример использования

```javascript
const web3 = require("@solana/web3.js");

// Fund a key to create transactions
let fromPublicKey = web3.Keypair.generate();
let connection = new web3.Connection(web3.clusterApiUrl('devnet'), 'confirmed');

let airdropSignature = await connection.requestAirdrop(
    fromPublicKey.publicKey,
    web3.LAMPORTS_PER_SOL,
);
await connection.confirmTransaction(airdropSignature);

// Create Account
let stakeAccount = web3.Keypair.generate();
let authorizedAccount = web3.Keypair.generate();
/* Note: This is the minimum amount for a stake account -- Add additional Lamports for staking
    For example, we add 50 lamports as part of the stake */
let lamportsForStakeAccount = (await connection.getMinimumBalanceForRentExemption(web3.StakeProgram.space)) + 50;

let createAccountTransaction = web3.StakeProgram.createAccount({
    fromPubkey: fromPublicKey.publicKey,
    authorized: new web3.Authorized(authorizedAccount.publicKey, authorizedAccount.publicKey),
    lamports: lamportsForStakeAccount,
    lockup: new web3.Lockup(0, 0, fromPublicKey.publicKey),
    stakePubkey: stakeAccount.publicKey
});
await web3.sendAndConfirmTransaction(connection, createAccountTransaction, [fromPublicKey, stakeAccount]);

// Check that stake is available
let stakeBalance = await connection.getBalance(stakeAccount.publicKey);
console.log(`Stake balance: ${stakeBalance}`)
// Stake balance: 2282930

// We can verify the state of our stake. This may take some time to become active
let stakeState = await connection.getStakeActivation(stakeAccount.publicKey);
console.log(`Stake Stake: ${stakeState.state}`);
// Stake State: inactive

// To delegate our stake, we get the current vote accounts and choose the first
let voteAccounts = await connection.getVoteAccounts();
let voteAccount = voteAccounts.current.concat(
    voteAccounts.delinquent,
)[0];
let votePubkey = new web3.PublicKey(voteAccount.votePubkey);

// We can then delegate our stake to the voteAccount
let delegateTransaction = web3.StakeProgram.delegate({
    stakePubkey: stakeAccount.publicKey,
    authorizedPubkey: authorizedAccount.publicKey,
    votePubkey: votePubkey,
});
await web3.sendAndConfirmTransaction(connection, delegateTransaction, [fromPublicKey, authorizedAccount]);

// To withdraw our funds, we first have to deactivate the stake
let deactivateTransaction = web3.StakeProgram.deactivate({
    stakePubkey: stakeAccount.publicKey,
    authorizedPubkey: authorizedAccount.publicKey,
});
await web3.sendAndConfirmTransaction(connection, deactivateTransaction, [fromPublicKey, authorizedAccount]);

// Once deactivated, we can withdraw our funds
let withdrawTransaction = web3.StakeProgram.withdraw({
    stakePubkey: stakeAccount.publicKey,
    authorizedPubkey: authorizedAccount.publicKey,
    toPubkey: fromPublicKey.publicKey,
    lamports: stakeBalance,
});

await web3.sendAndConfirmTransaction(connection, withdrawTransaction, [fromPublicKey, authorizedAccount]);
```

### Authorized

[Исходная документация](https://solana-labs.github.io/solana-web3.js/classes/Authorized.html)

Authorized — это объект, используемый при создании авторизованной учетной записи для ставок в Solana. Вы можете указать «стейкера» и «выводчика» отдельно, что позволит снимать средства с другой учетной записи, отличной от стейкера.

Вы можете узнать больше об использовании объекта `Authorized` в [`StakeProgram`](javascript-api.md#StakeProgram)

### Lockup

[Исходная документация](https://solana-labs.github.io/solana-web3.js/classes/Lockup.html)

Блокировка используется вместе с [StakeProgram](javascript-api.md#StakeProgram) для создания учетной записи. Блокировка используется для определения того, как долго ставка будет заблокирована или не может быть извлечена. Если для блокировки установлено значение 0 как для эпохи, так и для временной метки Unix, блокировка будет отключена для учетной записи доли.

#### Пример использования

```javascript
const {Authorized, Keypair, Lockup, StakeProgram} = require("@solana/web3.js");

let account = Keypair.generate();
let stakeAccount = Keypair.generate();
let authorized = new Authorized(account.publicKey, account.publicKey);
let lockup = new Lockup(0, 0, account.publicKey);

let createStakeAccountInstruction = StakeProgram.createAccount({
    fromPubkey: account.publicKey,
    authorized: authorized,
    lamports: 1000,
    lockup: lockup,
    stakePubkey: stakeAccount.publicKey
});
```

Приведенный выше код создает `createStakeAccountInstruction`, которая будет использоваться при создании учетной записи с помощью `StakeProgram`. Для блокировки установлено значение 0 как для эпохи, так и для временной метки Unix, что отключает блокировку для учетной записи.

См. [StakeProgram](javascript-api.md#StakeProgram) для получения дополнительной информации.
