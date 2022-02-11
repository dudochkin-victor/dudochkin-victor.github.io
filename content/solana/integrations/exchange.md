+++
title = "Добавьте Солану на свою биржу"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

В этом руководстве описывается, как добавить собственный токен Solana SOL на вашу биржу криптовалют.

## Настройка узла

Мы настоятельно рекомендуем настроить по крайней мере два узла на полноценных компьютерах/облачных экземплярах, быстро выполнить обновление до более новых версий и следить за операциями службы с помощью встроенного инструмента мониторинга.

Эта настройка позволяет:

- иметь самоуправляемый шлюз к бета-кластеру основной сети Solana для получения данных и отправки транзакций по снятию средств
- иметь полный контроль над тем, сколько исторических данных блока сохраняется
- поддерживать доступность вашего сервиса, даже если один узел выйдет из строя

Узлы Solana требуют относительно высокой вычислительной мощности для обработки наших быстрых блоков и высокого TPS. Конкретные требования см. в [рекомендациях по оборудованию](../running-validator/validator-reqs/).

Чтобы запустить узел API:

1. [Установите набор инструментов командной строки Solana](../cli/install-solana-cli-tools/)
2. Запустите валидатор как минимум со следующими параметрами:

```bash
solana-validator \
  --ledger <LEDGER_PATH> \
  --identity <VALIDATOR_IDENTITY_KEYPAIR> \
  --entrypoint <CLUSTER_ENTRYPOINT> \
  --expected-genesis-hash <EXPECTED_GENESIS_HASH> \
  --rpc-port 8899 \
  --no-voting \
  --enable-rpc-transaction-history \
  --limit-ledger-size \
  --known-validator <VALIDATOR_ADDRESS> \
  --only-known-rpc
```

Настройте `--ledger` на желаемое место хранения леджера, а `--rpc-port` на порт, который вы хотите открыть.

Параметры --entrypoint и --expected-genesis-hash относятся к кластеру, к которому вы присоединяетесь.
[Текущие параметры для бета-версии основной сети](../clusters.md#example-solana-validator-command-line-2)

Параметр `--limit-ledger-size` позволяет вам указать, сколько леджеров [клочков](../terminology.md#shred) ваш узел сохраняет на диске. Если вы не укажете этот параметр, валидатор будет хранить всю книгу до тех пор, пока не закончится место на диске. Значение по умолчанию пытается сохранить использование диска леджера ниже 500 ГБ. Можно запросить большее или меньшее использование диска, добавив аргумент к `--limit-ledger-size`, если это необходимо. Проверьте `solana-validator --help` для предельного значения по умолчанию, используемого `--limit-ledger-size`. Дополнительная информация о выборе пользовательского предельного значения [доступна здесь](https://github.com/solana-labs/solana/blob/583cec922b6107e0f85c7e14cb5e642bc7dfb340/core/src/ledger_cleanup_service.rs#L15-L26).

Указание одного или нескольких параметров `--known-validator` может защитить вас от загрузки с вредоносного снимка. [Подробнее о ценности загрузки с известными валидаторами](../running-validator/validator-start.md#known-validators)

Необязательные параметры для рассмотрения:

- `--private-rpc` предотвращает публикацию вашего порта RPC для использования другими узлами
- `--rpc-bind-address` позволяет указать другой IP-адрес для привязки порта RPC

### Автоматический перезапуск и мониторинг

Мы рекомендуем настроить каждый из ваших узлов на автоматический перезапуск при выходе, чтобы вы пропустили как можно меньше данных. Запуск программного обеспечения solana в качестве службы systemd — отличный вариант.

Для мониторинга мы предоставляем [`solana-watchtower`](https://github.com/solana-labs/solana/blob/master/watchtower/README.md), который может отслеживать ваш валидатор и обнаруживать с помощью `solana-watchtower` процесс валидатора неисправен. Его можно напрямую настроить для оповещения через Slack, Telegram, Discord или Twillio. Для получения подробной информации запустите `solana-watchtower --help`.

```bash
solana-watchtower --validator-identity <YOUR VALIDATOR IDENTITY>
```

#### Объявления о новых выпусках программного обеспечения

Мы часто выпускаем новое программное обеспечение (около 1 выпуска в неделю).
Иногда более новые версии включают несовместимые изменения протокола, что требует своевременного обновления программного обеспечения во избежание ошибок при обработке блоков.

Наши официальные объявления о выпуске всех видов выпусков (обычных и безопасных) передаются через канал разногласий под названием [`#mb-announcement`](https://discord.com/channels/428295358100013066/669406841830244375) (`mb` означает `основная сеть-бета`).

Как и в случае стейкинговых валидаторов, мы ожидаем, что любые валидаторы, управляемые биржей, будут обновлены при первой же возможности в течение рабочего дня или двух после обычного объявления о выпуске. Для выпусков, связанных с безопасностью, могут потребоваться более срочные действия.

### Непрерывность реестра

По умолчанию каждый из ваших узлов будет загружаться со снимка, предоставленного одним из ваших известных валидаторов. Этот моментальный снимок отражает текущее состояние цепочки, но не содержит полного исторического реестра. Если один из ваших узлов выходит и загружается с нового снимка, в реестре на этом узле может быть пробел. Чтобы предотвратить эту проблему, добавьте параметр --no-snapshot-fetch к вашей команде solana-validator, чтобы получать исторические данные реестра вместо моментального снимка.

Не передавайте параметр `--no-snapshot-fetch` при начальной загрузке, так как невозможно полностью загрузить узел из исходного блока. Вместо этого сначала загрузитесь со снимка, а затем добавьте параметр --no-snapshot-fetch для перезагрузки.

Важно отметить, что объем исторической книги, доступной вашим узлам из остальной части сети, ограничен в любой момент времени. После запуска, если ваши валидаторы испытывают значительное время простоя, они могут быть не в состоянии догнать сеть и должны будут загрузить новый снимок от известного валидатора. При этом ваши валидаторы теперь будут иметь пробел в своих исторических данных реестра, который не может быть заполнен.

### Минимизация воздействия порта валидатора

Валидатор требует, чтобы различные порты UDP и TCP были открыты для входящего трафика от всех других валидаторов Solana. Хотя это наиболее эффективный режим работы, и он настоятельно рекомендуется, можно ограничить валидатор, чтобы он требовал только входящий трафик от одного другого валидатора Solana.

Сначала добавьте аргумент `--restricted-repair-only-mode`. Это заставит валидатор работать в ограниченном режиме, когда он не будет получать запросы от остальных валидаторов, и вместо этого ему нужно будет постоянно опрашивать другие валидаторы на наличие блоков. Валидатор будет передавать только UDP-пакеты другим валидаторам, используя порты _Gossip_ и _ServeR_ ("обслуживание ремонта"), и только получать UDP-пакеты на свои порты _Gossip_ и _Repair_.

Порт _Gossip_ является двунаправленным и позволяет вашему валидатору оставаться на связи с остальной частью кластера. Ваш валидатор передает на _ServeR_ запросы на восстановление для получения новых блоков из остальной части сети, поскольку Turbine теперь отключена. После этого ваш валидатор получит ответы на ремонт порта _Repair_ от других валидаторов.

Чтобы дополнительно ограничить валидатор запросом блоков только от одного или нескольких валидаторов, сначала определите публичный ключ идентификации для этого валидатора и добавьте аргументы `--gossip-pull-validator PUBKEY --repair-validator PUBKEY` для каждого PUBKEY. Это приведет к тому, что ваш валидатор будет истощать ресурсы каждого добавленного вами валидатора, поэтому делайте это экономно и только после консультации с целевым валидатором.

Теперь ваш валидатор должен обмениваться данными только с явно указанными валидаторами и только на портах _Gossip_, _Repair_ и _ServeR_.

## Настройка депозитных счетов

Учетные записи Solana не требуют какой-либо инициализации в сети; как только они содержат некоторое SOL, они существуют. Чтобы настроить депозитный счет для своей биржи, просто создайте пару ключей Solana, используя любой из наших [инструментов кошелька](../wallet-guide/cli/).

Мы рекомендуем использовать уникальный депозитный счет для каждого из ваших пользователей.

Аккаунты Solana должны быть освобождены от арендной платы за счет содержания [арендной платы] (developing/programming-model/accounts.md#rent) за 2 года в SOL. Чтобы найти минимальный безвозмездный баланс для ваших депозитных счетов, запросите конечную точку [`getMinimumBalanceForRentExemption`](developing/clients/jsonrpc-api.md#getminimumbalanceforrentexemption):

```bash
curl -X POST -H "Content-Type: application/json" -d '{"jsonrpc": "2.0","id":1,"method":"getMinimumBalanceForRentExemption","params":[0]}' localhost:8899

{"jsonrpc":"2.0","result":890880,"id":1}
```

### Автономные учетные записи

Вы можете хранить ключи для одной или нескольких учетных записей в автономном режиме для большей безопасности. Если это так, вам нужно будет перенести SOL на горячие аккаунты, используя наши [автономные методы](../offline-signing/).

## Прослушивание депозитов

Когда пользователь хочет внести SOL на вашу биржу, попросите его отправить перевод на соответствующий адрес депозита.

### Опрос блоков

Чтобы отслеживать все депозитные счета для вашей биржи, опрашивайте каждый подтвержденный блок и проверяйте интересующие адреса, используя службу JSON-RPC вашего узла Solana API.

- Чтобы определить, какие блоки доступны, отправьте запрос [`getConfirmedBlocks`](developing/clients/jsonrpc-api.md#getconfirmedblocks), передав последний уже обработанный блок в качестве параметра start-slot:

```bash
curl -X POST -H "Content-Type: application/json" -d '{"jsonrpc": "2.0","id":1,"method":"getConfirmedBlocks","params":[5]}' localhost:8899

{"jsonrpc":"2.0","result":[5,6,8,9,11],"id":1}
```

Не каждый слот производит блок, поэтому в последовательности целых чисел могут быть пробелы.

- Для каждого блока запросите его содержимое с помощью запроса [`getConfirmedBlock`](developing/clients/jsonrpc-api.md#getconfirmedblock):

```bash
curl -X POST -H "Content-Type: application/json" -d '{"jsonrpc": "2.0","id":1,"method":"getConfirmedBlock","params":[5, "json"]}' localhost:8899

{
  "jsonrpc": "2.0",
  "result": {
    "blockhash": "2WcrsKSVANoe6xQHKtCcqNdUpCQPQ3vb6QTgi1dcE2oL",
    "parentSlot": 4,
    "previousBlockhash": "7ZDoGW83nXgP14vnn9XhGSaGjbuLdLWkQAoUQ7pg6qDZ",
    "rewards": [],
    "transactions": [
      {
        "meta": {
          "err": null,
          "fee": 5000,
          "postBalances": [
            2033973061360,
            218099990000,
            42000000003
          ],
          "preBalances": [
            2044973066360,
            207099990000,
            42000000003
          ],
          "status": {
            "Ok": null
          }
        },
        "transaction": {
          "message": {
            "accountKeys": [
              "Bbqg1M4YVVfbhEzwA9SpC9FhsaG83YMTYoR4a8oTDLX",
              "47Sbuv6jL7CViK9F2NMW51aQGhfdpUu7WNvKyH645Rfi",
              "11111111111111111111111111111111"
            ],
            "header": {
              "numReadonlySignedAccounts": 0,
              "numReadonlyUnsignedAccounts": 1,
              "numRequiredSignatures": 1
            },
            "instructions": [
              {
                "accounts": [
                  0,
                  1
                ],
                "data": "3Bxs3zyH82bhpB8j",
                "programIdIndex": 2
              }
            ],
            "recentBlockhash": "7GytRgrWXncJWKhzovVoP9kjfLwoiuDb3cWjpXGnmxWh"
          },
          "signatures": [
            "dhjhJp2V2ybQGVfELWM1aZy98guVVsxRCB5KhNiXFjCBMK5KEyzV8smhkVvs3xwkAug31KnpzJpiNPtcD5bG1t6"
          ]
        }
      }
    ]
  },
  "id": 1
}
```

Поля preBalances и postBalances позволяют отслеживать изменения баланса в каждой учетной записи без необходимости анализировать всю транзакцию. Они перечисляют начальный и конечный баланс каждой учетной записи в [lamports](../terminology.md#lamport), проиндексированные в списке `accountKeys`. Например, если адрес депозита с процентами `47Sbuv6jL7CViK9F2NMW51aQGhfdpUu7WNvKyH645Rfi`, эта транзакция представляет собой перевод 218099990000 - 207099990000 = 11000000000 лампортов = 11 SOL.

Если вам нужна дополнительная информация о типе транзакции или других особенностях, вы можете запросить блок у RPC в двоичном формате и проанализировать его с помощью нашего [Rust SDK](https://github.com/solana-labs/solana) или [Javascript SDK](https://github.com/solana-labs/solana-web3.js).

### История адресов

Вы также можете запросить историю транзакций определенного адреса. Как правило, это _не_ жизнеспособный метод для отслеживания всех ваших депозитных адресов во всех слотах, но он может быть полезен для проверки нескольких учетных записей за определенный период времени.

- Отправьте запрос [`getConfirmedSignaturesForAddress2`](developing/clients/jsonrpc-api.md#getconfirmedsignaturesforaddress2) на узел API:

```bash
curl -X POST -H "Content-Type: application/json" -d '{"jsonrpc": "2.0","id":1,"method":"getConfirmedSignaturesForAddress2","params":["6H94zdiaYfRfPfKjYLjyr2VFBg6JHXygy84r3qhc3NsC", {"limit": 3}]}' localhost:8899

{
  "jsonrpc": "2.0",
  "result": [
    {
      "err": null,
      "memo": null,
      "signature": "35YGay1Lwjwgxe9zaH6APSHbt9gYQUCtBWTNL3aVwVGn9xTFw2fgds7qK5AL29mP63A9j3rh8KpN1TgSR62XCaby",
      "slot": 114
    },
    {
      "err": null,
      "memo": null,
      "signature": "4bJdGN8Tt2kLWZ3Fa1dpwPSEkXWWTSszPSf1rRVsCwNjxbbUdwTeiWtmi8soA26YmwnKD4aAxNp8ci1Gjpdv4gsr",
      "slot": 112
    },
    {
      "err": null,
      "memo": null,
      "signature": "dhjhJp2V2ybQGVfELWM1aZy98guVVsxRCB5KhNiXFjCBMK5KEyzV8smhkVvs3xwkAug31KnpzJpiNPtcD5bG1t6",
      "slot": 108
    }
  ],
  "id": 1
}
```

- Для каждой возвращенной подписи получите сведения о транзакции, отправив запрос [`getConfirmedTransaction`](developing/clients/jsonrpc-api.md#getconfirmedtransaction):

```bash
curl -X POST -H "Content-Type: application/json" -d '{"jsonrpc": "2.0","id":1,"method":"getConfirmedTransaction","params":["dhjhJp2V2ybQGVfELWM1aZy98guVVsxRCB5KhNiXFjCBMK5KEyzV8smhkVvs3xwkAug31KnpzJpiNPtcD5bG1t6", "json"]}' localhost:8899

// Result
{
  "jsonrpc": "2.0",
  "result": {
    "slot": 5,
    "transaction": {
      "message": {
        "accountKeys": [
          "Bbqg1M4YVVfbhEzwA9SpC9FhsaG83YMTYoR4a8oTDLX",
          "47Sbuv6jL7CViK9F2NMW51aQGhfdpUu7WNvKyH645Rfi",
          "11111111111111111111111111111111"
        ],
        "header": {
          "numReadonlySignedAccounts": 0,
          "numReadonlyUnsignedAccounts": 1,
          "numRequiredSignatures": 1
        },
        "instructions": [
          {
            "accounts": [
              0,
              1
            ],
            "data": "3Bxs3zyH82bhpB8j",
            "programIdIndex": 2
          }
        ],
        "recentBlockhash": "7GytRgrWXncJWKhzovVoP9kjfLwoiuDb3cWjpXGnmxWh"
      },
      "signatures": [
        "dhjhJp2V2ybQGVfELWM1aZy98guVVsxRCB5KhNiXFjCBMK5KEyzV8smhkVvs3xwkAug31KnpzJpiNPtcD5bG1t6"
      ]
    },
    "meta": {
      "err": null,
      "fee": 5000,
      "postBalances": [
        2033973061360,
        218099990000,
        42000000003
      ],
      "preBalances": [
        2044973066360,
        207099990000,
        42000000003
      ],
      "status": {
        "Ok": null
      }
    }
  },
  "id": 1
}
```

## Отправка вывода средств

Чтобы удовлетворить запрос пользователя на отзыв SOL, вы должны сгенерировать транзакцию передачи Solana и отправить ее на узел API для пересылки в ваш кластер.

### Синхронный

Отправка синхронной передачи в кластер Solana позволяет легко убедиться, что передача выполнена успешно и завершена кластером.

Инструмент командной строки Solana предлагает простую команду «solana transfer» для создания, отправки и подтверждения транзакций перевода. По умолчанию этот метод будет ждать и отслеживать ход выполнения на stderr, пока транзакция не будет завершена кластером. Если транзакция не удалась, он сообщит обо всех ошибках транзакции.

```bash
solana transfer <USER_ADDRESS> <AMOUNT> --allow-unfunded-recipient --keypair <KEYPAIR> --url http://localhost:8899
```

[Solana Javascript SDK](https://github.com/solana-labs/solana-web3.js) предлагает аналогичный подход для экосистемы JS. Используйте `SystemProgram` для создания транзакции перевода и отправьте ее с помощью метода `sendAndConfirmTransaction`.

### Асинхронный

Для большей гибкости вы можете отправлять переводы на снятие средств асинхронно. В этих случаях вы обязаны убедиться, что транзакция прошла успешно и была завершена кластером.

**Примечание.** Каждая транзакция содержит [недавний хэш блока](developing/programming-model/transactions.md#blockhash-format), чтобы указать на ее жизнеспособность. Крайне важно дождаться истечения срока действия этого блок-хэша перед повторной попыткой перевода вывода средств, который не был подтвержден или завершен кластером. В противном случае вы рискуете получить двойную трату. Подробнее о истечение срока действия хэша см. ниже.

Во-первых, получите последний хэш блока, используя конечную точку [`getFees`](developing/clients/jsonrpc-api.md#getfees) или команду CLI:

```bash
solana fees --url http://localhost:8899
```

В инструменте командной строки передайте аргумент `--no-wait`, чтобы отправить перевод асинхронно, и включите свой недавний хеш-блок с аргументом `--blockhash`:

```bash
solana transfer <USER_ADDRESS> <AMOUNT> --no-wait --allow-unfunded-recipient --blockhash <RECENT_BLOCKHASH> --keypair <KEYPAIR> --url http://localhost:8899
```

Вы также можете создать, подписать и сериализовать транзакцию вручную и отправить ее в кластер с помощью JSON-RPC [конечная точка sendTransaction] (developing/clients/jsonrpc-api.md#sendtransaction).

#### Подтверждение транзакции и завершенность

Получите статус пакета транзакций, используя конечную точку [`getSignatureStatuses` JSON-RPC](developing/clients/jsonrpc-api.md#getsignaturestatuses).
Поле «подтверждения» сообщает, сколько [подтвержденных блоков] (../terminology.md#confirmed-block) прошло с момента обработки транзакции. Если `confirmations: null`, это [finalized](../terminology.md#finality).

```bash
curl -X POST -H "Content-Type: application/json" -d '{"jsonrpc":"2.0", "id":1, "method":"getSignatureStatuses", "params":[["5VERv8NMvzbJMEkV8xnrLkEaWRtSz9CosKDYjCJjBRnbJLgp8uirBgmQpjKhoR4tjF3ZpRzrFmBV6UjKdiSZkQUW", "5j7s6NiJS3JAkvgkoc18WVAsiSaci2pxB2A6ueCJP4tprA2TFg9wSyTLeYouxPBJEMzJinENTkpA52YStRW5Dia7"]]}' http://localhost:8899

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
        }
      },
      {
        "slot": 48,
        "confirmations": null,
        "err": null,
        "status": {
          "Ok": null
        }
      }
    ]
  },
  "id": 1
}
```

#### Срок действия блокчейна

Вы можете проверить, действителен ли конкретный хэш блока, отправив запрос [`getFeeCalculatorForBlockhash`](developing/clients/jsonrpc-api.md#getfeecalculatorforblockhash) с хэшем блока в качестве параметра. Если значение ответа `null`, срок действия хэш-блока истек, и транзакция вывода средств с использованием этого хэш-блока никогда не должна быть успешной.

### Проверка адресов счетов, предоставленных пользователями, для снятия средств

Поскольку снятие средств является необратимым, может быть хорошей практикой проверить адрес учетной записи, предоставленный пользователем, перед авторизацией вывода, чтобы предотвратить случайную потерю средств пользователя.

#### Базовая проверка

Solana обращается к 32-байтовому массиву, закодированному биткойнским алфавитом base58. Это приводит к текстовой строке ASCII, соответствующей следующему регулярному выражению:

```
[1-9A-HJ-NP-Za-km-z]{32,44}
```

Эта проверка сама по себе недостаточна, поскольку адреса Solana не суммируются, поэтому опечатки не могут быть обнаружены. Для дальнейшей проверки ввода пользователя можно декодировать строку и подтвердить, что длина результирующего массива байтов равна 32. Однако есть некоторые адреса, которые можно декодировать до 32 байт, несмотря на опечатку, такую ​​как отсутствие одного символа, перевернутые символы и игнорирование регистра.

#### Расширенная проверка

Из-за уязвимости к опечаткам, описанной выше, рекомендуется запрашивать баланс для возможных адресов вывода средств и предлагать пользователю подтвердить свои намерения, если обнаруживается ненулевой баланс.

#### Действительная проверка публичного ключа ed25519

Адрес обычной учетной записи в Solana представляет собой закодированную в Base58 строку 256-битного открытого ключа ed25519. Не все битовые шаблоны являются действительными открытыми ключами для кривой ed25519, поэтому можно гарантировать, что предоставленные пользователем адреса учетных записей являются по крайней мере правильными открытыми ключами ed25519.

#### Ява

Вот пример Java проверки введенного пользователем адреса как действительного открытого ключа ed25519:

В следующем примере кода предполагается, что вы используете Maven.

`pom.xml`:

```xml
<repositories>
  ...
  <repository>
    <id>spring</id>
    <url>https://repo.spring.io/libs-release/</url>
  </repository>
</repositories>

...

<dependencies>
  ...
  <dependency>
      <groupId>io.github.novacrypto</groupId>
      <artifactId>Base58</artifactId>
      <version>0.1.3</version>
  </dependency>
  <dependency>
      <groupId>cafe.cryptography</groupId>
      <artifactId>curve25519-elisabeth</artifactId>
      <version>0.1.0</version>
  </dependency>
<dependencies>
```

```java
import io.github.novacrypto.base58.Base58;
import cafe.cryptography.curve25519.CompressedEdwardsY;

public class PubkeyValidator
{
    public static boolean verifyPubkey(String userProvidedPubkey)
    {
        try {
            return _verifyPubkeyInternal(userProvidedPubkey);
        } catch (Exception e) {
            return false;
        }
    }

    public static boolean _verifyPubkeyInternal(String maybePubkey) throws Exception
    {
        byte[] bytes = Base58.base58Decode(maybePubkey);
        return !(new CompressedEdwardsY(bytes)).decompress().isSmallOrder();
    }
}
```

## Минимальные суммы депозита и снятия

Каждый ввод и вывод SOL должен быть больше или равен минимальному необлагаемому арендной платой балансу учетной записи по адресу кошелька (базовая учетная запись SOL, не содержащая данных), в настоящее время: 0,000890880 SOL.

Точно так же каждый депозитный счет должен содержать как минимум этот баланс.

```bash
curl -X POST -H "Content-Type: application/json" -d '{"jsonrpc": "2.0","id":1,"method":"getMinimumBalanceForRentExemption","params":[0]}' localhost:8899

{"jsonrpc":"2.0","result":890880,"id":1}
```

## Поддержка стандарта токенов SPL

[Токен SPL] (https://spl.solana.com/token) — это стандарт для создания и обмена обернутых/синтетических токенов в блокчейне Solana.

Рабочий процесс токена SPL похож на рабочий процесс собственных токенов SOL, но есть несколько отличий, которые будут обсуждаться в этом разделе.

### Монетные дворы токенов

Каждый _type_ токена SPL объявляется путем создания _mint_ учетной записи. В этой учетной записи хранятся метаданные, описывающие характеристики токенов, такие как предложение, количество десятичных знаков и различные органы, контролирующие монетный двор. Каждая учетная запись токена SPL ссылается на связанный с ней монетный двор и может взаимодействовать только с токенами SPL этого типа.

### Установка CLI-инструмента `spl-token`

Учетные записи SPL Token запрашиваются и изменяются с помощью утилиты командной строки `spl-token`. Примеры, представленные в этом разделе, зависят от того, установлен ли он в локальной системе.

`spl-token` распространяется из Rust [crates.io](https://crates.io/crates/spl-token) через утилиту командной строки Rust `cargo`. Последнюю версию `cargo` можно установить с помощью удобной однострочной строки для вашей платформы на [rustup.rs](https://rustup.rs).
После установки `cargo`, `spl-token` можно получить с помощью следующей команды:

```
cargo install spl-token-cli
```

Затем вы можете проверить установленную версию, чтобы проверить

```
spl-token --version
```

Что должно привести к чему-то вроде

```
spl-token-cli 2.0.1
```

### Создание аккаунта

Учетные записи SPL Token имеют дополнительные требования, которых нет у собственных учетных записей системной программы:

1. Учетные записи токенов SPL должны быть созданы до того, как можно будет внести определенное количество токенов. Учетные записи токенов могут быть созданы явно с помощью команды `spl-token create-account` или неявно с помощью команды `spl-token transfer --fund-recipient ...`.
2. Учетные записи с токенами SPL должны оставаться [освобожденными от арендной платы](developing/programming-model/accounts.md#rent-exemption) на время их существования и, следовательно, требуют внесения небольшого количества собственных токенов SOL при создании учетной записи. Для аккаунтов SPL Token v2 эта сумма составляет 0,00203928 SOL (2 039 280 лэмпортов).

#### Командная строка

Чтобы создать учетную запись токена SPL со следующими свойствами:

1. Связан с данным монетным двором
2. Принадлежит паре ключей учетной записи финансирования

```
spl-token create-account <TOKEN_MINT_ADDRESS>
```

#### Пример

```
$ spl-token create-account AkUFCWTXb3w9nY2n6SFJvBV6VwvFUCe4KBMCcgLsa2ir
Creating account 6VzWGL51jLebvnDifvcuEDec17sK6Wupi4gYhm5RzfkV
Signature: 4JsqZEPra2eDTHtHpB4FMWSfk3UgcCVmkKkP7zESZeMrKmFFkDkNd91pKP3vPVVZZPiu5XxyJwS73Vi5WsZL88D7
```

Или создать учетную запись токена SPL с определенной парой ключей:

```
$ solana-keygen new -o token-account.json
$ spl-token create-account AkUFCWTXb3w9nY2n6SFJvBV6VwvFUCe4KBMCcgLsa2ir token-account.json
Creating account 6VzWGL51jLebvnDifvcuEDec17sK6Wupi4gYhm5RzfkV
Signature: 4JsqZEPra2eDTHtHpB4FMWSfk3UgcCVmkKkP7zESZeMrKmFFkDkNd91pKP3vPVVZZPiu5XxyJwS73Vi5WsZL88D7
```

### Проверка баланса счета

#### Командная строка

```
spl-token balance <TOKEN_ACCOUNT_ADDRESS>
```

#### Пример

```
$ solana balance 6VzWGL51jLebvnDifvcuEDec17sK6Wupi4gYhm5RzfkV
0
```

### Переводы токенов

Исходная учетная запись для перевода — это фактическая учетная запись токена, содержащая сумму.

Однако адрес получателя может быть обычной учетной записью кошелька. Если связанная учетная запись токена для данного монетного двора еще не существует для этого кошелька, передача создаст ее при условии, что указан аргумент `--fund-recipient`.

#### Командная строка

```
spl-token transfer <SENDER_ACCOUNT_ADDRESS> <AMOUNT> <RECIPIENT_WALLET_ADDRESS> --fund-recipient
```

#### Пример

```
$ spl-token transfer 6B199xxzw3PkAm25hGJpjj3Wj3WNYNHzDAnt1tEqg5BN 1 6VzWGL51jLebvnDifvcuEDec17sK6Wupi4gYhm5RzfkV
Transfer 1 tokens
  Sender: 6B199xxzw3PkAm25hGJpjj3Wj3WNYNHzDAnt1tEqg5BN
  Recipient: 6VzWGL51jLebvnDifvcuEDec17sK6Wupi4gYhm5RzfkV
Signature: 3R6tsog17QM8KfzbcbdP4aoMfwgo6hBggJDVy7dZPVmH2xbCWjEj31JKD53NzMrf25ChFjY7Uv2dfCDq4mGFFyAj
```

### Депонирование

Поскольку для каждой пары «(кошелек, монетный двор)» требуется отдельная учетная запись в цепочке. Рекомендуется, чтобы адреса для этих учетных записей были получены из депозитных кошельков SOL с использованием схемы [Associated Token Account](https://spl.solana.com/associated-token-account) (ATA) и чтобы _только_ вносились депозиты с адресов ATA. быть принятым.

Мониторинг депозитных транзакций должен следовать описанному выше методу опроса блоков. Каждый новый блок следует сканировать на наличие успешных транзакций, выдающих токен SPL [Transfer](https://github.com/solana-labs/solana-program-library/blob/fc0d6a2db79bd6499f04b9be7ead0c400283845e/token/program/src/instruction.rs#L105) или [TransferChecked](https://github.com/solana-labs/solana-program-library/blob/fc0d6a2db79bd6499f04b9be7ead0c400283845e/token/program/src/instruction.rs#L268)
инструкции, относящиеся к учетным записям пользователей. Возможно, передача инициирована
смарт-контрактом через [вызов кросс-программы](/developing/programming-model/calling-between-programs#cross-program-invocations), поэтому [внутренние инструкции](/terminology#inner-instruction) также должны быть проверены.
Поля `preTokenBalance` и `postTokenBalance` из метаданных транзакции должны затем использоваться для определения фактического изменения баланса.

### Снятие

Адрес для вывода средств, который предоставляет пользователь, должен совпадать с адресом его кошелька SOL.

Перед выполнением вывода перевода биржа должна проверить адрес как описано выше.
Кроме того, этот адрес должен принадлежать Системной программе и не содержать учетных данных. Если на адресе нет баланса SOL, необходимо получить подтверждение пользователя, прежде чем приступать к снятию средств. Все другие адреса для вывода средств должны быть отклонены.

С адреса вывода [Связанная учетная запись токена](https://spl.solana.com/associated-token-account)
(ATA) для правильного монетного двора, и перевод осуществляется на эту учетную запись через [TransferChecked](https://github.com/solana-labs/solana-program-library/blob/fc0d6a2db79bd6499f04b9be7ead0c400283845e/token/program/src/ инструкция.rs#L268) инструкция. Обратите внимание, что возможно, что адрес ATA еще не существует, и в этот момент биржа должна пополнить счет от имени пользователя. Для счетов SPL Token v2 для пополнения счета для вывода средств потребуется 0,00203928 SOL (2 039 280 лэмпортов).

Шаблон команды `spl-token transfer` для вывода:

```
$ spl-token transfer --fund-recipient <exchange token account> <withdrawal amount> <withdrawal address>
```

### Другие соображения

#### Заморозить полномочия

Из соображений соответствия нормативным требованиям организация, выпускающая токены SPL, может при желании иметь «полномочие заморозить» все учетные записи, созданные в связи с его монетным двором. Это позволяет им [замораживать](https://spl.solana.com/token#freezing-accounts) активы в данной учетной записи по своему желанию, делая учетную запись непригодной для использования до тех пор, пока она не будет разморожена.
Если эта функция используется, публичный ключ органа заморозки будет зарегистрирован в учетной записи монетного двора токена SPL.

## Тестирование интеграции

Обязательно протестируйте весь рабочий процесс в Solana devnet и testnet [clusters](../clusters/), прежде чем переходить к рабочей версии mainnet-beta. Devnet является наиболее открытым и гибким и идеально подходит для начальной разработки, в то время как тестовая сеть предлагает более реалистичную конфигурацию кластера. И devnet, и testnet поддерживают сборщик, запустите solana airdrop 1, чтобы получить SOL devnet или testnet для разработки и тестирования.
