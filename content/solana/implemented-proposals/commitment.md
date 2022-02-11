+++
title = "Обязательство"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Метрика приверженности предназначена для того, чтобы предоставить клиентам меру подтверждения сети и уровней ставок в конкретном блоке. Затем клиенты могут использовать эту информацию для получения собственных показателей приверженности.

# Расчет RPC

Клиенты могут запрашивать метрики фиксации у валидатора для подписи `s` через `get_block_commitment(s: Signature) -> BlockCommitment` через RPC. Структура `BlockCommitment` содержит массив u64 `[u64, MAX_CONFIRMATIONS]`. Этот массив представляет собой метрику обязательства для конкретного блока «N», который содержит подпись «s» для последнего блока «M», за который проголосовал валидатор.

Запись «s» с индексом «i» в массиве «BlockCommitment» означает, что валидатор наблюдал общую долю «s» в кластере, достигшую «i» подтверждений в блоке «N», как это наблюдалось в некотором блоке «M». В этом массиве будут элементы MAX_CONFIRMATIONS, представляющие все возможное количество подтверждений от 1 до MAX_CONFIRMATIONS.

# Вычисление метрики приверженности

Построение этой структуры `BlockCommitment` использует уже выполненные вычисления для достижения консенсуса. Функция `collect_vote_lockouts` в `consensus.rs` создает HashMap, где каждая запись имеет вид `(b, s)`, где `s` – сумма ставки в банке `b`.

Это вычисление выполняется для голосующего банка-кандидата "b" следующим образом.

```
   let output: HashMap<b, Stake> = HashMap::new();
   for vote_account in b.vote_accounts {
       for v in vote_account.vote_stack {
           for a in ancestors(v) {
               f(*output.get_mut(a), vote_account, v);
           }
       }
   }
```

где `f` — некоторая функция накопления, которая изменяет запись `Ставка` для слота `а` некоторыми данными, полученными из голосования `v` и `vote_account` (ставка, блокировка и т. д.). Обратите внимание, что «предки» здесь включают только те слоты, которые присутствуют в кеше текущего состояния. Подписи для банков, предшествующие тем, которые присутствуют в кеше состояния, в любом случае не будут доступны для запроса, поэтому эти банки не включены в расчеты обязательств здесь.

Теперь мы можем естественным образом расширить вышеприведенное вычисление, чтобы также создать массив BlockCommitment для каждого банка b следующим образом:

1. Добавление ForkCommitmentCache для сбора структур BlockCommitment.
2. Замена `f` на `f'` таким образом, чтобы приведенное выше вычисление также создавало это `BlockCommitment` для каждого банка `b`.

Мы продолжим с деталями 2), так как 1) тривиально.

Прежде чем продолжить, следует отметить, что для некоторого счета голосования валидатора «a» количество локальных подтверждений для этого валидатора в слоте «s» равно «v.num_confirmations», где «v» — наименьший голос в стеке голосов. a.votes` таким образом, что `v.slot >= s` (т.е. нет необходимости смотреть какие-либо голоса > v, так как количество подтверждений будет ниже).

Теперь, более конкретно, мы увеличим приведенное выше вычисление до:

```
   let output: HashMap<b, Stake> = HashMap::new();
   let fork_commitment_cache = ForkCommitmentCache::default();
   for vote_account in b.vote_accounts {
       // vote stack is sorted from oldest vote to newest vote
       for (v1, v2) in vote_account.vote_stack.windows(2) {
           for a in ancestors(v1).difference(ancestors(v2)) {
               f'(*output.get_mut(a), *fork_commitment_cache.get_mut(a), vote_account, v);
           }
       }
   }
```

где `f'` определяется как:

```
    fn f`(
        stake: &mut Stake,
        some_ancestor: &mut BlockCommitment,
        vote_account: VoteAccount,
        v: Vote, total_stake: u64
    ){
        f(stake, vote_account, v);
        *some_ancestor.commitment[v.num_confirmations] += vote_account.stake;
    }
```