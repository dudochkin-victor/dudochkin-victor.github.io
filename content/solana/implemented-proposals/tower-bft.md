+++
title = "Tower BFT"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Этот проект описывает алгоритм _Tower BFT_ Соланы. Он решает следующие проблемы:

- Некоторые форки могут быть не приняты подавляющим большинством кластера, и избирателям необходимо восстановиться после голосования по таким форкам.
- За многие вилки могут голосовать разные избиратели, и каждый голосующий может видеть свой набор вилок, за которые можно голосовать. Выбранные ответвления должны в конечном итоге сойтись для кластера.
- Голосование на основе вознаграждения связано с риском. Избиратели должны иметь возможность настроить степень риска, на который они идут.
- [Стоимость отката](tower-bft.md#cost-of-rollback) должна быть вычисляемой. Это важно для клиентов, которые полагаются на какую-то измеримую форму согласованности. Затраты на нарушение согласованности должны быть вычислимыми и увеличиваться сверхлинейно для более старых голосов.
- Скорости ASIC различаются между узлами, и злоумышленники могут использовать ASICS Proof of History, которые намного быстрее, чем остальная часть кластера. Консенсус должен быть устойчивым к атакам, использующим изменчивость скорости Proof of History ASIC.

Для краткости этот дизайн предполагает, что один голосующий с долей развернут как отдельный валидатор в кластере.

## Время

Кластер Solana генерирует источник времени с помощью функции проверяемой задержки, которую мы называем [Proof of History](../cluster/synchronization/).

Proof of History используется для создания детерминированного кругового расписания для всех активных лидеров. В любой момент времени только 1 лидер, который может быть вычислен из самой книги, может предложить ответвление. Для получения дополнительной информации см. [генерация вилки](../cluster/fork-generation/) и [ротация лидера](../cluster/leader-rotation/).

## Блокировки

Цель блокировки — заставить валидатора зафиксировать упущенную выгоду для определенного форка. Блокировки измеряются в слотах и ​​поэтому представляют собой вынужденную задержку в реальном времени, которую валидатор должен выждать, прежде чем нарушить обязательство по форку.

Валидаторы, которые нарушают локауты и голосуют за расходящийся форк в рамках локаута, должны быть наказаны. Предлагаемое наказание состоит в том, чтобы сократить долю валидатора, если кластеру может быть доказано одновременное голосование в рамках блокировки для форка, не являющегося потомком.

## Алгоритм

Основная идея этого подхода заключается в суммировании консенсусных голосов и двойных локаутах. Каждый голос в стеке является подтверждением форка. Каждая подтвержденная вилка является предком вилки над ней. Каждое голосование имеет «блокировку» в единицах слотов, прежде чем валидатор сможет отправить голосование, которое не содержит подтвержденного форка в качестве предка.

Когда голос добавляется в стек, блокировки всех предыдущих голосов в стеке удваиваются \(подробнее об этом в [Откат](tower-bft.md#Откат)\). С каждым новым голосом валидатор переводит предыдущие голоса в постоянно увеличивающуюся блокировку. При 32 голосах мы можем считать, что голосование находится в состоянии «максимальной блокировки», любые голоса с блокировкой, равной или превышающей «1<<32», удаляются из очереди \(FIFO\). Удаление из очереди голосования является триггером для вознаграждения. Если срок действия голоса истекает до того, как он будет исключен из очереди, он и все голоса над ним удаляются \(LIFO\) из стека голосов. Валидатор должен начать перестраивать стек с этой точки.

### Откат

Прежде чем голосование будет помещено в стек, все голоса, ведущие к голосованию с более низким временем блокировки, чем новое голосование, извлекаются. После отката локауты не удваиваются, пока валидатор не догонит высоту отката голосов.

Например, стек голосования со следующим состоянием:

| vote | vote time | lockout | lock expiration time |
| ----:| ---------:| -------:| --------------------:|
| 4    | 4         | 2       | 6                    |
| 3    | 3         | 4       | 7                    |
| 2    | 2         | 8       | 10                   |
| 1    | 1         | 16      | 17                   |

_Vote 5_ находится в момент времени 9, и результирующее состояние

| vote | vote time | lockout | lock expiration time |
| ----:| ---------:| -------:| --------------------:|
| 5    | 9         | 2       | 11                   |
| 2    | 2         | 8       | 10                   |
| 1    | 1         | 16      | 17                   |

_Голосование 6_ в момент времени 10

| vote | vote time | lockout | lock expiration time |
| ----:| ---------:| -------:| --------------------:|
| 6    | 10        | 2       | 12                   |
| 5    | 9         | 4       | 13                   |
| 2    | 2         | 8       | 10                   |
| 1    | 1         | 16      | 17                   |

В момент времени 10 новые голоса догнали предыдущие голоса. Но срок действия _vote 2_ истекает в 10, поэтому, когда применяется _vote 7_ в момент 11, голоса, включая и выше _vote 2_, будут извлечены.

| vote | vote time | lockout | lock expiration time |
| ----:| ---------:| -------:| --------------------:|
| 7    | 11        | 2       | 13                   |
| 1    | 1         | 16      | 17                   |

Блокировка для первого голоса не увеличится с 16 до тех пор, пока в стеке не будет 5 голосов.

### Рубка и награды

Валидаторы должны быть вознаграждены за выбор форка, который остальная часть кластера выбирала как можно чаще. Это хорошо согласуется с получением вознаграждения, когда стек голосов заполнен и самый старый голос необходимо исключить из очереди. Таким образом, вознаграждение должно генерироваться за каждое успешное удаление из очереди.

### Стоимость отката

Стоимость отката _fork A_ определяется как стоимость времени блокировки для валидатора для подтверждения любого другого форка, который не включает _fork A_ в качестве предка.

**Экономическая завершенность** _форка A_ может быть рассчитана как потеря всех вознаграждений от отката _форка A_ и его потомков, плюс альтернативная стоимость вознаграждения из-за экспоненциально растущего локаута голосов, которые подтвердили _форк A_.

### Пороги

Каждый валидатор может независимо установить порог приверженности кластера к форку до того, как этот валидатор зафиксирует форк. Например, при индексе стека голосования 7 блокировка составляет 256 единиц времени. Валидатор может отказать в голосовании и позволить истечь срокам действия голосов 0-7, если голосование с индексом 7 не имеет более 50% обязательств в кластере. Это позволяет каждому валидатору независимо контролировать степень риска, связанного с форком. Совершение форков с более высокой частотой позволит валидатору заработать больше вознаграждений.

### Параметры алгоритма

Необходимо настроить следующие параметры:

- Количество голосов в стеке до исключения из очереди \(32\).
- Скорость роста локаутов в стеке \(2x\).
- Запуск блокировки по умолчанию \(2\).
- Пороговая глубина для минимального выделения кластера перед фиксацией ветки \(8\).
- Минимальный размер обязательства кластера при пороговой глубине \(50%+\).

### Свободный выбор

«Свободный выбор» — это неисполнимое действие валидатора. Протокол не может кодировать и применять эти действия, поскольку каждый валидатор может изменять код и корректировать алгоритм. Валидатор, который максимизирует самовознаграждение по всем возможным вариантам будущего, должен вести себя таким образом, чтобы система была стабильной, а локальный жадный выбор должен приводить к жадному выбору по всем возможным вариантам будущего. Набор валидаторов, принимающих участие в выборах для нарушения протокола, должен быть связан своим весом доли с отказом в обслуживании. Два варианта выхода для валидатора:

- валидатор может опередить предыдущего валидатора в виртуальной генерации и отправить параллельный форк
- валидатор может приостановить голосование, чтобы понаблюдать за несколькими разветвлениями перед голосованием

В обоих случаях у валидатора в кластере есть несколько ответвлений, из которых можно одновременно выбирать, даже если каждое ответвление представляет собой разную высоту. В обоих случаях протокол не может определить, является ли поведение валидатора преднамеренным или нет.

### Жадный выбор параллельных форков

При оценке нескольких форков каждый валидатор должен использовать следующие правила:

1. Вилки должны удовлетворять правилу _Threshold_.
2. Выберите ветку, которая максимизирует общее время блокировки кластера для всех веток-предков.
3. Выберите вилку с наибольшей комиссией за транзакцию кластера.
4. Выберите последний форк с точки зрения PoH.

Плата за транзакцию кластера — это плата, которая вносится в майнинговый пул, как описано в разделе [Вознаграждения за стейкинг](staking-rewards/).

## Сопротивление PoH ASIC

Голоса и блокировки растут в геометрической прогрессии, в то время как скорость ASIC линейна. Есть два возможных вектора атаки с использованием более быстрой ASIC.

### Цензура ASIC

Злоумышленник создает параллельный форк, который опережает предыдущих лидеров, пытаясь подвергнуть их цензуре. Форк, предложенный этим злоумышленником, будет доступен одновременно со следующим доступным лидером. Чтобы узлы могли выбрать эту вилку, она должна удовлетворять правилу _Greedy Choice_.

1. Форк должен иметь равное количество голосов для форка-предка.
2. Вилка не может быть настолько далекой, чтобы привести к просроченным голосам.
3. Форк должен иметь большую комиссию за транзакцию кластера.

Затем эта атака ограничивается цензурой сборов предыдущих лидеров и отдельных транзакций. Но он не может остановить кластер или уменьшить набор валидаторов по сравнению с параллельным форком. Плата за цензуру ограничивается платой за доступ, поступающей лидерам, но не валидаторам.

### Откат ASIC

Злоумышленник создает параллельную вилку из более старого блока, чтобы попытаться откатить кластер. В этой атаке одновременный форк конкурирует с форками, за которые уже проголосовали. Эта атака ограничена экспоненциальным ростом локаутов.

- 1 голос блокирует 2 слота. Параллельная вилка должна быть как минимум на 2 слота впереди и производиться в 1 слоте. Поэтому требуется ASIC в 2 раза быстрее.
- 2 голоса имеют блокировку 4х слотов. Параллельная вилка должна быть как минимум на 4 слота впереди и производиться в 2 слотах. Поэтому требуется ASIC в 2 раза быстрее.
- 3 голоса имеют блокировку 8 слотов. Параллельная вилка должна быть как минимум на 8 слотов впереди и производиться в 3 слотах. Поэтому требуется ASIC в 2,6 раза быстрее.
- 10 голосов имеют блокировку 1024 слотов. 1024/10 или в 102,4 раза быстрее ASIC.
- 20 голосов имеют блокировку 2^20 слотов. 2^20/20, или в 52 428,8 раз быстрее ASIC.