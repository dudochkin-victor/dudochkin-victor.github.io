+++
title = "Делегирование ставок и вознаграждения"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Стейкеры получают вознаграждение за помощь в проверке реестра. Они делают это, делегируя свою долю узлам валидатора. Эти валидаторы выполняют всю рутинную работу по воспроизведению реестра и отправке голосов на учетную запись для каждого узла, на которую участники могут делегировать свои доли. Остальная часть кластера использует эти голоса, взвешенные по ставкам, для выбора блока при возникновении разветвлений. И валидатор, и стейкер нуждаются в некотором экономическом стимуле, чтобы сыграть свою роль. Валидатор должен получить компенсацию за свое оборудование, а стейкер должен получить компенсацию за риск сокращения своей доли. Экономика освещена в [вознаграждениях за стекинг](../implemented-proposals/staking-rewards/). В этом разделе, с другой стороны, описывается основная механика его реализации.

## Основной дизайн

Общая идея заключается в том, что валидатор владеет учетной записью для голосования. Учетная запись Vote отслеживает голоса валидатора, подсчитывает кредиты, сгенерированные валидатором, и предоставляет любое дополнительное состояние валидатора. Учетная запись Vote не знает о делегированных ей ставках и не имеет веса стейкинга.

Отдельная учетная запись Stake \(созданная стейкером\) называет учетную запись Vote, на которую делегируется ставка. Полученные вознаграждения пропорциональны количеству поставленных лампортов. Аккаунт Stake принадлежит только стейкеру. Некоторая часть лампортов, хранящихся в этой учетной записи, является ставкой.

## Пассивное делегирование

Любое количество учетных записей Stake может делегироваться одной учетной записи Vote без интерактивного действия со стороны удостоверения личности, контролирующего учетную запись Vote или отправляющего голоса на учетную запись.

Общая ставка, выделенная для учетной записи Vote, может быть рассчитана по сумме всех учетных записей Stake, которые имеют открытый ключ учетной записи Vote как `StakeState::Stake::voter_pubkey`.

## Аккаунты для голосования и ставок

Процесс вознаграждения разделен на две программы в сети. Программа Vote решает проблему сокращения ставок. Программа Stake действует как хранитель пула вознаграждений и обеспечивает пассивное делегирование. Программа Stake отвечает за выплату вознаграждений стейкеру и избирателю, когда показано, что делегат стейкера участвовал в проверке реестра.

### голосование

VoteState — это текущее состояние всех голосов, которые валидатор отправил в сеть. VoteState содержит следующую информацию о состоянии:

- `votes` - Структура данных отправленных голосов.
- `кредиты` – общее количество вознаграждений, полученных этой программой голосования за время ее существования.
- `root_slot` - последний слот для достижения полной блокировки, необходимой для вознаграждений.
- `commission` - комиссия, взимаемая этим VoteState за любые вознаграждения, требуемые счетами Stake стейкера. Это процентный потолок вознаграждения.
- Account::lamports - Накопленные лампорты от комиссии. Они не считаются ставками.
- `authorized_voter` - только этот идентификатор имеет право подавать голоса. Это поле может быть изменено только этим идентификатором.
- `node_pubkey` - узел Solana, который голосует в этом аккаунте.
- `authorized_withdrawer` - личность лица, отвечающего за лампы этой учетной записи, отдельно от адреса учетной записи и авторизованного лица, подписывающего голоса.

### VoteInstruction::Initialize\(VoteInit\)

- `account[0]` - RW - Состояние голосования.
  
  `VoteInit` содержит `node_pubkey`, `authorized_voter`, `authorized_withdrawer` и `commission` новой учетной записи для голосования.
  
  другие члены VoteState по умолчанию.

### VoteInstruction::Authorize\(Pubkey, VoteAuthorize\)

Обновляет учетную запись новым авторизованным избирателем или отзывателем в соответствии с параметром VoteAuthorize \(`Voter` или `Withdrawer`\). Транзакция должна быть подписана текущим `authorized_voter` или `authorized_withdrawer` учетной записи для голосования.

- `account[0]` - RW - Состояние голосования.
  Для `VoteState::authorized_voter` или `authorized_withdrawer` установлено значение `Pubkey`.

### VoteInstruction::Vote\(Vote\)

- `account[0]` - RW - Состояние голосования.
  `VoteState::lockouts` и `VoteState::credits` обновляются в соответствии с правилами блокировки голосования, см. [Tower BFT](../implemented-proposals/tower-bft/).
- `account[1]` - RO - `sysvar::slot_hashes` Список некоторых N самых последних слотов и их хэшей для проверки голосования.
- `account[2]` - RO - `sysvar::clock` Текущее сетевое время, выраженное в слотах, эпохах.

### StakeState

StakeState принимает одну из четырех форм: StakeState::Uninitialized, StakeState::Initialized, StakeState::Stake и StakeState::RewardsPool. В стейкинге используются только первые три формы, но интерес представляет только StakeState::Stake. Все RewardsPools создаются при генезисе.

### StakeState::Stake

StakeState::Stake — это текущий параметр делегирования **стейкера**, который содержит следующую информацию о состоянии:

- Account::lamports - Доступные для стейкинга Lamports.
- `stake` - сумма ставки \(с учетом прогрева и перезарядки\) для получения вознаграждения, всегда меньше или равна Account::lamports.
- `voter_pubkey` - pubkey экземпляра VoteState, которому делегированы lamports.
- `credits_observed` - общее количество кредитов, заявленных за время существования программы.
- `activated` - эпоха, в которой эта ставка была активирована/делегирована. Полная ставка будет засчитана после разминки.
- `деактивировано` - эпоха, в которой эта ставка была деактивирована, требуется несколько периодов восстановления, прежде чем учетная запись будет полностью деактивирована, и ставка доступна для снятия.
- `authorized_staker` - публичный ключ лица, которое должно подписывать транзакции делегирования, активации и деактивации.
- `authorized_withdrawer` - личность лица, отвечающего за лампы этой учетной записи, отдельно от адреса учетной записи и авторизованного стейкера.

### StakeState::RewardsPool

Чтобы избежать единой общесетевой блокировки или конкуренции при погашении, 256 RewardsPools являются частью генезиса с заранее определенными ключами, каждый из которых имеет кредиты std::u64::MAX, чтобы иметь возможность удовлетворять погашения в соответствии со стоимостью баллов.

Stakes и RewardsPool — это учетные записи, принадлежащие одной и той же программе «Stake».

### StakeInstruction::DelegateStake

Учетная запись Stake перемещается из формы Initialized в форму StakeState::Stake или из деактивированной (т.е. полностью охлажденной) формы StakeState::Stake в активированную форму StakeState::Stake. Вот как стейкеры выбирают учетную запись для голосования и узел валидатора, на который делегируются их лампы стейк-аккаунта. Транзакция должна быть подписана авторизованным_стейкером доли.

- `account[0]` - RW - Экземпляр StakeState::Stake. `StakeState::Stake::credits_observed` инициализируется как `VoteState::credits`, `StakeState::Stake::voter_pubkey` инициализируется как `account[1]`. Если это начальное делегирование ставки, `StakeState::Stake::stake` инициализируется балансом счета в лэмпортах, `StakeState::Stake::activated` инициализируется текущей эпохой банка, а `StakeState::Stake` ::deactivated` инициализируется как std::u64::MAX
- `account[1]` - R - Экземпляр VoteState.
- `account[2]` - R - учетная запись sysvar::clock, несет информацию о текущей эпохе банка.
- `account[3]` - R - учетная запись sysvar::stakehistory, содержит информацию об истории ставок.
- `account[4]` - R - учетная запись на ставке::Конфигурация, содержит конфигурацию разогрева, восстановления и слэшинга.

### StakeInstruction::Authorize\(Pubkey, StakeAuthorize\)

Обновляет учетную запись новым авторизованным стейкером или снимающим в соответствии с параметром StakeAuthorize \(`Стейкер` или `Снимающий`\). Транзакция должна быть подписана текущим `authorized_staker` или `authorized_withdrawer` учетной записи Stake. Срок действия любой блокировки доли должен быть истек, или хранитель блокировки также должен подписать транзакцию.

- `account[0]` - RW - StakeState.
  
  Для `StakeState::authorized_staker` или `authorized_withdrawer` установлено значение `Pubkey`.

### StakeInstruction::Деактивировать

Стейкер может пожелать выйти из сети. Для этого он должен сначала деактивировать свою ставку и дождаться восстановления.
Транзакция должна быть подписана авторизованным_стейкером доли.

- `account[0]` - RW - Деактивируемый экземпляр StakeState::Stake.
- `account[1]` - R - счет sysvar::clock из банка, который содержит текущую эпоху.

StakeState::Stake::deactivated устанавливается на текущую эпоху + время восстановления. К этому времени ставка учетной записи упадет до нуля, и Account::lamports будет доступен для вывода.

### StakeInstruction::Withdraw\(u64\)

Лампорты со временем накапливаются на счете Stake, и любое превышение над активированной ставкой может быть снято. Транзакция должна быть подписана авторизованным_сборщиком ставки.

- `account[0]` - RW - StakeState::Stake, с которого нужно вывести средства.
- `account[1]` - RW - Счет, на который должны быть зачислены выведенные лэмпорты.
- `account[2]` - R - счет sysvar::clock из банка, который содержит текущую эпоху, для расчета ставки.
- `account[3]` - R - sysvar::stake_history учетная запись из банка, в которой хранится история прогрева/отката кола.

## Преимущества конструкции

- Единое голосование для всех стейкеров.
- Очистка переменной кредита не требуется для получения вознаграждения.
- Каждая делегированная ставка может претендовать на вознаграждение независимо.
- Комиссия за работу вносится, когда запрашивается вознаграждение делегированной ставкой.

## Пример потока вызовов

![Passive Staking Callflow](/img/passive-staking-callflow.png)

## Награды за стекинг

Конкретная механика и правила режима вознаграждения валидатора описаны здесь. Награды зарабатываются путем делегирования доли валидатору, который правильно голосует. Голосование неправильно подвергает ставки этого валидатора [слэшингу](../proposals/slashing/).

### Основы

Сеть выплачивает вознаграждение за часть сетевой [инфляции](../terminology.md#inflation). Количество лампортов, доступных для выплаты вознаграждений за эпоху, фиксировано и должно быть поровну разделено между всеми узлами с ставками в соответствии с их относительным весом ставки и участием. Весовая единица называется [точка](../terminology.md#точка).

Награды за эпоху недоступны до конца этой эпохи.

В конце каждой эпохи общее количество очков, заработанных в течение эпохи, суммируется и используется для деления доли вознаграждения от инфляции эпохи, чтобы получить значение в очках. Это значение записывается в банке в [sysvar](../terminology.md#sysvar), который сопоставляет эпохи с точечными значениями.

Во время погашения программа ставок подсчитывает очки, заработанные ставкой для каждой эпохи, умножает их на стоимость очков эпохи и переводит эту сумму лампортов со счета вознаграждений на счета ставок и голосов в соответствии с настройками комиссии счета голосов.

### Экономика

Стоимость точки за эпоху зависит от совокупного участия в сети. Если участие в эпохе падает, баллы для тех, кто участвует, выше.

### Получение кредитов

Валидаторы зарабатывают один кредит голоса за каждый правильный голос, который превышает максимальную блокировку, то есть каждый раз, когда учетная запись для голосования валидатора удаляет слот из своего списка блокировки, делая этот голос корневым для узла.

Стейкеры, делегировавшие полномочия этому валидатору, зарабатывают баллы пропорционально своей ставке. Заработанные очки являются результатом кредитов голосов и ставок.

### Прогрев ставки, кулдаун, вывод

Ставки, однажды делегированные, не вступают в силу немедленно. Сначала они должны пройти период разогрева. В этот период какая-то часть ставки считается «действующей», а остальная – «активирующейся». Изменения происходят на границах эпох.

Программа ставок ограничивает скорость изменения общей доли сети, что отражено в `config::warmup_rate` программы ставок (установлено на 25% за эпоху в текущей реализации).

Сумма ставки, которая может быть разогрета в каждую эпоху, зависит от общей эффективной ставки предыдущей эпохи, общей активирующей ставки и настроенной скорости разогрева программы ставок.

Перезарядка работает так же. После того, как ставка деактивирована, некоторая ее часть считается «действующей», а также «деактивируемой». По мере того, как ставка остывает, она продолжает приносить вознаграждение и подвергаться рубке, но также становится доступной для вывода.

Ставки Bootstrap не подлежат прогреву.

Награды выплачиваются против «эффективной» части ставки за эту эпоху.

#### Пример разминки

Рассмотрим ситуацию с одной долей в 1000, активированной в эпоху N, с коэффициентом прогрева сети 20% и неактивной общей долей в сети в эпоху N, равной 2000.

В эпоху N+1 сумма, доступная для активации для сети, составляет 400 \(20% от 2000\), а в эпоху N этот пример ставки является единственной активируемой ставкой, и поэтому имеет право на всю комнату для разминки. доступный.

| epoch | effective | activating | total effective | total activating |
|:----- | ---------:| ----------:| ---------------:| ----------------:|
| N-1   |           |            | 2,000           | 0                |
| N     | 0         | 1,000      | 2,000           | 1,000            |
| N+1   | 400       | 600        | 2,400           | 600              |
| N+2   | 880       | 120        | 2,880           | 120              |
| N+3   | 1000      | 0          | 3,000           | 0                |

Were 2 stakes \(X and Y\) to activate at epoch N, they would be awarded a portion of the 20% in proportion to their stakes. At each epoch effective and activating for each stake is a function of the previous epoch's state.

| epoch | X eff | X act | Y eff | Y act | total effective | total activating |
|:----- | -----:| -----:| -----:| -----:| ---------------:| ----------------:|
| N-1   |       |       |       |       | 2,000           | 0                |
| N     | 0     | 1,000 | 0     | 200   | 2,000           | 1,200            |
| N+1   | 333   | 667   | 67    | 133   | 2,400           | 800              |
| N+2   | 733   | 267   | 146   | 54    | 2,880           | 321              |
| N+3   | 1000  | 0     | 200   | 0     | 3,200           | 0                |

### Снятие

В любое время могут быть выведены только лэмпорты, превышающие эффективную+активирующую ставку. Это означает, что во время разминки фактически ни одна ставка не может быть отозвана. Во время перезарядки любые жетоны, превышающие действующую ставку, могут быть изъяты \(активация == 0\). Поскольку заработанные вознаграждения автоматически добавляются к ставкам, снятие средств, как правило, возможно только после деактивации.

### Блокировка

Счета ставок поддерживают понятие блокировки, когда баланс счета ставок недоступен для вывода до определенного времени. Блокировка определяется как высота эпохи, то есть минимальная высота эпохи, которая должна быть достигнута сетью, прежде чем баланс счета доли будет доступен для снятия, если транзакция также не подписана указанным хранителем. Эта информация собирается при создании учетной записи доли и сохраняется в поле Блокировка состояния учетной записи доли. Изменение авторизованного стейкера или вывода средств также подлежит блокировке, поскольку такая операция фактически является переводом.
