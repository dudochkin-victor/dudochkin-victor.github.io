+++
title = "JSON RPC API"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Узлы Solana принимают HTTP-запросы, используя спецификацию [JSON-RPC 2.0](https://www.jsonrpc.org/specification).

Чтобы взаимодействовать с узлом Solana внутри приложения JavaScript, используйте
библиотека [solana-web3.js](https://github.com/solana-labs/solana-web3.js), которая
предоставляет удобный интерфейс для методов RPC.

## Конечная точка RPC HTTP

**Порт по умолчанию:** 8899, например. [http://localhost:8899](http://localhost:8899), [http://192.168.1.88:8899](http://192.168.1.88:8899)

## Конечная точка RPC PubSub WebSocket

**Порт по умолчанию:** 8900, например. ws://локальный:8900, [http://192.168.1.88:8900](http://192.168.1.88:8900)

## Методы

- [getAccountInfo](jsonrpc-api.md#getaccountinfo)
- [getBalance](jsonrpc-api.md#getbalance)
- [getBlock](jsonrpc-api.md#getblock)
- [getBlockHeight](jsonrpc-api.md#getblockheight)
- [getBlockProduction](jsonrpc-api.md#getblockproduction)
- [getBlockCommitment](jsonrpc-api.md#getblockcommitment)
- [getBlocks](jsonrpc-api.md#getblocks)
- [getBlocksWithLimit](jsonrpc-api.md#getblockswithlimit)
- [getBlockTime](jsonrpc-api.md#getblocktime)
- [getClusterNodes](jsonrpc-api.md#getclusternodes)
- [getEpochInfo](jsonrpc-api.md#getepochinfo)
- [getEpochSchedule](jsonrpc-api.md#getepochschedule)
- [getFeeForMessage](jsonrpc-api.md#getfeeformessage)
- [getFirstAvailableBlock](jsonrpc-api.md#getfirstavailableblock)
- [getGenesisHash](jsonrpc-api.md#getgenesishash)
- [getHealth](jsonrpc-api.md#gethealth)
- [getHighestSnapshotSlot](jsonrpc-api.md#gethighestsnapshotslot)
- [getIdentity](jsonrpc-api.md#getidentity)
- [getInflationGovernor](jsonrpc-api.md#getinflationgovernor)
- [getInflationRate](jsonrpc-api.md#getinflationrate)
- [getInflationReward](jsonrpc-api.md#getinflationreward)
- [getLargestAccounts](jsonrpc-api.md#getlargestaccounts)
- [getLatestBlockhash](jsonrpc-api.md#getlatestblockhash)
- [getLeaderSchedule](jsonrpc-api.md#getleaderschedule)
- [getMaxRetransmitSlot](jsonrpc-api.md#getmaxretransmitslot)
- [getMaxShredInsertSlot](jsonrpc-api.md#getmaxshredinsertslot)
- [getMinimumBalanceForRentExemption](jsonrpc-api.md#getminimumbalanceforrentexemption)
- [getMultipleAccounts](jsonrpc-api.md#getmultipleaccounts)
- [getProgramAccounts](jsonrpc-api.md#getprogramaccounts)
- [getRecentPerformanceSamples](jsonrpc-api.md#getrecentperformancesamples)
- [getSignaturesForAddress](jsonrpc-api.md#getsignaturesforaddress)
- [getSignatureStatuses](jsonrpc-api.md#getsignaturestatuses)
- [getSlot](jsonrpc-api.md#getslot)
- [getSlotLeader](jsonrpc-api.md#getslotleader)
- [getSlotLeaders](jsonrpc-api.md#getslotleaders)
- [getStakeActivation](jsonrpc-api.md#getstakeactivation)
- [getSupply](jsonrpc-api.md#getsupply)
- [getTokenAccountBalance](jsonrpc-api.md#gettokenaccountbalance)
- [getTokenAccountsByDelegate](jsonrpc-api.md#gettokenaccountsbydelegate)
- [getTokenAccountsByOwner](jsonrpc-api.md#gettokenaccountsbyowner)
- [getTokenLargestAccounts](jsonrpc-api.md#gettokenlargestaccounts)
- [getTokenSupply](jsonrpc-api.md#gettokensupply)
- [getTransaction](jsonrpc-api.md#gettransaction)
- [getTransactionCount](jsonrpc-api.md#gettransactioncount)
- [getVersion](jsonrpc-api.md#getversion)
- [getVoteAccounts](jsonrpc-api.md#getvoteaccounts)
- [isBlockhashValid](jsonrpc-api.md#isblockhashvalid)
- [minimumLedgerSlot](jsonrpc-api.md#minimumledgerslot)
- [requestAirdrop](jsonrpc-api.md#requestairdrop)
- [sendTransaction](jsonrpc-api.md#sendtransaction)
- [simulateTransaction](jsonrpc-api.md#simulatetransaction)
- [Subscription Websocket](jsonrpc-api.md#subscription-websocket)
  - [accountSubscribe](jsonrpc-api.md#accountsubscribe)
  - [accountUnsubscribe](jsonrpc-api.md#accountunsubscribe)
  - [logsSubscribe](jsonrpc-api.md#logssubscribe)
  - [logsUnsubscribe](jsonrpc-api.md#logsunsubscribe)
  - [programSubscribe](jsonrpc-api.md#programsubscribe)
  - [programUnsubscribe](jsonrpc-api.md#programunsubscribe)
  - [signatureSubscribe](jsonrpc-api.md#signaturesubscribe)
  - [signatureUnsubscribe](jsonrpc-api.md#signatureunsubscribe)
  - [slotSubscribe](jsonrpc-api.md#slotsubscribe)
  - [slotUnsubscribe](jsonrpc-api.md#slotunsubscribe)

### Нестабильные методы

В нестабильных методах могут появиться критические изменения в выпусках исправлений, и они могут не поддерживаться бессрочно.

- [blockSubscribe](jsonrpc-api.md#blocksubscribe---unstable-disabled-by-default)
- [blockUnsubscribe](jsonrpc-api.md#blockunsubscribe)
- [slotsUpdatesSubscribe](jsonrpc-api.md#slotsupdatessubscribe---unstable)
- [slotsUpdatesUnsubscribe](jsonrpc-api.md#slotsupdatesunsubscribe)
- [voteSubscribe](jsonrpc-api.md#votesubscribe---unstable-disabled-by-default)
- [voteUnsubscribe](jsonrpc-api.md#voteunsubscribe)

### Устаревшие методы

- [getConfirmedBlock](jsonrpc-api.md#getconfirmedblock)
- [getConfirmedBlocks](jsonrpc-api.md#getconfirmedblocks)
- [getConfirmedBlocksWithLimit](jsonrpc-api.md#getconfirmedblockswithlimit)
- [getConfirmedSignaturesForAddress2](jsonrpc-api.md#getconfirmedsignaturesforaddress2)
- [getConfirmedTransaction](jsonrpc-api.md#getconfirmedtransact)
- [getFeeCalculatorForBlockhash](jsonrpc-api.md#getfeecalculatorforblockhash)
- [getFeeRateGovernor](jsonrpc-api.md#getfeerategovernor)
- [getFees](jsonrpc-api.md#getfees)
- [getRecentBlockhash](jsonrpc-api.md#getrecentblockhash)
- [getSnapshotSlot](jsonrpc-api.md#getsnapshotslot)

## Форматирование запроса

Чтобы сделать запрос JSON-RPC, отправьте запрос HTTP POST с `Content-Type:
заголовок application/json`. Данные запроса JSON должны содержать 4 поля:

- `jsonrpc: <строка>`, установите значение `"2.0"`
- `id: <число>`, уникальное целое число, сгенерированное клиентом.
- `метод: <строка>`, строка, содержащая вызываемый метод.
- `params: <array>`, массив JSON упорядоченных значений параметров

Пример использования curl:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {
    "jsonrpc": "2.0",
    "id": 1,
    "method": "getBalance",
    "params": [
      "83astBRguLMdt2h5U1Tpdq5tjFoJ6noeGwaY3mDLVcri"
    ]
  }
'
```

Вывод ответа будет объектом JSON со следующими полями:

- `jsonrpc: <string>`, соответствующий спецификации запроса
- `id: <число>`, соответствующий идентификатору запроса
- `результат: <массив|число|объект|строка>`, запрошенные данные или подтверждение успеха

Запросы можно отправлять пакетами, отправляя массив объектов запросов JSON-RPC в качестве данных для одного POST.

## Определения

- Хэш: хэш SHA-256 фрагмента данных.
- Pubkey: открытый ключ пары ключей Ed25519.
- Транзакция: список инструкций Solana, подписанных парой ключей клиента для авторизации этих действий.
- Подпись: подпись Ed25519 полезных данных транзакции, включая инструкции. Это можно использовать для идентификации транзакций.

## Настройка фиксации состояния

Для предполетных проверок и обработки транзакций узлы Solana выбирают, какой банк
состояние для запроса на основе требования обязательства, установленного клиентом. То
обязательство описывает, насколько завершен блок в данный момент времени. Когда
запрашивая состояние леджера, рекомендуется использовать более низкие уровни обязательств
чтобы сообщить о ходе выполнения и более высоких уровнях, чтобы гарантировать, что состояние не будет откатываться.

В порядке убывания обязательств (от наиболее завершенных к наименее завершенным) клиенты
может указать:

- `"finalized"` - узел будет запрашивать самый последний блок, подтвержденный квалифицированным большинством
  кластера как достигшего максимальной блокировки, что означает, что кластер
  распознал этот блок как завершенный
- `"подтверждено"` - узел будет запрашивать самый последний блок, за который проголосовало подавляющее большинство кластера.
  - Он включает голоса из сплетен и повторов.
  - Не учитываются голоса потомков блока, только прямые голоса в этом блоке.
  - Этот уровень подтверждения также поддерживает гарантии «оптимистического подтверждения» в
    версии 1.3 и выше.
- `"processed"` - узел будет запрашивать свой самый последний блок. Обратите внимание, что блок
  может быть пропущен кластером.

Для последовательной обработки множества зависимых транзакций рекомендуется использовать
`"подтвержденное"` обязательство, которое уравновешивает скорость с безопасностью отката.
Для полной безопасности рекомендуется использовать «завершенное» обязательство.

#### Пример

Параметр commit должен быть включен в качестве последнего элемента в массив params:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {
    "jsonrpc": "2.0",
    "id": 1,
    "method": "getBalance",
    "params": [
      "83astBRguLMdt2h5U1Tpdq5tjFoJ6noeGwaY3mDLVcri",
      {
        "commitment": "finalized"
      }
    ]
  }
'
```

#### По умолчанию:

Если конфигурация фиксации не указана, узел по умолчанию будет использовать «завершенную» фиксацию.

Только методы, запрашивающие состояние банка, принимают параметр commit. Они указаны в Справочнике по API ниже.

#### Структура RpcResponse

Многие методы, которые принимают параметр фиксации, возвращают объект RpcResponse JSON, состоящий из двух частей:

- `context`: структура JSON RpcResponseContext, включающая поле `slot`, в котором оценивалась операция.
- `value`: значение, возвращаемое самой операцией.

## Проверка здоровья (Health Check)

Хотя это и не JSON RPC API, `GET /health` в конечной точке HTTP RPC обеспечивает
механизм проверки работоспособности для использования балансировщиками нагрузки или другой сетью
инфраструктура. Этот запрос всегда будет возвращать ответ HTTP 200 OK с телом
«ок», «отстает» или «неизвестно» на основе следующих условий:

1. Если один или несколько аргументов `--known-validator` переданы `solana-validator`, возвращается "ok"
   когда узел имеет слоты `HEALTH_CHECK_SLOT_DISTANCE` самого высокого
   известный валидатор, иначе "позади". «неизвестно» возвращается, когда нет слота
   информация от известных валидаторов пока недоступна.
2. "ok" всегда возвращается, если не указаны известные валидаторы.

## Справочник по JSON RPC API

### получить информацию об учетной записи

Возвращает всю информацию, связанную с учетной записью предоставленного Pubkey.

#### Параметры:

- `<string>` - открытый ключ учетной записи для запроса в виде строки в кодировке base-58.
- `<object>` - (необязательный) объект конфигурации, содержащий следующие необязательные
  поля:
  - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment)
  - `encoding: <string>` - кодировка данных Пользователя, либо "base58" (*slow*), "base64", "base64+zstd", либо "jsonParsed".
    «base58» ограничен данными учетной записи размером менее 129 байт.
    «base64» вернет данные в кодировке base64 для данных учетной записи любого размера.
    «base64+zstd» сжимает данные учетной записи с помощью [Zstandard](https://facebook.github.io/zstd/) и кодирует результат в base64.
    Кодирование «jsonParsed» пытается использовать анализаторы состояния для конкретных программ, чтобы возвращать более удобочитаемые и явные данные о состоянии учетной записи. Если запрашивается "jsonParsed", но синтаксический анализатор не может быть найден, поле возвращается к кодировке "base64", обнаруживаемой, когда поле `data` имеет тип `<string>`.
  - (необязательно) `dataSlice: <object>` – ограничить возвращаемые данные учетной записи, используя предоставленные поля `offset: <usize>` и `length: <usize>`; доступно только для кодировок "base58", "base64" или "base64+zstd".

#### Результаты:

Результатом будет объект RpcResponse JSON со значением, равным:

- `<null>` - если запрошенная учетная запись не существует
- `<object>` - иначе объект JSON, содержащий:
  - `lamports: <u64>`, количество ламп, назначенных этой учетной записи, как u64
  - `owner: <string>`, Pubkey в кодировке base-58 программы, которой назначена эта учетная запись.
  - `data: <[string, encoding]|object>`, данные, связанные с учетной записью, либо в виде закодированных двоичных данных, либо в формате JSON `{<program>: <state>}`, в зависимости от параметра кодирования
  - `executable: <bool>`, логическое значение, указывающее, содержит ли учетная запись программу \ (и строго только для чтения \)
  - `rentEpoch: <u64>`, эпоха, в которую эта учетная запись будет в следующий раз платить арендную плату, как u64

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {
    "jsonrpc": "2.0",
    "id": 1,
    "method": "getAccountInfo",
    "params": [
      "vines1vzrYbzLMRdu58ou5XTby4qAqVRLmqo36NKPTg",
      {
        "encoding": "base58"
      }
    ]
  }
'
```

Ответ:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "context": {
      "slot": 1
    },
    "value": {
      "data": [
        "11116bv5nS2h3y12kD1yUKeMZvGcKLSjQgX6BeV7u1FrjeJcKfsHRTPuR3oZ1EioKtYGiYxpxMG5vpbZLsbcBYBEmZZcMKaSoGx9JZeAuWf",
        "base58"
      ],
      "executable": false,
      "lamports": 1000000000,
      "owner": "11111111111111111111111111111111",
      "rentEpoch": 2
    }
  },
  "id": 1
}
```

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {
    "jsonrpc": "2.0",
    "id": 1,
    "method": "getAccountInfo",
    "params": [
      "4fYNw3dojWmQ4dXtSGE9epjRGy9pFSx62YypT7avPYvA",
      {
        "encoding": "jsonParsed"
      }
    ]
  }
'
```

Ответ:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "context": {
      "slot": 1
    },
    "value": {
      "data": {
        "nonce": {
          "initialized": {
            "authority": "Bbqg1M4YVVfbhEzwA9SpC9FhsaG83YMTYoR4a8oTDLX",
            "blockhash": "3xLP3jK6dVJwpeGeTDYTwdDK3TKchUf1gYYGHa4sF3XJ",
            "feeCalculator": {
              "lamportsPerSignature": 5000
            }
          }
        }
      },
      "executable": false,
      "lamports": 1000000000,
      "owner": "11111111111111111111111111111111",
      "rentEpoch": 2
    }
  },
  "id": 1
}
```

### getBalance

Возвращает баланс аккаунта предоставленного Pubkey

#### Параметры:

- `<string>` - открытый ключ учетной записи для запроса в виде строки в кодировке base-58.
- `<объект>` - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment)

#### Результаты:

- `RpcResponse<u64>` - Объект RpcResponse JSON с полем `value`, установленным на баланс

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0", "id":1, "method":"getBalance", "params":["83astBRguLMdt2h5U1Tpdq5tjFoJ6noeGwaY3mDLVcri"]}
'
```

Результат:

```json
{"jsonrpc":"2.0","result":{"context":{"slot":1},"value":0},"id":1}
```

### getBlock

Возвращает идентификационную информацию и информацию о транзакции для подтвержденного блока в леджере.

#### Параметры:

- `<u64>` - слот, как целое число u64
- `<object>` - (необязательный) объект конфигурации, содержащий следующие необязательные поля:
  - (необязательно) `encoding: <string>` - кодировка для каждой возвращаемой транзакции, либо "json", "jsonParsed", "base58" (*slow*), "base64". Если параметр не указан, используется кодировка по умолчанию "json".
    Кодировка «jsonParsed» пытается использовать синтаксические анализаторы инструкций для конкретных программ, чтобы возвращать более удобочитаемые и явные данные в списке «transaction.message.instructions». Если запрашивается "jsonParsed", но синтаксический анализатор не может быть найден, инструкция возвращается к обычной кодировке JSON (поля "accounts", "data" и "programIdIndex").
  - (необязательно) `transactionDetails: <string>` - уровень детализации возвращаемой транзакции: "полный", "подписи" или "нет". Если параметр не указан, уровень детализации по умолчанию — «полный».
  - (необязательно) `rewards: bool` - заполнять ли массив `rewards`. Если параметр не указан, по умолчанию включены награды.
  - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment); «обработано» не поддерживается. Если параметр не указан, по умолчанию используется значение "finalized".

#### Результаты:

Поле результата будет объектом со следующими полями:

- `<null>` - если указанный блок не подтвержден
- `<object>` - если блок подтвержден, объект со следующими полями:
  - `blockhash: <string>` - хеш блока этого блока в виде строки в кодировке base-58
  - `previousBlockhash: <string>` - хеш блока родителя этого блока в виде строки в кодировке base-58; если родительский блок недоступен из-за очистки реестра, это поле вернет «11111111111111111111111111111111»
  - `parentSlot: <u64>` - индекс слота родителя этого блока
  - `transactions: <массив>` - присутствует, если запрашиваются "полные" детали транзакции; массив объектов JSON, содержащий:
    - `transaction: <object|[string,encoding]>` - объект Transaction либо в формате JSON, либо в закодированных двоичных данных, в зависимости от параметра кодирования
    - `meta: <object>` - объект метаданных состояния транзакции, содержащий `null` или:
      - `ошибка: <объект | null>` - Ошибка, если транзакция не удалась, null, если транзакция прошла успешно. [Определения TransactionError] (https://github.com/solana-labs/solana/blob/c0c60386544ec9a9ec7119229f37386d9f070523/sdk/src/transaction/error.rs#L13)
      - `fee: <u64>` - комиссия, взимаемая за эту транзакцию, в виде целого числа u64
      - `preBalances: <массив>` - массив балансов счетов u64 до обработки транзакции
      - `postBalances: <массив>` - массив балансов аккаунтов u64 после обработки транзакции
      - `innerInstructions: <array|null>` - Список внутренних инструкций или `null`, если запись внутренних инструкций не была включена во время этой транзакции
      - `preTokenBalances: <array|undefined>` - Список балансов токенов до того, как транзакция была обработана или пропущена, если запись баланса токенов еще не была включена во время этой транзакции.
      - `postTokenBalances: <array|undefined>` - Список балансов токенов после обработки транзакции или опущен, если запись баланса токенов еще не была включена во время этой транзакции
      - `logMessages: <array|null>` - массив строковых сообщений журнала или `null`, если запись сообщений журнала не была включена во время этой транзакции
      - УСТАРЕЛО: `статус: <объект>` - Статус транзакции
        - `"Ok": <null>` - Транзакция прошла успешно
        - `"Err": <ERR>` - Транзакция не удалась с TransactionError
  - `signatures: <массив>` - присутствует, если для деталей транзакции запрашиваются "подписи"; массив строк подписей, соответствующих порядку транзакций в блоке
  - `rewards: <массив>` - присутствует, если запрошены награды; массив объектов JSON, содержащий:
    — `pubkey: <string>` — открытый ключ в виде строки в кодировке base-58 учетной записи, получившей вознаграждение.
    - `lamports: <i64>`- количество наградных лампортов, зачисленных или списанных со счета, как i64
    - `postBalance:<u64>` - баланс аккаунта в лэмпортах после применения вознаграждения
    - `rewardType: <string|undefined>` - тип вознаграждения: "комиссия", "аренда", "голосование", "стейкинг"
    - `комиссия: <u8|undefined>` - комиссия за голосовой счет, когда вознаграждение было зачислено, присутствует только для вознаграждений за голосование и стекинг
  - `Время_блока: <i64 | null>` - предполагаемое время производства в виде временной метки Unix (в секундах с эпохи Unix). ноль, если недоступен
  - `высота блока: <u64 | null>` - количество блоков под этим блоком

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc": "2.0","id":1,"method":"getBlock","params":[430, {"encoding": "json","transactionDetails":"full","rewards":false}]}
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "blockHeight": 428,
    "blockTime": null,
    "blockhash": "3Eq21vXNB5s86c62bVuUfTeaMif1N2kUqRPBmGRJhyTA",
    "parentSlot": 429,
    "previousBlockhash": "mfcyqEXB3DnHXki6KjjmZck6YjmZLvpAByy2fj4nh6B",
    "transactions": [
      {
        "meta": {
          "err": null,
          "fee": 5000,
          "innerInstructions": [],
          "logMessages": [],
          "postBalances": [
            499998932500,
            26858640,
            1,
            1,
            1
          ],
          "postTokenBalances": [],
          "preBalances": [
            499998937500,
            26858640,
            1,
            1,
            1
          ],
          "preTokenBalances": [],
          "status": {
            "Ok": null
          }
        },
        "transaction": {
          "message": {
            "accountKeys": [
              "3UVYmECPPMZSCqWKfENfuoTv51fTDTWicX9xmBD2euKe",
              "AjozzgE83A3x1sHNUR64hfH7zaEBWeMaFuAN9kQgujrc",
              "SysvarS1otHashes111111111111111111111111111",
              "SysvarC1ock11111111111111111111111111111111",
              "Vote111111111111111111111111111111111111111"
            ],
            "header": {
              "numReadonlySignedAccounts": 0,
              "numReadonlyUnsignedAccounts": 3,
              "numRequiredSignatures": 1
            },
            "instructions": [
              {
                "accounts": [
                  1,
                  2,
                  3,
                  0
                ],
                "data": "37u9WtQpcm6ULa3WRQHmj49EPs4if7o9f1jSRVZpm2dvihR9C8jY4NqEwXUbLwx15HBSNcP1",
                "programIdIndex": 4
              }
            ],
            "recentBlockhash": "mfcyqEXB3DnHXki6KjjmZck6YjmZLvpAByy2fj4nh6B"
          },
          "signatures": [
            "2nBhEBYYvfaAe16UMNqRHre4YNSskvuYgx3M6E4JP1oDYvZEJHvoPzyUidNgNX5r9sTyN1J9UxtbCXy2rqYcuyuv"
          ]
        }
      }
    ]
  },
  "id": 1
}
```

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc": "2.0","id":1,"method":"getBlock","params":[430, "base64"]}
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "blockHeight": 428,
    "blockTime": null,
    "blockhash": "3Eq21vXNB5s86c62bVuUfTeaMif1N2kUqRPBmGRJhyTA",
    "parentSlot": 429,
    "previousBlockhash": "mfcyqEXB3DnHXki6KjjmZck6YjmZLvpAByy2fj4nh6B",
    "rewards": [],
    "transactions": [
      {
        "meta": {
          "err": null,
          "fee": 5000,
          "innerInstructions": null,
          "logMessages": null,
          "postBalances": [
            499998932500,
            26858640,
            1,
            1,
            1
          ],
          "postTokenBalances": [],
          "preBalances": [
            499998937500,
            26858640,
            1,
            1,
            1
          ],
          "preTokenBalances": [],
          "status": {
            "Ok": null
          }
        },
        "transaction": [
          "AVj7dxHlQ9IrvdYVIjuiRFs1jLaDMHixgrv+qtHBwz51L4/ImLZhszwiyEJDIp7xeBSpm/TX5B7mYzxa+fPOMw0BAAMFJMJVqLw+hJYheizSoYlLm53KzgT82cDVmazarqQKG2GQsLgiqktA+a+FDR4/7xnDX7rsusMwryYVUdixfz1B1Qan1RcZLwqvxvJl4/t3zHragsUp0L47E24tAFUgAAAABqfVFxjHdMkoVmOYaR1etoteuKObS21cc1VbIQAAAAAHYUgdNXR0u3xNdiTr072z2DVec9EQQ/wNo1OAAAAAAAtxOUhPBp2WSjUNJEgfvy70BbxI00fZyEPvFHNfxrtEAQQEAQIDADUCAAAAAQAAAAAAAACtAQAAAAAAAAdUE18R96XTJCe+YfRfUp6WP+YKCy/72ucOL8AoBFSpAA==",
          "base64"
        ]
      }
    ]
  },
  "id": 1
}
```

#### Структура сделки

Транзакции сильно отличаются от транзакций в других блокчейнах. Обязательно ознакомьтесь с [Анатомией транзакции](developing/programming-model/transactions/), чтобы узнать о транзакциях в Solana.

Структура JSON транзакции определяется следующим образом:

- `signatures: <array[string]>` – список подписей, закодированных по основанию 58, применяемых к транзакции. Список всегда имеет длину `message.header.numRequiredSignatures` и не пуст. Подпись с индексом `i` соответствует открытому ключу с индексом `i` в `message.account_keys`. Первый используется как [идентификатор транзакции](../../terminology.md#transaction-id).
- `message: <object>` — определяет содержание транзакции.
  - `accountKeys: <array[string]>` - Список открытых ключей в кодировке base-58, используемых транзакцией, в том числе инструкциями и для подписей. Первые открытые ключи message.header.numRequiredSignatures должны подписывать транзакцию.
  - `header: <object>` — подробно описывает типы учетных записей и подписи, необходимые для транзакции.
    - `numRequiredSignatures: <число>` – общее количество подписей, необходимых для подтверждения транзакции. Подписи должны совпадать с первым значением numRequiredSignatures сообщения message.account_keys.
    - `numReadonlySignedAccounts: <число>` – последние `numReadonlySignedAccounts` подписанных ключей являются учетными записями только для чтения. Программы могут обрабатывать несколько транзакций, которые загружают учетные записи только для чтения в рамках одной записи PoH, но им не разрешено кредитовать или дебетовать лампы или изменять данные учетной записи. Транзакции, нацеленные на одну и ту же учетную запись для чтения и записи, оцениваются последовательно.
    - `numReadonlyUnsignedAccounts: <число>` – последние `numReadonlyUnsignedAccounts` неподписанных ключей являются учетными записями только для чтения.
  - `recentBlockhash: <string>` – хэш последнего блока в реестре в кодировке base-58, используемый для предотвращения дублирования транзакций и увеличения срока действия транзакций.
  - `instructions: <array[object]>` — список программных инструкций, которые будут выполняться последовательно и фиксируются в одной атомарной транзакции, если все будут успешными.
    - `programIdIndex: <number>` - индекс в массиве `message.accountKeys`, указывающий учетную запись программы, которая выполняет эту инструкцию.
    - `accounts: <array[number]>` – Список упорядоченных индексов в массиве `message.accountKeys`, указывающий, какие счета передать программе.
    - `data: <string>` - Входные данные программы, закодированные в строке base-58.

#### Структура внутренних инструкций

Среда выполнения Solana записывает кросс-программные инструкции, которые вызываются во время обработки транзакции, и делает их доступными для большей прозрачности того, что было выполнено в цепочке для каждой инструкции транзакции. Вызванные инструкции группируются по инструкции исходной транзакции и перечислены в порядке обработки.

Структура внутренних инструкций JSON определяется как список объектов в следующей структуре:

- `index: number` - Индекс инструкции по транзакции, из которой возникла внутренняя инструкция(и)
- `instructions: <array[object]>` — упорядоченный список внутренних инструкций программы, которые были вызваны во время одной инструкции транзакции.
  - `programIdIndex: <number>` - индекс в массиве `message.accountKeys`, указывающий учетную запись программы, которая выполняет эту инструкцию.
  - `accounts: <array[number]>` – Список упорядоченных индексов в массиве `message.accountKeys`, указывающий, какие счета передать программе.
  - `data: <string>` - Входные данные программы, закодированные в строке base-58.

#### Структура баланса токенов

Структура JSON балансов токенов определяется как список объектов в следующей структуре:

- `accountIndex:<число>` - Индекс счета, на котором предоставляется баланс токена.
- `mint: <string>` - Pubkey монетного двора токена.
- `владелец: <строка | undefined>` - Pubkey владельца баланса токена.
- `uiTokenAmount: <объект>` -
  - `amount: <string>` - необработанное количество токенов в виде строки, без учета десятичных знаков.
  - `decimals: <number>` - количество десятичных знаков, настроенное для чеканки токена.
  - `uiAmount: <число | null>` — сумма токена в виде числа с плавающей запятой с учетом десятичных знаков. **УСТАРЕЛО**
  - `uiAmountString: <string>` - Сумма токена в виде строки с учетом десятичных знаков.

### получитьBlockHeight

Возвращает текущую высоту блока узла

#### Параметры:

- `<объект>` - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment)

#### Результаты:

- `<u64>` - Текущая высота блока

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"getBlockHeight"}
'
```

Результат:

```json
{"jsonrpc":"2.0","result":1233,"id":1}
```

### getBlockProduction

Возвращает недавнюю информацию о производстве блоков из текущей или предыдущей эпохи.

#### Параметры:

- `<object>` - (необязательный) объект конфигурации, содержащий следующие необязательные поля:
  - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment)
  - (необязательный) `range: <object>` - диапазон слотов для возврата производства блока. Если параметр не указан, по умолчанию используется текущая эпоха.
    - `firstSlot: <u64>` - первый слот для возврата информации о производстве блока (включительно)
    - (необязательно) `lastSlot: <u64>` - последний слот для возврата информации о производстве блока (включительно). Если параметр не указан, по умолчанию используется самый высокий слот
  - (необязательно) `identity: <string>` – возвращать результаты только для этого удостоверения валидатора (в кодировке base-58)

#### Результаты:

Результатом будет объект RpcResponse JSON со значением, равным:

- `<объект>`
  - `byIdentity: <object>` - словарь идентификаторов валидатора,
    как строки в кодировке base-58. Значение представляет собой массив из двух элементов, содержащий
    количество слотов лидеров и количество произведенных блоков.
  - `range: <object>` - диапазон слотов для производства блоков
    - `firstSlot: <u64>` - первый слот информации о производстве блока (включительно)
    - `lastSlot: <u64>` - последний слот информации о производстве блока (включительно)

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"getBlockProduction"}
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "context": {
      "slot": 9887
    },
    "value": {
      "byIdentity": {
        "85iYT5RuzRTDgjyRa3cP8SYhM2j21fj7NhfJ3peu1DPr": [
          9888,
          9886
        ]
      },
      "range": {
        "firstSlot": 0,
        "lastSlot": 9887,
      }
    }
  },
  "id": 1
}
```

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {
    "jsonrpc": "2.0",
    "id": 1,
    "method": "getBlockProduction",
    "params": [
      {
        "identity": "85iYT5RuzRTDgjyRa3cP8SYhM2j21fj7NhfJ3peu1DPr",
        "range": {
          "firstSlot": 40,
          "lastSlot": 50
        }
      }
    ]
  }
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "context": {
      "slot": 10102
    },
    "value": {
      "byIdentity": {
        "85iYT5RuzRTDgjyRa3cP8SYhM2j21fj7NhfJ3peu1DPr": [
          11,
          11
        ]
      },
      "range": {
        "firstSlot": 50,
        "lastSlot": 40
      }
    }
  },
  "id": 1
}
```

### getBlockCommitment

Возвращает обязательство для определенного блока

#### Параметры:

- `<u64>` - блок, определяемый слотом

#### Результаты:

Поле результата будет объектом JSON, содержащим:

- "обязательство" - обязательство, включающее:
  - `<null>` - Неизвестный блок
  - `<array>` - обязательство, массив целых чисел u64, регистрирующий количество долей кластера в lamports, которые проголосовали за блок на каждой глубине от 0 до `MAX_LOCKOUT_HISTORY` + 1
- `totalStake` - общая активная ставка в лэмпортах на текущую эпоху

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"getBlockCommitment","params":[5]}
'
```

Результат:

```json
{
  "jsonrpc":"2.0",
  "result":{
    "commitment":[0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,10,32],
    "totalStake": 42
  },
  "id":1
}
```

### getBlocks

Возвращает список подтвержденных блоков между двумя слотами

#### Параметры:

- `<u64>` - начальный_слот, как целое число u64
- `<u64>` - (необязательно) end_slot, как целое число u64
- (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment); «обработано» не поддерживается. Если параметр не указан, по умолчанию используется значение "finalized".

#### Результаты:

Поле результата будет массивом целых чисел u64 со списком подтвержденных блоков.
между `start_slot` и либо `end_slot`, если он указан, либо последним подтвержденным блоком,
включительно. Максимально допустимый диапазон составляет 500 000 слотов.

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc": "2.0","id":1,"method":"getBlocks","params":[5, 10]}
'
```

Результат:

```json
{"jsonrpc":"2.0","result":[5,6,7,8,9,10],"id":1}
```

### getBlocksWithLimit

Возвращает список подтвержденных блоков, начиная с данного слота.

#### Параметры:

- `<u64>` - начальный_слот, как целое число u64
- `<u64>` - ограничение, как целое число u64
- (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment); «обработано» не поддерживается. Если параметр не указан, по умолчанию используется значение "finalized".

#### Результаты:

Поле результата будет массивом целых чисел u64 со списком подтвержденных блоков.
начиная с `start_slot` до `limit` блоков включительно.

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc": "2.0","id":1,"method":"getBlocksWithLimit","params":[5, 3]}
'
```

Результат:

```json
{"jsonrpc":"2.0","result":[5,6,7],"id":1}
```

### getBlockTime

Возвращает расчетное время производства блока.

Каждый валидатор сообщает свое время UTC в бухгалтерскую книгу через регулярные промежутки времени.
периодическое добавление метки времени к голосованию за определенный блок. запрошенный
время блока рассчитывается на основе средневзвешенного значения временных меток голосования.
в наборе последних блоков, записанных в леджере.

#### Параметры:

- `<u64>` - блок, определяемый слотом

#### Результаты:

- `<i64>` - предполагаемое время производства, как метка времени Unix (секунды с эпохи Unix)
- `<null>` - временная метка недоступна для этого блока

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"getBlockTime","params":[5]}
'
```

Результат:

```json
{"jsonrpc":"2.0","result":1574721591,"id":1}
```

### getClusterNodes

Возвращает информацию обо всех узлах, участвующих в кластере

#### Параметры:

Никто

#### Результаты:

Поле результата будет представлять собой массив объектов JSON, каждый со следующими подполями:

- `pubkey: <string>` - открытый ключ узла, как строка в кодировке base-58.
- `сплетни: <string | null>` - Сетевой адрес Gossip для узла
- `tpu: <строка | null>` - сетевой адрес TPU для узла
- `rpc: <строка | null>` — сетевой адрес JSON RPC для узла или `null`, если служба JSON RPC не включена.
- `версия: <строка | null>` — версия программного обеспечения узла или `null`, если информация о версии недоступна.
- `featureSet: <u32 | null >` — уникальный идентификатор набора функций узла.
- `shredVersion: <u16 | null>` — Версия уничтожения, на использование которой был настроен узел.

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0", "id":1, "method":"getClusterNodes"}
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": [
    {
      "gossip": "10.239.6.48:8001",
      "pubkey": "9QzsJf7LPLj8GkXbYT3LFDKqsj2hHG7TA3xinJHu8epQ",
      "rpc": "10.239.6.48:8899",
      "tpu": "10.239.6.48:8856",
      "version": "1.0.0 c375ce1f"
    }
  ],
  "id": 1
}
```

### getEpochInfo

Возвращает информацию о текущей эпохе

#### Параметры:

- `<объект>` - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment)

#### Результаты:

Поле результата будет объектом со следующими полями:

- `absoluteSlot: <u64>`, текущий слот
- `blockHeight: <u64>`, текущая высота блока
- `эпоха: <u64>`, текущая эпоха
- `slotIndex: <u64>`, текущий слот относительно начала текущей эпохи
- `slotsInEpoch: <u64>`, количество слотов в этой эпохе
- `transactionCount: <u64 | null>`, общее количество транзакций, обработанных без ошибок с момента создания

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"getEpochInfo"}
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "absoluteSlot": 166598,
    "blockHeight": 166500,
    "epoch": 27,
    "slotIndex": 2790,
    "slotsInEpoch": 8192,
    "transactionCount": 22661093
  },
  "id": 1
}
```

### getEpochSchedule

Возвращает информацию о расписании эпохи из конфигурации генезиса этого кластера.

#### Параметры:

Никто

#### Результаты:

Поле результата будет объектом со следующими полями:

- `slotsPerEpoch: <u64>`, максимальное количество слотов в каждой эпохе
- `leaderScheduleSlotOffset: <u64>`, количество слотов до начала эпохи для расчета графика лидера для этой эпохи
- `warmup: <bool>`, начинаются ли эпохи короткими и растут
- `firstNormalEpoch: <u64>`, первая эпоха нормальной длины, log2(slotsPerEpoch) - log2(MINIMUM_SLOTS_PER_EPOCH)
- `firstNormalSlot: <u64>`, MINIMUM_SLOTS_PER_EPOCH \* (2.pow(firstNormalEpoch) - 1)

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"getEpochSchedule"}
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "firstNormalEpoch": 8,
    "firstNormalSlot": 8160,
    "leaderScheduleSlotOffset": 8192,
    "slotsPerEpoch": 8192,
    "warmup": true
  },
  "id": 1
}
```

### getFeeForMessage

**НОВОЕ: этот метод доступен только в solana-core v1.9 или новее. Пожалуйста, используйте
[getFees](jsonrpc-api.md#getfees) для solana-core v1.8**

Получите плату, которую сеть будет взимать за конкретное сообщение

#### Параметры:

- `message: <string>` - Сообщение в кодировке Base-64
- `<object>` - (необязательно) [Commitment](jsonrpc-api.md#configuring-state-commitment) (используется для получения хэша блока)

#### Результаты:

- `<u64 | null>` - Плата, соответствующая сообщению в указанном хэше блока

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
{
  "id":1,
  "jsonrpc":"2.0",
  "method":"getFeeForMessage",
  "params":[
    "AQABAgIAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAEBAQAA",
    {
      "commitment":"processed"
    }
  ]
}
'
```

Результат:

```json
{"jsonrpc":"2.0","result":{"context":{"slot":5068},"value":5000},"id":1}
```

### getFirstAvailableBlock

Возвращает слот самого низкого подтвержденного блока, который не был удален из леджера.

#### Параметры:

Никто

#### Результаты:

- `<u64>` - Слот

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"getFirstAvailableBlock"}
'
```

Результат:

```json
{"jsonrpc":"2.0","result":250000,"id":1}
```

### getGenesisHash

Возвращает исходный хеш

#### Параметры:

Никто

#### Результаты:

- `<string>` - хэш в виде строки в кодировке base-58.

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"getGenesisHash"}
'
```

Результат:

```json
{"jsonrpc":"2.0","result":"GH7ome3EiwEr7tu9JuTh2dpYWBJK3z69Xm1ZE3MEE6JC","id":1}
```

### getHealth

Возвращает текущее состояние узла.

Если для
solana-validator, возвращается "ok", когда узел находится в пределах
`HEALTH_CHECK_SLOT_DISTANCE` слотов самого высокого известного валидатора, в противном случае
возвращается ошибка. "ok" всегда возвращается, если нет известных валидаторов.
при условии.

#### Параметры:

Никто

#### Результаты:

Если узел исправен: "ok"
Если узел неисправен, возвращается ответ об ошибке JSON RPC. Специфика
ответа об ошибке являются **НЕСТАБИЛЬНЫМИ** и могут измениться в будущем

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"getHealth"}
'
```

Здоровый результат:

```json
{"jsonrpc":"2.0","result": "ok","id":1}
```

Нездоровый результат (общий):

```json
{
  "jsonrpc": "2.0",
  "error": {
    "code": -32005,
    "message": "Node is unhealthy",
    "data": {}
  },
  "id": 1
}
```

Нездоровый результат (если доступна дополнительная информация)

```json
{
  "jsonrpc": "2.0",
  "error": {
    "code": -32005,
    "message": "Node is behind by 42 slots",
    "data": {
      "numSlotsBehind": 42
    }
  },
  "id": 1
}
```

### getHighestSnapshotSlot

**НОВОЕ: этот метод доступен только в solana-core v1.9 или новее. Пожалуйста, используйте [getSnapshotSlot](jsonrpc-api.md#getsnapshotslot) для solana-core v1.8**

Возвращает информацию о самом верхнем слоте, для которого у узла есть снимки.

Это найдет самый высокий полный слот моментального снимка и самый высокий добавочный
слот моментального снимка _на основе_ полного слота моментального снимка, если он есть.

#### Параметры:

Никто

#### Результаты:

- `<объект>`
  - `full: <u64>` - самый высокий слот для полных снимков.
  - `инкрементный: <u64 | undefined>` — Наибольший слот инкрементного снимка _основанный на_ `full`

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1,"method":"getHighestSnapshotSlot"}
'
```

Результат:

```json
{"jsonrpc":"2.0","result":{"full":100,"incremental":110},"id":1}
```

Результат, когда узел не имеет моментального снимка:

```json
{"jsonrpc":"2.0","error":{"code":-32008,"message":"No snapshot"},"id":1}
```

### getIdentity

Возвращает идентификатор pubkey для текущего узла

#### Параметры:

Никто

#### Результаты:

Поле результата будет объектом JSON со следующими полями:

- `identity`, идентификатор pubkey текущего узла \(как строка в кодировке base-58\)

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"getIdentity"}
'
```

Результат:

```json
{"jsonrpc":"2.0","result":{"identity": "2r1F4iWqVcb8M1DbAjQuFpebkQHY9hcVU4WuW2DJBppN"},"id":1}
```

### getInflationGovernor

Возвращает текущий регулятор инфляции

#### Параметры:

- `<объект>` - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment)

#### Результаты:

Поле результата будет объектом JSON со следующими полями:

- `initial: <f64>`, начальный процент инфляции с момента 0
- `terminal: <f64>`, терминальный процент инфляции
- `taper: <f64>`, ставка в год, при которой снижается инфляция.
    Снижение скорости производится с использованием целевого времени слота в конфигурации генезиса.
- `foundation: <f64>`, процент от общей инфляции, отнесенный к фонду
- `foundationTerm: <f64>`, продолжительность инфляции фонда в годах

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"getInflationGovernor"}
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "foundation": 0.05,
    "foundationTerm": 7,
    "initial": 0.15,
    "taper": 0.15,
    "terminal": 0.015
  },
  "id": 1
}
```

### getInflationRate

Возвращает конкретные значения инфляции для текущей эпохи

#### Параметры:

Никто

#### Результаты:

Поле результата будет объектом JSON со следующими полями:

- `total: <f64>`, общая инфляция
- `валидатор: <f64>`, инфляция распределяется между валидаторами
- `foundation: <f64>`, инфляция, отнесенная к фонду
- `epoch: <u64>`, эпоха, для которой эти значения действительны

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"getInflationRate"}
'
```

Результат:

```json
{"jsonrpc":"2.0","result":{"epoch":100,"foundation":0.001,"total":0.149,"validator":0.148},"id":1}
```

### getInflationReward

Возвращает вознаграждение за инфляцию для списка адресов за эпоху.

#### Параметры:

- `<array>` - Массив адресов для запроса в виде строк в кодировке base-58.

- `<object>` - (необязательный) объект конфигурации, содержащий следующие необязательные поля:
  
  - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment)
  - (необязательно) `epoch: <u64>` - эпоха, за которую происходит вознаграждение. Если опущено, будет использована предыдущая эпоха

#### Результаты

Поле результата будет представлять собой массив JSON со следующими полями:

- `epoch: <u64>`, эпоха, за которую произошло вознаграждение
- `efficientSlot: <u64>`, слот, в котором действуют награды.
- `amount: <u64>`, сумма вознаграждения в лэмпортах
- `postBalance:<u64>`, разместить баланс аккаунта в лэмпортах
- `commission: <u8|undefined>` - комиссия голосового счета при зачислении вознаграждения

#### Пример

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {
    "jsonrpc": "2.0",
    "id": 1,
    "method": "getInflationReward",
    "params": [
       ["6dmNQ5jwLeLk5REvio1JcMshcbvkYMwy26sJ8pbkvStu", "BGsqMegLpV6n6Ve146sSX2dTjUMj3M92HnU8BbNRMhF2"], {"epoch": 2}
    ]
  }
'
```

Ответ:

```json
  {
    "jsonrpc": "2.0",
    "result": [
        {
            "amount": 2500,
            "effectiveSlot": 224,
            "epoch": 2,
            "postBalance": 499999442500
        },
        null
    ],
    "id": 1
  }
```

### getLargestAccounts

Возвращает 20 крупнейших аккаунтов по балансу лампорта (результаты могут кэшироваться до двух часов)

#### Параметры:

- `<object>` - (необязательный) объект конфигурации, содержащий следующие необязательные поля:
  - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment)
  - (необязательно) `filter: <string>` - фильтровать результаты по типу учетной записи; в настоящее время поддерживается: `циркулирующий|нециркулирующий`

#### Результаты:

Результатом будет объект RpcResponse JSON со значением, равным массиву:

- `<object>` - иначе объект JSON, содержащий:
  - `address: <string>`, адрес учетной записи в кодировке base-58
  - `lamports: <u64>`, количество лампортов в аккаунте, как u64

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"getLargestAccounts"}
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "context": {
      "slot": 54
    },
    "value": [
      {
        "lamports": 999974,
        "address": "99P8ZgtJYe1buSK8JXkvpLh8xPsCFuLYhz9hQFNw93WJ"
      },
      {
        "lamports": 42,
        "address": "uPwWLo16MVehpyWqsLkK3Ka8nLowWvAHbBChqv2FZeL"
      },
      {
        "lamports": 42,
        "address": "aYJCgU7REfu3XF8b3QhkqgqQvLizx8zxuLBHA25PzDS"
      },
      {
        "lamports": 42,
        "address": "CTvHVtQ4gd4gUcw3bdVgZJJqApXE9nCbbbP4VTS5wE1D"
      },
      {
        "lamports": 20,
        "address": "4fq3xJ6kfrh9RkJQsmVd5gNMvJbuSHfErywvEjNQDPxu"
      },
      {
        "lamports": 4,
        "address": "AXJADheGVp9cruP8WYu46oNkRbeASngN5fPCMVGQqNHa"
      },
      {
        "lamports": 2,
        "address": "8NT8yS6LiwNprgW4yM1jPPow7CwRUotddBVkrkWgYp24"
      },
      {
        "lamports": 1,
        "address": "SysvarEpochSchedu1e111111111111111111111111"
      },
      {
        "lamports": 1,
        "address": "11111111111111111111111111111111"
      },
      {
        "lamports": 1,
        "address": "Stake11111111111111111111111111111111111111"
      },
      {
        "lamports": 1,
        "address": "SysvarC1ock11111111111111111111111111111111"
      },
      {
        "lamports": 1,
        "address": "StakeConfig11111111111111111111111111111111"
      },
      {
        "lamports": 1,
        "address": "SysvarRent111111111111111111111111111111111"
      },
      {
        "lamports": 1,
        "address": "Config1111111111111111111111111111111111111"
      },
      {
        "lamports": 1,
        "address": "SysvarStakeHistory1111111111111111111111111"
      },
      {
        "lamports": 1,
        "address": "SysvarRecentB1ockHashes11111111111111111111"
      },
      {
        "lamports": 1,
        "address": "SysvarFees111111111111111111111111111111111"
      },
      {
        "lamports": 1,
        "address": "Vote111111111111111111111111111111111111111"
      }
    ]
  },
  "id": 1
}
```

### getLatestBlockhash

**НОВОЕ: этот метод доступен только в solana-core v1.9 или новее. Пожалуйста, используйте [getRecentBlockhash](jsonrpc-api.md#getrecentblockhash) для solana-core v1.8**

Возвращает последний блокхэш

#### Параметры:

- `<object>` - (необязательно) [Commitment](jsonrpc-api.md#configuring-state-commitment) (используется для получения хэша блока)

#### Результаты:

- `RpcResponse<object>` — объект JSON RpcResponse с полем `value`, установленным на объект JSON, включая:
- `blockhash: <string>` - хэш в виде строки в кодировке base-58.
- `lastValidBlockHeight: u64` - Слот

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {
    "id":1,
    "jsonrpc":"2.0",
    "method":"getLatestBlockhash",
    "params":[
      {
        "commitment":"processed"
      }
    ]
  }
'
```

Результат:

```json
{
  "jsonrpc":"2.0",
  "result":{
    "context":{
      "slot":2792
    },
    "value":{
      "blockhash":"EkSnNWid2cvwEVnVx9aBqawnmiCNiDgp3gUdkDPTKN1N",
      "lastValidBlockHeight":3090
    }
  },
  "id":1
}
```

### getLeaderSchedule

Возвращает расписание лидера для эпохи

#### Параметры:

- `<u64>` - (необязательно) Получить расписание лидера для эпохи, соответствующей предоставленному слоту.
  
  ```
        Если не указано, извлекается расписание лидера для текущей эпохи.
  ```

- `<object>` - (необязательный) Объект конфигурации, содержащий следующее поле:
  
  - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment)
  - (необязательно) `identity: <string>` – возвращать результаты только для этого удостоверения валидатора (в кодировке base-58)

#### Результаты:

- `<null>` - если запрошенная эпоха не найдена
- `<object>` - в противном случае поле результата будет словарем идентификаторов валидаторов,
   как строки, закодированные с основанием 58, и их соответствующие индексы слота лидера как значения
   (индексы относятся к первому слоту запрошенной эпохи)

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"getLeaderSchedule"}
'
```

Результат:

```json
{
  "jsonrpc":"2.0",
  "result":{
    "4Qkev8aNZcqFNSRhQzwyLMFSsi94jHqE8WNVTJzTP99F":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63]
  },
  "id":1
}
```

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {
    "jsonrpc": "2.0",
    "id": 1,
    "method": "getLeaderSchedule",
    "params": [
      null,
      {
        "identity": "4Qkev8aNZcqFNSRhQzwyLMFSsi94jHqE8WNVTJzTP99F"
      }
    ]
  }
'
```

Результат:

```json
{
  "jsonrpc":"2.0",
  "result":{
    "4Qkev8aNZcqFNSRhQzwyLMFSsi94jHqE8WNVTJzTP99F":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63]
  },
  "id":1
}
```

### getMaxRetransmitSlot

Получите максимальный слот, видимый на этапе повторной передачи.

#### Результаты:

- `<u64>` - Слот

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"getMaxRetransmitSlot"}
'
```

Результат:

```json
{"jsonrpc":"2.0","result":1234,"id":1}
```

### getMaxShredInsertSlot

Получите максимальный слот, видимый после вставки измельчения.

#### Результаты:

- `<u64>` - Слот

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"getMaxShredInsertSlot"}
'
```

Результат:

```json
{"jsonrpc":"2.0","result":1234,"id":1}
```

### getMinimumBalanceForRentExemption

Возвращает минимальный баланс, необходимый для освобождения счета от арендной платы.

#### Параметры:

- `<usize>` - длина данных учетной записи
- `<объект>` - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment)

#### Результаты:

- `<u64>` - минимальные лэмпорты, необходимые для аккаунта

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0", "id":1, "method":"getMinimumBalanceForRentExemption", "params":[50]}
'
```

Результат:

```json
{"jsonrpc":"2.0","result":500,"id":1}
```

### getMultipleAccounts

Возвращает информацию об учетной записи для списка открытых ключей.

#### Параметры:

- `<array>` - Массив открытых ключей для запроса в виде строк в кодировке base-58.
- `<object>` - (необязательный) объект конфигурации, содержащий следующие необязательные поля:
  - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment)
  - `encoding: <string>` - кодировка данных Пользователя, либо "base58" (*slow*), "base64", "base64+zstd", либо "jsonParsed".
    «base58» ограничен данными учетной записи размером менее 129 байт.
    «base64» вернет данные в кодировке base64 для данных учетной записи любого размера.
    «base64+zstd» сжимает данные учетной записи с помощью [Zstandard](https://facebook.github.io/zstd/) и кодирует результат в base64.
    Кодирование «jsonParsed» пытается использовать анализаторы состояния для конкретных программ, чтобы возвращать более удобочитаемые и явные данные о состоянии учетной записи. Если запрашивается "jsonParsed", но синтаксический анализатор не может быть найден, поле возвращается к кодировке "base64", обнаруживаемой, когда поле `data` имеет тип `<string>`.
  - (необязательно) `dataSlice: <object>` – ограничить возвращаемые данные учетной записи, используя предоставленные поля `offset: <usize>` и `length: <usize>`; доступно только для кодировок "base58", "base64" или "base64+zstd".

#### Результаты:

Результатом будет объект RpcResponse JSON со значением, равным:

Массив из:

- `<null>` - если учетная запись в этом публичном ключе не существует
- `<object>` - иначе объект JSON, содержащий:
  - `lamports: <u64>`, количество ламп, назначенных этой учетной записи, как u64
  - `owner: <string>`, Pubkey в кодировке base-58 программы, которой назначена эта учетная запись.
  - `data: <[string, encoding]|object>`, данные, связанные с учетной записью, либо в виде закодированных двоичных данных, либо в формате JSON `{<program>: <state>}`, в зависимости от параметра кодирования
  - `executable: <bool>`, логическое значение, указывающее, содержит ли учетная запись программу \ (и строго только для чтения \)
  - `rentEpoch: <u64>`, эпоха, в которую эта учетная запись будет в следующий раз платить арендную плату, как u64

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {
    "jsonrpc": "2.0",
    "id": 1,
    "method": "getMultipleAccounts",
    "params": [
      [
        "vines1vzrYbzLMRdu58ou5XTby4qAqVRLmqo36NKPTg",
        "4fYNw3dojWmQ4dXtSGE9epjRGy9pFSx62YypT7avPYvA"
      ],
      {
        "dataSlice": {
          "offset": 0,
          "length": 0
        }
      }
    ]
  }
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "context": {
      "slot": 1
    },
    "value": [
      {
        "data": [
          "AAAAAAEAAAACtzNsyJrW0g==",
          "base64"
        ],
        "executable": false,
        "lamports": 1000000000,
        "owner": "11111111111111111111111111111111",
        "rentEpoch": 2
      },
      {
        "data": [
          "",
          "base64"
        ],
        "executable": false,
        "lamports": 5000000000,
        "owner": "11111111111111111111111111111111",
        "rentEpoch": 2
      }
    ]
  },
  "id": 1
}
```

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {
    "jsonrpc": "2.0",
    "id": 1,
    "method": "getMultipleAccounts",
    "params": [
      [
        "vines1vzrYbzLMRdu58ou5XTby4qAqVRLmqo36NKPTg",
        "4fYNw3dojWmQ4dXtSGE9epjRGy9pFSx62YypT7avPYvA"
      ],
      {
        "encoding": "base58"
      }
    ]
  }
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "context": {
      "slot": 1
    },
    "value": [
      {
        "data": [
          "11116bv5nS2h3y12kD1yUKeMZvGcKLSjQgX6BeV7u1FrjeJcKfsHRTPuR3oZ1EioKtYGiYxpxMG5vpbZLsbcBYBEmZZcMKaSoGx9JZeAuWf",
          "base58"
        ],
        "executable": false,
        "lamports": 1000000000,
        "owner": "11111111111111111111111111111111",
        "rentEpoch": 2
      },
      {
        "data": [
          "",
          "base58"
        ],
        "executable": false,
        "lamports": 5000000000,
        "owner": "11111111111111111111111111111111",
        "rentEpoch": 2
      }
    ]
  },
  "id": 1
}
```

### getProgramAccounts

Возвращает все учетные записи, принадлежащие предоставленной программе Pubkey.

#### Параметры:

- `<string>` - Пубключ программы в виде строки в кодировке base-58.

- `<object>` - (необязательный) объект конфигурации, содержащий следующие необязательные поля:
  
  - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment)
  
  - `encoding: <string>` - кодировка данных Пользователя, либо "base58" (*slow*), "base64", "base64+zstd", либо "jsonParsed".
    «base58» ограничен данными учетной записи размером менее 129 байт.
    «base64» вернет данные в кодировке base64 для данных учетной записи любого размера.
    «base64+zstd» сжимает данные учетной записи с помощью [Zstandard](https://facebook.github.io/zstd/) и кодирует результат в base64.
    Кодирование «jsonParsed» пытается использовать анализаторы состояния для конкретных программ, чтобы возвращать более удобочитаемые и явные данные о состоянии учетной записи. Если запрашивается "jsonParsed", но синтаксический анализатор не может быть найден, поле возвращается к кодировке "base64", обнаруживаемой, когда поле `data` имеет тип `<string>`.
  
  - (необязательно) `dataSlice: <object>` – ограничить возвращаемые данные учетной записи, используя предоставленные поля `offset: <usize>` и `length: <usize>`; доступно только для кодировок "base58", "base64" или "base64+zstd".
  
  - (необязательно) `filters: <массив>` - фильтровать результаты, используя различные [объекты фильтра] (jsonrpc-api.md#filters); учетная запись должна соответствовать всем критериям фильтрации, чтобы быть включенной в результаты
  
  - (необязательно) `withContext: bool` – обернуть результат в объект JSON RpcResponse.
    
    ##### Фильтры:

- `memcmp: <объект>` - сравнивает предоставленную серию байтов с данными учетной записи программы по определенному смещению. Поля:
  
  - `offset: <usize>` - смещение в данные учетной записи программы для начала сравнения
  - `bytes: <string>` - данные для сопоставления в виде строки в кодировке base-58 и ограничены менее чем 129 байтами.

- `dataSize: <u64>` - сравнивает длину данных учетной записи программы с предоставленным размером данных

#### Результаты:

По умолчанию поле результата будет массивом объектов JSON. Если установлен флаг `withContext`, массив будет заключен в объект RpcResponse JSON.

Массив будет содержать:

- `pubkey: <string>` - Pubkey учетной записи в виде строки в кодировке base-58.
- `account: <object>` – объект JSON со следующими подполями:
  - `lamports: <u64>`, количество ламп, назначенных этой учетной записи, как u64
  - `owner: <string>`, Pubkey в кодировке base-58 программы, которой назначена эта учетная запись.
  - `data: <[string,encoding]|object>`, данные, связанные с учетной записью, либо в виде закодированных двоичных данных, либо в формате JSON `{<program>: <state>}`, в зависимости от параметра кодирования
  - `executable: <bool>`, логическое значение, указывающее, содержит ли учетная запись программу \ (и строго только для чтения \)
  - `rentEpoch: <u64>`, эпоха, в которую эта учетная запись будет в следующий раз платить арендную плату, как u64

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0", "id":1, "method":"getProgramAccounts", "params":["4Nd1mBQtrMJVYVfKf2PJy9NZUZdTAsp7D4xWLs4gDB4T"]}
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": [
    {
      "account": {
        "data": "2R9jLfiAQ9bgdcw6h8s44439",
        "executable": false,
        "lamports": 15298080,
        "owner": "4Nd1mBQtrMJVYVfKf2PJy9NZUZdTAsp7D4xWLs4gDB4T",
        "rentEpoch": 28
      },
      "pubkey": "CxELquR1gPP8wHe33gZ4QxqGB3sZ9RSwsJ2KshVewkFY"
    }
  ],
  "id": 1
}
```

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {
    "jsonrpc": "2.0",
    "id": 1,
    "method": "getProgramAccounts",
    "params": [
      "4Nd1mBQtrMJVYVfKf2PJy9NZUZdTAsp7D4xWLs4gDB4T",
      {
        "filters": [
          {
            "dataSize": 17
          },
          {
            "memcmp": {
              "offset": 4,
              "bytes": "3Mc6vR"
            }
          }
        ]
      }
    ]
  }
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": [
    {
      "account": {
        "data": "2R9jLfiAQ9bgdcw6h8s44439",
        "executable": false,
        "lamports": 15298080,
        "owner": "4Nd1mBQtrMJVYVfKf2PJy9NZUZdTAsp7D4xWLs4gDB4T",
        "rentEpoch": 28
      },
      "pubkey": "CxELquR1gPP8wHe33gZ4QxqGB3sZ9RSwsJ2KshVewkFY"
    }
  ],
  "id": 1
}
```

### getRecentPerformanceSamples

Возвращает список последних примеров производительности в обратном порядке слотов. Образцы производительности берутся каждые 60 секунд и
включают количество транзакций и слотов, которые происходят в данном временном окне.

#### Параметры:

- `limit: <usize>` - (необязательно) количество возвращаемых выборок (максимум 720)

#### Результаты:

Массив из:

- `RpcPerfSample<объект>`
  - `slot: <u64>` - Слот, в котором была взята выборка
  - `numTransactions: <u64>` - количество транзакций в выборке
  - `numSlots: <u64>` - Количество слотов в выборке
  - `samplePeriodSecs: <u16>` - Количество секунд в окне выборки

#### Пример:

Запрос:

```bash
// Request
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0", "id":1, "method":"getRecentPerformanceSamples", "params": [4]}
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": [
    {
      "numSlots": 126,
      "numTransactions": 126,
      "samplePeriodSecs": 60,
      "slot": 348125
    },
    {
      "numSlots": 126,
      "numTransactions": 126,
      "samplePeriodSecs": 60,
      "slot": 347999
    },
    {
      "numSlots": 125,
      "numTransactions": 125,
      "samplePeriodSecs": 60,
      "slot": 347873
    },
    {
      "numSlots": 125,
      "numTransactions": 125,
      "samplePeriodSecs": 60,
      "slot": 347748
    }
  ],
  "id": 1
}
```

### getSignaturesForAddress

Возвращает подписи для подтвержденных транзакций, которые включают данный адрес в свой список `accountKeys`. Возвращает подписи назад во времени от предоставленной подписи или самого последнего подтвержденного блока

#### Параметры:

- `<string>` - адрес аккаунта в виде строки в кодировке base-58

- `<object>` - (необязательный) Объект конфигурации, содержащий следующие поля:
  
  - `limit: <number>` - (необязательно) максимальное количество возвращаемых подписей транзакций (от 1 до 1000, по умолчанию: 1000).
  
  - `before: <string>` - (необязательно) начать поиск в обратном направлении от этой подписи транзакции.
    
    ```
    Если не указано, поиск начинается с верхней части максимального подтвержденного блока.
    ```
  
  - `until: <string>` - (необязательно) поиск до этой подписи транзакции, если она была найдена до достижения лимита.
  
  - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment); «обработано» не поддерживается. Если параметр не указан, по умолчанию используется значение "finalized".

#### Результаты:

Поле результата будет представлять собой массив информации о подписи транзакции, упорядоченный
от самой новой к самой старой транзакции:

- `<объект>`
  - `signature: <string>` - подпись транзакции в виде строки в кодировке base-58.
  - `slot: <u64>` - Слот, содержащий блок с транзакцией
  - `ошибка: <объект | null>` - Ошибка, если транзакция не удалась, null, если транзакция прошла успешно. [Определения TransactionError](https://github.com/solana-labs/solana/blob/c0c60386544ec9a9ec7119229f37386d9f070523/sdk/src/transaction/error.rs#L13)
  - `memo: <string |null>` - памятка, связанная с транзакцией, ноль, если памятки нет.
  - `Время_блока: <i64 | null>` - предполагаемое время производства, как временная метка Unix (секунды с эпохи Unix), когда транзакция была обработана. ноль, если он недоступен.

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {
    "jsonrpc": "2.0",
    "id": 1,
    "method": "getSignaturesForAddress",
    "params": [
      "Vote111111111111111111111111111111111111111",
      {
        "limit": 1
      }
    ]
  }
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": [
    {
      "err": null,
      "memo": null,
      "signature": "5h6xBEauJ3PK6SWCZ1PGjBvj8vDdWG3KpwATGy1ARAXFSDwt8GFXM7W5Ncn16wmqokgpiKRLuS83KUxyZyv2sUYv",
      "slot": 114,
      "blockTime": null
    }
  ],
  "id": 1
}
```

### getSignatureStatuses

Возвращает статусы списка подписей. Если только
Параметр конфигурации `searchTransactionHistory` включен, этот метод выполняет поиск только в кэше последних состояний подписей, в котором сохраняются состояния для всех активных слотов, а также корневых слотов `MAX_RECENT_BLOCKHASHES`.

#### Параметры:

- `<массив>` - массив подписей транзакций для подтверждения в виде строк в кодировке base-58.
- `<object>` - (необязательный) Объект конфигурации, содержащий следующее поле:
  - `searchTransactionHistory: <bool>` - если true, узел Solana будет искать в своем кеше реестра любые подписи, не найденные в кеше последних состояний.

#### Результаты:

RpcResponse, содержащий объект JSON, состоящий из массива объектов TransactionStatus.

- `RpcResponse<object>` - Объект RpcResponse JSON с полем `value`:

Массив из:

- `<null>` - Неизвестная транзакция
- `<объект>`
  - `slot: <u64>` - слот, в котором была обработана транзакция
  - `подтверждения: <usize | null>` - Количество блоков с момента подтверждения подписи, нуль, если укоренен, а также финализирован квалифицированным большинством кластера
  - `ошибка: <объект | null>` - Ошибка, если транзакция не удалась, null, если транзакция прошла успешно. [Определения TransactionError](https://github.com/solana-labs/solana/blob/c0c60386544ec9a9ec7119229f37386d9f070523/sdk/src/transaction/error.rs#L13)
  - `confirmationStatus: <string | null>` - Статус подтверждения кластера транзакции; либо «обработано», либо «подтверждено», либо «завершено». См. [Обязательство](jsonrpc-api.md#configuring-state-commitment) для получения дополнительной информации об оптимистичном подтверждении.
  - УСТАРЕЛО: `статус: <объект>` - Статус транзакции
    - `"Ok": <null>` - Транзакция прошла успешно
    - `"Err": <ERR>` - Транзакция не удалась с TransactionError

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {
    "jsonrpc": "2.0",
    "id": 1,
    "method": "getSignatureStatuses",
    "params": [
      [
        "5VERv8NMvzbJMEkV8xnrLkEaWRtSz9CosKDYjCJjBRnbJLgp8uirBgmQpjKhoR4tjF3ZpRzrFmBV6UjKdiSZkQUW",
        "5j7s6NiJS3JAkvgkoc18WVAsiSaci2pxB2A6ueCJP4tprA2TFg9wSyTLeYouxPBJEMzJinENTkpA52YStRW5Dia7"
      ]
    ]
  }
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "context": {
      "slot": 82
    },
    "value": [
      {
        "slot": 72,
        "confirmations": 10,
        "err": null,
        "status": {
          "Ok": null
        },
        "confirmationStatus": "confirmed",
      },
      null
    ]
  },
  "id": 1
}
```

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {
    "jsonrpc": "2.0",
    "id": 1,
    "method": "getSignatureStatuses",
    "params": [
      [
        "5VERv8NMvzbJMEkV8xnrLkEaWRtSz9CosKDYjCJjBRnbJLgp8uirBgmQpjKhoR4tjF3ZpRzrFmBV6UjKdiSZkQUW"
      ],
      {
        "searchTransactionHistory": true
      }
    ]
  }
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "context": {
      "slot": 82
    },
    "value": [
      {
        "slot": 48,
        "confirmations": null,
        "err": null,
        "status": {
          "Ok": null
        },
        "confirmationStatus": "finalized",
      },
      null
    ]
  },
  "id": 1
}
```

### getSlot

Возвращает слот, который достиг [данного уровня или уровня обязательств по умолчанию](jsonrpc-api.md#configuring-state-commitment)

#### Параметры:

- `<объект>` - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment)

#### Результаты:

- `<u64>` - Текущий слот

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"getSlot"}
'
```

Результат:

```json
{"jsonrpc":"2.0","result":1234,"id":1}
```

### getSlotLeader

Возвращает лидера текущего слота

#### Параметры:

- `<объект>` - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment)

#### Результаты:

- `<string>` - идентификатор узла Pubkey в виде строки в кодировке base-58.

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"getSlotLeader"}
'
```

Результат:

```json
{"jsonrpc":"2.0","result":"ENvAW7JScgYq6o4zKZwewtkzzJgDzuJAFxYasvmEQdpS","id":1}
```

### getSlotLeaders

Возвращает лидеров слотов для заданного диапазона слотов

#### Параметры:

- `<u64>` - Начальный слот, как целое число u64
- `<u64>` - Ограничение, как целое число u64

#### Результаты:

- `<array<string>>` — открытые ключи идентификации узла в виде строк в кодировке base-58.

#### Пример:

Если текущий слот #99, запросите следующие 10 лидеров со следующим Запросом:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"getSlotLeaders", "params":[100, 10]}
'
```

Результат:

Первый возвращенный лидер — это лидер для слота № 100:

```json
{
  "jsonrpc": "2.0",
  "result": [
    "ChorusmmK7i1AxXeiTtQgQZhQNiXYU84ULeaYF1EH15n",
    "ChorusmmK7i1AxXeiTtQgQZhQNiXYU84ULeaYF1EH15n",
    "ChorusmmK7i1AxXeiTtQgQZhQNiXYU84ULeaYF1EH15n",
    "ChorusmmK7i1AxXeiTtQgQZhQNiXYU84ULeaYF1EH15n",
    "Awes4Tr6TX8JDzEhCZY2QVNimT6iD1zWHzf1vNyGvpLM",
    "Awes4Tr6TX8JDzEhCZY2QVNimT6iD1zWHzf1vNyGvpLM",
    "Awes4Tr6TX8JDzEhCZY2QVNimT6iD1zWHzf1vNyGvpLM",
    "Awes4Tr6TX8JDzEhCZY2QVNimT6iD1zWHzf1vNyGvpLM",
    "DWvDTSh3qfn88UoQTEKRV2JnLt5jtJAVoiCo3ivtMwXP",
    "DWvDTSh3qfn88UoQTEKRV2JnLt5jtJAVoiCo3ivtMwXP"
  ],
  "id": 1
}
```

### getStakeActivation

Возвращает информацию об активации эпохи для учетной записи доли

#### Параметры:

- `<string>` - Pubkey учетной записи доли для запроса в виде строки в кодировке base-58.
- `<object>` - (необязательный) объект конфигурации, содержащий следующие необязательные поля:
  - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment)
  - (необязательный) `epoch: <u64>` - эпоха, для которой вычисляются детали активации. Если параметр не указан, по умолчанию используется текущая эпоха.

#### Результаты:

Результатом будет объект JSON со следующими полями:

- `state: <string` - состояние активации стейк-аккаунта, одно из: `active`, `inactive`, `activating`, `deactivating`
- `active: <u64>` - ставка активна в течение эпохи
- `inactive: <u64>` - ставка неактивна в течение эпохи

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"getStakeActivation", "params": ["CYRJWqiSjLitBAcRxPvWpgX3s5TvmN2SuRY3eEYypFvT"]}
'
```

Результат:

```json
{"jsonrpc":"2.0","result":{"active":197717120,"inactive":0,"state":"active"},"id":1}
```

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {
    "jsonrpc": "2.0",
    "id": 1,
    "method": "getStakeActivation",
    "params": [
      "CYRJWqiSjLitBAcRxPvWpgX3s5TvmN2SuRY3eEYypFvT",
      {
        "epoch": 4
      }
    ]
  }
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "active": 124429280,
    "inactive": 73287840,
    "state": "activating"
  },
  "id": 1
}
```

### getSupply

Возвращает информацию о текущем запасе.

#### Параметры:

- `<object>` - (необязательный) объект конфигурации, содержащий следующие необязательные поля:
  - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment)
  - (необязательно) `excludeNonCirculatingAccountsList: <bool>` - исключить из ответа список не находящихся в обращении счетов

#### Результаты:

Результатом будет объект JSON RpcResponse со значением, равным объекту JSON, содержащему:

- `total: <u64>` - Общий запас в лэмпортах
- `circulating: <u64>` - Циркуляционная подача в лампортах
- `nonCirculating: <u64>` - Нециркуляционная подача в лэмпортах
- `nonCirculatingAccounts: <массив>` - массив адресов нециркулирующих счетов в виде строк. Если `excludeNonCirculatingAccountsList` включен, возвращаемый массив будет пустым.

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0", "id":1, "method":"getSupply"}
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "context": {
      "slot": 1114
    },
    "value": {
      "circulating": 16000,
      "nonCirculating": 1000000,
      "nonCirculatingAccounts": [
        "FEy8pTbP5fEoqMV1GdTz83byuA8EKByqYat1PKDgVAq5",
        "9huDUZfxoJ7wGMTffUE7vh1xePqef7gyrLJu9NApncqA",
        "3mi1GmwEE3zo2jmfDuzvjSX9ovRXsDUKHvsntpkhuLJ9",
        "BYxEJTDerkaRWBem3XgnVcdhppktBXa2HbkHPKj2Ui4Z"
      ],
      "total": 1016000
    }
  },
  "id": 1
}
```

### getTokenAccountBalance

Возвращает баланс токенов учетной записи SPL Token.

#### Параметры:

- `<string>` - Pubkey учетной записи Token для запроса в виде строки в кодировке base-58.
- `<объект>` - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment)

#### Результаты:

Результатом будет объект JSON RpcResponse со значением, равным объекту JSON, содержащему:

- `amount: <string>` - необработанный баланс без десятичных знаков, строковое представление u64
- `decimals: <u8>` - количество цифр по основанию 10 справа от десятичного знака
- `uiAmount: <число | null>` - баланс с использованием десятичных знаков, предписанных монетным двором **УСТАРЕЛО**
- `uiAmountString: <string>` - баланс в виде строки с использованием заданных монетным двором десятичных знаков

Подробнее о возвращаемых данных:
Структура балансов токенов ответ от getBlock имеет аналогичную структуру.

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0", "id":1, "method":"getTokenAccountBalance", "params": ["7fUAJdStEuGbc3sM84cKRL6yYaaSstyLSU4ve5oovLS7"]}
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "context": {
      "slot": 1114
    },
    "value": {
      "amount": "9864",
      "decimals": 2,
      "uiAmount": 98.64,
      "uiAmountString": "98.64",
    },
    "id": 1
  }
}
```

### getTokenAccountsByDelegate

Возвращает все учетные записи SPL Token утвержденным делегатом.

#### Параметры:

- `<string>` - Pubkey делегата учетной записи для запроса в виде строки в кодировке base-58.
- `<объект>` - Либо:
  - `mint: <string>` - Pubkey конкретного токена Mint для ограничения учетных записей в виде строки в кодировке base-58; или
  - `programId: <string>` - Pubkey идентификатора программы токена, которому принадлежат учетные записи, в виде строки в кодировке base-58.
- `<object>` - (необязательный) объект конфигурации, содержащий следующие необязательные поля:
  - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment)
  - `encoding: <string>` - кодировка данных Пользователя, либо "base58" (*slow*), "base64", "base64+zstd" или "jsonParsed".
    Кодирование «jsonParsed» пытается использовать анализаторы состояния для конкретных программ, чтобы возвращать более удобочитаемые и явные данные о состоянии учетной записи. Если запрашивается «jsonParsed», но не удается найти действительный монетный двор для определенной учетной записи, эта учетная запись будет отфильтрована из результатов.
  - (необязательно) `dataSlice: <object>` – ограничить возвращаемые данные учетной записи, используя предоставленные поля `offset: <usize>` и `length: <usize>`; доступно только для кодировок "base58", "base64" или "base64+zstd".

#### Результаты:

Результатом будет объект JSON RpcResponse со значением, равным массиву объектов JSON, который будет содержать:

- `pubkey: <string>` - Pubkey учетной записи в виде строки в кодировке base-58.
- `account: <object>` – объект JSON со следующими подполями:
  - `lamports: <u64>`, количество ламп, назначенных этой учетной записи, как u64
  - `owner: <string>`, Pubkey в кодировке base-58 программы, которой назначена эта учетная запись.
  - `data: <object>`, данные о состоянии токена, связанные с учетной записью, либо в виде закодированных двоичных данных, либо в формате JSON `{<program>: <state>}`
  - `executable: <bool>`, логическое значение, указывающее, содержит ли учетная запись программу \ (и строго только для чтения \)
  - `rentEpoch: <u64>`, эпоха, в которую эта учетная запись будет в следующий раз платить арендную плату, как u64

Когда данные запрашиваются с кодировкой `jsonParsed`, внутри структуры можно ожидать формат, аналогичный формату Структура балансов токенов, как для `tokenAmount`, так и для `delegatedAmount`, причем последний является необязательным объектом.

#### Пример:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {
    "jsonrpc": "2.0",
    "id": 1,
    "method": "getTokenAccountsByDelegate",
    "params": [
      "4Nd1mBQtrMJVYVfKf2PJy9NZUZdTAsp7D4xWLs4gDB4T",
      {
        "programId": "TokenkegQfeZyiNwAJbNbGKPFXCWuBvf9Ss623VQ5DA"
      },
      {
        "encoding": "jsonParsed"
      }
    ]
  }
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "context": {
      "slot": 1114
    },
    "value": [
      {
        "account": {
          "data": {
            "program": "spl-token",
            "parsed": {
              "info": {
                "tokenAmount": {
                  "amount": "1",
                  "decimals": 1,
                  "uiAmount": 0.1,
                  "uiAmountString": "0.1"
                },
                "delegate": "4Nd1mBQtrMJVYVfKf2PJy9NZUZdTAsp7D4xWLs4gDB4T",
                "delegatedAmount": {
                  "amount": "1",
                  "decimals": 1,
                  "uiAmount": 0.1,
                  "uiAmountString": "0.1"
                },
                "state": "initialized",
                "isNative": false,
                "mint": "3wyAj7Rt1TWVPZVteFJPLa26JmLvdb1CAKEFZm3NY75E",
                "owner": "CnPoSPKXu7wJqxe59Fs72tkBeALovhsCxYeFwPCQH9TD"
              },
              "type": "account"
            },
            "space": 165
          },
          "executable": false,
          "lamports": 1726080,
          "owner": "TokenkegQfeZyiNwAJbNbGKPFXCWuBvf9Ss623VQ5DA",
          "rentEpoch": 4
        },
        "pubkey": "28YTZEwqtMHWrhWcvv34se7pjS7wctgqzCPB3gReCFKp"
      }

    ]
  },
  "id": 1
}
```

### getTokenAccountsByOwner

Возвращает все учетные записи SPL Token по владельцу токена.

#### Параметры:

- `<string>` - открытый ключ владельца учетной записи для запроса в виде строки в кодировке base-58.
- `<объект>` - Либо:
  - `mint: <string>` - Pubkey конкретного токена Mint для ограничения учетных записей в виде строки в кодировке base-58; или
  - `programId: <string>` - Pubkey идентификатора программы токена, которому принадлежат учетные записи, в виде строки в кодировке base-58.
- `<object>` - (необязательный) объект конфигурации, содержащий следующие необязательные поля:
  - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment)
  - `encoding: <string>` - кодировка данных Пользователя, либо "base58" (*slow*), "base64", "base64+zstd" или "jsonParsed".
    Кодирование «jsonParsed» пытается использовать анализаторы состояния для конкретных программ, чтобы возвращать более удобочитаемые и явные данные о состоянии учетной записи. Если запрашивается «jsonParsed», но не удается найти действительный монетный двор для определенной учетной записи, эта учетная запись будет отфильтрована из результатов.
  - (необязательно) `dataSlice: <object>` – ограничить возвращаемые данные учетной записи, используя предоставленные поля `offset: <usize>` и `length: <usize>`; доступно только для кодировок "base58", "base64" или "base64+zstd".

#### Результаты:

Результатом будет объект JSON RpcResponse со значением, равным массиву объектов JSON, который будет содержать:

- `pubkey: <string>` - Pubkey учетной записи в виде строки в кодировке base-58.
- `account: <object>` – объект JSON со следующими подполями:
  - `lamports: <u64>`, количество ламп, назначенных этой учетной записи, как u64
  - `owner: <string>`, Pubkey в кодировке base-58 программы, которой назначена эта учетная запись.
  - `data: <object>`, данные о состоянии токена, связанные с учетной записью, либо в виде закодированных двоичных данных, либо в формате JSON `{<program>: <state>}`
  - `executable: <bool>`, логическое значение, указывающее, содержит ли учетная запись программу \ (и строго только для чтения \)
  - `rentEpoch: <u64>`, эпоха, в которую эта учетная запись будет в следующий раз платить арендную плату, как u64

Когда данные запрашиваются с кодировкой `jsonParsed`, внутри структуры можно ожидать формат, аналогичный формату Структура балансов токенов, как для `tokenAmount`, так и для `delegatedAmount`, причем последний является необязательным объектом.

#### Пример:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {
    "jsonrpc": "2.0",
    "id": 1,
    "method": "getTokenAccountsByOwner",
    "params": [
      "4Qkev8aNZcqFNSRhQzwyLMFSsi94jHqE8WNVTJzTP99F",
      {
        "mint": "3wyAj7Rt1TWVPZVteFJPLa26JmLvdb1CAKEFZm3NY75E"
      },
      {
        "encoding": "jsonParsed"
      }
    ]
  }
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "context": {
      "slot": 1114
    },
    "value": [
      {
        "account": {
          "data": {
              "program": "spl-token",
              "parsed": {
                "accountType": "account",
                "info": {
                  "tokenAmount": {
                    "amount": "1",
                    "decimals": 1,
                    "uiAmount": 0.1,
                    "uiAmountString": "0.1"
                  },
                  "delegate": "4Nd1mBQtrMJVYVfKf2PJy9NZUZdTAsp7D4xWLs4gDB4T",
                  "delegatedAmount": {
                    "amount": "1",
                    "decimals": 1,
                    "uiAmount": 0.1,
                    "uiAmountString": "0.1"
                  },
                  "state": "initialized",
                  "isNative": false,
                  "mint": "3wyAj7Rt1TWVPZVteFJPLa26JmLvdb1CAKEFZm3NY75E",
                  "owner": "4Qkev8aNZcqFNSRhQzwyLMFSsi94jHqE8WNVTJzTP99F"
                },
                "type": "account"
              },
              "space": 165
            },
            "executable": false,
            "lamports": 1726080,
            "owner": "TokenkegQfeZyiNwAJbNbGKPFXCWuBvf9Ss623VQ5DA",
            "rentEpoch": 4
        },
        "pubkey": "C2gJg6tKpQs41PRS1nC8aw3ZKNZK3HQQZGVrDFDup5nx"
      }
    ]
  },
  "id": 1
}
```

### getTokenLargestAccounts

Возвращает 20 крупнейших учетных записей определенного типа токена SPL.

#### Параметры:

- `<string>` - Pubkey токена Mint для запроса в виде строки в кодировке base-58.
- `<объект>` - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment)

#### Результаты:

Результатом будет объект JSON RpcResponse со значением, равным массиву объектов JSON, содержащих:

- `address: <string>` - адрес учетной записи токена
- `amount: <string>` - необработанный баланс счета токена без десятичных знаков, строковое представление u64
- `decimals: <u8>` - количество цифр по основанию 10 справа от десятичного знака
- `uiAmount: <число | null>` - баланс учетной записи токена с использованием десятичных знаков, предписанных монетным двором **УСТАРЕЛО**
- `uiAmountString: <string>` - баланс учетной записи токена в виде строки с использованием десятичных знаков, предписанных монетным двором.

#### Пример:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0", "id":1, "method":"getTokenLargestAccounts", "params": ["3wyAj7Rt1TWVPZVteFJPLa26JmLvdb1CAKEFZm3NY75E"]}
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "context": {
      "slot": 1114
    },
    "value": [
      {
        "address": "FYjHNoFtSQ5uijKrZFyYAxvEr87hsKXkXcxkcmkBAf4r",
        "amount": "771",
        "decimals": 2,
        "uiAmount": 7.71,
        "uiAmountString": "7.71"
      },
      {
        "address": "BnsywxTcaYeNUtzrPxQUvzAWxfzZe3ZLUJ4wMMuLESnu",
        "amount": "229",
        "decimals": 2,
        "uiAmount": 2.29,
        "uiAmountString": "2.29"
      }
    ]
  },
  "id": 1
}
```

### getTokenSupply

Возвращает общее предложение типа токена SPL.

#### Параметры:

- `<string>` - Pubkey токена Mint для запроса в виде строки в кодировке base-58.
- `<объект>` - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment)

#### Результаты:

Результатом будет объект JSON RpcResponse со значением, равным объекту JSON, содержащему:

- `amount: <string>` - необработанное общее количество токенов без десятичных знаков, строковое представление u64.
- `decimals: <u8>` - количество цифр по основанию 10 справа от десятичного знака
- `uiAmount: <число | null>` - общее количество токенов с использованием заданных монетным двором десятичных знаков **УСТАРЕЛО**
- `uiAmountString: <string>` - общее количество токенов в виде строки с использованием десятичных знаков, предписанных монетным двором.

#### Пример:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0", "id":1, "method":"getTokenSupply", "params": ["3wyAj7Rt1TWVPZVteFJPLa26JmLvdb1CAKEFZm3NY75E"]}
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "context": {
      "slot": 1114
    },
    "value": {
      "amount": "100000",
      "decimals": 2,
      "uiAmount": 1000,
      "uiAmountString": "1000",
    }
  },
  "id": 1
}
```

### getTransaction

Возвращает детали транзакции для подтвержденной транзакции

#### Параметры:

- `<string>` - подпись транзакции в виде строки в кодировке base-58.
- `<object>` - (необязательный) объект конфигурации, содержащий следующие необязательные поля:
  - (необязательно) `encoding: <string>` - кодировка для каждой возвращаемой транзакции, либо "json", "jsonParsed", "base58" (*slow*), "base64". Если параметр не указан, используется кодировка по умолчанию "json".
    Кодировка «jsonParsed» пытается использовать синтаксические анализаторы инструкций для конкретных программ, чтобы возвращать более удобочитаемые и явные данные в списке «transaction.message.instructions». Если запрашивается "jsonParsed", но синтаксический анализатор не может быть найден, инструкция возвращается к обычной кодировке JSON (поля "accounts", "data" и "programIdIndex").
  - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment); «обработано» не поддерживается. Если параметр не указан, по умолчанию используется значение "finalized".

#### Результаты:

- `<null>` - если транзакция не найдена или не подтверждена

- `<объект>` - если транзакция подтверждена, объект со следующими полями:
  
  - `slot: <u64>` - слот, в котором эта транзакция была обработана
  
  - `transaction: <object|[string,encoding]>` - объект Transaction либо в формате JSON, либо в закодированных двоичных данных, в зависимости от параметра кодирования
  
  - `Время_блока: <i64 | null>` - предполагаемое время производства в виде временной метки Unix (секунды с эпохи Unix), когда транзакция была обработана. ноль, если недоступен
  
  - `мета: <объект | null>` - объект метаданных статуса транзакции:
    
    - `ошибка: <объект | null>` - Ошибка, если транзакция не удалась, null, если транзакция прошла успешно. [Определения TransactionError](https://docs.rs/solana-sdk/VERSION_FOR_DOCS_RS/solana_sdk/transaction/enum.TransactionError.html)
    
    - `fee: <u64>` - комиссия, взимаемая за эту транзакцию, в виде целого числа u64
    
    - `preBalances: <массив>` - массив балансов счетов u64 до обработки транзакции
    
    - `postBalances: <массив>` - массив балансов аккаунтов u64 после обработки транзакции
    
    - `innerInstructions: <array|null>` - Список внутренних инструкций или `null`, если запись внутренних инструкций не была включена во время этой транзакции
    
    - `preTokenBalances: <array|undefined>` - Список балансов токенов до того, как транзакция была обработана или пропущена, если запись баланса токенов еще не была включена во время этой транзакции.
    
    - `postTokenBalances: <array|undefined>` - Список балансов токенов после обработки транзакции или опущен, если запись баланса токенов еще не была включена во время этой транзакции
    
    - `logMessages: <array|null>` - массив строковых сообщений журнала или `null`, если запись сообщений журнала не была включена во время этой транзакции
    
    - УСТАРЕЛО: `статус: <объект>` - Статус транзакции
      
      - `"Ok": <null>` - Транзакция прошла успешно
      - `"Err": <ERR>` - Транзакция не удалась с TransactionError
    
    - `rewards: <массив>` - присутствует, если запрошены награды; массив объектов JSON, содержащий:
      
      - `pubkey: <string>` — открытый ключ в виде строки в кодировке base-58 учетной записи, получившей вознаграждение.
      
      - `lamports: <i64>`- количество наградных лампортов, зачисленных или списанных со счета, как i64
      
      - `postBalance:<u64>` - баланс аккаунта в лэмпортах после применения вознаграждения
      
      - `rewardType: <string>` - тип вознаграждения: на данный момент только "аренда", другие типы могут быть добавлены в будущем
      
      - `комиссия: <u8|undefined>` - комиссия за голосовой счет, когда вознаграждение было зачислено, присутствует только для вознаграждений за голосование и стекинг

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {
    "jsonrpc": "2.0",
    "id": 1,
    "method": "getTransaction",
    "params": [
      "2nBhEBYYvfaAe16UMNqRHre4YNSskvuYgx3M6E4JP1oDYvZEJHvoPzyUidNgNX5r9sTyN1J9UxtbCXy2rqYcuyuv",
      "json"
    ]
  }
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "meta": {
      "err": null,
      "fee": 5000,
      "innerInstructions": [],
      "postBalances": [
        499998932500,
        26858640,
        1,
        1,
        1
      ],
      "postTokenBalances": [],
      "preBalances": [
        499998937500,
        26858640,
        1,
        1,
        1
      ],
      "preTokenBalances": [],
      "status": {
        "Ok": null
      }
    },
    "slot": 430,
    "transaction": {
      "message": {
        "accountKeys": [
          "3UVYmECPPMZSCqWKfENfuoTv51fTDTWicX9xmBD2euKe",
          "AjozzgE83A3x1sHNUR64hfH7zaEBWeMaFuAN9kQgujrc",
          "SysvarS1otHashes111111111111111111111111111",
          "SysvarC1ock11111111111111111111111111111111",
          "Vote111111111111111111111111111111111111111"
        ],
        "header": {
          "numReadonlySignedAccounts": 0,
          "numReadonlyUnsignedAccounts": 3,
          "numRequiredSignatures": 1
        },
        "instructions": [
          {
            "accounts": [
              1,
              2,
              3,
              0
            ],
            "data": "37u9WtQpcm6ULa3WRQHmj49EPs4if7o9f1jSRVZpm2dvihR9C8jY4NqEwXUbLwx15HBSNcP1",
            "programIdIndex": 4
          }
        ],
        "recentBlockhash": "mfcyqEXB3DnHXki6KjjmZck6YjmZLvpAByy2fj4nh6B"
      },
      "signatures": [
        "2nBhEBYYvfaAe16UMNqRHre4YNSskvuYgx3M6E4JP1oDYvZEJHvoPzyUidNgNX5r9sTyN1J9UxtbCXy2rqYcuyuv"
      ]
    }
  },
  "blockTime": null,
  "id": 1
}
```

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {
    "jsonrpc": "2.0",
    "id": 1,
    "method": "getTransaction",
    "params": [
      "2nBhEBYYvfaAe16UMNqRHre4YNSskvuYgx3M6E4JP1oDYvZEJHvoPzyUidNgNX5r9sTyN1J9UxtbCXy2rqYcuyuv",
      "base64"
    ]
  }
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "meta": {
      "err": null,
      "fee": 5000,
      "innerInstructions": [],
      "postBalances": [
        499998932500,
        26858640,
        1,
        1,
        1
      ],
      "postTokenBalances": [],
      "preBalances": [
        499998937500,
        26858640,
        1,
        1,
        1
      ],
      "preTokenBalances": [],
      "status": {
        "Ok": null
      }
    },
    "slot": 430,
    "transaction": [
      "AVj7dxHlQ9IrvdYVIjuiRFs1jLaDMHixgrv+qtHBwz51L4/ImLZhszwiyEJDIp7xeBSpm/TX5B7mYzxa+fPOMw0BAAMFJMJVqLw+hJYheizSoYlLm53KzgT82cDVmazarqQKG2GQsLgiqktA+a+FDR4/7xnDX7rsusMwryYVUdixfz1B1Qan1RcZLwqvxvJl4/t3zHragsUp0L47E24tAFUgAAAABqfVFxjHdMkoVmOYaR1etoteuKObS21cc1VbIQAAAAAHYUgdNXR0u3xNdiTr072z2DVec9EQQ/wNo1OAAAAAAAtxOUhPBp2WSjUNJEgfvy70BbxI00fZyEPvFHNfxrtEAQQEAQIDADUCAAAAAQAAAAAAAACtAQAAAAAAAAdUE18R96XTJCe+YfRfUp6WP+YKCy/72ucOL8AoBFSpAA==",
      "base64"
    ]
  },
  "id": 1
}
```

### getTransactionCount

Возвращает текущее количество транзакций из реестра.

#### Параметры:

- `<объект>` - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment)

#### Результаты:

- `<u64>` - считать

#### Пример:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"getTransactionCount"}
'
```

Результат:

```json
{"jsonrpc":"2.0","result":268,"id":1}
```

### getVersion

Возвращает текущие версии solana, работающие на узле.

#### Параметры:

Никто

#### Результаты:

Поле результата будет объектом JSON со следующими полями:

- `solana-core`, программная версия solana-core
- `feature-set`, уникальный идентификатор текущего набора функций программного обеспечения.

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"getVersion"}
'
```

Результат:

```json
{"jsonrpc":"2.0","result":{"solana-core": "1.10.0"},"id":1}
```

### getVoteAccounts

Возвращает информацию об учетной записи и соответствующую ставку для всех счетов для голосования в текущем банке.

#### Параметры:

- `<object>` - (необязательный) Объект конфигурации, содержащий следующее поле:
  - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment)
  - (необязательно) `votePubkey: <string>` – возвращать результаты только для этого адреса голосования валидатора (в кодировке base-58)
  - (необязательно) `keepUnstakedDelinquents: <bool>` - Не отфильтровывать просроченных валидаторов без доли
  - (необязательно) `delinquentSlotDistance: <u64>` - Укажите количество слотов за наконечником, которое должен пройти валидатор, чтобы считаться просроченным. **ПРИМЕЧАНИЕ.** В целях единообразия продуктов экосистемы _**не** рекомендуется указывать этот аргумент._

#### Результаты:

Поле результата будет представлять собой JSON-объект текущих и просроченных учетных записей,
каждый из которых содержит массив объектов JSON со следующими подполями:

- `votePubkey: <string>` - Адрес учетной записи для голосования, как строка в кодировке base-58.
- `nodePubkey: <string>` - Идентификатор валидатора, как строка в кодировке base-58.
- `activatedStake:<u64>` - ставка в лэмпортах, делегированная на этот счет для голосования и активная в эту эпоху
- `epochVoteAccount: <bool>` - bool, застейкается ли счет для голосования на эту эпоху
- `комиссия: <число>`, процент (0-100) выплаты вознаграждения, причитающийся голосовому аккаунту
- `lastVote: <u64>` - Последний слот, за который проголосовал данный счет для голосования.
- `epochCredits: <массив>` – история количества кредитов, заработанных к концу каждой эпохи, в виде массива массивов, содержащих: `[эпоха, кредиты, предыдущие кредиты]`

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"getVoteAccounts"}
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "current": [
      {
        "commission": 0,
        "epochVoteAccount": true,
        "epochCredits": [
          [ 1, 64, 0 ],
          [ 2, 192, 64 ]
        ],
        "nodePubkey": "B97CCUW3AEZFGy6uUg6zUdnNYvnVq5VG8PUtb2HayTDD",
        "lastVote": 147,
        "activatedStake": 42,
        "votePubkey": "3ZT31jkAGhUaw8jsy4bTknwBMP8i4Eueh52By4zXcsVw"
      }
    ],
    "delinquent": [
      {
        "commission": 127,
        "epochVoteAccount": false,
        "epochCredits": [],
        "nodePubkey": "6ZPxeQaDo4bkZLRsdNrCzchNQr5LN9QMc9sipXv9Kw8f",
        "lastVote": 0,
        "activatedStake": 0,
        "votePubkey": "CmgCk4aMS7KW1SHX3s9K5tBJ6Yng2LBaC8MFov4wx9sm"
      }
    ]
  },
  "id": 1
}
```

#### Пример: Ограничить результаты одной учетной записью для голосования валидатора

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {
    "jsonrpc": "2.0",
    "id": 1,
    "method": "getVoteAccounts",
    "params": [
      {
        "votePubkey": "3ZT31jkAGhUaw8jsy4bTknwBMP8i4Eueh52By4zXcsVw"
      }
    ]
  }
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "current": [
      {
        "commission": 0,
        "epochVoteAccount": true,
        "epochCredits": [
          [ 1, 64, 0 ],
          [ 2, 192, 64 ]
        ],
        "nodePubkey": "B97CCUW3AEZFGy6uUg6zUdnNYvnVq5VG8PUtb2HayTDD",
        "lastVote": 147,
        "activatedStake": 42,
        "votePubkey": "3ZT31jkAGhUaw8jsy4bTknwBMP8i4Eueh52By4zXcsVw"
      }
    ],
    "delinquent": []
  },
  "id": 1
}
```

### isBlockhashValid

**НОВОЕ: этот метод доступен только в solana-core v1.9 или новее. Пожалуйста, используйте [getFeeCalculatorForBlockhash](jsonrpc-api.md#getfeecalculatorforblockhash) для solana-core v1.8**

Возвращает, действителен ли блокхэш или нет

#### Параметры:

- `blockhash: <string>` - хеш блока этого блока в виде строки в кодировке base-58
- `<object>` - (необязательно) [Commitment](jsonrpc-api.md#configuring-state-commitment) (используется для получения хэша блока)

#### Результаты:

- `<bool>` – True, если блок-хэш все еще действителен.

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {
    "id":45,
    "jsonrpc":"2.0",
    "method":"isBlockhashValid",
    "params":[
      "J7rBdM6AecPDEZp8aPq5iPSNKVkU5Q76F3oAV4eW5wsW",
      {"commitment":"processed"}
    ]
  }
'
```

Результат:

```json
{
  "jsonrpc":"2.0",
  "result":{
    "context":{
      "slot":2483
    },
    "value":false
  },"id":1
}
```

### minimumLedgerSlot

Возвращает самый нижний слот, о котором нода имеет информацию в своей книге. Этот
значение может увеличиваться со временем, если узел настроен на очистку старых данных реестра.

#### Параметры:

Никто

#### Результаты:

- `u64` - Минимальный слот леджера

#### Пример:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"minimumLedgerSlot"}
'
```

Результат:

```json
{"jsonrpc":"2.0","result":1234,"id":1}
```

### requestAirdrop

Запрашивает раздачу ламп портов на публичный ключ

#### Параметры:

- `<string>` - Пубключ учетной записи для получения лампортов в виде строки в кодировке base-58.
- `<integer>` - лэмпортс, как u64
- `<object>` - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment) (используется для получения хэша блока и проверки успешности раздачи)

#### Результаты:

- `<string>` - Подпись транзакции раздачи, как строка в кодировке base-58

#### Пример:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"requestAirdrop", "params":["83astBRguLMdt2h5U1Tpdq5tjFoJ6noeGwaY3mDLVcri", 1000000000]}
'
```

Результат:

```json
{"jsonrpc":"2.0","result":"5VERv8NMvzbJMEkV8xnrLkEaWRtSz9CosKDYjCJjBRnbJLgp8uirBgmQpjKhoR4tjF3ZpRzrFmBV6UjKdiSZkQUW","id":1}
```

### sendTransaction

Отправляет подписанную транзакцию в кластер для обработки.

Этот метод никак не изменяет транзакцию; он передает
транзакция, созданная клиентами для узла как есть.

Если служба rpc узла получает транзакцию, этот метод сразу завершается успешно, не дожидаясь каких-либо подтверждений. Успешный ответ от этого метода не гарантирует, что транзакция будет обработана или подтверждена кластером.

В то время как служба rpc будет разумно повторять попытку отправить ее, транзакция может быть отклонена, если срок действия `recent_blockhash` транзакции истечет до того, как она приземлится.

Используйте [`getSignatureStatuses`](jsonrpc-api.md#getsignaturestatuses), чтобы убедиться, что транзакция обработана и подтверждена.

Перед отправкой выполняются следующие предполетные проверки:

1. Подписи транзакций проверяются
2. Транзакция моделируется для слота банка, указанного предварительным обязательством. В случае неудачи будет возвращена ошибка. При желании предварительные проверки можно отключить. Рекомендуется указывать одно и то же обязательство и предпечатное обязательство, чтобы избежать путаницы в поведении.

Возвращаемая подпись является первой подписью в транзакции, которая
используется для идентификации транзакции ([идентификатор транзакции](../../terminology.md#идентификатор-транзакции)).
Этот идентификатор можно легко извлечь из данных транзакции перед отправкой.

#### Параметры:

- `<string>` - полностью подписанная транзакция в виде закодированной строки
- `<object>` - (необязательный) Объект конфигурации, содержащий следующее поле:
  - `skipPreflight: <bool>` - если true, пропустить предварительную проверку транзакций (по умолчанию: false)
  - `preflightCommitment: <string>` - (необязательно) [Commitment](jsonrpc-api.md#configuring-state-commitment) уровень для использования для предварительной проверки (по умолчанию: `"finalized"`).
  - `encoding: <string>` - (необязательно) Кодировка, используемая для данных транзакции. Либо `"base58"` (*медленно*, **УСТАРЕЛО**), либо `"base64"`. (по умолчанию: `"base58"`).
  - `maxRetries: <usize>` - (необязательно) Максимальное количество повторных попыток узла RPC отправить транзакцию лидеру.
    Если этот параметр не указан, узел RPC будет повторять транзакцию до тех пор, пока она не будет завершена или пока не истечет срок действия хэша блока.

#### Результаты:

- `<string>` - Подпись первой транзакции, встроенная в транзакцию, в виде строки в кодировке base-58 ([идентификатор транзакции](../../terminology.md#transaction-id))

#### Пример:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {
    "jsonrpc": "2.0",
    "id": 1,
    "method": "sendTransaction",
    "params": [
      "4hXTCkRzt9WyecNzV1XPgCDfGAZzQKNxLXgynz5QDuWWPSAZBZSHptvWRL3BjCvzUXRdKvHL2b7yGrRQcWyaqsaBCncVG7BFggS8w9snUts67BSh3EqKpXLUm5UMHfD7ZBe9GhARjbNQMLJ1QD3Spr6oMTBU6EhdB4RD8CP2xUxr2u3d6fos36PD98XS6oX8TQjLpsMwncs5DAMiD4nNnR8NBfyghGCWvCVifVwvA8B8TJxE1aiyiv2L429BCWfyzAme5sZW8rDb14NeCQHhZbtNqfXhcp2tAnaAT"
    ]
  }
'
```

Результат:

```json
{"jsonrpc":"2.0","result":"2id3YC2jK9G5Wo2phDx4gJVAew8DcY5NAojnVuao8rkxwPYPe8cSwE5GzhEgJA2y8fVjDEo6iR6ykBvDxrTQrtpb","id":1}
```

### simulateTransaction

Имитация отправки транзакции

#### Параметры:

- `<string>` - транзакция в виде закодированной строки. Транзакция должна иметь действительный хэш блока, но не обязательно должна быть подписана.
- `<object>` - (необязательный) Объект конфигурации, содержащий следующие поля:
  - `sigVerify: <bool>` - если true, подписи транзакций будут проверены (по умолчанию: false, конфликтует с `replaceRecentBlockhash`)
  - `commitment: <string>` - (необязательно) [Commitment](jsonrpc-api.md#configuring-state-commitment) уровень для имитации транзакции (по умолчанию: `"finalized"`).
  - `encoding: <string>` - (необязательно) Кодировка, используемая для данных транзакции. Либо `"base58"` (*медленно*, **УСТАРЕЛО**), либо `"base64"`. (по умолчанию: `"base58"`).
  - `replaceRecentBlockhash: <bool>` - (необязательно) если true, последний хеш блока транзакции будет заменен самым последним хэшем блока.
    (по умолчанию: false, конфликтует с `sigVerify`)
  - `accounts: <object>` - (необязательно) Объект конфигурации Accounts, содержащий следующие поля:
    - `encoding: <string>` - (необязательно) кодировка возвращаемых данных Пользователя: "base64" (по умолчанию), "base64+zstd" или "jsonParsed".
       Кодирование «jsonParsed» пытается использовать анализаторы состояния для конкретных программ, чтобы возвращать более удобочитаемые и явные данные о состоянии учетной записи. Если запрашивается "jsonParsed", но синтаксический анализатор не может быть найден, поле возвращается к двоичному кодированию, которое можно обнаружить, когда поле `data` имеет тип `<string>`.
    - `addresses: <array>` – массив учетных записей для возврата в виде строк в кодировке base-58.

#### Результаты:

RpcResponse, содержащий объект TransactionStatus
Результатом будет объект JSON RpcResponse со значением, установленным на объект JSON со следующими полями:

- `ошибка: <объект | строка | null>` - Ошибка, если транзакция не удалась, null, если транзакция прошла успешно. [Определения TransactionError] (https://github.com/solana-labs/solana/blob/c0c60386544ec9a9ec7119229f37386d9f070523/sdk/src/transaction/error.rs#L13)
- `журналы: <массив | null>` — массив сообщений журнала, которые инструкции транзакции выводят во время выполнения, нуль, если симуляция не удалась до того, как транзакция смогла быть выполнена (например, из-за недопустимого хэша блока или ошибки проверки подписи)
- `accounts: <массив> | null>` - массив аккаунтов той же длины, что и массив `accounts.addresses` в запросе
  - `<null>` - если учетная запись не существует или если `err` не равно нулю
  - `<object>` - иначе объект JSON, содержащий:
    - `lamports: <u64>`, количество ламп, назначенных этой учетной записи, как u64
    - `owner: <string>`, Pubkey в кодировке base-58 программы, которой назначена эта учетная запись.
    - `data: <[string, encoding]|object>`, данные, связанные с учетной записью, либо в виде закодированных двоичных данных, либо в формате JSON `{<program>: <state>}`, в зависимости от параметра кодирования
    - `executable: <bool>`, логическое значение, указывающее, содержит ли учетная запись программу \ (и строго только для чтения \)
    - `rentEpoch: <u64>`, эпоха, в которую эта учетная запись будет в следующий раз платить арендную плату, как u64
- `unitsConsumed: <u64 | undefined>`, количество единиц вычислительного бюджета, израсходованных во время обработки этой транзакции.

#### Пример:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {
    "jsonrpc": "2.0",
    "id": 1,
    "method": "simulateTransaction",
    "params": [
      "4hXTCkRzt9WyecNzV1XPgCDfGAZzQKNxLXgynz5QDuWWPSAZBZSHptvWRL3BjCvzUXRdKvHL2b7yGrRQcWyaqsaBCncVG7BFggS8w9snUts67BSh3EqKpXLUm5UMHfD7ZBe9GhARjbNQMLJ1QD3Spr6oMTBU6EhdB4RD8CP2xUxr2u3d6fos36PD98XS6oX8TQjLpsMwncs5DAMiD4nNnR8NBfyghGCWvCVifVwvA8B8TJxE1aiyiv2L429BCWfyzAme5sZW8rDb14NeCQHhZbtNqfXhcp2tAnaAT"
    ]
  }
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "context": {
      "slot": 218
    },
    "value": {
      "err": null,
      "accounts": null,
      "logs": [
        "BPF program 83astBRguLMdt2h5U1Tpdq5tjFoJ6noeGwaY3mDLVcri success"
      ]
    }
  },
  "id": 1
}
```

## Подписка (Websocket)

После подключения к веб-сокету RPC PubSub по адресу `ws://<АДРЕС>/`:

- Отправьте запросы на подписку в веб-сокет, используя методы, указанные ниже.
- Несколько подписок могут быть активны одновременно
- Многие подписки принимают необязательный параметр [`commitment`](jsonrpc-api.md#configuring-state-commitment), определяющий, насколько завершенным должно быть изменение, чтобы вызвать уведомление. Для подписок, если обязательство не указано, значением по умолчанию является `"finalized"`.

### accountSubscribe

Подпишитесь на учетную запись, чтобы получать уведомления об изменении ламповых портов или данных для открытого ключа данной учетной записи.

#### Параметры:

- `<string>` - Pubkey учетной записи, как строка в кодировке base-58.
- `<object>` - (необязательный) объект конфигурации, содержащий следующие необязательные поля:
  - `<объект>` - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment)
  - `encoding: <string>` - кодировка данных Пользователя, либо "base58" (*slow*), "base64", "base64+zstd" или "jsonParsed".
    Кодирование «jsonParsed» пытается использовать анализаторы состояния для конкретных программ, чтобы возвращать более удобочитаемые и явные данные о состоянии учетной записи. Если запрашивается "jsonParsed", но синтаксический анализатор не может быть найден, поле возвращается к двоичному кодированию, которое можно обнаружить, когда поле `data` имеет тип `<string>`.

#### Результаты:

- `<номер>` - идентификатор подписки \(необходим для отказа от подписки\)

#### Пример:

Запрос:

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "accountSubscribe",
  "params": [
    "CM78CPUeXjn8o3yroDHxUtKsZZgoy4GPkPPXfouKNH12",
    {
      "encoding": "base64",
      "commitment": "finalized"
    }
  ]
}
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "accountSubscribe",
  "params": [
    "CM78CPUeXjn8o3yroDHxUtKsZZgoy4GPkPPXfouKNH12",
    {
      "encoding": "jsonParsed"
    }
  ]
}
```

Результат:

```json
{"jsonrpc": "2.0","result": 23784,"id": 1}
```

#### Формат уведомлений:

Формат уведомления такой же, как в HTTP-методе [getAccountInfo](jsonrpc-api.md#getAccountInfo) RPC.

Кодировка Base58:

```json
{
  "jsonrpc": "2.0",
  "method": "accountNotification",
  "params": {
    "result": {
      "context": {
        "slot": 5199307
      },
      "value": {
        "data": ["11116bv5nS2h3y12kD1yUKeMZvGcKLSjQgX6BeV7u1FrjeJcKfsHPXHRDEHrBesJhZyqnnq9qJeUuF7WHxiuLuL5twc38w2TXNLxnDbjmuR", "base58"],
        "executable": false,
        "lamports": 33594,
        "owner": "11111111111111111111111111111111",
        "rentEpoch": 635
      }
    },
    "subscription": 23784
  }
}
```

Кодировка Parsed-JSON:

```json
{
  "jsonrpc": "2.0",
  "method": "accountNotification",
  "params": {
    "result": {
      "context": {
        "slot": 5199307
      },
      "value": {
        "data": {
           "program": "nonce",
           "parsed": {
              "type": "initialized",
              "info": {
                 "authority": "Bbqg1M4YVVfbhEzwA9SpC9FhsaG83YMTYoR4a8oTDLX",
                 "blockhash": "LUaQTmM7WbMRiATdMMHaRGakPtCkc2GHtH57STKXs6k",
                 "feeCalculator": {
                    "lamportsPerSignature": 5000
                 }
              }
           }
        },
        "executable": false,
        "lamports": 33594,
        "owner": "11111111111111111111111111111111",
        "rentEpoch": 635
      }
    },
    "subscription": 23784
  }
}
```

### accountUnsubscribe

Отписаться от уведомлений об изменении аккаунта

#### Параметры:

- `<номер>` - идентификатор учетной записи Подписка для отмены

#### Результаты:

- `<bool>` - сообщение об успешной отмене подписки

#### Пример:

Запрос:

```json
{"jsonrpc":"2.0", "id":1, "method":"accountUnsubscribe", "params":[0]}
```

Результат:

```json
{"jsonrpc": "2.0","result": true,"id": 1}
```

### blockSubscribe - Unstable, disabled by default

**Эта подписка нестабильна и доступна только в том случае, если был запущен валидатор с флагом --rpc-pubsub-enable-block-subscription. Формат этого подписка может измениться в будущем**

Подпишитесь, чтобы получать уведомления в любое время, когда новый блок подтвержден или завершен.

#### Параметры:

- `filter: <string>|<object>` - критерии фильтрации журналов для получения результатов по типу учетной записи; в настоящее время поддерживается:
  - "все" - включить все транзакции в блок
  - `{ "mentionsAccountOrProgram": <string> }` - возвращает только транзакции, в которых упоминается предоставленный открытый ключ (в виде строки в кодировке base-58). Если в данном блоке нет упоминаний, уведомление не будет отправлено.
- `<object>` - (необязательный) объект конфигурации, содержащий следующие необязательные поля:
  - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment)
  - (необязательно) `encoding: <string>` - кодировка данных Пользователя: "base58" (*slow*), "base64", "base64+zstd" или "jsonParsed".
    Кодирование «jsonParsed» пытается использовать анализаторы состояния для конкретных программ, чтобы возвращать более удобочитаемые и явные данные о состоянии учетной записи. Если запрашивается "jsonParsed", но синтаксический анализатор не может быть найден, поле возвращается к кодировке base64, обнаруживаемой, когда поле `data` имеет тип `<string>`. По умолчанию "base64".
  - (необязательно) `transactionDetails: <string>` - уровень детализации возвращаемой транзакции: "полный", "подписи" или "нет". Если параметр не указан, уровень детализации по умолчанию — «полный».
  - (необязательно) `showRewards: bool` - заполнять ли массив `rewards`. Если параметр не указан, по умолчанию включены награды.

#### Результаты:

- `integer` - идентификатор подписки \(необходим для отписки\)

#### Пример:

Запрос:

```json
{"jsonrpc": "2.0", "id": "1", "method": "blockSubscribe", "params": ["all"]}
```

```json
{
  "jsonrpc": "2.0",
  "id": "1",
  "method": "blockSubscribe",
  "params": [
    {"mentionsAccountOrProgram": "LieKvPRE8XeX3Y2xVNHjKlpAScD12lYySBVQ4HqoJ5op"},
    {
      "commitment": "confirmed",
      "encoding": "base64",
      "showRewards": true,
      "transactionDetails": "full"
    }
  ]
}
```

Результат:

```json
{"jsonrpc": "2.0","result": 0,"id": 1}
```

#### Формат уведомлений:

Уведомление будет представлять собой объект со следующими полями:

-`slot: <u64>` - Соответствующий слот.

- `ошибка: <объект | null>` — Ошибка, если что-то пошло не так, при публикации уведомления, в противном случае — null.
- `блок: <объект | null>` — блочный объект, как показано в HTTP-методе [getBlock](jsonrpc-api.md#getblock) RPC.

```json
{
  "jsonrpc": "2.0",
  "method": "blockNotification",
  "params": {
    "result": {
      "context": {
        "slot": 112301554
      },
      "value": {
        "slot": 112301554,
        "block": {
          "previousBlockhash": "GJp125YAN4ufCSUvZJVdCyWQJ7RPWMmwxoyUQySydZA",
          "blockhash": "6ojMHjctdqfB55JDpEpqfHnP96fiaHEcvzEQ2NNcxzHP",
          "parentSlot": 112301553,
          "transactions": [
            {
              "transaction": [
                "OpltwoUvWxYi1P2U8vbIdE/aPntjYo5Aa0VQ2JJyeJE2g9Vvxk8dDGgFMruYfDu8/IfUWb0REppTe7IpAuuLRgIBAAkWnj4KHRpEWWW7gvO1c0BHy06wZi2g7/DLqpEtkRsThAXIdBbhXCLvltw50ZnjDx2hzw74NVn49kmpYj2VZHQJoeJoYJqaKcvuxCi/2i4yywedcVNDWkM84Iuw+cEn9/ROCrXY4qBFI9dveEERQ1c4kdU46xjxj9Vi+QXkb2Kx45QFVkG4Y7HHsoS6WNUiw2m4ffnMNnOVdF9tJht7oeuEfDMuUEaO7l9JeUxppCvrGk3CP45saO51gkwVYEgKzhpKjCx3rgsYxNR81fY4hnUQXSbbc2Y55FkwgRBpVvQK7/+clR4Gjhd3L4y+OtPl7QF93Akg1LaU9wRMs5nvfDFlggqI9PqJl+IvVWrNRdBbPS8LIIhcwbRTkSbqlJQWxYg3Bo2CTVbw7rt1ZubuHWWp0mD/UJpLXGm2JprWTePNULzHu67sfqaWF99LwmwjTyYEkqkRt1T0Je5VzHgJs0N5jY4iIU9K3lMqvrKOIn/2zEMZ+ol2gdgjshx+sphIyhw65F3J/Dbzk04LLkK+CULmN571Y+hFlXF2ke0BIuUG6AUF+4214Cu7FXnqo3rkxEHDZAk0lRrAJ8X/Z+iwuwI5cgbd9uHXZaGT2cvhRs7reawctIXtX1s3kTqM9YV+/wCpDLAp8axcEkaQkLDKRoWxqp8XLNZSKial7Rk+ELAVVKWoWLRXRZ+OIggu0OzMExvVLE5VHqy71FNHq4gGitkiKYNFWSLIE4qGfdFLZXy/6hwS+wq9ewjikCpd//C9BcCL7Wl0iQdUslxNVCBZHnCoPYih9JXvGefOb9WWnjGy14sG9j70+RSVx6BlkFELWwFvIlWR/tHn3EhHAuL0inS2pwX7ZQTAU6gDVaoqbR2EiJ47cKoPycBNvHLoKxoY9AZaBjPl6q8SKQJSFyFd9n44opAgI6zMTjYF/8Ok4VpXEESp3QaoUyTI9sOJ6oFP6f4dwnvQelgXS+AEfAsHsKXxGAIUDQENAgMEBQAGBwgIDg8IBJCER3QXl1AVDBADCQoOAAQLERITDAjb7ugh3gOuTy==",
                "base64"
              ],
              "meta": {
                "err": null,
                "status": {
                  "Ok": null
                },
                "fee": 5000,
                "preBalances": [
                  1758510880,
                  2067120,
                  1566000,
                  1461600,
                  2039280,
                  2039280,
                  1900080,
                  1865280,
                  0,
                  3680844220,
                  2039280
                ],
                "postBalances": [
                  1758505880,
                  2067120,
                  1566000,
                  1461600,
                  2039280,
                  2039280,
                  1900080,
                  1865280,
                  0,
                  3680844220,
                  2039280
                ],
                "innerInstructions": [
                  {
                    "index": 0,
                    "instructions": [
                      {
                        "programIdIndex": 13,
                        "accounts": [
                          1,
                          15,
                          3,
                          4,
                          2,
                          14
                        ],
                        "data": "21TeLgZXNbtHXVBzCaiRmH"
                      },
                      {
                        "programIdIndex": 14,
                        "accounts": [
                          3,
                          4,
                          1
                        ],
                        "data": "6qfC8ic7Aq99"
                      },
                      {
                        "programIdIndex": 13,
                        "accounts": [
                          1,
                          15,
                          3,
                          5,
                          2,
                          14
                        ],
                        "data": "21TeLgZXNbsn4QEpaSEr3q"
                      },
                      {
                        "programIdIndex": 14,
                        "accounts": [
                          3,
                          5,
                          1
                        ],
                        "data": "6LC7BYyxhFRh"
                      }
                    ]
                  },
                  {
                    "index": 1,
                    "instructions": [
                      {
                        "programIdIndex": 14,
                        "accounts": [
                          4,
                          3,
                          0
                        ],
                        "data": "7aUiLHFjSVdZ"
                      },
                      {
                        "programIdIndex": 19,
                        "accounts": [
                          17,
                          18,
                          16,
                          9,
                          11,
                          12,
                          14
                        ],
                        "data": "8kvZyjATKQWYxaKR1qD53V"
                      },
                      {
                        "programIdIndex": 14,
                        "accounts": [
                          9,
                          11,
                          18
                        ],
                        "data": "6qfC8ic7Aq99"
                      }
                    ]
                  }
                ],
                "logMessages": [
                  "Program QMNeHCGYnLVDn1icRAfQZpjPLBNkfGbSKRB83G5d8KB invoke [1]",
                  "Program QMWoBmAyJLAsA1Lh9ugMTw2gciTihncciphzdNzdZYV invoke [2]"
                ],
                "preTokenBalances": [
                  {
                    "accountIndex": 4,
                    "mint": "iouQcQBAiEXe6cKLS85zmZxUqaCqBdeHFpqKoSz615u",
                    "uiTokenAmount": {
                      "uiAmount": null,
                      "decimals": 6,
                      "amount": "0",
                      "uiAmountString": "0"
                    },
                    "owner": "LieKvPRE8XeX3Y2xVNHjKlpAScD12lYySBVQ4HqoJ5op"
                  },
                  {
                    "accountIndex": 5,
                    "mint": "iouQcQBAiEXe6cKLS85zmZxUqaCqBdeHFpqKoSz615u",
                    "uiTokenAmount": {
                      "uiAmount": 11513.0679,
                      "decimals": 6,
                      "amount": "11513067900",
                      "uiAmountString": "11513.0679"
                    },
                    "owner": "rXhAofQCT7NN9TUqigyEAUzV1uLL4boeD8CRkNBSkYk"
                  },
                  {
                    "accountIndex": 10,
                    "mint": "Saber2gLauYim4Mvftnrasomsv6NvAuncvMEZwcLpD1",
                    "uiTokenAmount": {
                      "uiAmount": null,
                      "decimals": 6,
                      "amount": "0",
                      "uiAmountString": "0"
                    },
                    "owner": "CL9wkGFT3SZRRNa9dgaovuRV7jrVVigBUZ6DjcgySsCU"
                  },
                  {
                    "accountIndex": 11,
                    "mint": "Saber2gLauYim4Mvftnrasomsv6NvAuncvMEZwcLpD1",
                    "uiTokenAmount": {
                      "uiAmount": 15138.514093,
                      "decimals": 6,
                      "amount": "15138514093",
                      "uiAmountString": "15138.514093"
                    },
                    "owner": "LieKvPRE8XeX3Y2xVNHjKlpAScD12lYySBVQ4HqoJ5op"
                  }
                ],
                "postTokenBalances": [
                  {
                    "accountIndex": 4,
                    "mint": "iouQcQBAiEXe6cKLS85zmZxUqaCqBdeHFpqKoSz615u",
                    "uiTokenAmount": {
                      "uiAmount": null,
                      "decimals": 6,
                      "amount": "0",
                      "uiAmountString": "0"
                    },
                    "owner": "LieKvPRE8XeX3Y2xVNHjKlpAScD12lYySBVQ4HqoJ5op"
                  },
                  {
                    "accountIndex": 5,
                    "mint": "iouQcQBAiEXe6cKLS85zmZxUqaCqBdeHFpqKoSz615u",
                    "uiTokenAmount": {
                      "uiAmount": 11513.103028,
                      "decimals": 6,
                      "amount": "11513103028",
                      "uiAmountString": "11513.103028"
                    },
                    "owner": "rXhAofQCT7NN9TUqigyEAUzV1uLL4boeD8CRkNBSkYk"
                  },
                  {
                    "accountIndex": 10,
                    "mint": "Saber2gLauYim4Mvftnrasomsv6NvAuncvMEZwcLpD1",
                    "uiTokenAmount": {
                      "uiAmount": null,
                      "decimals": 6,
                      "amount": "0",
                      "uiAmountString": "0"
                    },
                    "owner": "CL9wkGFT3SZRRNa9dgaovuRV7jrVVigBUZ6DjcgySsCU"
                  },
                  {
                    "accountIndex": 11,
                    "mint": "Saber2gLauYim4Mvftnrasomsv6NvAuncvMEZwcLpD1",
                    "uiTokenAmount": {
                      "uiAmount": 15489.767829,
                      "decimals": 6,
                      "amount": "15489767829",
                      "uiAmountString": "15489.767829"
                    },
                    "owner": "BeiHVPRE8XeX3Y2xVNrSsTpAScH94nYySBVQ4HqgN9at"
                  }
                ],
                "rewards": []
              }
            }
          ],
          "blockTime": 1639926816,
          "blockHeight": 101210751
        },
        "err": null
      }
    },
    "subscription": 14
  }
}
```

### blockUnsubscribe

Отписаться от уведомлений о блокировке

#### Параметры:

- `<integer>` - идентификатор подписки для отмены

#### Результаты:

- `<bool>` - сообщение об успешной отмене подписки

#### Пример:

Запрос:

```json
{"jsonrpc":"2.0", "id":1, "method":"blockUnsubscribe", "params":[0]}
```

Ответ:

```json
{"jsonrpc": "2.0","result": true,"id": 1}
```

### logsSubscribe

Подпишитесь на журнал транзакций

#### Параметры:

- `filter: <string>|<object>` - критерии фильтрации журналов для получения результатов по типу учетной записи; в настоящее время поддерживается:
  - "все" - подписываться на все транзакции, кроме простых транзакций голосования
  - "allWithVotes" - подписка на все транзакции, включая простые транзакции голосования
  - `{ "mentions": [ <string> ] }` - подписываться на все транзакции, в которых упоминается предоставленный Pubkey (как строка в кодировке base-58)
- `<object>` - (необязательный) объект конфигурации, содержащий следующие необязательные поля:
  - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment)

#### Результаты:

- `<integer>` - идентификатор подписки \(необходим для отказа от подписки\)

#### Пример:

Запрос:

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "logsSubscribe",
  "params": [
    {
      "mentions": [ "11111111111111111111111111111111" ]
    },
    {
      "commitment": "finalized"
    }
  ]
}
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "logsSubscribe",
  "params": [ "all" ]
}
```

Результат:

```json
{"jsonrpc": "2.0","result": 24040,"id": 1}
```

#### Формат уведомлений:

Уведомление будет представлять собой объект JSON RpcResponse со значением, равным:

- `signature: <string>` — подпись транзакции в кодировке base58.
- `err: <объект | null>` - Ошибка, если транзакция не удалась, null, если транзакция прошла успешно. [Определения TransactionError](https://github.com/solana-labs/solana/blob/c0c60386544ec9a9ec7119229f37386d9f070523/sdk/src/transaction/error.rs#L13)
- `logs: <массив | null>` — массив сообщений журнала, которые инструкции транзакции выводят во время выполнения, нуль, если симуляция не удалась до того, как транзакция смогла быть выполнена (например, из-за недопустимого хэша блока или ошибки проверки подписи)

Пример:

```json
{
  "jsonrpc": "2.0",
  "method": "logsNotification",
  "params": {
    "result": {
      "context": {
        "slot": 5208469
      },
      "value": {
        "signature": "5h6xBEauJ3PK6SWCZ1PGjBvj8vDdWG3KpwATGy1ARAXFSDwt8GFXM7W5Ncn16wmqokgpiKRLuS83KUxyZyv2sUYv",
        "err": null,
        "logs": [
          "BPF program 83astBRguLMdt2h5U1Tpdq5tjFoJ6noeGwaY3mDLVcri success"
        ]
      }
    },
    "subscription": 24040
  }
}
```

### logsUnsubscribe

Отписаться от регистрации транзакций

#### Параметры:

- `<integer>` - идентификатор подписки для отмены

#### Результаты:

- `<bool>` - сообщение об успешной отмене подписки

#### Пример:

Запрос:

```json
{"jsonrpc":"2.0", "id":1, "method":"logsUnsubscribe", "params":[0]}
```

Результат:

```json
{"jsonrpc": "2.0","result": true,"id": 1}
```

### programSubscribe

Подпишитесь на программу, чтобы получать уведомления об изменении ламповых портов или данных для данной учетной записи, принадлежащей программе.

#### Параметры:

- `<string>` - Pubkey program_id, как строка в кодировке base-58
- `<object>` - (необязательный) объект конфигурации, содержащий следующие необязательные поля:
  - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment)
  - `encoding: <string>` - кодировка данных Пользователя, либо "base58" (*slow*), "base64", "base64+zstd" или "jsonParsed".
    Кодирование «jsonParsed» пытается использовать анализаторы состояния для конкретных программ, чтобы возвращать более удобочитаемые и явные данные о состоянии учетной записи. Если запрашивается "jsonParsed", но синтаксический анализатор не может быть найден, поле возвращается к кодировке base64, обнаруживаемой, когда поле `data` имеет тип `<string>`.
  - (необязательно) `filters: <массив>` - фильтровать результаты, используя различные [объекты фильтра] (jsonrpc-api.md#filters); учетная запись должна соответствовать всем критериям фильтрации, чтобы быть включенной в результаты

#### Результаты:

- `<integer>` - идентификатор подписки \(необходим для отказа от подписки\)

#### Пример:

Запрос:

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "programSubscribe",
  "params": [
    "11111111111111111111111111111111",
    {
      "encoding": "base64",
      "commitment": "finalized"
    }
  ]
}
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "programSubscribe",
  "params": [
    "11111111111111111111111111111111",
    {
      "encoding": "jsonParsed"
    }
  ]
}
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "programSubscribe",
  "params": [
    "11111111111111111111111111111111",
    {
      "encoding": "base64",
      "filters": [
        {
          "dataSize": 80
        }
      ]
    }
  ]
}
```

Результат:

```json
{"jsonrpc": "2.0","result": 24040,"id": 1}
```

#### Формат уведомлений:

Формат уведомления представляет собой <b>**одиночный**</b> объект учетной записи программы, как показано в HTTP-методе [getProgramAccounts](jsonrpc-api.md#getProgramAccounts) RPC.

Кодировка Base58:

```json
{
  "jsonrpc": "2.0",
  "method": "programNotification",
  "params": {
    "result": {
      "context": {
        "slot": 5208469
      },
      "value": {
        "pubkey": "H4vnBqifaSACnKa7acsxstsY1iV1bvJNxsCY7enrd1hq",
        "account": {
          "data": ["11116bv5nS2h3y12kD1yUKeMZvGcKLSjQgX6BeV7u1FrjeJcKfsHPXHRDEHrBesJhZyqnnq9qJeUuF7WHxiuLuL5twc38w2TXNLxnDbjmuR", "base58"],
          "executable": false,
          "lamports": 33594,
          "owner": "11111111111111111111111111111111",
          "rentEpoch": 636
        },
      }
    },
    "subscription": 24040
  }
}
```

Кодировка Parsed-JSON:

```json
{
  "jsonrpc": "2.0",
  "method": "programNotification",
  "params": {
    "result": {
      "context": {
        "slot": 5208469
      },
      "value": {
        "pubkey": "H4vnBqifaSACnKa7acsxstsY1iV1bvJNxsCY7enrd1hq",
        "account": {
          "data": {
             "program": "nonce",
             "parsed": {
                "type": "initialized",
                "info": {
                   "authority": "Bbqg1M4YVVfbhEzwA9SpC9FhsaG83YMTYoR4a8oTDLX",
                   "blockhash": "LUaQTmM7WbMRiATdMMHaRGakPtCkc2GHtH57STKXs6k",
                   "feeCalculator": {
                      "lamportsPerSignature": 5000
                   }
                }
             }
          },
          "executable": false,
          "lamports": 33594,
          "owner": "11111111111111111111111111111111",
          "rentEpoch": 636
        },
      }
    },
    "subscription": 24040
  }
}
```

### programUnsubscribe

Отказ от подписки на уведомления об изменении учетной записи, принадлежащей программе

#### Параметры:

- `<integer>` - идентификатор учетной записи Подписка для отмены

#### Результаты:

- `<bool>` - сообщение об успешной отмене подписки

#### Пример:

Запрос:

```json
{"jsonrpc":"2.0", "id":1, "method":"programUnsubscribe", "params":[0]}
```

Результат:

```json
{"jsonrpc": "2.0","result": true,"id": 1}
```

### signatureSubscribe

Подпишитесь на подпись транзакции, чтобы получать уведомление, когда транзакция подтверждена. В `signatureNotification` подписка автоматически отменяется.

#### Параметры:

- `<string>` - подпись транзакции в виде строки в кодировке base-58.
- `<объект>` - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment)

#### Результаты:

- `integer` - идентификатор подписки \(необходим для отписки\)

#### Пример:

Запрос:

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "signatureSubscribe",
  "params": [
    "2EBVM6cB8vAAD93Ktr6Vd8p67XPbQzCJX47MpReuiCXJAtcjaxpvWpcg9Ege1Nr5Tk3a2GFrByT7WPBjdsTycY9b"
  ]
}

{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "signatureSubscribe",
  "params": [
    "2EBVM6cB8vAAD93Ktr6Vd8p67XPbQzCJX47MpReuiCXJAtcjaxpvWpcg9Ege1Nr5Tk3a2GFrByT7WPBjdsTycY9b",
    {
      "commitment": "finalized"
    }
  ]
}
```

Результат:

```json
{"jsonrpc": "2.0","result": 0,"id": 1}
```

#### Формат уведомлений:

Уведомление будет представлять собой объект RpcResponse JSON со значением, содержащим объект с:

- `err: <объект | null>` - Ошибка, если транзакция не удалась, null, если транзакция прошла успешно. [Определения TransactionError] (https://github.com/solana-labs/solana/blob/c0c60386544ec9a9ec7119229f37386d9f070523/sdk/src/transaction/error.rs#L13)

Пример:

```json
{
  "jsonrpc": "2.0",
  "method": "signatureNotification",
  "params": {
    "result": {
      "context": {
        "slot": 5207624
      },
      "value": {
        "err": null
      }
    },
    "subscription": 24006
  }
}
```

### signatureUnsubscribe

Отписаться от уведомления о подтверждении подписи

#### Параметры:

- `<integer>` - идентификатор подписки для отмены

#### Результаты:

- `<bool>` - сообщение об успешной отмене подписки

#### Пример:

Запрос:

```json
{"jsonrpc":"2.0", "id":1, "method":"signatureUnsubscribe", "params":[0]}
```

Результат:

```json
{"jsonrpc": "2.0","result": true,"id": 1}
```

### slotSubscribe

Подпишитесь, чтобы получать уведомления каждый раз, когда слот обрабатывается валидатором.

#### Параметры:

Никто

#### Результаты:

- `integer` - идентификатор подписки \(необходим для отписки\)

#### Пример:

Запрос:

```json
{"jsonrpc":"2.0", "id":1, "method":"slotSubscribe"}
```

Результат:

```json
{"jsonrpc": "2.0","result": 0,"id": 1}
```

#### Формат уведомлений:

Уведомление будет представлять собой объект со следующими полями:

- `parent: <u64>` - родительский слот
- `root: <u64>` - Текущий корневой слот
- `slot: <u64>` - Новое установленное значение слота

Пример:

```json
{
  "jsonrpc": "2.0",
  "method": "slotNotification",
  "params": {
    "result": {
      "parent": 75,
      "root": 44,
      "slot": 76
    },
    "subscription": 0
  }
}
```

### slotUnsubscribe

Отписаться от уведомлений о слотах

#### Параметры:

- `<integer>` - идентификатор подписки для отмены

#### Результаты:

- `<bool>` - сообщение об успешной отмене подписки

#### Пример:

Запрос:

```json
{"jsonrpc":"2.0", "id":1, "method":"slotUnsubscribe", "params":[0]}
```

Результат:

```json
{"jsonrpc": "2.0","result": true,"id": 1}
```

### slotsUpdatesSubscribe - Unstable

**Эта подписка нестабильна; формат этой подписки может измениться в
будущее, и оно может не всегда поддерживаться**

Подпишитесь, чтобы получать уведомления от валидатора о различных обновлениях
на каждом слоте

#### Параметры:

Никто

#### Результаты:

- `integer` - идентификатор подписки \(необходим для отписки\)

#### Пример:

Запрос:

```json
{"jsonrpc":"2.0", "id":1, "method":"slotsUpdatesSubscribe"}
```

Результат:

```json
{"jsonrpc": "2.0","result": 0,"id": 1}
```

#### Формат уведомлений:

Уведомление будет представлять собой объект со следующими полями:

- `parent: <u64>` - родительский слот
- `slot: <u64>` - Недавно обновленный слот
- `timestamp: <i64>` - временная метка обновления Unix.
- `type: <string>` - Тип обновления, один из:
  - "firstShredReceived"
  - "completed"
  - "createdBank"
  - "frozen"
  - "dead"
  - "optimisticConfirmation"
  - "root"

```bash
{
  "jsonrpc": "2.0",
  "method": "slotsUpdatesNotification",
  "params": {
    "result": {
      "parent": 75,
      "slot": 76,
      "timestamp": 1625081266243,
      "type": "optimisticConfirmation"
    },
    "subscription": 0
  }
}
```

### slotsUpdatesUnsubscribe

Отписаться от уведомлений об обновлениях слотов

#### Параметры:

- `<integer>` - идентификатор подписки для отмены

#### Результаты:

- `<bool>` - сообщение об успешной отмене подписки

#### Пример:

Запрос:

```json
{"jsonrpc":"2.0", "id":1, "method":"slotsUpdatesUnsubscribe", "params":[0]}
```

Результат:

```json
{"jsonrpc": "2.0","result": true,"id": 1}
```

### rootSubscribe

Подпишитесь, чтобы получать уведомления каждый раз, когда валидатором устанавливается новый корень.

#### Параметры:

Никто

#### Результаты:

- `integer` - идентификатор подписки \(необходим для отписки\)

#### Пример:

Запрос:

```json
{"jsonrpc":"2.0", "id":1, "method":"rootSubscribe"}
```

Результат:

```json
{"jsonrpc": "2.0","result": 0,"id": 1}
```

#### Формат уведомлений:

Результатом является последний номер корневого слота.

```json
{
  "jsonrpc": "2.0",
  "method": "rootNotification",
  "params": {
    "result": 42,
    "subscription": 0
  }
}
```

### rootUnsubscribe

Отписаться от корневых уведомлений

#### Параметры:

- `<integer>` - идентификатор подписки для отмены

#### Результаты:

- `<bool>` - сообщение об успешной отмене подписки

#### Пример:

Запрос:

```json
{"jsonrpc":"2.0", "id":1, "method":"rootUnsubscribe", "params":[0]}
```

Результат:

```json
{"jsonrpc": "2.0","result": true,"id": 1}
```

### voteSubscribe - Unstable, disabled by default

**Эта подписка нестабильна и доступна только в том случае, если валидатор был запущен с флагом `--rpc-pubsub-enable-vote-subscription`. Формат этой подписки может измениться в будущем**

Подпишитесь, чтобы получать уведомления каждый раз, когда в сплетнях будет наблюдаться новое голосование.
Эти голоса являются предварительным консенсусом, поэтому нет никакой гарантии, что эти голоса будут
войти в реестр.

#### Параметры:

Никто

#### Результаты:

- `integer` - идентификатор подписки \(необходим для отписки\)

#### Пример:

Запрос:

```json
{"jsonrpc":"2.0", "id":1, "method":"voteSubscribe"}
```

Результат:

```json
{"jsonrpc": "2.0","result": 0,"id": 1}
```

#### Формат уведомлений:

Уведомление будет представлять собой объект со следующими полями:

- `hash: <string>` - Хэш голосования
- `slots: <массив>` - слоты, охваченные голосованием, в виде массива целых чисел u64.
- `отметка времени: <i64 | null>` - Отметка времени голосования

```json
{
  "jsonrpc": "2.0",
  "method": "voteNotification",
  "params": {
    "result": {
      "hash": "8Rshv2oMkPu5E4opXTRyuyBeZBqQ4S477VG26wUTFxUM",
      "slots": [1, 2],
      "timestamp": null
    },
    "subscription": 0
  }
}
```

### voteUnsubscribe

Отписаться от уведомлений о голосовании

#### Параметры:

- `<integer>` - идентификатор подписки для отмены

#### Результаты:

- `<bool>` - сообщение об успешной отмене подписки

#### Пример:

Запрос:

```json
{"jsonrpc":"2.0", "id":1, "method":"voteUnsubscribe", "params":[0]}
```

Response:

```json
{"jsonrpc": "2.0","result": true,"id": 1}
```

## Устаревшие методы JSON RPC API

### getConfirmedBlock

**УСТАРЕЛО: вместо этого используйте [getBlock](jsonrpc-api.md#getblock)**
Ожидается, что этот метод будет удален в solana-core v2.0.

Возвращает идентификационную информацию и информацию о транзакции для подтвержденного блока в леджере.

#### Параметры:

- `<u64>` - слот, как целое число u64
- `<object>` - (необязательный) объект конфигурации, содержащий следующие необязательные поля:
  - (необязательно) `encoding: <string>` - кодировка для каждой возвращаемой транзакции, либо "json", "jsonParsed", "base58" (*slow*), "base64". Если параметр не указан, используется кодировка по умолчанию "json".
    Кодировка «jsonParsed» пытается использовать синтаксические анализаторы инструкций для конкретных программ, чтобы возвращать более удобочитаемые и явные данные в списке «transaction.message.instructions». Если запрашивается "jsonParsed", но синтаксический анализатор не может быть найден, инструкция возвращается к обычной кодировке JSON (поля "accounts", "data" и "programIdIndex").
  - (необязательно) `transactionDetails: <string>` - уровень детализации возвращаемой транзакции: "полный", "подписи" или "нет". Если параметр не указан, уровень детализации по умолчанию — «полный».
  - (необязательно) `rewards: bool` - заполнять ли массив `rewards`. Если параметр не указан, по умолчанию включены награды.
  - (необязательно) [Обязательство](jsonrpc-api.md#configuring-state-commitment); «обработано» не поддерживается. Если параметр не указан, по умолчанию используется значение "finalized".

#### Результаты:

Поле результата будет объектом со следующими полями:

- `<null>` - если указанный блок не подтвержден
- `<object>` - если блок подтвержден, объект со следующими полями:
  - `blockhash: <string>` - хеш блока этого блока в виде строки в кодировке base-58
  - `previousBlockhash: <string>` - хеш блока родителя этого блока в виде строки в кодировке base-58; если родительский блок недоступен из-за очистки реестра, это поле вернет «11111111111111111111111111111111»
  - `parentSlot: <u64>` - индекс слота родителя этого блока
  - `transactions: <массив>` - присутствует, если запрашиваются "полные" детали транзакции; массив объектов JSON, содержащий:
    - `transaction: <object|[string,encoding]>` - объект Transaction либо в формате JSON, либо в закодированных двоичных данных, в зависимости от параметра кодирования
    - `meta: <object>` - объект метаданных состояния транзакции, содержащий `null` или:
      - `ошибка: <объект | null>` - Ошибка, если транзакция не удалась, null, если транзакция прошла успешно. [Определения TransactionError] (https://github.com/solana-labs/solana/blob/c0c60386544ec9a9ec7119229f37386d9f070523/sdk/src/transaction/error.rs#L13)
      - `fee: <u64>` - комиссия, взимаемая за эту транзакцию, в виде целого числа u64
      - `preBalances: <массив>` - массив балансов счетов u64 до обработки транзакции
      - `postBalances: <массив>` - массив балансов аккаунтов u64 после обработки транзакции
      - `innerInstructions: <array|null>` - Список внутренних инструкций или `null`, если запись внутренних инструкций не была включена во время этой транзакции
      - `preTokenBalances: <array|undefined>` - Список балансов токенов до того, как транзакция была обработана или пропущена, если запись баланса токенов еще не была включена во время этой транзакции.
      - `postTokenBalances: <array|undefined>` - Список балансов токенов после обработки транзакции или опущен, если запись баланса токенов еще не была включена во время этой транзакции
      - `logMessages: <array|null>` - массив строковых сообщений журнала или `null`, если запись сообщений журнала не была включена во время этой транзакции
      - УСТАРЕЛО: `статус: <объект>` - Статус транзакции
        - `"Ok": <null>` - Транзакция прошла успешно
        - `"Err": <ERR>` - Транзакция не удалась с TransactionError
  - `signatures: <массив>` - присутствует, если для деталей транзакции запрашиваются "подписи"; массив строк подписей, соответствующих порядку транзакций в блоке
  - `rewards: <массив>` - присутствует, если запрошены награды; массив объектов JSON, содержащий:
    — `pubkey: <string>` — открытый ключ в виде строки в кодировке base-58 учетной записи, получившей вознаграждение.
    - `lamports: <i64>`- количество наградных лампортов, зачисленных или списанных со счета, как i64
    - `postBalance:<u64>` - баланс аккаунта в лэмпортах после применения вознаграждения
    - `rewardType: <string|undefined>` - тип вознаграждения: "комиссия", "аренда", "голосование", "стейкинг"
    - `комиссия: <u8|undefined>` - комиссия за голосовой счет, когда вознаграждение было зачислено, присутствует только для вознаграждений за голосование и стекинг
  - `Время_блока: <i64 | null>` - предполагаемое время производства в виде временной метки Unix (в секундах с эпохи Unix). ноль, если недоступен

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc": "2.0","id":1,"method":"getConfirmedBlock","params":[430, {"encoding": "json","transactionDetails":"full","rewards":false}]}
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "blockTime": null,
    "blockhash": "3Eq21vXNB5s86c62bVuUfTeaMif1N2kUqRPBmGRJhyTA",
    "parentSlot": 429,
    "previousBlockhash": "mfcyqEXB3DnHXki6KjjmZck6YjmZLvpAByy2fj4nh6B",
    "transactions": [
      {
        "meta": {
          "err": null,
          "fee": 5000,
          "innerInstructions": [],
          "logMessages": [],
          "postBalances": [
            499998932500,
            26858640,
            1,
            1,
            1
          ],
          "postTokenBalances": [],
          "preBalances": [
            499998937500,
            26858640,
            1,
            1,
            1
          ],
          "preTokenBalances": [],
          "status": {
            "Ok": null
          }
        },
        "transaction": {
          "message": {
            "accountKeys": [
              "3UVYmECPPMZSCqWKfENfuoTv51fTDTWicX9xmBD2euKe",
              "AjozzgE83A3x1sHNUR64hfH7zaEBWeMaFuAN9kQgujrc",
              "SysvarS1otHashes111111111111111111111111111",
              "SysvarC1ock11111111111111111111111111111111",
              "Vote111111111111111111111111111111111111111"
            ],
            "header": {
              "numReadonlySignedAccounts": 0,
              "numReadonlyUnsignedAccounts": 3,
              "numRequiredSignatures": 1
            },
            "instructions": [
              {
                "accounts": [
                  1,
                  2,
                  3,
                  0
                ],
                "data": "37u9WtQpcm6ULa3WRQHmj49EPs4if7o9f1jSRVZpm2dvihR9C8jY4NqEwXUbLwx15HBSNcP1",
                "programIdIndex": 4
              }
            ],
            "recentBlockhash": "mfcyqEXB3DnHXki6KjjmZck6YjmZLvpAByy2fj4nh6B"
          },
          "signatures": [
            "2nBhEBYYvfaAe16UMNqRHre4YNSskvuYgx3M6E4JP1oDYvZEJHvoPzyUidNgNX5r9sTyN1J9UxtbCXy2rqYcuyuv"
          ]
        }
      }
    ]
  },
  "id": 1
}
```

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc": "2.0","id":1,"method":"getConfirmedBlock","params":[430, "base64"]}
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "blockTime": null,
    "blockhash": "3Eq21vXNB5s86c62bVuUfTeaMif1N2kUqRPBmGRJhyTA",
    "parentSlot": 429,
    "previousBlockhash": "mfcyqEXB3DnHXki6KjjmZck6YjmZLvpAByy2fj4nh6B",
    "rewards": [],
    "transactions": [
      {
        "meta": {
          "err": null,
          "fee": 5000,
          "innerInstructions": [],
          "logMessages": [],
          "postBalances": [
            499998932500,
            26858640,
            1,
            1,
            1
          ],
          "postTokenBalances": [],
          "preBalances": [
            499998937500,
            26858640,
            1,
            1,
            1
          ],
          "preTokenBalances": [],
          "status": {
            "Ok": null
          }
        },
        "transaction": [
          "AVj7dxHlQ9IrvdYVIjuiRFs1jLaDMHixgrv+qtHBwz51L4/ImLZhszwiyEJDIp7xeBSpm/TX5B7mYzxa+fPOMw0BAAMFJMJVqLw+hJYheizSoYlLm53KzgT82cDVmazarqQKG2GQsLgiqktA+a+FDR4/7xnDX7rsusMwryYVUdixfz1B1Qan1RcZLwqvxvJl4/t3zHragsUp0L47E24tAFUgAAAABqfVFxjHdMkoVmOYaR1etoteuKObS21cc1VbIQAAAAAHYUgdNXR0u3xNdiTr072z2DVec9EQQ/wNo1OAAAAAAAtxOUhPBp2WSjUNJEgfvy70BbxI00fZyEPvFHNfxrtEAQQEAQIDADUCAAAAAQAAAAAAAACtAQAAAAAAAAdUE18R96XTJCe+YfRfUp6WP+YKCy/72ucOL8AoBFSpAA==",
          "base64"
        ]
      }
    ]
  },
  "id": 1
}
```

Подробнее о возвращаемых данных:
- Структура транзакции
- Структура внутренних инструкций
- Структура балансов токенов

### getConfirmedBlocks

**УСТАРЕЛО: вместо этого используйте getBlocks**
Ожидается, что этот метод будет удален в solana-core v2.0.

Возвращает список подтвержденных блоков между двумя слотами

#### Параметры:

- `<u64>` - начальный_слот, как целое число u64
- `<u64>` - (необязательно) end_slot, как целое число u64
- (необязательно) Обязательство; «обработано» не поддерживается. Если параметр не указан, по умолчанию используется значение "finalized".

#### Результаты:

Поле результата будет массивом целых чисел u64 со списком подтвержденных блоков между start_slot и end_slot, если он указан, или последним подтвержденным блоком включительно. Максимально допустимый диапазон составляет 500 000 слотов.

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc": "2.0","id":1,"method":"getConfirmedBlocks","params":[5, 10]}
'
```

Результат:

```json
{"jsonrpc":"2.0","result":[5,6,7,8,9,10],"id":1}
```

### getConfirmedBlocksWithLimit

**УСТАРЕЛО: вместо этого используйте getBlocksWithLimit**
Ожидается, что этот метод будет удален в solana-core v2.0.

Возвращает список подтвержденных блоков, начиная с данного слота.

#### Параметры:

- `<u64>` - начальный_слот, как целое число u64
- `<u64>` - ограничение, как целое число u64
- (необязательно) Обязательство; «обработано» не поддерживается. Если параметр не указан, по умолчанию используется значение "finalized".

#### Результаты:

Поле результата будет представлять собой массив целых чисел u64 со списком подтвержденных блоков, начиная с `start_slot`, и заканчивая блоками `limit` включительно.

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc": "2.0","id":1,"method":"getConfirmedBlocksWithLimit","params":[5, 3]}
'
```

Результат:

```json
{"jsonrpc":"2.0","result":[5,6,7],"id":1}
```

### getConfirmedSignaturesForAddress2

**УСТАРЕЛО: вместо этого используйте getSignaturesForAddress**
Ожидается, что этот метод будет удален в solana-core v2.0.

Возвращает подписи для подтвержденных транзакций, которые включают указанный адрес в
их список `accountKeys`. Возвращает подписи назад во времени от
предоставленная подпись или последний подтвержденный блок

#### Параметры:

- `<string>` - адрес аккаунта в виде строки в кодировке base-58

- `<object>` - (необязательный) Объект конфигурации, содержащий следующие поля:
  
  - `limit: <number>` - (необязательно) максимальное количество возвращаемых подписей транзакций (от 1 до 1000, по умолчанию: 1000).
  
  - `before: <string>` - (необязательно) начать поиск в обратном направлении от этой подписи транзакции.
    
    ```
    If not provided the search starts from the top of the highest max confirmed block.
    ```
  
  - `until: <string>` - (необязательно) поиск до этой подписи транзакции, если она была найдена до достижения лимита.
  
  - (необязательно) Обязательство; «обработано» не поддерживается. Если параметр не указан, по умолчанию используется значение "finalized".

#### Результаты:

Поле результата будет представлять собой массив информации о подписи транзакции, упорядоченный
от самой новой к самой старой транзакции:

- `<объект>`
  - `signature: <string>` - подпись транзакции в виде строки в кодировке base-58.
  - `slot: <u64>` - Слот, содержащий блок с транзакцией
  - `ошибка: <объект | null>` - Ошибка, если транзакция не удалась, null, если транзакция прошла успешно. [Определения TransactionError](https://github.com/solana-labs/solana/blob/c0c60386544ec9a9ec7119229f37386d9f070523/sdk/src/transaction/error.rs#L13)
  - `memo: <string |null>` - памятка, связанная с транзакцией, ноль, если памятки нет.
  - `Время_блока: <i64 | null>` - предполагаемое время производства, как временная метка Unix (секунды с эпохи Unix), когда транзакция была обработана. ноль, если он недоступен.

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {
    "jsonrpc": "2.0",
    "id": 1,
    "method": "getConfirmedSignaturesForAddress2",
    "params": [
      "Vote111111111111111111111111111111111111111",
      {
        "limit": 1
      }
    ]
  }
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": [
    {
      "err": null,
      "memo": null,
      "signature": "5h6xBEauJ3PK6SWCZ1PGjBvj8vDdWG3KpwATGy1ARAXFSDwt8GFXM7W5Ncn16wmqokgpiKRLuS83KUxyZyv2sUYv",
      "slot": 114,
      "blockTime": null
    }
  ],
  "id": 1
}
```

### getConfirmedTransaction

**УСТАРЕЛО: вместо этого используйте getTransaction**
Ожидается, что этот метод будет удален в solana-core v2.0.

Возвращает детали транзакции для подтвержденной транзакции

#### Параметры:

- `<string>` - подпись транзакции в виде строки в кодировке base-58.
- `<object>` - (необязательный) объект конфигурации, содержащий следующие необязательные поля:
  - (необязательно) `encoding: <string>` - кодировка для каждой возвращаемой транзакции, либо "json", "jsonParsed", "base58" (*slow*), "base64". Если параметр не указан, используется кодировка по умолчанию "json".
    Кодировка «jsonParsed» пытается использовать синтаксические анализаторы инструкций для конкретных программ, чтобы возвращать более удобочитаемые и явные данные в списке «transaction.message.instructions». Если запрашивается "jsonParsed", но синтаксический анализатор не может быть найден, инструкция возвращается к обычной кодировке JSON (поля "accounts", "data" и "programIdIndex").
  - (необязательно) Обязательство; «обработано» не поддерживается. Если параметр не указан, по умолчанию используется значение "finalized".

#### Результаты:

- `<null>` - если транзакция не найдена или не подтверждена
- `<объект>` - если транзакция подтверждена, объект со следующими полями:
  - `slot: <u64>` - слот, в котором эта транзакция была обработана
  - `transaction: <object|[string,encoding]>` - объект Transaction либо в формате JSON, либо в закодированных двоичных данных, в зависимости от параметра кодирования
  - `Время_блока: <i64 | null>` - предполагаемое время производства в виде временной метки Unix (секунды с эпохи Unix), когда транзакция была обработана. ноль, если недоступен
  - `мета: <объект | null>` - объект метаданных статуса транзакции:
    - `ошибка: <объект | null>` - Ошибка, если транзакция не удалась, null, если транзакция прошла успешно. [Определения TransactionError](https://docs.rs/solana-sdk/VERSION_FOR_DOCS_RS/solana_sdk/transaction/enum.TransactionError.html)
    - `fee: <u64>` - комиссия, взимаемая за эту транзакцию, в виде целого числа u64
    - `preBalances: <массив>` - массив балансов счетов u64 до обработки транзакции
    - `postBalances: <массив>` - массив балансов аккаунтов u64 после обработки транзакции
    - `innerInstructions: <array|null>` - Список внутренних инструкций или `null`, если запись внутренних инструкций не была включена во время этой транзакции
    - `preTokenBalances: <array|undefined>` - Список балансов токенов до того, как транзакция была обработана или пропущена, если запись баланса токенов еще не была включена во время этой транзакции.
    - `postTokenBalances: <array|undefined>` - Список балансов токенов после обработки транзакции или опущен, если запись баланса токенов еще не была включена во время этой транзакции
    - `logMessages: <array|null>` - массив строковых сообщений журнала или `null`, если запись сообщений журнала не была включена во время этой транзакции
    - УСТАРЕЛО: `статус: <объект>` - Статус транзакции
      - `"Ok": <null>` - Транзакция прошла успешно
      - `"Err": <ERR>` - Транзакция не удалась с TransactionError

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {
    "jsonrpc": "2.0",
    "id": 1,
    "method": "getConfirmedTransaction",
    "params": [
      "2nBhEBYYvfaAe16UMNqRHre4YNSskvuYgx3M6E4JP1oDYvZEJHvoPzyUidNgNX5r9sTyN1J9UxtbCXy2rqYcuyuv",
      "json"
    ]
  }
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "meta": {
      "err": null,
      "fee": 5000,
      "innerInstructions": [],
      "postBalances": [
        499998932500,
        26858640,
        1,
        1,
        1
      ],
      "postTokenBalances": [],
      "preBalances": [
        499998937500,
        26858640,
        1,
        1,
        1
      ],
      "preTokenBalances": [],
      "status": {
        "Ok": null
      }
    },
    "slot": 430,
    "transaction": {
      "message": {
        "accountKeys": [
          "3UVYmECPPMZSCqWKfENfuoTv51fTDTWicX9xmBD2euKe",
          "AjozzgE83A3x1sHNUR64hfH7zaEBWeMaFuAN9kQgujrc",
          "SysvarS1otHashes111111111111111111111111111",
          "SysvarC1ock11111111111111111111111111111111",
          "Vote111111111111111111111111111111111111111"
        ],
        "header": {
          "numReadonlySignedAccounts": 0,
          "numReadonlyUnsignedAccounts": 3,
          "numRequiredSignatures": 1
        },
        "instructions": [
          {
            "accounts": [
              1,
              2,
              3,
              0
            ],
            "data": "37u9WtQpcm6ULa3WRQHmj49EPs4if7o9f1jSRVZpm2dvihR9C8jY4NqEwXUbLwx15HBSNcP1",
            "programIdIndex": 4
          }
        ],
        "recentBlockhash": "mfcyqEXB3DnHXki6KjjmZck6YjmZLvpAByy2fj4nh6B"
      },
      "signatures": [
        "2nBhEBYYvfaAe16UMNqRHre4YNSskvuYgx3M6E4JP1oDYvZEJHvoPzyUidNgNX5r9sTyN1J9UxtbCXy2rqYcuyuv"
      ]
    }
  },
  "blockTime": null,
  "id": 1
}
```

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {
    "jsonrpc": "2.0",
    "id": 1,
    "method": "getConfirmedTransaction",
    "params": [
      "2nBhEBYYvfaAe16UMNqRHre4YNSskvuYgx3M6E4JP1oDYvZEJHvoPzyUidNgNX5r9sTyN1J9UxtbCXy2rqYcuyuv",
      "base64"
    ]
  }
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "meta": {
      "err": null,
      "fee": 5000,
      "innerInstructions": [],
      "postBalances": [
        499998932500,
        26858640,
        1,
        1,
        1
      ],
      "postTokenBalances": [],
      "preBalances": [
        499998937500,
        26858640,
        1,
        1,
        1
      ],
      "preTokenBalances": [],
      "status": {
        "Ok": null
      }
    },
    "slot": 430,
    "transaction": [
      "AVj7dxHlQ9IrvdYVIjuiRFs1jLaDMHixgrv+qtHBwz51L4/ImLZhszwiyEJDIp7xeBSpm/TX5B7mYzxa+fPOMw0BAAMFJMJVqLw+hJYheizSoYlLm53KzgT82cDVmazarqQKG2GQsLgiqktA+a+FDR4/7xnDX7rsusMwryYVUdixfz1B1Qan1RcZLwqvxvJl4/t3zHragsUp0L47E24tAFUgAAAABqfVFxjHdMkoVmOYaR1etoteuKObS21cc1VbIQAAAAAHYUgdNXR0u3xNdiTr072z2DVec9EQQ/wNo1OAAAAAAAtxOUhPBp2WSjUNJEgfvy70BbxI00fZyEPvFHNfxrtEAQQEAQIDADUCAAAAAQAAAAAAAACtAQAAAAAAAAdUE18R96XTJCe+YfRfUp6WP+YKCy/72ucOL8AoBFSpAA==",
      "base64"
    ]
  },
  "id": 1
}
```

### getFeeCalculatorForBlockhash

**УСТАРЕЛО: вместо этого используйте isBlockhashValid или getFeeForMessage**
Ожидается, что этот метод будет удален в solana-core v2.0.

Возвращает калькулятор комиссии, связанный с хэшем блока запроса, или `null`, если срок действия хэша блока истек.

#### Параметры:

- `<string>` - запрашивать хэш блока как строку в кодировке Base58.
- `<объект>` - (необязательно) Обязательство

#### Результаты:

Результатом будет объект RpcResponse JSON со значением, равным:

- `<null>` - если истек срок действия блок-хэша запроса
- `<object>` - иначе объект JSON, содержащий:
  - `feeCalculator: <object>`, объект `FeeCalculator`, описывающий ставку комиссии кластера в запрашиваемом хэше блока

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {
    "jsonrpc": "2.0",
    "id": 1,
    "method": "getFeeCalculatorForBlockhash",
    "params": [
      "GJxqhuxcgfn5Tcj6y3f8X4FeCDd2RQ6SnEMo1AAxrPRZ"
    ]
  }
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "context": {
      "slot": 221
    },
    "value": {
      "feeCalculator": {
        "lamportsPerSignature": 5000
      }
    }
  },
  "id": 1
}
```

### getFeeRateGovernor

Возвращает информацию о регуляторе ставки сбора из корневого банка.

#### Параметры:

Никто

#### Результаты:

Поле «результат» будет «объектом» со следующими полями:

- `burnPercent: <u8>`, процент сборов, собранных для уничтожения
- `maxLamportsPerSignature: <u64>`, максимальное значение `lamportsPerSignature`, которое может быть достигнуто для следующего слота
- `minLamportsPerSignature: <u64>`, наименьшее значение `lamportsPerSignature`, которое может быть достигнуто для следующего слота
- `targetLamportsPerSignature: <u64>`, желаемая ставка комиссии для кластера
- `targetSignaturesPerSlot: <u64>`, желаемая скорость подписи для кластера

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"getFeeRateGovernor"}
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "context": {
      "slot": 54
    },
    "value": {
      "feeRateGovernor": {
        "burnPercent": 50,
        "maxLamportsPerSignature": 100000,
        "minLamportsPerSignature": 5000,
        "targetLamportsPerSignature": 10000,
        "targetSignaturesPerSlot": 20000
      }
    }
  },
  "id": 1
}
```

### getFees

****УСТАРЕЛО: вместо этого используйте [getFeeForMessage](jsonrpc-api.md#getfeeformessage)**
Ожидается, что этот метод будет удален в solana-core v2.0.

Возвращает хэш последнего блока из леджера, график сборов, который можно использовать для расчета стоимости отправки транзакции с его использованием, и последний слот, в котором хеш блока будет действительным.

#### Параметры:

- `<объект>` - (необязательно) Обязательство

#### Результаты:

Результатом будет объект JSON RpcResponse со значением, установленным на объект JSON со следующими полями:

- `blockhash: <string>` - хэш в виде строки в кодировке base-58.
- `feeCalculator: <object>` - объект FeeCalculator, график комиссий для этого хэша блока
- `lastValidSlot: <u64>` - УСТАРЕЛО - это значение является неточным и на него нельзя полагаться
- `lastValidBlockHeight: <u64>` - последняя [высота блока](../../terminology.md#block-height), при которой хэш блока будет действительным

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"getFees"}
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "context": {
      "slot": 1
    },
    "value": {
      "blockhash": "CSymwgTNX1j3E4qhKfJAUE41nBWEwXufoYryPbkde5RR",
      "feeCalculator": {
        "lamportsPerSignature": 5000
      },
      "lastValidSlot": 297,
      "lastValidBlockHeight": 296
    }
  },
  "id": 1
}
```

### getRecentBlockhash

**УСТАРЕЛО: вместо этого используйте [getFeeForMessage](jsonrpc-api.md#getfeeformessage)**
Ожидается, что этот метод будет удален в solana-core v2.0.

Возвращает хэш последнего блока из реестра и график сборов, который можно использовать для расчета стоимости отправки транзакции с его использованием.

#### Параметры:

- `<объект>` - (необязательно) Обязательство

#### Результаты:

RpcResponse, содержащий объект JSON, состоящий из строкового хэша блока и объекта JSON FeeCalculator.

- `RpcResponse<object>` — объект JSON RpcResponse с полем `value`, установленным на объект JSON, включая:
- `blockhash: <string>` - хэш в виде строки в кодировке base-58.
- `feeCalculator: <object>` - объект FeeCalculator, график комиссий для этого хэша блока

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"getRecentBlockhash"}
'
```

Результат:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "context": {
      "slot": 1
    },
    "value": {
      "blockhash": "CSymwgTNX1j3E4qhKfJAUE41nBWEwXufoYryPbkde5RR",
      "feeCalculator": {
        "lamportsPerSignature": 5000
      }
    }
  },
  "id": 1
}
```

### getSnapshotSlot

**УСТАРЕЛО: вместо этого используйте getHighestSnapshotSlot**
Ожидается, что этот метод будет удален в solana-core v2.0.

Возвращает самый высокий слот, для которого у узла есть снимок.

#### Параметры:

Никто

#### Результаты:

- `<u64>` - слот для снимков

#### Пример:

Запрос:

```bash
curl http://localhost:8899 -X POST -H "Content-Type: application/json" -d '
  {"jsonrpc":"2.0","id":1, "method":"getSnapshotSlot"}
'
```

Результат:

```json
{"jsonrpc":"2.0","result":100,"id":1}
```

Результат, когда узел не имеет моментального снимка:

```json
{"jsonrpc":"2.0","error":{"code":-32008,"message":"No snapshot"},"id":1}
```
