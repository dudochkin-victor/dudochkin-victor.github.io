+++
title = "RiP Curl: RPC с малой задержкой, ориентированный на транзакции"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

## Проблема

Первоначальная реализация RPC в Solana была создана для того, чтобы пользователи могли подтверждать транзакции, которые только что были отправлены в кластер.
Он был разработан с учетом использования памяти, поэтому любой валидатор должен иметь возможность поддерживать API, не опасаясь DoS-атак.

Позже стало желательным использовать тот же API для поддержки обозревателя Solana. Первоначальный дизайн поддерживал только минуты истории, поэтому мы изменили его, чтобы вместо этого хранить статусы транзакций в локальном экземпляре RocksDB и предлагать дни истории. Затем мы продлили этот срок до 6 месяцев с помощью BigTable.

С каждой модификацией API становился все более подходящим для приложений, обслуживающих статический контент, и менее привлекательным для обработки транзакций. Клиенты опрашивают статус транзакции вместо того, чтобы получать уведомления, создавая ложное впечатление о более длительном времени подтверждения. Кроме того, то, что клиенты могут опрашивать, ограничено, что не позволяет им принимать разумные решения в режиме реального времени, например, признать транзакцию подтвержденной, как только определенные известные валидаторы проголосуют за нее.

## Предложенное решение

Удобный для Интернета, ориентированный на транзакции, потоковый API, построенный на основе ReplayStage валидатора.

Улучшенный клиентский опыт:

- Поддержка подключений непосредственно из приложений WebAssembly.
- Клиенты могут быть уведомлены о ходе подтверждения в режиме реального времени, включая голоса и вес ставки избирателя.
- Клиенты могут быть уведомлены при изменении самого тяжелого форка, если это влияет на количество подтверждений транзакций.

Валидаторам проще поддерживать:

- Каждый валидатор поддерживает некоторое количество одновременных подключений и в остальном не имеет существенных ограничений по ресурсам.
- Статус транзакции никогда не сохраняется в памяти и не может быть опрошен.
- Подписи хранятся в памяти только до достижения желаемого уровня фиксации или до истечения срока действия блок-хэша, в зависимости от того, что наступит позднее.

Как это работает:

1. Клиент подключается к валидатору, используя надежный канал связи, такой как веб-сокет.
2. Валидатор регистрирует подпись в ReplayStage.
3. Валидатор отправляет транзакцию в Гольфстрим и повторяет все известные форки до тех пор, пока не истечет срок действия хэша (но не до тех пор, пока транзакция не будет принята только на самом тяжелом форке). Если срок действия хэша истекает, подпись отменяется, клиент уведомляется и соединение закрывается.
4. Поскольку ReplayStage обнаруживает события, влияющие на статус транзакции, он уведомляет клиента в режиме реального времени.
5. После подтверждения того, что транзакция укоренена (`CommitmentLevel::Max`), подпись снимается с регистрации, и сервер закрывает восходящий канал.
