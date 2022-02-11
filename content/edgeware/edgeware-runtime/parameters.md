+++
title = "Параметры"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

---
description:
  Эта страница предназначена для перечисления текущих конфигураций сетевых параметров. Они могут быть изменены в процессе управления и могут быть перепроверены с помощью приведенных ниже инструкций.
---

>Эта страница отражает среду выполнения [файл цепочки, доступный для просмотра на Github](https://github.com/hicommonwealth/edgeware-node/blob/master/node/runtime/src/lib.rs), который является конечным источником орган власти. Вы также можете использовать приложения Polkadot.js, чтобы подтвердить текущее значение этих параметров в тестовой или основной сети на [вкладка «Состояние цепи» &gt; Константы &gt; Выберите параметр](https://polkadot.js.org/apps/#/chainstate/constants)

## Счета и транзакции

| **Parameter**           | Value     | Description                                                                                     |
|:----------------------- |:--------- |:----------------------------------------------------------------------------------------------- |
| **Reaping Threshold**   | 0.001 EDG | Минимальный EDG, необходимый на балансе учетной записи для создания или ведения учетной записи. |
| **Transaction Minimum** | 1e-18     | Мин. сумму, которую вы можете отправить на адрес Edgeware.                                      |

## Консенсус

| Parameter               | Value   |
|:----------------------- |:------- |
| **Consensus Mechanism** | AURA    |
| **Finality Gadget**     | GRANDPA |

## Время

Точный код времени выполнения, определяющий константы, связанные со временем блока и производством.

| Edgeware    | Time                    | Slots\* |
|:----------- |:----------------------- |:------- |
| **Block**   | 6 секунд                | 1       |
| **Epoch**   | 60 минут                | 600     |
| **Session** | 60 минут \(600 Blocks\) | 600     |
| **Era**     | 6 часов \(6 sessions\)  | 3600    |

>Этот код взят из [https://github.com/hicommonwealth/edgeware-node/blob/master/node/runtime/src/constants.rs](https://github.com/hicommonwealth/edgeware-node/blob/ мастер/узел/среда выполнения/src/constants.rs)
>
>Она может быть устаревшей на этой странице, обратитесь к ссылке выше, чтобы получить самые последние данные о времени выполнения.


```
    pub const MILLISECS_PER_BLOCK: Moment = 6000;
    pub const SECS_PER_BLOCK: Moment = MILLISECS_PER_BLOCK / 1000;

    pub const SLOT_DURATION: Moment = MILLISECS_PER_BLOCK;

    // 1 in 4 blocks (on average, not counting collisions) will be primary BABE blocks.
    pub const PRIMARY_PROBABILITY: (u64, u64) = (1, 4);

    pub const EPOCH_DURATION_IN_BLOCKS: BlockNumber = 1 * HOURS;
    pub const EPOCH_DURATION_IN_SLOTS: u64 = {
        const SLOT_FILL_RATE: f64 = MILLISECS_PER_BLOCK as f64 / SLOT_DURATION as f64;

        (EPOCH_DURATION_IN_BLOCKS as f64 * SLOT_FILL_RATE) as u64
    };

    // These time units are defined in number of blocks.
    pub const MINUTES: BlockNumber = 60 / (SECS_PER_BLOCK as BlockNumber);
    pub const HOURS: BlockNumber = MINUTES * 60;
    pub const DAYS: BlockNumber = HOURS * 24;
```

{% embed url="https://github.com/hicommonwealth/edgeware-node/blob/master/node/runtime/src/constants.rs" caption="" %}

## Стэйкинг

| **Parameter**                  | Value                              | Description                                                                                                                                                                           |
|:------------------------------ |:---------------------------------- |:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Validator Slots**            | 110                                | Общее количество слотов для активной проверки.                                                                                                                                        |
| **Validator Bonding Duration** | 28 hours                           | Как долго вы сможете разблокировать свои средства после стейкинга                                                                                                                     |
| **Slash Deferral Duration**    | 28 Eras \(7 days\)                 | Предотвращает перекос и «убегание» валидаторов и сокращение их номинаторов без каких-либо последствий для них самих.                                                                  |
| **Slash Cancellation Vote**    | Requires 3/4 of Council to Approve |                                                                                                                                                                                       |
| **Validator Term Duration**    |                                    | Время, в течение которого валидатор находится в наборе после избрания. Обратите внимание, что эта продолжительность может быть сокращена в случае неправильного поведения валидатора. |
| **Nomination Period**          |                                    | Обратный отсчет до выбора нового набора валидаторов по методу Фрагмена.                                                                                                               |

## Демократия \(Референдумы\)

| Parameter                                       | Value                                  | Description                                                                                                                                                                         |
|:----------------------------------------------- |:-------------------------------------- |:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Launch Period**                               | 7 days                                 | Как долго общественность может выбирать, по какому предложению провести референдум. т. е. каждую неделю будет выбираться предложение с наибольшим весом для проведения референдума. |
| **Voting Period**                               | 7 days                                 | Как долго общественность может голосовать на референдуме.                                                                                                                           |
| **Enactment Delay**                             | 8 days                                 | Время, необходимое для успешного проведения референдума в сети.                                                                                                                     |
| **Passing Vote Criteria**                       | Supermajority to pass                  |                                                                                                                                                                                     |
| **Fast Track Voting Period**                    | 3 days                                 | Минимальный период голосования, разрешенный для проведения экстренного референдума.                                                                                                 |
| **Veto**                                        | Not active                             |                                                                                                                                                                                     |
| **Proposal Cancellation Vote**                  | 2/3 of council to Approve Cancellation |                                                                                                                                                                                     |
| **Cool-off Period after Proposal Cancellation** | 7 days                                 | Время, в течение которого действует вето технического комитета, прежде чем предложение может быть представлено снова.                                                               |
| **Min. EDG Deposit to Vote**                    | 100 EDG                                |                                                                                                                                                                                     |

**Взвешивание голосов по времени блокировки**
График увеличения веса представляет собой квадратичную кривую, что означает, что для достижения меньшего пропорционального увеличения веса требуется экспоненциальное увеличение времени блокировки.

| Vote Weight | Locktime                         |
|:----------- |:-------------------------------- |
| 0.1x        | None                             |
| 1x          | 1x Период действия \(8 дней\)    |
| 2x          | 2x Период действия \(16 дней\)   |
| 3x          | 4x Период действия \(32 дня\)    |
| 4x          | 8x Период действия \(64 дня\)    |
| 5x          | 16x Период действия \(128 дней\) |
| 6x          | 32x Период действия \(256 дней\) |

## Выборы в Совет

| **Parameter**             | Value      | Description                                                                                                                                                                                                                                                                                          |
|:------------------------- |:---------- |:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Term Duration**         | 28 days    | Срок полномочий члена совета до следующего тура выборов. Новый член совета избирается каждые 28 дней на основе алгоритма Фрагмена. Если продолжительность срока изменена, текущий срок изменяется, когда `BlockNumber % TermDuration ==0`, после чего будет выбран новый совет \(или член совета?\). |
| **Candidacy Bond**        | 1000 EDG   | Сумма, которую пользователь должен внести, чтобы представить свою кандидатуру.                                                                                                                                                                                                                       |
| **Voting Bond**           | 10 EDG     | Сумма EDG, которую избиратель должен заблокировать, чтобы проголосовать за Совет.                                                                                                                                                                                                                    |
| **Council Member Slots**  | 13 members | Размер совета.                                                                                                                                                                                                                                                                                       |
| **Runners-up Slots**      | 7 members  | Количество слотов, которые будут отображаться как занявшие второе место.                                                                                                                                                                                                                             |
| **Council Voting Period** | ??         | Период голосования совета по предложениям.                                                                                                                                                                                                                                                           |

\*\*\*\*

## **Казначейство**

| **Parameter**                   | Value                   | Description                                                                                                                                                |
|:------------------------------- |:----------------------- |:---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Budgeting Period**            | 7 days                  | Когда казна может тратить снова после того, как потратила ранее.                                                                                           |
| **Proposal Bond**               | 5% and minumum 1000 EDG | Сумма, необходимая для облигации, чтобы предложить казначейские расходы. В случае одобрения оно возвращается, если предложение не проходит, оно сжигается. |
| **Burn unspent treasury funds** | Off                     | Это деактивирует сжигание всех неизрасходованных казначейских средств в конце бюджетного периода.                                                          |

\*\*\*\*

## **Сигнализация**

| Parameter                   | Value   | Description                                                                |
|:--------------------------- |:------- |:-------------------------------------------------------------------------- |
| **Signaling Period**        | 7 days  | Время, в течение которого сигнальное предложение активно для вовлечения.   |
| **Signaling Proposal Bond** | 100 EDG | Сумма EDG, необходимая для облигации, чтобы подать сигнальное предложение. |

## Личность

| Parameter                                            | Value   | Description                                                                                   |
|:---------------------------------------------------- |:------- |:--------------------------------------------------------------------------------------------- |
| **Required Bond Per Identity**                       | 10 EDG  | Залог необходимый для хранения идентификаторов в сети.                                        |
| **Required Bond Per Each Additional Identity Field** | 2.5 EDG | Требуется залог для хранения **дополнительных** идентификаторов в сети **Beyond Legal Name.** |
| **Sub-Account Deposit**                              | 2 EDG   | Сумма, необходимая для внесения депозита для создания дополнительной учетной записи.          |
| **Maximum Sub-Accounts**                             | 100     | Максимальное количество дополнительных учетных записей, которые может иметь учетная запись.   |

## Экономика

<table>
  <thead>
    <tr>
      <th style="text-align:left">Parameter</th>
      <th style="text-align:left">Value (Units)</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b>Minimum Inflation</b>
      </td>
      <td style="text-align:left">.025</td>
      <td style="text-align:left">The min. inflation rate the system will permit.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Maximum Inflation</b>
      </td>
      <td style="text-align:left">
        <p><b></b></p>
        <p>0.1</p>
      </td>
      <td style="text-align:left">The max. inflation rate the system will permit.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Ideal Staking Rate</b>
      </td>
      <td style="text-align:left">0.8</td>
      <td style="text-align:left">The ideal proportion of EDG tokens staked compared to total EDG supply.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Falloff (Decay) Rate</b>
      </td>
      <td style="text-align:left">
        <p><b></b></p>
        <p>0.05</p>
      </td>
      <td style="text-align:left">The decay rate on inflation when the actual staking rate becomes greater
        than the ideal staking rate.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Max Piece Count</b>
      </td>
      <td style="text-align:left">40</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Test Precision</b>
      </td>
      <td style="text-align:left">0.005</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Ideal Interest Rate</b>
      </td>
      <td style="text-align:left">12.5%</td>
      <td style="text-align:left">The ideal returns that an individual validator earns.</td>
    </tr>
  </tbody>
</table>

## Contract configuration

| Parameter                         | Value     | Description |
|:--------------------------------- |:--------- |:----------- |
| **Contract Transfer Fee**         | 0.10 EDG  | \*\*\*\*    |
| **Contract Creation Fee**         | 0.10 EDG  | \*\*\*\*    |
| **Contract Transaction Base Fee** | 0.10 EDG  | \*\*\*\*    |
| **Contract Transaction Byte Fee** | 0.001 EDG | \*\*\*\*    |
| **Contract Fee**                  | 0.10 EDG  | \*\*\*\*    |
| **Tombstone Deposit**             | 1 EDG     | \*\*\*\*    |
| **Rent Byte Fee**                 | 1 EDG     | \*\*\*\*    |
| **Rent Deposit Offset**           | 1000 EDG  | \*\*\*\*    |
| **Surcharge Reward**              | 150 EDG   |             |

## Genesis

| Parameter                     | Value                                                          |
|:----------------------------- |:-------------------------------------------------------------- |
| Council Members               | 1 Member at time of Genesis \(Commonwealth Labs 0x02456...\)   |
| Identity Attestation Provider | 1 Verifier at time of Genesis \(Commonwealth Labs 0x92c32...\) |

\*\*\*\*
