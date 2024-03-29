+++
title = "Разработка на Rust"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Solana поддерживает написание сетевых программ с использованием языка программирования [Rust](https://www.rust-lang.org/).

## Макет проекта

Программы Solana Rust следуют типичному [макету проекта Rust](https://doc.rust-lang.org/cargo/guide/project-layout.html):

```
/inc/
/src/
/Cargo.toml
```

Программы Solana Rust могут напрямую зависеть друг от друга, чтобы получить доступ к помощникам инструкций при выполнении [межпрограммных вызовов](developing/programming-model/calling-between-programs.md#cross-program-invocations). При этом важно не использовать символы точки входа зависимой программы, поскольку они могут конфликтовать с собственными символами программы. Чтобы избежать этого, программы должны определить функцию «no-entrypoint» в «Cargo.toml» и использовать для исключения точки входа.

- [Определить функцию](https://github.com/solana-labs/solana-program-library/blob/fca9836a2c8e18fc7e3595287484e9acd60a8f64/token/program/Cargo.toml#L12)
- [Исключить точку входа](https://github.com/solana-labs/solana-program-library/blob/fca9836a2c8e18fc7e3595287484e9acd60a8f64/token/program/src/lib.rs#L12)

Затем, когда другие программы включают эту программу в качестве зависимости, они должны делать это с помощью функции «no-entrypoint».

- [Включить без точки входа](https://github.com/solana-labs/solana-program-library/blob/fca9836a2c8e18fc7e3595287484e9acd60a8f64/token-swap/program/Cargo.toml#L22)

## Зависимости проекта

Как минимум, программы Solana Rust должны загружаться в крейт [solana-program](https://crates.io/crates/solana-program).

Программы Solana BPF имеют некоторые ограничения, которые могут препятствовать включению некоторых ящиков в качестве зависимостей или требовать специальной обработки.

Например:

- Ящики, требующие, чтобы архитектура была подмножеством тех, которые поддерживаются официальной цепочкой инструментов. Для этого нет обходного пути, если только этот крейт не разветвлен и к нему не добавлены проверки архитектуры BPF.
- Ящики могут зависеть от `rand`, который не поддерживается в детерминированной программной среде Solana. Чтобы включить крейт, зависящий от ранда, обратитесь к Зависит от ранда.
- Крейты могут переполнять стек, даже если код переполнения стека не включен в саму программу. Для получения дополнительной информации см. [Стек](overview/).

## Как построить

Сначала настройте среду:

- Установите последнюю стабильную версию Rust с https://rustup.rs/
- Установите последние инструменты командной строки Solana с https://docs.solana.com/cli/install-solana-cli-tools.

Обычная сборка груза доступна для создания программ на вашем хост-компьютере, которые можно использовать для модульного тестирования:

```bash
$ cargo build
```

Чтобы создать специальную программу, такую как токен SPL, для цели Solana BPF, которую можно развернуть в кластере:

```bash
$ cd <the program directory>
$ cargo build-bpf
```

## Как проверить

Программы Solana можно тестировать с помощью традиционного механизма «грузового теста».
прямое выполнение программных функций.

Чтобы упростить тестирование в среде, которая больше соответствует рабочему кластеру, разработчики могут использовать крейт [`program-test`](https://crates.io/crates/solana-program-test). Крейт «program-test» запускает локальный экземпляр среды выполнения и позволяет тестам отправлять несколько транзакций, сохраняя состояние на время теста.

Для получения дополнительной информации [пример теста в sysvar](https://github.com/solana-labs/solana-program-library/blob/master/examples/rust/sysvar/tests/functional.rs) показывает, как инструкция, содержащая sysvar отправляется и обрабатывается программой.

## Точка входа в программу

Программы экспортируют известный символ точки входа, который среда выполнения Solana ищет и вызывает при вызове программы. Solana поддерживает несколько [версий загрузчика BPF](overview.md#versions), и точки входа могут различаться между ними. Программы должны быть написаны и развернуты для одного и того же загрузчика. Подробнее см. [обзор](обзор#загрузчики).

В настоящее время поддерживаются два загрузчика [BPF Loader](https://github.com/solana-labs/solana/blob/d9b0fc0e3eec67dfe4a97d9298b15969b2804fab/sdk/program/src/bpf_loader.rs#L17) и [загрузчик BPF устарел](https: //github.com/solana-labs/solana/blob/d9b0fc0e3eec67dfe4a97d9298b15969b2804fab/sdk/program/src/bpf_loader_deprecated.rs#L14)

Они оба имеют одно и то же необработанное определение точки входа, ниже приведен необработанный символ, который ищет и вызывает среда выполнения:

```rust
#[no_mangle]
pub unsafe extern "C" fn entrypoint(input: *mut u8) -> u64;
```

Эта точка входа принимает общий массив байтов, который содержит сериализованные параметры программы (идентификатор программы, учетные записи, данные инструкций и т. д.). Для десериализации параметров каждый загрузчик содержит свой собственный макрос-оболочку, который экспортирует необработанную точку входа, десериализует параметры, вызывает определяемую пользователем функцию обработки инструкций и возвращает результаты.

Вы можете найти макросы точки входа здесь:

- [Макрос точки входа загрузчика BPF](https://github.com/solana-labs/solana/blob/9b1199cdb1b391b00d510ed7fc4866bdf6ee4eb3/sdk/program/src/entrypoint.rs#L42)
- [Макрос точки входа BPF Loader устарел](https://github.com/solana-labs/solana/blob/9b1199cdb1b391b00d510ed7fc4866bdf6ee4eb3/sdk/program/src/entrypoint_deprecated.rs#L38)

Программно определяемая функция обработки инструкций, которую вызывает макрос точки входа, должна иметь следующую форму:

```rust
pub type ProcessInstruction =
    fn(program_id: &Pubkey, accounts: &[AccountInfo], instruction_data: &[u8]) -> ProgramResult;
```

Обратитесь к [использованию точки входа в helloworld](https://github.com/solana-labs/example-helloworld/blob/1e049076e10be8712b1a725d2d886ce0cd036b2e/src/program-rust/src/lib.rs#L19) в качестве примера того, как подходят друг-другу.

### Десериализация параметров

Каждый загрузчик предоставляет вспомогательную функцию, которая десериализует входные параметры программы в типы Rust. Макросы точки входа автоматически вызывают помощник десериализации:

- [Десериализация загрузчика BPF](https://github.com/solana-labs/solana/blob/d9b0fc0e3eec67dfe4a97d9298b15969b2804fab/sdk/program/src/entrypoint.rs#L146)
- [Загрузчик BPF отказался от десериализации](https://github.com/solana-labs/solana/blob/d9b0fc0e3eec67dfe4a97d9298b15969b2804fab/sdk/program/src/entrypoint_deprecated.rs#L57)

Некоторым программам может потребоваться выполнить десериализацию самостоятельно, и они могут это сделать, предоставив собственную реализацию необработанной точки входа. Обратите внимание, что предоставленные функции десериализации сохраняют ссылки на сериализованный массив байтов для переменных, которые программа может изменять (lamports, данные учетной записи). Причина этого в том, что по возвращении загрузчик прочитает эти изменения, чтобы их можно было зафиксировать. Если программа реализует собственную функцию десериализации, она должна гарантировать, что любые изменения, которые программа хочет зафиксировать, будут записаны обратно во входной массив байтов.

Подробности о том, как загрузчик сериализует входные данные программы, можно найти в документах [Сериализация входных параметров](overview.md#input-parameter-serialization).

### Типы данных

Макросы точки входа загрузчика вызывают функцию процессора команд, определяемую программой, со следующими параметрами:

```rust
program_id: &Pubkey,
accounts: &[AccountInfo],
instruction_data: &[u8]
```

Идентификатор программы — это открытый ключ выполняемой в данный момент программы.

Учетные записи представляют собой упорядоченный фрагмент учетных записей, на которые ссылается инструкция и представленный в виде [AccountInfo](https://github.com/solana-labs/solana/blob/d9b0fc0e3eec67dfe4a97d9298b15969b2804fab/sdk/program/src/account_info.rs#L12 ) структуры. Место счета в массиве указывает на его значение, например, при передаче лэмпортов инструкция может определить первый счет как источник, а второй как место назначения.

Члены структуры `AccountInfo` доступны только для чтения, за исключением `lamports` и `data`. Оба могут быть изменены программой в соответствии с [политикой применения во время выполнения](developing/programming-model/accounts.md#policy). Оба этих члена защищены конструкцией Rust `RefCell`, поэтому они должны быть заимствованы для чтения или записи в них. Причина этого в том, что они оба указывают на исходный массив входных байтов, но в срезе учетных записей может быть несколько записей, которые указывают на одну и ту же учетную запись. Использование `RefCell` гарантирует, что программа случайно не выполнит перекрывающиеся операции чтения/записи одних и тех же базовых данных через несколько структур `AccountInfo`. Если программа реализует собственную функцию десериализации, следует позаботиться о правильной обработке повторяющихся учетных записей.

Данные инструкции представляют собой массив байтов общего назначения из [данных инструкции инструкции](разработка/программирование-модель/транзакции.md#instruction-data), которые обрабатываются.

## Куча

Программы на Rust реализуют кучу напрямую, определяя пользовательский [`global_allocator`]

Программы могут реализовывать свой собственный `global_allocator` в зависимости от своих конкретных потребностей.
Дополнительные сведения см. в пример пользовательской кучи.

## Ограничения

Сетевые программы Rust поддерживают большинство Rust libstd, libcore и liballoc, а также многие сторонние крейты.

Существуют некоторые ограничения, поскольку эти программы работают в однопоточной среде с ограниченными ресурсами и должны быть детерминированными:

- Нет доступа к
  - `rand`
  - `std::fs`
  - `std::net`
  - `std::os`
  - `std::future`
  - `std::process`
  - `std::sync`
  - `std::task`
  - `std::thread`
  - `std::time`
- Ограниченный доступ к:
  - `std::hash`
  - `std::os`
- Bincode чрезвычайно затратен в вычислительном отношении как в циклах, так и в глубине вызовов, и его следует избегать.
- Следует избегать форматирования строк, так как оно требует значительных вычислительных ресурсов.
- Нет поддержки `println!`, `print!`, вместо этого следует использовать Solana [помощники ведения журнала](# logging).
- Среда выполнения накладывает ограничение на количество инструкций, которые программа может выполнить во время обработки одной инструкции. См. [вычислительный бюджет](developing/programming-model/runtime.md#compute-budget) для получения дополнительной информации.

## В зависимости от Rand

Программы вынуждены работать детерминировано, поэтому случайные числа недоступны. Иногда программа может зависеть от ящика, который сам зависит от `rand`, даже если программа не использует какие-либо функции случайных чисел. Если программа зависит от `rand`, компиляция завершится ошибкой, потому что для Solana нет поддержки `get-random`. Обычно ошибка выглядит так:

```
error: target is not supported, for more information see: https://docs.rs/getrandom/#unsupported-targets
   --> /Users/jack/.cargo/registry/src/github.com-1ecc6299db9ec823/getrandom-0.1.14/src/lib.rs:257:9
    |
257 | /         compile_error!("\
258 | |             target is not supported, for more information see: \
259 | |             https://docs.rs/getrandom/#unsupported-targets\
260 | |         ");
    | |___________^
```

Чтобы обойти эту проблему с зависимостями, добавьте следующую зависимость в файл `Cargo.toml` программы:

```
getrandom = { version = "0.1.14", features = ["dummy"] }
```

или если зависимость от getrandom v0.2, добавьте:

```
getrandom = { version = "0.2.2", features = ["custom"] }
```

## Логирование

Макрос `println!` в Rust требует больших вычислительных ресурсов и не поддерживается. Вместо этого предоставляется вспомогательный макрос [`msg!`](https://github.com/solana-labs/solana/blob/d9b0fc0e3eec67dfe4a97d9298b15969b2804fab/sdk/program/src/log.rs#L33).

`msg!` имеет две формы:

```rust
msg!("A string");
```

или

```rust
msg!(0_64, 1_64, 2_64, 3_64, 4_64);
```

Обе формы выводят результаты в журналы программы. Если программа того пожелает, она может эмулировать `println!`, используя `format!`:

```rust
msg!("Some variable: {:?}", variable);
```

Раздел [debugging](debugging.md#logging) содержит больше информации о работе с журналами программ. Rust examples содержит пример ведения журнала.

## Паника

`panic!`, `assert!` и результаты внутренней паники Rust по умолчанию печатаются в [журналы программы](debugging.md#logging).

```
INFO  solana_runtime::message_processor] Finalized account CGLhHSuWsp1gT4B7MY2KACqp9RUwQRhcUFfVSuxpSajZ
INFO  solana_runtime::message_processor] Call BPF program CGLhHSuWsp1gT4B7MY2KACqp9RUwQRhcUFfVSuxpSajZ
INFO  solana_runtime::message_processor] Program log: Panicked at: 'assertion failed: `(left == right)`
      left: `1`,
     right: `2`', rust/panic/src/lib.rs:22:5
INFO  solana_runtime::message_processor] BPF program consumed 5453 of 200000 units
INFO  solana_runtime::message_processor] BPF program CGLhHSuWsp1gT4B7MY2KACqp9RUwQRhcUFfVSuxpSajZ failed: BPF program panicked
```

### Пользовательский обработчик паники

Программы могут переопределить обработчик паники по умолчанию, предоставив свою собственную реализацию.

Сначала определите функцию `custom-panic` в файле `Cargo.toml` программы.

```toml
[features]
default = ["custom-panic"]
custom-panic = []
```

Then provide a custom implementation of the panic handler:

```rust
#[cfg(all(feature = "custom-panic", target_arch = "bpf"))]
#[no_mangle]
fn custom_panic(info: &core::panic::PanicInfo<'_>) {
    solana_program::msg!("program custom panic enabled");
    solana_program::msg!("{}", info);
}
```

В приведенном выше фрагменте показана реализация по умолчанию, но разработчики могут заменить ее чем-то, что лучше соответствует их потребностям.

Одним из побочных эффектов поддержки полных сообщений паники по умолчанию является то, что программы несут затраты, связанные с добавлением большего количества реализации Rust `libstd` в общий объект программы. Типичные программы уже будут использовать достаточное количество `libstd` и могут не заметить значительного увеличения размера общего объекта. Но программы, которые явно пытаются быть очень маленькими, избегая `libstd`, могут иметь значительное влияние (~ 25 КБ). Чтобы устранить это влияние, программы могут предоставлять свой собственный обработчик паники с пустой реализацией.

```rust
#[cfg(all(feature = "custom-panic", target_arch = "bpf"))]
#[no_mangle]
fn custom_panic(info: &core::panic::PanicInfo<'_>) {
    // Do nothing to save space
}
```

## Вычислить бюджет

Используйте системный вызов [`sol_log_compute_units()`](https://github.com/solana-labs/solana/blob/d9b0fc0e3eec67dfe4a97d9298b15969b2804fab/sdk/program/src/log.rs#L141), чтобы записать сообщение, содержащее оставшееся число. вычислительных единиц, которые программа может использовать, прежде чем выполнение будет остановлено

См. [вычислить бюджет](developing/programming-model/runtime.md#compute-budget) для получения дополнительной информации.

## ELF Дамп

Внутреннее устройство общего объекта BPF можно сбросить в текстовый файл, чтобы получить более полное представление о составе программы и о том, что она может делать во время выполнения. Дамп будет содержать как информацию об ELF, так и список всех символов и инструкций, которые их реализуют. Некоторые из сообщений журнала ошибок загрузчика BPF будут ссылаться на определенные номера инструкций, в которых произошла ошибка. Эти ссылки можно найти в дампе ELF, чтобы идентифицировать нарушающую инструкцию и ее контекст.

Чтобы создать файл дампа:

```bash
$ cd <program directory>
$ cargo build-bpf --dump
```

## Примеры

Репозиторий [Solana Program Library github](https://github.com/solana-labs/solana-program-library/tree/master/examples/rust) содержит коллекцию примеров Rust.
