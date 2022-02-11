+++
title = "Мониторинг валидатора"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

## Проверить сплетни

Подтвердите, что IP-адрес и **паб-ключ** вашего валидатора видны в сети сплетен, запустив:

```bash
solana gossip
```

## Проверьте свой баланс

Баланс вашего счета должен уменьшаться на сумму комиссии за транзакцию по мере того, как ваш валидатор отправляет голоса, и увеличиваться после того, как он становится лидером. Передайте `--lampports`, чтобы наблюдать более мелкие детали:

```bash
solana balance --lamports
```

## Проверить активность голосования

Команда solana voice-account отображает последние действия по голосованию от вашего валидатора:

```bash
solana vote-account ~/vote-account-keypair.json
```

## Получить информацию о кластере

Есть несколько полезных конечных точек JSON-RPC для мониторинга вашего валидатора в кластере, а также работоспособности кластера:

```bash
# Similar to solana-gossip, you should see your validator in the list of cluster nodes
curl -X POST -H "Content-Type: application/json" -d '{"jsonrpc":"2.0","id":1, "method":"getClusterNodes"}' http://api.devnet.solana.com
# If your validator is properly voting, it should appear in the list of `current` vote accounts. If staked, `stake` should be > 0
curl -X POST -H "Content-Type: application/json" -d '{"jsonrpc":"2.0","id":1, "method":"getVoteAccounts"}' http://api.devnet.solana.com
# Returns the current leader schedule
curl -X POST -H "Content-Type: application/json" -d '{"jsonrpc":"2.0","id":1, "method":"getLeaderSchedule"}' http://api.devnet.solana.com
# Returns info about the current epoch. slotIndex should progress on subsequent calls.
curl -X POST -H "Content-Type: application/json" -d '{"jsonrpc":"2.0","id":1, "method":"getEpochInfo"}' http://api.devnet.solana.com
```
