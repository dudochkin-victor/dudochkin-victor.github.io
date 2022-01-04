+++
title = "Необязательные аргументы в Rust"
description = "Необязательные аргументы в Rust"
weight = 1
+++

[Перевод](https://hoverbear.org/blog/optional-arguments/)

При разработке API для вашего крэйта может возникнуть одна тема - как обрабатывать необязательные аргументы. Давайте изучим наши возможности в Rust!

## У нас нет знаков или множественных реализаций

В настоящее время Rust не предлагает функций с вариативными или необязательными аргументами. Это означает, что это невозможно: 

```rust
fn impossible(val: bool) {}
fn impossible(val: bool, other: bool) {}
fn impossible(val: bool, rest: bool...) {}
```

Переходя с таких языков, как JS или Ruby, вы можете упустить эту функциональность. В частности, я часто искал способ передать дополнительную конфигурацию или значение. 

```rust
// In the context of an configuration structure for a client:
client.connect("127.0.0.1")?;
client.connect("127.0.0.1", custom_config)?;
```

> Примечание относительно области действия: в настоящее время мне не интересно писать о том, как работать с вариативными аргументами. Если вам это не терпится, попробуйте сами, рассмотрите макрос, справочный формат!().

## Просто добавьте еще одну функцию!

Если это невозможно, какова жизнеспособная альтернатива?

Самый очевидный - иметь две функции. Технически в этом нет ничего плохого. Это совершенно разумно и приводит к очень приемлемому API. Никаких уловок, и это сразу понятно: 

```rust
fn connect(addr: String) -> Result<Self, io::Error> {
    Self::connect_with_config(addr, Config::default())
}

fn connect_with_config(addr: String, config: Config) -> Result<Self, io::Error> {
    // ...
}
```

Но это очень конкретный пример. Иногда бывают общие случаи, когда это не так разумно. Например, предположим, что у нас есть сообщение отправки, которое обычно принимает полезную нагрузку, но не поддерживает полезную нагрузку (например, для проверки связи). На данный момент техника выглядит странно: вы либо получаете сообщение с message_with_payload() везде, либо с функцией ping(), либо с функцией message_without_payload(). Используя свое воображение, вы могли бы придумать другие примеры в вашем собственном коде, где вы столкнулись с этим. 

## У нас есть варианты

Следующим логическим шагом, который начинает использовать абстракции Rust, является использование необязательного типа. 

```rust
fn message(addr: String, maybe_payload: Option<Vec<u8>>) -> Result<u64, io::Error> {
    // ...
    if let Some(payload) = maybe_payload {
        // Off you go...
    }
    // ...
}
// Using it:
message("127.0.0.1", Some(vec![1,2,3]))?;
message("127.0.0.1", None)?;
```

Это, безусловно, выполняет свою работу! Теперь у нас может быть одна функция, которая может выполнять внутреннюю обработку независимо от того, указано значение или нет. Это немного загромождает API, но, похоже, теперь у нас возникло странное ощущение, что варианты Some(_) и None слоняются по сторонам.

Мы решаем проблему с помощью некоторых универсальных шаблонов, чтобы лучше понять, чего мы пытаемся достичь, и для поддержки некоторых микробенчмарков. 

```rust
fn value_arg<Thing>(thing: Thing) -> Thing {
    thing
}

fn optional_arg<Thing>(thing: Option<Thing>) -> Thing
where Thing: Default {
    thing.unwrap_or(Thing::default())
}
```

Как видите, они намеренно настолько просты, насколько это возможно, но не позволяют оптимизировать их, они возвращают свое значение и позволяют испытателю принять их результат.

Давайте избавимся от Some(_). Мы можем использовать дженерики через impl Trait, inline generic или предложения where.

## В сторону: почему мне нравится impl Trait

Я думаю, что это обеспечивает более лаконичный стиль, лучшую семантику для некоторых случаев использования и помогает избежать одной конкретной ошибки удобства использования, которую легко сделать. Учтите следующее: 

```rust
fn handles_a_pair_of_stringish<Stringish>(first: Stringish, second: Stringish)
where Stringish: AsRef<str> {
    // ...
}

fn handles_a_pair_of_varying_stringish(first: impl AsRef<str>, second: impl AsRef<str>) {
    // ...
}

#[test]
fn send_stringishes_of_difference_type() {
    handles_a_pair_of_stringish("foo".to_string(), "foo"); // This won't compile!
    handles_a_pair_of_varying_stringish("foo".to_string(), "foo");
}
```

Я думаю, что в таких ситуациях impl Trait - эффективный выбор для удобочитаемости и гибкости. Я не призываю вас использовать его везде и везде. Стремитесь к удобочитаемости и понятности, а не к "гладким" / непонятным API.

## Общая реализация

Чтобы улучшить этот дизайн, мы можем обратить внимание на некоторые ключевые реализации трейтов: 

- impl<T> Into<Option<T>> for T
- impl<T> Default for Option<T>


Это означает, что мы можем делать следующее:

```rust
fn optional_arg_with_into<Thing>(
    thing: impl Into<Option<Thing>>
) -> Thing
where Thing: Default {
    thing.into().unwrap_or(Thing::default())
}

#[test]
fn verify_assemptions() {
    optional_arg_with_into(Vec::<u64>::default());
    optional_arg_with_into(vec![1_u64, 2, 3]);
    let out: Vec<u64> = optional_arg_with_into(None::<Vec<u64>>); // Type hinting
}
```

В этом очень ограниченном демонстрационном коде нам нужно предоставить довольно много подсказок типа для аргумента None. Обычно это не относится к большему количеству проектов с нетривиальной кодовой базой.

На этом этапе вы можете использовать пару основных характеристик (по умолчанию и From), чтобы предоставить вашим пользователям более гибкий API.

## Предостережения

Как вы могли заметить в последнем примере, в случае очень простого кода это может быть довольно многословным. Это потому, что мы ничего не делаем с возвращаемыми значениями / аргументами. По мере роста вашего проекта необходимость в подсказках исчезнет, так как компилятор сможет делать больше выводов о типах вещей.

Использование этой техники может снизить вашу способность гибко принимать вещи, которые превращаются во внутренний аргумент. Например, a U, где `impl From<U> for T`. Возможно, вам придется перепрыгнуть через обручи. Простой способ обойти это заставит ваших пользователей использовать `T::from (u)` в позиции аргумента при вызове. Тем не менее, не так уж и плохо.

## Бенчмаркинг

Давайте же убедимся, что добавление этой гибкости не сильно повлияет на производительность нашего кода. Я использовал Criterion и написал небольшой набор для нашего демонстрационного кода. Поскольку код тривиален (он просто преобразует и преобразует обратно), мы можем изучить стоимость простого выполнения этой операции. Вот полный код стенда: 

```rust
#[macro_use(criterion_main, criterion_group)] extern crate criterion;
use criterion::Criterion;

criterion_main!(benches);
criterion_group!(benches, optional_args::group);

mod optional_args {
    use criterion::Criterion;

    fn value_arg<Thing>(thing: Thing) -> Thing {
        thing
    }

    fn optional_arg<Thing>(thing: Option<Thing>) -> Thing
    where Thing: Default {
        thing.unwrap_or(Thing::default())
    }

    fn optional_arg_with_into<Thing>(
        thing: impl Into<Option<Thing>>
    ) -> Thing
    where Thing: Default {
        thing.into().unwrap_or(Thing::default())
    }

    pub(crate) fn group(c: &mut Criterion) {
        c.bench_function("value 1_u64", |b| b.iter(|| -> u64 {
            let value = value_arg(1_u64);
            value + 1
        }));
        c.bench_function("optional Some(1_u64)", |b| b.iter(|| -> u64 {
            let value = optional_arg(Some(1_u64));
            value + 1
        }));
        c.bench_function("optional_into 1_u64", |b| b.iter(|| -> u64 {
            let value = optional_arg_with_into(1_u64);
            value + 1
        }));
        c.bench_function("optional_into Some(1_u64)", |b| b.iter(|| -> u64 {
            let value: u64 = optional_arg_with_into(Some(1_u64));
            value + 1
        }));
        c.bench_function("optional_into (u64) None", |b| b.iter(|| -> u64 {
            let value: u64 = optional_arg_with_into(None);
            value + 1
        }));
        c.bench_function("optional_into 1_u8.into()", |b| b.iter(|| -> u64 {
            let value: u64 = optional_arg_with_into(u64::from(1_u8));
            value + 1
        }));
        c.bench_function("optional_into Vec<u64>::new()", |b| b.iter(|| -> Vec<u64> {
            let value: Vec<u64> = optional_arg_with_into(Vec::<u64>::new());
            value
        }));
        c.bench_function("optional_into (Vec<u64>) None", |b| b.iter(|| -> Vec<u64> {
            let value: Vec<u64> = optional_arg_with_into(None::<Vec<u64>>);
            value
        }));
    }
}
```

Это довольно быстро, достаточно, поэтому я до сих пор не совсем уверен, что его как-то не оптимизируют. Компьютеры коварны. 

```
value 1_u64             time:   [264.11 ps 264.50 ps 264.97 ps]                        
Found 15 outliers among 100 measurements (15.00%)
  1 (1.00%) low mild
  5 (5.00%) high mild
  9 (9.00%) high severe

optional Some(1_u64)    time:   [263.41 ps 263.86 ps 264.44 ps]                                 
Found 18 outliers among 100 measurements (18.00%)
  3 (3.00%) low mild
  4 (4.00%) high mild
  11 (11.00%) high severe

optional_into 1_u64     time:   [258.67 ps 259.43 ps 260.37 ps]                                
Found 7 outliers among 100 measurements (7.00%)
  3 (3.00%) high mild
  4 (4.00%) high severe

optional_into Some(1_u64)                                                                            
                        time:   [252.95 ps 253.41 ps 253.94 ps]
Found 6 outliers among 100 measurements (6.00%)
  2 (2.00%) high mild
  4 (4.00%) high severe

optional_into (u64) None                                                                            
                        time:   [256.31 ps 256.89 ps 257.70 ps]
Found 12 outliers among 100 measurements (12.00%)
  3 (3.00%) high mild
  9 (9.00%) high severe

optional_into 1_u8.into()                                                                            
                        time:   [255.77 ps 256.33 ps 257.04 ps]
Found 12 outliers among 100 measurements (12.00%)
  1 (1.00%) low mild
  7 (7.00%) high mild
  4 (4.00%) high severe

optional_into Vec<u64>::new()                                                                             
                        time:   [853.18 ps 858.14 ps 863.70 ps]
Found 4 outliers among 100 measurements (4.00%)
  4 (4.00%) high mild

optional_into (Vec<u64>) None                                                                             
                        time:   [830.59 ps 833.04 ps 835.56 ps]
```

## Что мы узнали и выводы

Мы узнали, что существует множество вариантов разработки API с помощью Rust. Во время нашего исследования мы отметили, что выбор семантического объявления наших функций и/или включение более гибкого API не требует больших затрат.

Если вам интересно узнать больше о включении гибких API-интерфейсов в Rust, я рекомендую изучить Руководство. Я также рекомендую изучить шаблон Builder, поскольку он обычно лучше используется в Rust.

Спасибо за чтение! Надеюсь вскоре использовать ваш прекрасный код. 😉

> P.S. Если вы ищете должность высшего уровня, увлечены этой темой и имеете опыт работы с распределенными системами, критически важными для безопасности системами, структурами данных, сетями и проектированием хаоса, напишите мне. Мы можем поговорить о возможности поработать в моей команде в PingCAP над экосистемой с открытым исходным кодом для TiKV и ее зависимостей. В будущем вы возьмете на себя наставничество новых юниоров, участников открытого исходного кода и членов сообщества в целом. Дружественность к глобально удаленным, конкурентоспособная заработная плата, покрытие поездок для говорящих, очень дружественная к меньшинствам (местоположение, раса, половая принадлежность, пол и т. Д.). 

