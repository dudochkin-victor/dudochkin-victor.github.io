+++
title = "Runtime"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

## Возможности программ

Среда выполнения разрешает только программе-владельцу дебетовать счет или изменять его данные. Затем программа определяет дополнительные правила, определяющие, может ли клиент изменять принадлежащие ему учетные записи. В случае программы System она позволяет пользователям передавать лэмпорты, распознавая подписи транзакций. Если он увидит, что клиент подписал транзакцию с помощью _закрытого ключа_ пары ключей, он узнает, что клиент авторизовал передачу токена.

Другими словами, весь набор учетных записей, принадлежащих данной программе, можно рассматривать как хранилище ключей и значений, где ключ — это адрес учетной записи, а значение — произвольные двоичные данные, зависящие от программы. Автор программы может решить, как управлять всем состоянием программы и, возможно, многими учетными записями.

После того, как среда выполнения выполнит каждую из инструкций транзакции, она использует метаданные учетной записи, чтобы убедиться, что политика доступа не была нарушена. Если программа нарушает политику, среда выполнения отбрасывает все изменения учетных записей, сделанные всеми инструкциями в транзакции, и помечает транзакцию как неудачную.

### Политика

После того, как программа обработала инструкцию, среда выполнения проверяет, выполняла ли программа только те операции, на которые ей было разрешено, и что результаты соответствуют политике среды выполнения.

Политика такова:

- Только владелец учетной записи может изменить владельца.
  - И только если учетная запись доступна для записи.
  - И только если аккаунт не исполняемый
  - И только если данные инициализированы нулями или пусты.
- Баланс аккаунта, не привязанного к программе, не может уменьшиться.
- Баланс только для чтения и исполняемых аккаунтов не может измениться.
- Только системная программа может изменить размер данных и только в том случае, если системная программа владеет учетной записью.
- Только владелец может изменить данные учетной записи.
  - И если учетная запись доступна для записи.
  - А если аккаунт не исполняемый.
- Исполняемый файл является односторонним (false->true) и только владелец учетной записи может установить его.
- Никто не может вносить изменения в rent_epoch, связанный с этой учетной записью.

## Расчет бюджета

Чтобы программа не злоупотребляла вычислительными ресурсами, каждой инструкции в транзакции назначается вычислительный бюджет. Бюджет состоит из вычислительных единиц, потребляемых программой при выполнении различных операций, и ограничений, которые программа не может превышать. Когда программа использует весь свой бюджет или превышает предел, среда выполнения останавливает программу и возвращает ошибку.

Следующие операции влекут за собой вычислительные затраты:

- Выполнение инструкций BPF
- Вызов системных вызовов
  - протоколирование
  - создание программных адресов
  - межпрограммные вызовы
  - ...

При межпрограммных вызовах вызываемые программы наследуют бюджет своего родителя. Если вызванная программа использует бюджет или превышает границу, вся цепочка вызовов и родитель останавливаются.

Текущий [вычислительный бюджет](https://github.com/solana-labs/solana/blob/d3a3a7548c857f26ec2cb10e270da72d373020ec/sdk/src/process_instruction.rs#L65) можно найти в Solana SDK.

Например, если текущий бюджет:

```rust
max_units: 200,000,
log_units: 100,
log_u64_units: 100,
create_program address units: 1500,
invoke_units: 1000,
max_invoke_depth: 4,
max_call_depth: 64,
stack_frame_size: 4096,
log_pubkey_units: 100,
```

Затем программа

- Может выполнить 200 000 инструкций BPF, если он не делает ничего другого
- Может регистрировать 2000 сообщений журнала
- Не может превышать 4 КБ использования стека
- Не может превышать глубину вызова BPF 64
- Не может превышать 4 уровня межпрограммных вызовов.

Поскольку бюджет вычислений расходуется постепенно по мере выполнения программы, общее потребление бюджета будет представлять собой комбинацию различных затрат на операции, которые она выполняет.

Во время выполнения программа может регистрировать оставшуюся часть вычислительного бюджета. См. [debugging](developing/on-chain-programs/debugging.md#monitoring-compute-budget-consumption) для получения дополнительной информации.

Значения бюджета зависят от включения функции, взгляните на [новую](https://github.com/solana-labs/solana/blob/d3a3a7548c857f26ec2cb10e270da72d373020ec/sdk/src/process_instruction.rs#L97) функцию вычислительного бюджета, чтобы узнать, как формируется бюджет. Понимание того, как работают [функции](runtime.md#features) и какие функции включены в используемом кластере, требуется для определения значений текущего бюджета.

## Новые возможности

По мере развития Solana могут вводиться новые функции или исправления, которые изменяют поведение кластера и выполнение программ. Изменения в поведении должны быть согласованы между различными узлами кластера, если узлы не координируются, эти изменения могут привести к нарушению консенсуса. Solana поддерживает механизм, называемый функциями времени выполнения, для облегчения беспрепятственного принятия изменений.

Функции времени выполнения — это скоординированные по эпохе события, в ходе которых происходит одно или несколько изменений поведения кластера. Новые изменения в Solana, которые изменят поведение, обернуты функциональными воротами и отключены по умолчанию. Затем инструменты Solana используются для активации функции, которая помечает ее как ожидающую, после того как она будет отмечена как ожидающая, функция будет активирована в следующую эпоху.

Чтобы определить, какие функции активированы, используйте [инструменты командной строки Solana](cli/install-solana-cli-tools/):

```bash
solana feature status
```

Если вы столкнулись с проблемами, сначала убедитесь, что версия инструментов Solana, которую вы используете, соответствует версии, возвращаемой `solana cluster-version`. Если они не совпадают [установите правильный набор инструментов](cli/install-solana-cli-tools/).