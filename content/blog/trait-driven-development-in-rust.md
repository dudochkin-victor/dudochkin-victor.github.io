+++
title = "Разработка на основе трейтов в Rust"
description = "Разработка на основе трейтов в Rust"
weight = 1
+++

[Перевод](https://deterministic.space/trait-driven-development-in-rust.html)

На самом деле, название - ложь: здесь мы просто сосредотачиваемся на трэйтах, но писать Rust также легко на основе тестирования, управления данными, управления документацией или [A-Z] [a-z] *.

Как и почти любой язык, Rust содержит множество концепций, которые можно смешивать и на которых можно сосредоточиться. Я считаю трэйты характера самыми интересными и сильными1.

Вместо того, чтобы делать что-то обычным способом ООП и описывать классы объектов, перечисляя их поля и их методы, в Rust вы определяете свои структуры данных (структуры, перечисления), а затем реализуете для них трейты.

## Трэйты и связанные предметы

То есть у вас есть набор вещей, которые связаны с определенной чертой (определенным типом поведения) и определяют, что они собой представляют для типа. Самый простой из этих связанных элементов - functions2. Определив функциональную панель как часть трейта Foo, она становится связанной функцией: 

```rust
trait Foo {
    fn bar(x: i32, y: i32) -> i32;
}
```

Это означает, что теоретически его можно называть как Foo::bar (21, 21). На практике вам нужно реализовать трейт в типе, чтобы иметь возможность его вызывать: 

```rust
trait Foo {
    fn bar(x: i32, y: i32) -> i32;
}

struct Baz {}

impl Foo for Baz {
    fn bar(x: i32, y: i32) -> i32 {
        42
    }
}

#[test]
fn bar_test() {
    let result = Baz::bar(21, 21);
    assert_eq!(42, result);
}
```

Посмотрите, как мы вызвали функцию с помощью Baz::здесь вместо Foo ::? Мы можем делать еще больше безумных вещей!

## Методы

В Rust есть довольно хороший способ определить, является ли функция в трейте методом или просто обычной связанной функцией: если первый параметр - self, это метод. (self имеет тип, в котором реализована характеристика; (изменяемая) ссылка на self тоже подойдет.) 

```rust
struct Amet {}

trait Lorem {
    fn ipsum(&self) -> i32;
}

impl Lorem for Amet {
    fn ipsum(&self) -> i32 {
        42
    }
}

#[test]
fn ipsum_test() {
    let amet = Amet {};
    let result = amet.ipsum();
    assert_eq!(42, result);
}
```

Вы все еще можете вызвать это с помощью синтаксиса, который мы видели ранее3: Lorem::ipsum (&amet).

## Все остальное

Это в основном это. Однако есть еще много чего интересного! Вот с чего стоит начать:

- Анонимные трэйты, реализованные для одного типа (struct Foo (i32); impl Foo {fn new (x: i32) -> Foo {Foo (x)}}, называемый как Foo::new (12))
- Общие трэйты с ограничениями и предложениями where (impl<T> Foo для Option<T> {…}, где T: Eq)
- Производные трэйты (например, Debug, PartialEq и Clone)
- Реализации по умолчанию для методов (запись тела связанной функции в определении трэйта с использованием только других доступных методов)
- Расширяющие трэйты (трэйты, которые реализованы в типах, реализующих другую трэйту)
- Маркерные трэйты (специальные «пустые» трэйты, такие как «Отправить» и «Синхронизировать»)

---
    1. Владение и заимствование можно рассматривать как ортогональные системе черт; в Rust функции живут в симбиозе, например через маркерные трэйты. ↩

    2. Другие связанные элементы, которые вы можете определить в трейте, - это псевдонимы типов и константы (нестабильные с Rust 1.13). ↩

    3. Использование полного имени трэйта и метода часто называется «синтаксисом универсального вызова функции» (UFCS), но, вероятно, его следует называть «синтаксисом полного трэйта и имени метода» (FuQuTraMeNS). ↩

Спасибо за чтение.