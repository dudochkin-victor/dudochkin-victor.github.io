+++
title = "Использование Web3"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

В этом руководстве рассматривается процесс использования Web3 для ручной подписи и отправки транзакции на узел разработки Edgeware EVM. В этом примере мы будем использовать Node.js и простой код JavaScript.

> **Примечание** Это руководство было создано с использованием версии Edgeware EVM. Платформа Edgeware EVM и компоненты [Frontier](https://github.com/paritytech/frontier), на которые она опирается для совместимости Ethereum на основе субстрата, все еще находятся в стадии активной разработки. Мы создали это руководство, чтобы вы могли протестировать функции совместимости Edgeware с Ethereum. Несмотря на то, что мы все еще находимся в разработке, мы считаем важным, чтобы заинтересованные члены сообщества и разработчики имели возможность начать пробовать что-то с Edgeware и оставлять отзывы.

## Проверка предварительных условий

Установлен [Nodejs](https://nodejs.org/en/) и конкретный менеджер пакетов, например [yarn](https://classic.yarnpkg.com/en/docs/install/#mac-stable) или [npm] (https://www.npmjs.com/get-npm), остальные батареи включены в это руководство. В этом руководстве предполагается, что у вас есть [работающий локальный узел Edgeware EVM, работающий в режиме `--dev`](https://github.com/hicommonwealth/edgeware-documentation/blob/master/docs/quickstart/evm-introduction/настройка локального узла.md).

```
git clone https://github.com/edgeware-builders/tutorials tutorials;cd tutorials/web3;yarn
```

Он переместится в ваш клонированный репозиторий, установит необходимые пакеты, и вы готовы к работе!

## Создание транзакции

В этом примере нам нужен только один файл JavaScript для создания транзакции, которую мы запустим с помощью команды «node» в терминале. Скрипт переведет 1337 ETH с аккаунта Genesis на другой адрес. Для простоты файл разделен на три раздела: определение переменных, создание транзакций и трансляция транзакций.

Нам нужно установить пару значений в определениях переменных:

- Создайте наш конструктор Web3 \(`web3`\)
- Определите переменную «privKey» в качестве закрытого ключа нашей исходной учетной записи, где хранятся все средства при развертывании вашего локального узла Edgeware EVM и что используется для подписи транзакций.
- Установите адрес «от» и «куда», убедившись, что значение «toAddress» установлено на другой адрес, например, тот, который был создан MetaMask при настройке локального кошелька.

```javascript
const Web3 = require('web3');

const privKey =
  '99B3C12287537E38C90A9219D4CB074A89A16E9CDB20BF85728EBD97C343E342'; // Genesis private key
const addressFrom = '0x6Be02d1d3665660d22FF9624b7BE0551ee1Ac91b';
const addressTo = '0xB90168C8CBcd351D069ffFdA7B71cd846924d551';
const web3 = new Web3('http://localhost:9933');
```

Оба раздела _create transaction_ и _deploy transaction_ заключены в асинхронную функцию, которая обрабатывает промисы из нашего экземпляра Web3. Чтобы создать транзакцию, мы используем команду `web3.eth.accounts.signTransaction(tx, privKey)`, где мы должны определить объект tx с некоторыми параметрами, такими как: `addressFrom`, `addressTo`, `количество токенов для отправки` и `лимит газа`.

```javascript
const deploy = async () => {
  console.log(
    `Attempting to make transaction from ${addressFrom} to ${addressTo}`
  );

  const createTransaction = await web3.eth.accounts.signTransaction(
    {
      from: addressFrom,
      to: addressTo,
      value: web3.utils.toWei('1337', 'ether'),
      gas: '2100000000',
    },
    privKey
  );
```

Полный `createTransaction.js` должен выглядеть так!

```javascript
const Web3 = require('web3');

const privKey =
  '99B3C12287537E38C90A9219D4CB074A89A16E9CDB20BF85728EBD97C343E342'; // Genesis private key
const addressFrom = '0x6Be02d1d3665660d22FF9624b7BE0551ee1Ac91b';
const addressTo = '0xB90168C8CBcd351D069ffFdA7B71cd846924d551';
const web3 = new Web3('http://localhost:9933');

// Create transaction
const deploy = async () => {
  console.log(
    `Attempting to make transaction from ${addressFrom} to ${addressTo}`
  );

  const createTransaction = await web3.eth.accounts.signTransaction(
    {
      from: addressFrom,
      to: addressTo,
      value: web3.utils.toWei('1337', 'ether'),
      gas: '2100000000',
    },
    privKey
  );

  // Deploy transaction
  const createReceipt = await web3.eth.sendSignedTransaction(
    createTransaction.rawTransaction
  );
  console.log(
    `Transaction successful with hash: ${createReceipt.transactionHash}`
  );
};

deploy();
```

## Проверить баланс на счетах

Перед запуском скрипта нам нужен еще один файл для проверки балансов обоих адресов до и после выполнения транзакции. Мы можем легко сделать это, используя функции совместимости Edgeware с Ethereum.

`balances.js` выглядит так:

```javascript
const Web3 = require('web3');

// Variables definition
const addressFrom = '0x6Be02d1d3665660d22FF9624b7BE0551ee1Ac91b';
const addressTo = '0x6bB5423f0Dd01B8C5028a1bc01e1f1bDe4523e72';
const web3 = new Web3('http://127.0.0.1:9933');

// Balance call
const balances = async () => {
  const balanceFrom = web3.utils.fromWei(
    await web3.eth.getBalance(addressFrom),
    'ether'
  );
  const balanceTo = await web3.utils.fromWei(
    await web3.eth.getBalance(addressTo),
    'ether'
  );

  console.log(`The balance of ${addressFrom} is: ${balanceFrom} ETH.`);
  console.log(`The balance of ${addressTo} is: ${balanceTo} ETH.`);
};

balances();
```

## Время игры

Запустите `node balance.js`, чтобы проверить начальный баланс на счетах.

![web3-init-balance.png](../../.gitbook/assets/web3-init-balance.png)

Запустите `node createTransaction.js`, чтобы передать некоторые вещи по цепочке

![web3-makeTransaction.png](../../.gitbook/assets/web3-makeTransaction.png)

Запустите `node balance.js`, чтобы проверить баланс результатов

![web3-result-balance.png](../../.gitbook/assets/web3-result-balance.png)

## Свяжитесь с нами для большего вовлечения

Рад, что вы прошли через это! 🥰 Мы стремимся помочь вам в ваших исследованиях с помощью функции совместимости Edgeware Ethereum. Мы **будем рады услышать ваш опыт и предложения, которые вы можете нам предложить.**. Вы можете свободно [общаться с нами в каналах Edgeware, таких как Discord, Element и Telegram](https://linktr.ee/edg_developers), мы можем помочь вам с проблемами, которые могут у вас возникнуть, или с проектом, который вы хотите финансировать. через нашу [программу казначейства](https://docs.edgewa.re/edgeware-runtime/treasury). Не стесняйтесь делиться своими отзывами на наших каналах, всегда есть место для улучшения! 🙌
