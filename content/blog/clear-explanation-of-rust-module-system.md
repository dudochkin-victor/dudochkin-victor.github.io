+++
title = "Четкое объяснение модульной системы Rust"
description = "Четкое объяснение модульной системы Rust"
weight = 1
hash = "467e5b19"
+++

[Перевод](https://www.sheshbabu.com/posts/rust-module-system/) | Автор оригинала: xxxx

Модульная система Rust на удивление сбивает с толку и вызывает много разочарований у новичков.

В этом посте я объясню модульную систему на практических примерах, чтобы вы получили четкое представление о том, как она работает, и сразу же применили ее в своих проектах.

Поскольку модульная система Rust довольно уникальна, я прошу читателя прочитать этот пост непредвзято и не сравнивать его с тем, как модули работают на других языках.

Давайте воспользуемся этой файловой структурой для моделирования реального проекта: 

```
my_project
├── Cargo.toml
└─┬ src
  ├── main.rs
  ├── config.rs
  ├─┬ routes
  │ ├── health_route.rs
  │ └── user_route.rs
  └─┬ models
    └── user_model.rs
```

Вот как мы можем использовать наши модули:

![](/imgs/posts/467e5b19_01.png)

Этих трех примеров должно быть достаточно, чтобы объяснить, как работает модульная система Rust.

## Пример 1

Начнем с первого примера - импорта config.rs в main.rs. 

```rust
// main.rs
fn main() {
  println!("main");
}

// config.rs
fn print_config() {
  println!("config");
}
```

Первая ошибка, которую делают все, - это потому, что у нас есть такие файлы, как config.rs, health_route.rs и т.д., Мы думаем, что эти файлы являются модулями, и можем импортировать их из других файлов.

Вот что мы видим (дерево файловой системы) и то, что видит компилятор (дерево модулей):

![](/imgs/posts/467e5b19_02.png)

Удивительно, но компилятор видит только модуль crate, которым является наш файл main.rs. Это связано с тем, что нам нужно явно построить дерево модулей в Rust - не существует неявного сопоставления между деревом файловой системы и деревом модулей.

> Нам нужно явно построить дерево модулей в Rust, здесь нет неявного сопоставления с файловой системой

Чтобы добавить файл в дерево модулей, нам нужно объявить этот файл как подмодуль, используя ключевое слово mod. Следующее, что смущает людей, это то, что вы предполагаете, что мы объявляем файл как модуль в том же файле. Но нам нужно объявить это в другом файле! Поскольку в дереве модулей есть только main.rs, давайте объявим config.rs как подмодуль в main.rs.

> Ключевое слово mod объявляет подмодуль

Ключевое слово mod имеет следующий синтаксис: 

```rust
mod my_module;
```

Здесь компилятор ищет my_module.rs или my_module / mod.rs в том же каталоге. 

```
my_project
├── Cargo.toml
└─┬ src
  ├── main.rs
  └── my_module.rs
```

or
```
my_project
├── Cargo.toml
└─┬ src
  ├── main.rs
  └─┬ my_module
    └── mod.rs
```

Поскольку main.rs и config.rs находятся в одном каталоге, давайте объявим модуль config следующим образом:

```rust
// main.rs
+ mod config;

fn main() {
+ config::print_config();
  println!("main");
}

// config.rs
fn print_config() {
  println!("config");
}
```

Мы обращаемся к функции print_config с помощью синтаксиса ::.

Вот как выглядит дерево модулей:

![](/imgs/posts/467e5b19_03.png)

Мы успешно объявили модуль конфигурации! Но этого недостаточно для вызова функции print_config внутри config.rs. Почти все в Rust по умолчанию является закрытым, нам нужно сделать функцию общедоступной с помощью ключевого слова pub:

> Ключевое слово pub делает информацию общедоступной 

```rust
// main.rs
mod config;

fn main() {
  config::print_config();
  println!("main");
}

// config.rs
- fn print_config() {
+ pub fn print_config() {
  println!("config");
}
```

Теперь это работает. Мы успешно вызвали функцию, определенную в другом файле!

## Пример 2

Давайте попробуем вызвать функцию print_health_route, определенную в routes / health_route.rs из main.rs.

```rust
// main.rs
mod config;

fn main() {
  config::print_config();
  println!("main");
}

// routes/health_route.rs
fn print_health_route() {
  println!("health_route");
}
```

Как мы обсуждали ранее, мы можем использовать ключевое слово mod только для my_module.rs или my_module / mod.rs в том же каталоге.

Итак, чтобы вызывать функции внутри routes / health_route.rs из main.rs, нам нужно сделать следующее:

- Создайте файл с именем routes / mod.rs и объявите подмодуль маршрутов в main.rs
- Объявите подмодуль health_route в routes / mod.rs и сделайте его общедоступным
- Сделайте функции внутри health_route.rs общедоступными 

```
my_project
├── Cargo.toml
└─┬ src
  ├── main.rs
  ├── config.rs
  ├─┬ routes
+ │ ├── mod.rs
  │ ├── health_route.rs
  │ └── user_route.rs
  └─┬ models
    └── user_model.rs
```

```rust
// main.rs
mod config;
+ mod routes;

fn main() {
+ routes::health_route::print_health_route();
  config::print_config();
  println!("main");
}

// routes/mod.rs
+ pub mod health_route;

// routes/health_route.rs
- fn print_health_route() {
+ pub fn print_health_route() {
  println!("health_route");
}
```

Вот как выглядит дерево модулей:

![](/imgs/posts/467e5b19_04.png)

Теперь мы можем вызвать функцию, определенную в файле внутри папки.

## Пример 3

Давайте попробуем позвонить из main.rs => routes / user_route.rs => models / user_model.rs 

```rust
// main.rs
mod config;
mod routes;

fn main() {
  routes::health_route::print_health_route();
  config::print_config();
  println!("main");
}

// routes/user_route.rs
fn print_user_route() {
  println!("user_route");
}

// models/user_model.rs
fn print_user_model() {
  println!("user_model");
}
```

Мы хотим вызвать функцию print_user_model из print_user_route из main.

Давайте внесем те же изменения, что и раньше - объявим подмодули, сделаем функции общедоступными и добавим файл mod.rs. 

```
my_project
├── Cargo.toml
└─┬ src
  ├── main.rs
  ├── config.rs
  ├─┬ routes
  │ ├── mod.rs
  │ ├── health_route.rs
  │ └── user_route.rs
  └─┬ models
+   ├── mod.rs
    └── user_model.rs
```

```rust
// main.rs
mod config;
mod routes;
+ mod models;

fn main() {
  routes::health_route::print_health_route();
+ routes::user_route::print_user_route();
  config::print_config();
  println!("main");
}

// routes/mod.rs
pub mod health_route;
+ pub mod user_route;

// routes/user_route.rs
- fn print_user_route() {
+ pub fn print_user_route() {
  println!("user_route");
}

// models/mod.rs
+ pub mod user_model;

// models/user_model.rs
- fn print_user_model() {
+ pub fn print_user_model() {
  println!("user_model");
}
```

Вот как выглядит дерево модулей:

![](/imgs/posts/467e5b19_05.png)

Подождите, на самом деле мы не вызывали print_user_model из print_user_route! До сих пор мы вызывали только функции, определенные в других модулях из main.rs, как это сделать из других файлов?

Если мы посмотрим на наше дерево модулей, функция print_user_model находится в пути crate::models::user_model. Итак, чтобы использовать модуль в файлах, которые не являются main.rs, мы должны думать о пути, необходимом для достижения этого модуля в дереве модулей. 

```rust
// routes/user_route.rs
pub fn print_user_route() {
+ crate::models::user_model::print_user_model();
  println!("user_route");
}
```

Мы успешно вызвали функцию, определенную в файле, из файла, который не является main.rs.

## супер

Полное имя становится слишком длинным, если наша файловая организация состоит из нескольких каталогов. Допустим, по какой-то причине мы хотим вызвать print_health_route из print_user_route. Они находятся по путям crate::routes::health_route и crate::routes::user_route соответственно.

Мы можем вызвать его, используя полное имя crate::routes::health_route::print_health_route(), но мы также можем использовать относительный путь super::health_route::print_health_route() ;. Обратите внимание, что мы использовали super для обозначения родительской области.

> Ключевое слово super в пути к модулю относится к родительской области 

```rust
pub fn print_user_route() {
  crate::routes::health_route::print_health_route();
  // can also be called using
  super::health_route::print_health_route();

  println!("user_route");
}
```

## use

Было бы утомительно использовать полное имя или даже относительное имя в приведенных выше примерах. Чтобы сократить имена, мы можем использовать ключевое слово use для привязки пути к новому имени или псевдониму.

> Ключевое слово use используется для сокращения пути к модулю 

```rust
pub fn print_user_route() {
  crate::models::user_model::print_user_model();
  println!("user_route");
}
```

Приведенный выше код можно реорганизовать как: 

```rust
use crate::models::user_model::print_user_model;

pub fn print_user_route() {
  print_user_model();
  println!("user_route");
}
```

Вместо использования имени print_user_model мы также можем присвоить ему псевдоним другого: 

```rust
use crate::models::user_model::print_user_model as log_user_model;

pub fn print_user_route() {
  log_user_model();
  println!("user_route");
}
```

## Внешние модули

Зависимости, добавленные в Cargo.toml, доступны глобально для всех модулей внутри проекта. Нам не нужно явно импортировать или объявлять что-либо, чтобы использовать зависимость.

> Внешние зависимости глобально доступны для всех модулей внутри проекта.

Например, предположим, что мы добавили крэйт rand в наш проект. Мы можем использовать его в нашем коде напрямую как: 

```rust
pub fn print_health_route() {
  let random_number: u8 = rand::random();
  println!("{}", random_number);
  println!("health_route");
}
```

Мы также можем использовать use для сокращения пути:

```rust
use rand::random;

pub fn print_health_route() {
  let random_number: u8 = random();
  println!("{}", random_number);
  println!("health_route");
}
```

## Резюме

- Модульная система явная - нет сопоставления 1: 1 с файловой системой.
- Мы объявляем файл как модуль в его родительском элементе, а не сам по себе
- Ключевое слово mod используется для объявления подмодулей
- Нам нужно явно объявить функции, структуры и т.д. Как общедоступные, чтобы их можно было использовать в других модулях.
- Ключевое слово pub делает информацию общедоступной.
- Ключевое слово use используется для сокращения пути к модулю
- Нам не нужно явно объявлять сторонние модули.

Спасибо за чтение! Не стесняйтесь подписываться на меня в Twitter, чтобы увидеть больше подобных сообщений :) 
