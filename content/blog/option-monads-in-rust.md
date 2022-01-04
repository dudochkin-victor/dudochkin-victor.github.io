+++
title = "Варианты монады в Rust"
description = "Варианты монады в Rust"
weight = 1
+++

[Перевод](https://hoverbear.org/blog/option-monads-in-rust/)

Одной из распространенных монадических структур является тип Option (или Maybe в Haskell и других языках). Это можно рассматривать как тип инкапсуляции. Рассмотрим функцию, которая может не выдавать значимое значение для определенных входных данных. Например, 

```rust
fn main() {
  // Parses a string into an integer.
  from_str::<int>("4"); // A valid input.
  from_str::<int>("Potato"); // Definitely invalid.
}
```

Функция from_str не может возвращать значимое значение для «Potato». Rust (и многие другие функциональные языки) не имеют null, так что же нам возвращать? Здесь становится полезным тип Option. В нашем примере вместо возврата типа int функция from_str возвращает тип Option<int>.

В Rust перечисление Option представлено либо Some(x), либо None, где x - инкапсулированное значение. Таким образом, монаду Option можно рассматривать как коробку. Он инкапсулирует значение x, где x - любой тип. Rust определяет Option как таковой, где<T> и (T) обозначают, что он обрабатывает общий тип, а это означает, что T может быть int, str, vec или чем-то еще, даже другими типами Option. 

```rust
enum Option<T> { None, Some(T) }
```

## Value In, Value Out

Поскольку мы можем видеть Option как коробку или тип инкапсуляции, нам нужно иметь возможность помещать вещи в коробку или вынимать вещи.

Поместить значение в опцию восхитительно просто. Просто используйте Some(x) или None вместо x или (мнимого) null. В большинстве случаев вы будете получать или возвращать Option на основе введенных данных, а не просто создавать их напрямую. Вот несколько примеров различных техник. 

```rust
fn main() {
    // Ways to create an Option containing an int.
    let w = Some(3i); // Something
    let x: Option<int> = None; // Nothing
    // Receive from function.
    let y = some_on_even(2); // Something
    let z = some_on_even(3); // Nothing
}

fn some_on_even(val: int) -> Option<int> {
    match val {
        // Matches an even number.
        x if x % 2 == 0 => Some(x),
        // Matches anything else.
        _                     => None
    }
}
```

Чтобы извлечь что-то из Option, нам нужно иметь возможность извлекать или «разворачивать» значение. Есть несколько способов сделать это. Некоторые распространенные методы используют match или .expect().

     Если вы хотите написать код, который не даст сбоев, избегайте .expect() и его двоюродного брата .unwrap() и используйте более безопасные альтернативы, такие как unwrap_or_default() или unwrap_or(). 

```rust
fn main() {
    // Create an Option containing the value 1.
    let a_monad: Option<int> = Some(1);
    // Extract and branch based on result.
    let value_from_match = match a_monad {
        Some(x) => x,
        None    => 0i // A fallback value.
    };
    // Extract with failure message.
    let value_from_expect = a_monad.expect("No result.");
    // Extract, or get a default value
    let value_or_default = a_monad.unwrap_or_default();
    let value_or_fallback = a_monad.unwrap_or(42i);
}
```

## Не просто ноль

К настоящему времени вы, вероятно, задаетесь вопросом примерно следующего:

> Так почему бы просто не иметь null? Что еще дает монада Option?

Это очень хороший вопрос. Каковы преимущества этой парадигмы?

- Вы должны обрабатывать все возможные возвраты или их отсутствие. Компилятор выдаст ошибки, если вы неправильно обработаете Option. Вы не можете просто забыть обработать случай None (или null).
- Нуля не существует. Читателям и потребителям сразу становится ясно, какие функции могут не возвращать значимое значение. Попытка использовать значение из Option без его обработки приводит к ошибке компилятора.
- Значения не заключены в рамки. Значения параметров не переносят указатели, они переносят значения. Чтобы иметь значение NULL, вам обязательно понадобится указатель. (Спасибо, cmr!)
- Композиция становится легкой. Монада Option становится намного более мощной, когда она используется в композиции, поскольку ее характеристики позволяют создавать конвейеры, которым не нужно явно обрабатывать ошибки на каждом шаге.

Давайте подробнее рассмотрим композиционную идею ...

## Составление симфонии функций

Nirvana может составлять ряд функций вместе, не вводя между ними тесную зависимость, так что их можно перемещать или изменять, не беспокоясь о том, как это может повлиять на другие функции. Например, предположим, что у нас есть функции со следующими подписями: 

```rust
fn log(value: f64)     -> f64; // This could fail. (log(-2) == ??)
fn sqrt(value: f64)    -> f64; // This could fail. (sqrt(-2) == ??)
fn square(value: f64)  -> f64;
fn double(value: f64)  -> f64;
fn inverse(value: f64) -> f64;
```

Довольно маленькая математическая библиотека, которая у нас есть! Как насчет того, чтобы придумать способ превратить 20 во что-то еще, используя циклический конвейер? 

```rust
sqrt(-1 * (log(-1 * (20 * 2)))^2)
```

С нашей небольшой библиотекой это выглядело бы примерно так: 

```rust
// This code will not compile, it's invalid.
// `Null` isn't a real type in Rust.
fn main() {
    let number: f64 = 20.;
    match log(inverse(double(number))) {
        x => {
            match sqrt(square(inverse(x)))) {
                y => println!("The result is {}", y),
                Null => println!(".sqrt failed.")
            }
        },
        Null => println!(".log failed.")
    }
}
```

В этом случае у нас были две функции, которые могли выйти из строя, поскольку у нас не было типа Option, автор должен знать и обрабатывать возможные значения Null. Обратите внимание, что ответственность за то, чтобы знать, когда может быть возвращено значение NULL, лежала на программисте, и не забывайте обрабатывать его, а не на компиляторе.

Давайте посмотрим, как будет выглядеть тот же код с использованием монады Option. В этом примере все функции определены соответствующим образом. 

```rust
fn main() {
    let number: f64 = 20.;
    // Perform a pipeline of options.
    let result = Some(number)
        .map(inverse) // Described below.
        .map(double)
        .map(inverse)
        .and_then(log) // Described below.
        .map(square)
        .and_then(sqrt);
    // Extract the result.
    match result {
        Some(x) => println!("Result was {}.", x),
        None    => println!("This failed.")
    }
}
// You can ignore these.
fn log(value: f64) -> Option<f64> {
    match value.log2() {
        x if x.is_normal() => Some(x),
        _                  => None
    }
}
fn sqrt(value: f64) -> Option<f64> {
    match value.sqrt() {
        x if x.is_normal() => Some(x),
        _                  => None
    }
}
fn double(value: f64) -> f64 {
    value * 2.
}
fn square(value: f64) -> f64 {
    value.powi(2 as i32)
}
fn inverse(value: f64) -> f64 {
    value * -1.
}
```

Этот код аккуратно обрабатывает все возможные ответвления результатов, и автору не нужно явно обрабатывать каждый возможный результат None, ему нужно только обработать конечный результат. Если какая-либо из функций, которые могут дать сбой (вызываемые and_then()), все же не сработают, остальные вычисления будут пропущены. Кроме того, это значительно упрощает выражение и понимание конвейера вычислений.

map и and_then (наряду с целым рядом других функций, перечисленных здесь) предоставляют надежный набор инструментов для объединения функций вместе. Давайте посмотрим, их подписи ниже. 

```rust
fn map<U>     (self, f: |T| -> U)         -> Option<U>
fn and_then<U>(self, f: |T| -> Option<U>) -> Option<U>
```

## Интерфейс функтора: .map()

map предоставляет возможность применить функцию подписи | T | -> U в Option<T>, возвращая Option<U>. Это идеально подходит для таких функций, как double(), которые не возвращают Option.

Этот вызов соответствует fmap в Haskell, который является частью функтора. У монад есть эта трэйта, потому что каждая монада является функтором.

## Интерфейс монад: .and_then()

and_then позволяет применить | T | -> Option<U> в Option<T>, возвращая Option<U>. Это позволяет применять функции, которые могут не возвращать значения, такие как sqrt().

Этот вызов соответствует связыванию в Haskell и теоретических определениях Monad. Между тем разворачивание Some<T> или None эквивалентно возврату. (Спасибо dirkt)

## Примеры

Работа с параметрами в векторах. Разбор вектора строк на целые числа. Обратите внимание, что итераторы Rust ленивы, поэтому, если collect() не вызывается, сам итератор может быть скомпонован с другими. 

```rust
fn main() {
    let strings = vec!("4", "12", "foo", "15", "bar", "baz", "1");
    let numbers: Vec<int> = strings.iter()
        // `filter_map` transforms `Vec<&'static str>` to `Vec<int>`
        // Any `None` will be removed,
        // while any `Some` will be unwrapped.
        .filter_map(|&x| from_str::<int>(x))
        // `collect` forces iteration through the lazy iterator.
        .collect();
    println!("{}", numbers);
}
```

Простой конвейер. В этом примере сильное значение разбивается на итератор. next() выбирает следующий токен, который является Option. 

```rust
fn main() {
    let mut input = "15 Bear".split(' ');
    // Need to pull the number and parse it.
    let number = input.next()
        // Process Option<&'static str> to Option<int>
        .and_then(|x| from_str::<int>(x))
        .expect("Was not provided a valid number.");
    // The next token is our animal.
    let animal = input.next()
        .expect("Was not provided an animal.");
    // Ouput `number` times.
    for x in std::iter::range(0, number) {
        println!("{} {} says hi!", animal, x)
    }
}
```
## Обсуждение:

- Сообщение Reddit
- Хакерский новостной пост

## Дополнительные ресурсы:

- Статья в Википедии
- Монады 101
- Учебное пособие по монаде для программистов на Clojure
- Clojure.algo.monads
- Функторы, аппликативы и монады в изображениях
- Горсть монад 

