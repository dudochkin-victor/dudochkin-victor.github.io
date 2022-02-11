+++
title = "Руководство по стилю кода"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

## Форматирование

- Отступ с помощью табуляции.
- Строки должны быть длиннее 100 символов только в исключительных случаях и, конечно, не длиннее 120. Для этой цели ширина табуляции считается 4 символа.
- Уровни отступа должны быть больше 5 только в исключительных случаях и, конечно же, не должны превышать 8. Если они больше 5, рассмотрите возможность использования `let` или вспомогательных функций для удаления сложных встроенных выражений.
- Никогда не ставьте пробелы в строке перед непробельным символом
- Последующие строки представляют собой только один отступ от исходной строки.

```rust
fn calculation(some_long_variable_a: i8, some_long_variable_b: i8) -> bool {
    let x = some_long_variable_a * some_long_variable_b
        - some_long_variable_b / some_long_variable_a
        + sqrt(some_long_variable_a) - sqrt(some_long_variable_b);
    x > 10
}
```

* Отступ должен следовать за открытыми скобками/квадратными скобками, но должен быть свернут до наименьшего количества фактически используемых уровней:

```rust
fn calculate(
    some_long_variable_a: f32,
    some_long_variable_b: f32,
    some_long_variable_c: f32,
) -> f32 {
    (-some_long_variable_b + sqrt(
        // two parens open, but since we open & close them both on the
        // same line, only one indent level is used
        some_long_variable_b * some_long_variable_b
        - 4 * some_long_variable_a * some_long_variable_c
    // both closed here at beginning of line, so back to the original indent
    // level
    )) / (2 * some_long_variable_a)
}
```

* `where` имеет отступ, а его элементы сдвинуты на один дальше.
* Списки аргументов или вызовы функций, которые слишком длинны для размещения в одной строке, располагаются с отступом аналогично блокам кода, и как только один параметр имеет такой отступ, все остальные должны быть такими же. Списки параметров запуска также приемлемы для однострочных запусков вызовов базовых функций.

```rust
// OK
fn foo(
    really_long_parameter_name_1: SomeLongTypeName,
    really_long_parameter_name_2: SomeLongTypeName,
    shrt_nm_1: u8,
    shrt_nm_2: u8,
) {
   ...
}

// NOT OK
fn foo(really_long_parameter_name_1: SomeLongTypeName, really_long_parameter_name_2: SomeLongTypeName,
    shrt_nm_1: u8, shrt_nm_2: u8) {
    ...
}
```

```rust
{
    // Complex line (not just a function call, also a let statement). Full
    // structure.
    let (a, b) = bar(
        really_long_parameter_name_1,
        really_long_parameter_name_2,
        shrt_nm_1,
        shrt_nm_2,
    );

    // Long, simple function call.
    waz(
        really_long_parameter_name_1, 
        really_long_parameter_name_2,
        shrt_nm_1, 
        shrt_nm_2,
    );

    // Short function call. Inline.
    baz(a, b);
}
```

* Всегда заканчивайте последний элемент многострочного набора с разделителями-запятыми символом `,`, если это допустимо:

```rust
struct Point<T> {
    x: T,
    y: T,    // <-- Multiline comma-delimited lists end with a trailing ,
}

// Single line comma-delimited items do not have a trailing `,`
enum Meal { Breakfast, Lunch, Dinner };
```

* Избегайте завершающих `;` там, где это не нужно.

```rust
if condition {
    return 1    // <-- no ; here
}
```

* `match` ветви могут быть либо блоками, либо заканчиваться `,`, но не тем и другим одновременно.
* Блоки не должны использоваться без необходимости.

```rust
match meal {
    Meal::Breakfast => "eggs",
    Meal::Lunch => { check_diet(); recipe() },
//    Meal::Dinner => { return Err("Fasting") }   // WRONG
    Meal::Dinner => return Err("Fasting"),
}
```

## Стиль

- Паникующий код требуют явных доказательств, который они вызывают. Вызов `unwrap` не рекомендуется. Исключением из этого правила является тестовыый кодо. Если возможно, предпочтительнее избегать паникеров путем реструктуризации кода.

```rust
let mut target_path = 
    self.path().expect(
        "self is instance of DiskDirectory;\
        DiskDirectory always returns path;\
        qed"
    );
```

* Небезопасный код требует явных доказательств, как это делают паникеры. При внедрении небезопасного кода рассмотрите компромисс между эффективностью, с одной стороны, и надежностью, безопасностью и затратами на обслуживание, с другой.
  
  Вот список вопросов, которые могут помочь оценить компромисс при подготовке или рассмотрении PR:
  
  - насколько более производительным или компактным будет полученный код при использовании небезопасного кода,
  - насколько вероятно, что инварианты могут быть нарушены,
  - проблемы, возникающие из-за использования небезопасного кода, обнаруженного существующими тестами/инструментами,
  - какие последствия, если проблемы скатятся в продакшн.
