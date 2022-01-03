+++
title = "Путешествие в итераторы"
description = "Путешествие в итераторы"
weight = 1
+++

[Перевод](https://hoverbear.org/blog/a-journey-into-iterators/)

Одна из моих любимых функций Rust - итераторы. Это быстрый, безопасный и «ленивый» способ работы со структурами данных, потоками и другими более творческими приложениями.

     Вы можете подыграть на http://play.rust-lang.org/ или зайдя здесь. Эта статья не заменяет документацию или опыт.

## Наш первый итератор

Иметь всего в два раза больше - это здорово, правда? Возьмем набор значений и удвоим его! 

```rust
fn main() {
    // First, we get a set of values.
    let input = [1, 2, 3];
    // Create an iterator over them.
    let iterator = input.iter();
    // Specify things to do along the chain.
    let mapped = iterator.map(|&x| x * 2);
    // Do something with the output.
    let output = mapped.collect::<Vec<usize>>();
    println!("{:?}", output);
}
```

Хорошо, сначала нужно отметить несколько важных моментов, даже из этого простого примера:

- .iter() можно использовать со многими разными типами.
- Объявите значение, затем создайте итератор над ним. В противном случае значение не будет жить достаточно долго, чтобы его можно было перебрать. (Итераторы ленивы и не обязательно владеют своими данными.)
- .collect::<Vec<usize>>() выполняет итерацию по всему итератору и помещает их значения в указанный нами тип сбора данных. (Если вы хотите написать свою собственную структуру данных, взгляните на это.)
- Его можно использовать и сковать! Вам не обязательно указывать значения .collect() в конце вызова .map().

## n за раз

Вы можете получить доступ к следующему элементу в итераторе с помощью .next(). Если вам нужен пакет, вы можете использовать .take (n) для создания нового итератора, который перебирает следующие n элементов. Есть несколько, о которых вы не заботитесь? Используйте .skip (n), чтобы отбросить n элементов. 

```rust
fn main() {
    let vals = [ 1, 2, 3, 4, 5];
    let mut iter = vals.iter();
    println!("{:?}", iter.next());
    println!("{:?}", iter.skip(2).take(2)
        .collect::<Vec<_>>());
}
// Ouputs:
// Some(1)
// [4, 5]
```

## Наблюдая за ленью

Мы говорили о том, что итераторы ленивы, но наш первый пример этого не продемонстрировал. Давайте использовать вызовы .inspect() для наблюдения за оценкой. 

```rust
fn main() {
    let input = [1, 2, 3];
    let iterator = input.iter();
    let mapped = iterator
        .inspect(|&x| println!("Pre map:\t{}", x))
        .map(|&x| x * 10) // This gets fed into...
        .inspect(|&x| println!("First map:\t{}", x))
        .map(|x| x + 5)   // ... This.
        .inspect(|&x| println!("Second map:\t{}", x));
    mapped.collect::<Vec<usize>>();
}
```

Результат: 

```
Pre map:    1
First map:  10
Second map: 15
Pre map:    2
First map:  20
Second map: 25
Pre map:    3
First map:  30
Second map: 35
```

Как видите, функции карты оцениваются только при перемещении итератора. (В противном случае мы бы увидели 1, 2, 3, 10, ...)

     Обратите внимание, как .inspect() предоставляет только свою функцию a &x вместо &mut или самого значения. Это предотвращает мутации и гарантирует, что ваша проверка не нарушит конвейер данных.

Это имеет некоторые действительно интересные последствия, например, мы можем иметь бесконечные или циклические итераторы. 

```rust
fn main() {
    let input = [1, 2, 3];
    // This tells the iterator to cycle over itself.
    let cycled = input.iter().cycle();
    let output = cycled.take(9)
        .collect::<Vec<&usize>>();
    println!("{:?}", output);
}
// Outputs [1, 2, 3, 1, 2, 3, 1, 2, 3]
```

## Что-то общее

Не зацикливайтесь на мысли, что [1, 2, 3] и ему подобные - единственное, на чем вы можете использовать итераторы.

Многие структуры данных поддерживают этот стиль, мы также можем использовать такие вещи, как Vectors и VecDeques! Ищите вещи, реализующие iter(). 

```rust
use std::collections::VecDeque;

fn main() {
    // Create a Vector of values.
    let input = vec![1, 2, 3];
    let iterator = input.iter();
    let mapped = iterator.map(|&x| {
            return x * 2;
        });
    // Gather the result in a RingBuf.
    let output = mapped.collect::<VecDeque<_>>();
    println!("{:?}", output);
}
// Outputs [2, 4, 6]
```

Обратите внимание, как здесь мы собираем в VecDeque? Это потому, что он реализует FromIterator.

Теперь вы, вероятно, думаете: «Ага! Держу пари, вы не можете использовать HashMap, дерево или что-то в этом роде, Hoverbear!» Что ж, ты ошибаешься! Ты сможешь! 

```rust
use std::collections::HashMap;
fn main() {
    // Initialize an input map.
    let mut input = HashMap::<u64, u64>::new();
    input.insert(1, 10); // Type inferred here.
    input.insert(2, 20);
    input.insert(3, 30);
    // Continue...
    let iterator = input.iter();
    let mapped = iterator.map(|(&key, &value)| {
            return (key, value * 10);
        });
    let output = mapped.collect::<Vec<_>>();
    println!("{:?}", output);
}
// [(1, 100), (3, 300), (2, 200)]
```

Когда мы выполняем итерацию по HashMap, функция .map() изменяется, чтобы принять кортеж, а .collect() получает кортежи. Конечно, вы также можете собрать обратно в HashMap (или что-то еще).

Вы заметили, как изменился порядок? HashMap не обязательно в порядке. Помните об этом!

     Попробуйте изменить код, чтобы встроить Vec <(u64, u64)> в HashMap.

## Написание итератора

Итак, мы познакомились с теми вещами, которые могут предлагать итераторы, но мы также можем сделать свои собственные. А как насчет итератора, который считает бесконечно? 

```rust
struct CountUp {
    current: usize,
}

impl Iterator for CountUp {
    type Item = usize;
    // The only fn we need to provide for a basic iterator.
    fn next(&mut self) -> Option<usize> {
        self.current += 1;
        Some(self.current)
    }
}

fn main() {
    // In more sophisticated code use `::new()` from `impl CountUp`
    let iterator = CountUp { current: 0 };
    // This is an infinite iterator, only take so many.
    let output = iterator.take(20).collect::<Vec<_>>();
    println!("{:?}", output);
}
// Outputs [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20]
```

Здесь нам не нужно было вызывать .iter(), и это имеет смысл, поскольку мы фактически реализуем итератор. (Не превращать что-то в итератор, как раньше.)

     Попробуйте изменить значения current и .take().

Посмотрите, как мы могли бы использовать .take() и другие функции, не реализуя их отдельно для нашего нового итератора? Если вы посмотрите документацию для iter, вы увидите, что существуют различные трейты, такие как Iterator, RandomAccessIterator.

## Вне диапазона с диапазонами

В следующих примерах вы увидите использование синтаксиса x..y. Это создает Range. Они реализуют Iterator, поэтому нам не нужно вызывать для них .iter(). Вы также можете использовать (0..100) .step_by (2), если хотите использовать определенные приращения шага, если вы используете их в качестве итераторов.

Обратите внимание, что они открыты и не включают в себя. 

```
0..5 == [ 0, 1, 2, 3, 4, ]
2..6 == [ 2, 3, 4, 5, ]
```
We can also index into our collections.

```rust
fn main() {
    let range = (0..10).collect::<Vec<usize>>();
    println!("{:?}", &range[..5]);
    println!("{:?}", &range[2..5]);
    println!("{:?}", &range[7..]);
}
// Outputs:
// [0, 1, 2, 3, 4]
// [2, 3, 4]
// [7, 8, 9]
```

    Попался: использование .step_by() не работает таким образом, поскольку StepBy не реализует Idx, а Range поддерживает.

## Цепочка и архивирование

Объединение интераторов различными способами позволяет получить очень красивый, выразительный код. 

```rust
fn main() {
    // Demonstrate Chain
    let first = 0..5;
    let second = 5..10;
    let chained = first.chain(second);
    println!("Chained: {:?}", chained.collect::<Vec<_>>());
    // Demonstrate Zip
    let first = 0..5;
    let second = 5..10;
    let zipped = first.zip(second);
    println!("Zipped: {:?}", zipped.collect::<Vec<_>>());
}
// Chained: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
// Zipped: [(0, 5), (1, 6), (2, 7), (3, 8), (4, 9)]
```

.zip() позволяет объединять итераторы, а .chain() эффективно создает «расширенный» итератор. Да, есть .unzip().

     Попробуйте использовать .zip() на двух срезах usize, а затем .collect() получившиеся кортежи для создания HashMap.

## Любознательность

.count(), .max_by(), .min_by(), .all() и .any() - распространенные способы обращения к итератору. 

```rust
#![feature(core)] // Must be run on nightly at time of publishing.
#[derive(Eq, PartialEq, Debug)]
enum BearSpecies { Brown, Black, Polar, Grizzly }

#[derive(Debug)]
struct Bear {
    species: BearSpecies,
    age: usize
}

fn main() {
    let bears = [
        Bear { species: BearSpecies::Brown, age: 5 },
        Bear { species: BearSpecies::Black, age: 12 },
        Bear { species: BearSpecies::Polar, age: 15 },
        Bear { species: BearSpecies::Grizzly, age: 16 },
    ];
    // Max/Min of a set.
    let oldest = bears.iter().max_by(|x| x.age);
    let youngest = bears.iter().min_by(|x| x.age);
    println!("Oldest: {:?}\nYoungest: {:?}", oldest, youngest);
    // Any/All
    let has_polarbear = bears.iter().any(|x| {
        x.species == BearSpecies::Polar
    });
    let all_minors = bears.iter().all(|x| {
        x.age <= 18
    });
    println!("At least one polarbear?: {:?}", has_polarbear);
    println!("Are they all minors? (<18): {:?}", all_minors);
}
// Outputs:
// Oldest: Some(Bear { species: Grizzly, age: 16 })
// Youngest: Some(Bear { species: Brown, age: 5 })
// At least one polarbear?: true
// Are they all minors? (<18): true
```

    Попробуйте использовать один и тот же итератор для всех вышеперечисленных вызовов. .any() - единственный, который заимствует изменчиво и не будет работать так же, как другие. Это потому, что он не обязательно может использовать весь итератор.

## Фильтр, Карта, Красный ... Подождите ... Сложите

Если вы, как и я, привыкли к Javascript, вы, вероятно, ожидаете святой троицы из .filter(), .map(), .reduce(). Что ж, они все есть и в Rust, но .reduce() называется .fold(), что я предпочитаю.

Базовый пример: 

```rust
fn main() {
    let input = 1..10;
    let output = input
        .filter(|&item| item % 2 == 0) // Keep Evens
        .map(|item| item * 2) // Multiply by two.
        .fold(0, |accumulator, item| accumulator + item);
    println!("{}", output);
}
```

Конечно, не пытайтесь быть слишком умным, приведенное выше может быть просто: 

```rust
fn main() {
    let input = 1..10;
    let output = input
        .fold(0, |acc, item| {
            if b % 2 == 0 {
                acc + (item*2)
            } else {
                acc
            }
        });
    println!("{}", output);
}
```

Способность подходить к подобным проблемам разными способами позволяет Rust быть довольно гибким и выразительным.

## Разделить и сканировать

Также есть скан, если вам нужен вариант складывания, который каждый раз дает результат. Это полезно, если вы ждете определенной накопленной суммы и хотите проверять каждую итерацию.

Возможно разделение итератора на две части. Вы можете просто использовать простую функцию группировки, которая возвращает логическое значение с разделением.

Давайте воспользуемся этими двумя концепциями, чтобы разделить большой кусок, сгруппировать его по совпадениям и разногласиям, а затем постепенно суммировать их и убедиться, что часть эвенов всегда меньше суммы шансов. (Это потому, что даже начинается с 0.) 

```rust
fn main() {
    let set = 0..1000;
    let (even, odd): (Vec<_>, Vec<_>) = set.partition(|&n| n % 2 == 0);
    let even_scanner = even.iter().scan(0, |acc, &x| { *acc += x; Some(*acc) });
    let odd_scanner  = odd.iter().scan(0, |acc, &x| { *acc += x; Some(*acc) });
    let even_always_less = even_scanner.zip(odd_scanner)
        .all(|(e, o)| e <= o);
    println!("Even was always less: {}", even_always_less);
}
// Outputs:
// Even was always less: true
```

Сканирование можно использовать для получения таких вещей, как скользящее среднее. Это полезно при чтении файлов, данных и датчиков. Разбиение на разделы - обычная задача при перетасовке данных.

Другая общая цель - сгруппировать элементы вместе на основе определенного значения. Теперь, если вы ожидаете от Lodash чего-то вроде _.groupBy(), это не так просто. Учтите: в Rust есть BTreeMap, HashMap, VecMap и другие типы данных, наш метод группировки не должен быть самоуверенным.

Чтобы использовать простой пример, давайте создадим бесконечный итератор, который будет циклически проходить от 0 до 5 включительно. В вашем собственном коде это могут быть сложные структуры или кортежи, но пока подойдут простые целые числа. Мы сгруппируем их по трем категориям: нули, пятерки и остальные. 

```rust
use std::collections::HashMap;

#[derive(Debug, PartialEq, Eq, Hash)]
enum Kind { Zero, Five, Other }

fn main() {
    let values = 0..6; // Not inclusive.
    let cycling = values.cycle();
    // Group them into a HashMap.
    let grouped = cycling.take(20).map(|x| {
        // Return a tuple matching the (key, value) desired.
        match x {
            x if x == 5 => (Kind::Five, 5),
            x if x == 0 => (Kind::Zero, 0),
            x => (Kind::Other, x),
        }
    // Accumulate the values
    }).fold(HashMap::<Kind, Vec<_>>::new(), |mut acc, (k, x)| {
        acc.entry(k).or_insert(vec![]).push(x);
        acc
    });
    println!("{:?}", grouped);
}
// Outputs: {Zero: [0, 0, 0, 0], Five: [5, 5, 5], Other: [1, 2, 3, 4, 1, 2, 3, 4, 1, 2, 3, 4, 1]}
```
    Бессмысленно хранить все эти реплицированные значения. Попробуйте сделать так, чтобы этот пример возвращал количество событий вместо значений. В качестве дальнейшего исследования попробуйте использовать этот метод для более сложного значения, такого как структура, вы также можете изменить используемые ключи.

## С флангом

Типаж DoubleEndedIterator полезен для определенных целей. Например, когда вам нужно поведение очереди и стека. 

```rust
fn main() {
    let mut vals = 0..10;
    println!("{:?}", vals.next());
    println!("{:?}", vals.next_back());
    println!("{:?}", vals.next());
    println!("{:?}", vals.next_back());
}
// Some(0)
// Some(9)
// Some(1)
// Some(8)
```

## Играйте самостоятельно!

Это хорошее время, чтобы сделать перерыв, выпить чаю и открыть манеж. Попробуйте изменить один из приведенных выше примеров, поиграйте с некоторыми новыми вещами в документации API ниже и, как правило, хорошо проводите время.

- std::collections
- std::iter

Если вы застряли, не паникуйте! Попробуйте погуглить об ошибке, если она вас сбивает с толку, Rust имеет активность в домене * .rust-lang.org, а также в Github и Stack Exchange. Вы также можете написать мне по электронной почте или посетить нас в IRC.

Мы просто царапаем поверхность ... Нас ждет огромный мир. 