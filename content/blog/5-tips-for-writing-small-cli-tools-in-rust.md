+++
title = "5 советов по написанию небольших инструментов командной строки на Rust"
description = "5 советов по написанию небольших инструментов командной строки на Rust"
weight = 1
+++

[Перевод](https://deterministic.space/rust-cli-tips.html) | Автор оригинала: Pascal Hertleif

Rust - отличный язык для написания небольших инструментов командной строки. Хотя он дает вам некоторые инструменты для общих задач, позволяет создавать красивые абстракции, он также имеет систему типов и подход к дизайну API, которые побуждают вас писать надежный код. Позвольте мне показать вам несколько приемов, которые помогут сделать этот опыт приятным.

## Быстрая обработка аргументов CLI

Есть много библиотек, которые помогут вам в этом. Что мне понравилось, так это structopt: она дает вам возможность аннотировать структуру или перечисление и превращать ее поля / варианты во флаги интерфейса командной строки: 

```rust
#[macro_use] extern crate structopt_derive;

use structopt::StructOpt;

/// Do fancy things
#[derive(StructOpt, Debug)]
#[structopt(name = "fancify")]
struct Cli {
    /// The source, possibly unfancy
    source: String,

    /// Level of fanciness we should aim for
    #[structopt(long = "level", short = "l", default_value = "42")]
    level: u8,

    /// Output file
    #[structopt(long = "output", short = "w", default_value = "/dev/null")]
    output: String,
}

fn main() {
    Cli::from_args();
}
```

Это очень лаконично, но при этом очень мощно! (Он использует хлопок за кулисами.) 

```
$ cargo run -- --help
    Finished dev [unoptimized + debuginfo] target(s) in 0.0 secs
     Running `target/debug/fancify --help`
fancify 0.1.0
Pascal Hertleif <killercup@gmail.com>
Do fancy things

USAGE:
    fancify [OPTIONS] <source>

FLAGS:
    -h, --help       Prints help information
    -V, --version    Prints version information

OPTIONS:
    -l, --level <level>      Level of fanciness we should aim for [default: 42]
    -w, --output <output>    Output file [default: /dev/null]

ARGS:
    <source>    The source, possibly unfancy
```

Или:

```
$ cargo run -- whatever --levl
    Finished dev [unoptimized + debuginfo] target(s) in 0.0 secs
     Running `target/debug/fancify whatever --levl`
error: Found argument '--levl' which wasn't expected, or isn't valid in this context
        Did you mean --level?

USAGE:
    fancify <source> --level <level>

For more information try --help
```

## Обработка ошибок

Это было обновлено в апреле 2020 года.

Во многих приложениях CLI обработка ошибок не должна быть сложной. Все, что вам нужно, это библиотека, которая позволяет вам выделять практически все типы ошибок и, при желании, добавлять к ним некоторый контекст (описания). 

```rust
use std::fs::File;
use anyhow::Context;

// Anyhow exports a type alias for a Result with its own error type.
// Having main return this makes it print the error as well as a list of causes.
fn main() -> anyhow::Result<()> {
    // Let's say this is user input
    let source = "./whatever";
    let level = "42";

    // Opening a file can fail, but the error message is something like
    // "OS error 2: No such file or directory"…
    let source = File::open(source)
        // …so, let's add a bit of context to it:
        .with_context(|| format!("Can't open `{}`", source))?;

    let level = level.parse()?; // can return a ParseIntError
    let source_fanciness = get_fanciness(&source)?; // returns generic error as well

    // An assert that returns an Error
    anyhow::ensure!(source_fanciness < level, "source is already fancy");

    // Everything is fine, and main returns an empty "okay"
    Ok(())
}

fn get_fanciness(_source: &File) -> anyhow::Result<u8> {
    Ok(255) // Let's assume all inputs are fancy
}
```

Это выглядит достаточно просто, правда? Есть много способов улучшить работу с ошибками, например, написав свои собственные типы ошибок. Но для быстрых инструментов CLI в этом чаще всего нет необходимости.

Кстати, вот как выглядит вывод ошибки: 

```
$ cargo run
    Finished dev [unoptimized + debuginfo] target(s) in 0.61 secs
     Running `target/debug/fancify`
Error: Can't open `./whatever`

Caused by:
    No such file or directory (os error 2)
```

Или:

```
$ touch whatever
$ cargo run
    Finished dev [unoptimized + debuginfo] target(s) in 0.0 secs
     Running `target/debug/fancify`
Error: source is already fancy
```

## Множество маленьких крэйтов

Не бойтесь зависеть от множества крэйтов. Cargo действительно хорош тем, что позволяет вам не заботиться о компиляции и обновлении зависимостей, поэтому позвольте Cargo и сообществу Rust помочь вам!

Например, я недавно написал инструмент CLI с 37 строками кода. Это первый блок: 

```rust
#[macro_use] extern crate structopt_derive;
#[macro_use] extern crate error_chain;
#[macro_use] extern crate serde_derive;
#[macro_use] extern crate serde_json as json;
use serde_yaml as yaml;
```

## Множество небольших вспомогательных функций

Я обычно пишу множество мелких функций. Вот пример такой «небольшой вспомогательной функции»: 

```rust
fn open(path: &str) -> Result<File> {
    File::open(path).with_context(|| format!("Can't open `{}`", path))
}
```

Ладно, это немного не впечатляет. Как насчет этого? 

```rust
fn read(path: &str) -> Result<String> {
    let mut result = String::new();
    let mut file = open(path)?;
    file.read_to_string(&mut result)?;
    Ok(result)
}
```

Она на один уровень более абстрактна, чем стандартная библиотека, скрывает выделение String с неизвестной длиной, но ... это действительно удобно.

Я знаю, что можно было бы поместить тела функций прямо в основной код, но дать этим маленьким кодовым блокам имена и получить возможность их повторного использования - это действительно мощный инструмент. Это также делает основную функцию более абстрактной и более простой для чтения (не нужно вдаваться в подробности реализации). Более того (но это обычно не очень хорошо работает в небольших приложениях с интерфейсом командной строки), это упрощает модульное тестирование.

И между прочим: в большинстве небольших инструментов CLI производительность не так важна. Не стесняйтесь предпочесть .clone() добавлению в код параметров времени жизни.

## Множество структур

По моему опыту, использование большого количества структур действительно окупается. Некоторые сценарии: 

- Есть ли выбор между использованием serde_json::Value с набором совпадений или структурой с `#[derive (Deserialize)]`? Выберите структуру, получите производительность, хорошие ошибки и документацию о форме, которую вы ожидаете.
- Передать те же 3 параметра кучке функций? Сгруппируйте их в структуру (кортеж), дайте группе имя и, возможно, даже превратите некоторые из этих функций в методы.
- Видите, как вы пишете много шаблонного кода? Посмотрите, сможете ли вы написать структуру / перечисление и использовать наследование.

## Бонус: ведение журнала

И бонусный раунд: немного регистрации с помощью loggerv! (Это действительно просто, но обычно этого достаточно для приложений с интерфейсом командной строки. На данный момент нет необходимости вдаваться в потоковое воспроизведение журналов JSON в logstash.) 

```rust
#[macro_use] extern crate log;

#[macro_use] extern crate error_chain;
#[macro_use] extern crate structopt_derive;
use structopt::StructOpt;

/// Do fancy things
#[derive(StructOpt, Debug)]
#[structopt(name = "fancify")]
struct Cli {
    /// Enable logging, use multiple `v`s to increase verbosity
    #[structopt(short = "v", long = "verbose")]
    verbosity: u64,
}

quick_main!(|| -> Result<()> {
    let args = Cli::from_args();

    loggerv::init_with_verbosity(args.verbosity)?;

    // ...
    let thing = "foobar";
    debug!("Thing happened: {}", thing);
    // ...

    info!("It's all good!");
    Ok(())
});

error_chain! {
    foreign_links {
        Log(::log::SetLoggerError);
    }
}
```

Давайте запустим его три раза с большим или меньшим количеством подробностей! 

```
$ cargo run
    Finished dev [unoptimized + debuginfo] target(s) in 0.0 secs
     Running `target/debug/fancify`
$ cargo run -- -v
    Finished dev [unoptimized + debuginfo] target(s) in 0.0 secs
     Running `target/debug/fancify -v`
fancify: It's all good!
$ cargo run -- -vv
    Finished dev [unoptimized + debuginfo] target(s) in 0.0 secs
     Running `target/debug/fancify -vv`
fancify: Thing happened: foobar
fancify: It's all good!
```

## Вывод

Это были мои пять советов по написанию небольших приложений CLI на Rust (создание хороших библиотек - другая тема). Если у вас есть еще советы, дайте мне знать!

Если вы хотите копнуть немного глубже, я бы посоветовал посмотреть, как многоплатформенный сборщик бинарных выпусков Rust, как использовать clap для автозаполнения аргументов CLI и как написать интеграционный тест для ваших приложений CLI (предстоящая публикация) .

Спасибо за чтение.