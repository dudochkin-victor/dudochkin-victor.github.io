+++
title = "FAQ"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

При написании или взаимодействии с программами Solana часто возникают общие вопросы или проблемы. Ниже приведены ресурсы, которые помогут ответить на эти вопросы.

Если не обратиться сюда, канал Solana [#developers](https://discord.gg/RxeGBH) Discord — отличный ресурс.

## Ошибка `CallDepth`

Эта ошибка означает, что этот межпрограммный вызов превысил допустимую глубину вызова вызова.

См. [Глубина вызовов между программами](developing/programming-model/calling-between-programs.md#call-depth).

## Ошибка `CallDepthExceeded`

Эта ошибка означает, что превышена глубина стека BPF.

См. [глубина вызова](overview.md#call-depth)

## Вычислительные ограничения

См. [вычислительные ограничения](developing/programming-model/runtime.md#compute-budget)

## Плавающие типы Rust

См. [поддержка с плавающей запятой](overview.md#float-support)

## Размер кучи

См. [куча](overview.md#heap)

## Неверные данные учетной записи

Эта ошибка программы может произойти по многим причинам. Обычно это вызвано передачей программе учетной записи, которую программа не ожидает, либо в неправильном положении в инструкции, либо учетной записи, несовместимой с выполняемой инструкцией.

Реализация программы также может вызвать эту ошибку при выполнении кросс-программной инструкции и забывании указать учетную запись для вызываемой программы.

## Неверные данные инструкции

Эта программная ошибка может возникнуть при попытке десериализовать инструкцию. Убедитесь, что переданная структура точно соответствует инструкции. Между полями может быть отступ. Если в программе реализован трейт Rust `Pack`, попробуйте упаковать и распаковать тип инструкции `T`, чтобы определить точную кодировку, которую ожидает программа:

https://github.com/solana-labs/solana/blob/v1.4/sdk/program/src/program_pack.rs

## Отсутствует необходимая подпись

Некоторые инструкции требуют, чтобы учетная запись была подписантом; эта ошибка возвращается, если ожидается, что учетная запись будет подписана, но это не так.

Реализация программы также может вызвать эту ошибку при выполнении межпрограммного вызова, для которого требуется подписанный адрес программы, но переданные начальные числа подписывающей стороны передаются в [`invoke_signed`](developing/programming-model/calling-between-programs.md ) не соответствуют начальным числам подписывающей стороны, использованным для создания адреса программы [`create_program_address`](developing/programming-model/calling-between-programs.md#program-derived-addresses).

## `rand` Зависимость Rust вызывает ошибку компиляции

См. [Зависимости проекта Rust](developing-rust.md#project-dependencies).

## Ограничения в ржавчине

См. [Ограничения Rust](developing-rust.md#restrictions)

## Размер стека

См. [стек](overview.md#stack)
