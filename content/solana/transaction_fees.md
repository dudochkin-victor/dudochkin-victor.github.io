+++
title = "Transaction Fees"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

**Возможны изменения.**

Каждая транзакция, отправленная через сеть, которая должна быть обработана текущим ведущим клиентом проверки и подтверждена как транзакция глобального состояния, содержит комиссию за транзакцию. Плата за транзакции дает много преимуществ в экономическом дизайне Solana, например:

- предоставить единичную компенсацию сети валидатора за ресурсы CPU/GPU, необходимые для обработки транзакции состояния,
- уменьшить сетевой спам, введя реальную стоимость транзакций,
- и обеспечить потенциальную долгосрочную экономическую стабильность сети за счет фиксированной протоколом минимальной суммы комиссии за транзакцию, как описано ниже.

Сетевые согласованные голоса отправляются как обычные системные передачи, а это означает, что валидаторы платят транзакционные сборы за участие в консенсусе.

Многие современные экономики блокчейна \(например, Биткойн, Эфириум\) полагаются на вознаграждения на основе протокола для поддержки экономики в краткосрочной перспективе, исходя из предположения, что доход, полученный за счет комиссий за транзакции, будет поддерживать экономику в долгосрочной перспективе, когда протокол полученные награды истекают. В попытке создать устойчивую экономику с помощью вознаграждений на основе протокола и комиссий за транзакции фиксированная часть (первоначально 50%) каждой комиссии за транзакцию уничтожается, а оставшаяся часть комиссии идет текущему лидеру, обрабатывающему транзакцию. Запланированный глобальный уровень инфляции обеспечивает источник вознаграждений, распределяемых среди клиентов проверки посредством процесса, описанного выше.

Плата за транзакции устанавливается сетевым кластером на основе последней исторической пропускной способности, см. [Комиссии, обусловленные перегрузкой](implemented-proposals/transaction-fees.md#congestion-driven-fees). Эта минимальная часть комиссии за каждую транзакцию может динамически корректироваться в зависимости от исторического количества подписей на слот. Таким образом, протокол может использовать минимальную плату для целевого использования оборудования. Отслеживая протокол, указанный _подписей на слот_ в отношении желаемого целевого объема использования, минимальную плату можно повысить/понизить, что, в свою очередь, должно снизить/увеличить фактическое _подпись на слот_ на блок до тех пор, пока оно не достигнет целевая сумма. Этот процесс корректировки можно рассматривать как аналогичный алгоритму корректировки сложности в протоколе Биткойн, однако в данном случае он корректирует минимальную комиссию за транзакцию, чтобы довести использование оборудования для обработки транзакций до желаемого уровня.

Как уже упоминалось, фиксированная часть комиссии за каждую транзакцию подлежит уничтожению. Цель этого дизайна состоит в том, чтобы сохранить стимул лидера для включения как можно большего количества транзакций в течение времени слота лидера, обеспечивая при этом механизм ограничения инфляции, который защищает от атак «уклонения от уплаты налогов» (т. Е. Платежей по сторонним каналам).

Кроме того, сожженные сборы могут учитываться при выборе вилки. В случае форка PoH со злонамеренным, цензурирующим лидером мы ожидаем, что общая сумма уничтоженных сборов будет меньше, чем у сопоставимого честного форка, из-за комиссий, потерянных из-за цензуры. Если лидер цензуры должен компенсировать эти потерянные сборы за протокол, ему придется самим заменить сгоревшие сборы на своем форке, что, в первую очередь, потенциально снижает стимул к цензуре.
