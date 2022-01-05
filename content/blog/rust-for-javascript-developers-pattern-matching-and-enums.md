+++
title = "Rust для JavaScript-разработчиков - сопоставление с образцом и перечисления"
description = "Rust для JavaScript-разработчиков - сопоставление с образцом и перечисления"
weight = 1
hash = "d8ce9ce5"
+++

[Перевод](https://www.sheshbabu.com/posts/rust-for-javascript-developers-pattern-matching-and-enums/) | Автор оригинала: Shesh

Это четвертая часть из серии о знакомстве с языком Rust для JavaScript-разработчиков. Вот все главы:

1. Обзор инструментальной экосистемы
2. Переменные и типы данных
3. Функции и поток управления
4. Сопоставление с образцом и перечисления

## Соответствие шаблону

Чтобы понять, что такое сопоставление с шаблоном, давайте начнем с чего-нибудь знакомого по JavaScript - Switch Case.

Вот простой пример, который использует регистр переключателя в JavaScript: 

```js
function print_color(color) {
  switch (color) {
    case "rose":
      console.log("roses are red,");
      break;
    case "violet":
      console.log("violets are blue,");
      break;
    default:
      console.log("sugar is sweet, and so are you.");
  }
}

print_color("rose"); // roses are red,
print_color("violet"); // violets are blue,
print_color("you"); // sugar is sweet, and so are you.
```

Вот эквивалентный код на Rust: 

```rust
fn print_color(color: &str) {
  match color {
    "rose" => println!("roses are red,"),
    "violet" => println!("violets are blue,"),
    _ => println!("sugar is sweet, and so are you."),
  }
}

fn main() {
  print_color("rose"); // roses are red,
  print_color("violet"); // violets are blue,
  print_color("you"); // sugar is sweet, and so are you.
}
```

Большая часть кода должна быть сразу понятна. Выражение соответствия имеет следующую сигнатуру: 

```
match VALUE {
  PATTERN1 => EXPRESSION1,
  PATTERN2 => EXPRESSION2,
  PATTERN3 => EXPRESSION3,
}
```

Синтаксис толстой стрелки => может сбить нас с толку из-за сходства со стрелочными функциями JavaScript, но они не связаны. Последний шаблон, в котором используется подчеркивание _, называется общим шаблоном и аналогичен случаю по умолчанию в случае переключения. Каждая комбинация ШАБЛОН => ВЫРАЖЕНИЕ называется матчевой рукой.

Приведенный выше пример на самом деле не показывает, насколько полезно сопоставление с образцом - он просто выглядит как switch case с другим синтаксисом и причудливым именем. Давайте поговорим о деструктуризации и перечислениях, чтобы понять, почему сопоставление с образцом полезно.

## Деструктуризация

Деструктуризация - это процесс извлечения внутренних полей массива или структуры в отдельные переменные. Если вы использовали деструктуризацию в JavaScript, она очень похожа на Rust.

Вот пример на JavaScript: 

```js
let rgb = [96, 172, 57];
let [red, green, blue] = rgb;
console.log(red); // 96
console.log(green); // 172
console.log(blue); // 57

let person = { name: "shesh", city: "singapore" };
let { name, city } = person;
console.log(name); // name
console.log(city); // city
```

Вот тот же пример в Rust: 

```rust
struct Person {
  name: String,
  city: String,
}

fn main() {
  let rgb = [96, 172, 57];
  let [red, green, blue] = rgb;
  println!("{}", red); // 96
  println!("{}", green); // 172
  println!("{}", blue); // 57

  let person = Person {
    name: "shesh".to_string(),
    city: "singapore".to_string(),
  };
  let Person { name, city } = person;
  println!("{}", name); // name
  println!("{}", city); // city
}
```

## Сравнение структур

Очень часто пишут код типа «если это, то то». Сочетание деструктуризации и сопоставления с образцом позволяет нам очень кратко написать логику такого типа.

Возьмем следующий пример на JavaScript. Это надумано, но вы, вероятно, когда-нибудь в своей карьере написали что-то подобное: 

```js
const point = { x: 0, y: 30 };
const { x, y } = point;

if (x === 0 && y === 0) {
  console.log("both are zero");
} else if (x === 0) {
  console.log(`x is zero and y is ${y}`);
} else if (y === 0) {
  console.log(`x is ${x} and y is zero`);
} else {
  console.log(`x is ${x} and y is ${y}`);
}
```

Давайте напишем тот же код на Rust, используя сопоставление с образцом: 

```rust
struct Point {
  x: i32,
  y: i32,
}

fn main() {
  let point = Point { x: 10, y: 0 };

  match point {
    Point { x: 0, y: 0 } => println!("both are zero"),
    Point { x: 0, y } => println!("x is zero and y is {}", y),
    Point { x, y: 0 } => println!("x is {} and y is zero", x),
    Point { x, y } => println!("x is {} and y is {}", x, y),
  }
}
```

Это немного лаконично по сравнению с логикой if else, но также может сбивать с толку, поскольку мы выполняем сравнение, деструктуризацию и привязку переменных одновременно.

Вот как это выглядит визуально:

![](/imgs/posts/d8ce9ce5_01.png)

![](/imgs/posts/d8ce9ce5_01.png)

Мы начинаем понимать, почему это называется «сопоставление с образцом» - мы вводим данные и смотрим, какой образец в спичечных рычагах «подходит» лучше - это похоже на игрушки-сортировщики форм, с которыми играют дети. Помимо сравнения, мы также выполняем связывание переменных во 2-м, 3-м и 4-м матчах. Мы передаем переменные x или y или обе в их соответствующие выражения.

Сопоставление с образцом также является исчерпывающим, то есть заставляет вас обрабатывать все возможные случаи. Попробуйте удалить последнюю совпадающую руку, и Rust не позволит вам скомпилировать код.

## Enum

В JavaScript нет перечислений, но если вы использовали TypeScript, вы можете думать о перечислениях Rust как о комбинации перечислений TypeScript и дискриминируемых объединений TypeScript.

В простейшем случае Enums можно использовать как группу констант.

Например, хотя в JavaScript нет перечислений, вы могли использовать этот шаблон: 

```js
const DIRECTION = {
  FORWARD: "FORWARD",
  BACKWARD: "BACKWARD",
  LEFT: "LEFT",
  RIGHT: "RIGHT",
};

function move_drone(direction) {
  switch (direction) {
    case DIRECTION.FORWARD:
      console.log("Move Forward");
      break;
    case DIRECTION.BACKWARD:
      console.log("Move Backward");
      break;
    case DIRECTION.LEFT:
      console.log("Move Left");
      break;
    case DIRECTION.RIGHT:
      console.log("Move Right");
      break;
  }
}

move_drone(DIRECTION.FORWARD); // "Move Forward"
```

Здесь мы могли бы определить FORWARD, BACKWARD, LEFT и RIGHT как отдельные константы, но группирование их внутри объекта DIRECTION дает следующие преимущества:

- Имена FORWARD, BACKWARD, LEFT и RIGHT помещены в пространство имен под DIRECTION, поэтому конфликтов имен можно избежать.
- Это самодокументируется, так как мы можем быстро увидеть все действительные направления, доступные в кодовой базе.

Однако у этого подхода есть некоторые проблемы:

- Что, если кто-то передаст NORTH или UP в качестве аргумента move_drone? Чтобы исправить это, мы можем добавить проверку, чтобы убедиться, что в функции перемещения разрешены только значения, присутствующие в объекте DIRECTION.
- Что, если мы решим поддерживать UP и DOWN в будущем или переименуем LEFT / RIGHT в PORT / STARBOARD? Нам нужно найти все места, где используются похожие switch-case или if-else. Есть вероятность, что мы упустим несколько мест, которые могут вызвать проблемы в производственной среде.

Перечисления в строго типизированных языках, таких как Rust, более эффективны, поскольку они решают эти проблемы без написания дополнительного кода.

- Если функция может принимать только небольшой набор допустимых входных данных, Enums можно использовать для обеспечения соблюдения этого ограничения.
- Перечисления с сопоставлением с образцом вынуждают охватить все случаи. Полезно при обновлении Enums в будущем

Вот эквивалентный пример на Rust: 

```rust
enum Direction {
  Forward,
  Backward,
  Left,
  Right,
}

fn move_drone(direction: Direction) {
  match direction {
    Direction::Forward => println!("Move Forward"),
    Direction::Backward => println!("Move Backward"),
    Direction::Left => println!("Move Left"),
    Direction::Right => println!("Move Right"),
  }
}

fn main() {
  move_drone(Direction::Forward);
}
```

Мы получаем доступ к вариантам внутри Enum, используя нотацию ::. Попробуйте отредактировать этот код, вызвав «move_drone (Direction::Up)» или добавив «Down» в качестве нового элемента в перечислении Direction. В первом случае компилятор выдаст ошибку, сообщающую, что «Вверх» не найдено в «Направление», а во втором случае компилятор будет жаловаться, что мы не покрыли «Вниз» в блоке соответствия.

Rust Enums может не только действовать как группа констант - мы также можем связать данные с вариантом Enum. 

```rust
enum Direction {
  Forward,
  Backward,
  Left,
  Right,
}

enum Operation {
  PowerOn,
  PowerOff,
  Move(Direction),
  Rotate,
  TakePhoto { is_landscape: bool, zoom_level: i32 },
}

fn operate_drone(operation: Operation) {
  match operation {
    Operation::PowerOn => println!("Power On"),
    Operation::PowerOff => println!("Power Off"),
    Operation::Move(direction) => move_drone(direction),
    Operation::Rotate => println!("Rotate"),
    Operation::TakePhoto {
      is_landscape,
      zoom_level,
    } => println!("TakePhoto {}, {}", is_landscape, zoom_level),
  }
}

fn move_drone(direction: Direction) {
  match direction {
    Direction::Forward => println!("Move Forward"),
    Direction::Backward => println!("Move Backward"),
    Direction::Left => println!("Move Left"),
    Direction::Right => println!("Move Right"),
  }
}

fn main() {
  operate_drone(Operation::Move(Direction::Forward));
  operate_drone(Operation::TakePhoto {
    is_landscape: true,
    zoom_level: 10,
  })
}
```

Здесь мы добавили еще одно перечисление под названием Operation, которое содержит варианты, похожие на единицы (PowerOn, PowerOff, Rotate) и варианты, похожие на структуру (Move, TakePhoto). Обратите внимание, как мы использовали сопоставление с образцом с деструктуризацией и связыванием переменных.

Если вы использовали TypeScript или Flow, это похоже на различаемые объединения или типы сумм: 

```ts
interface PowerOn {
  kind: "PowerOn";
}

interface PowerOff {
  kind: "PowerOff";
}

type Direction = "Forward" | "Backward" | "Left" | "Right";

interface Move {
  kind: "Move";
  direction: Direction;
}

interface Rotate {
  kind: "Rotate";
}

interface TakePhoto {
  kind: "TakePhoto";
  is_landscape: boolean;
  zoom_level: number;
}

type Operation = PowerOn | PowerOff | Move | Rotate | TakePhoto;

function operate_drone(operation: Operation) {
  switch (operation.kind) {
    case "PowerOn":
      console.log("Power On");
      break;
    case "PowerOff":
      console.log("Power Off");
      break;
    case "Move":
      move_drone(operation.direction);
      break;
    case "Rotate":
      console.log("Rotate");
      break;
    case "TakePhoto":
      console.log(`TakePhoto ${operation.is_landscape}, ${operation.zoom_level}`);
      break;
  }
}

function move_drone(direction: Direction) {
  switch (direction) {
    case "Forward":
      console.log("Move Forward");
      break;
    case "Backward":
      console.log("Move Backward");
      break;
    case "Left":
      console.log("Move Left");
      break;
    case "Right":
      console.log("Move Right");
      break;
  }
}

operate_drone({
  kind: "Move",
  direction: "Forward",
});

operate_drone({
  kind: "TakePhoto",
  is_landscape: true,
  zoom_level: 10,
});
```

## Option

Мы узнали о типе Option в главе 2. Option - это фактически Enum с двумя вариантами - Some и None: 

```rust
enum Option<T> {
  Some(T),
  None,
}
```

Вот как мы обрабатывали значение Option в этой главе: 

```rust
fn read_file(path: &str) -> Option<&str> {
  let contents = "hello";

  if path != "" {
    return Some(contents);
  }

  return None;
}

fn main() {
  let file = read_file("path/to/file");

  if file.is_some() {
    let contents = file.unwrap();
    println!("{}", contents);
  } else {
    println!("Empty!");
  }
}
```

Используя сопоставление с образцом, мы можем реорганизовать логику следующим образом: 

```rust
fn main() {
  let file = read_file("path/to/file");

  match file {
    Some(contents) => println!("{}", contents),
    None => println!("Empty!"),
  }
}
```

Спасибо за чтение! Не стесняйтесь подписываться на меня в Twitter, чтобы увидеть больше подобных сообщений :) 
