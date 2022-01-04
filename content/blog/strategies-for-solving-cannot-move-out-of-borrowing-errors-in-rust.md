+++
title = "Стратегии решения ошибок заимствования, из которых невозможно выйти, в Rust"
description = "Стратегии решения ошибок заимствования, из которых невозможно выйти, в Rust"
weight = 1
+++

[Перевод](https://hermanradtke.com/2015/06/09/strategies-for-solving-cannot-move-out-of-borrowing-errors-in-rust.html)

Правила использования ссылок и заимствования в Rust довольно просты. Учитывая принадлежащую переменную, нам разрешено иметь столько неизменяемых ссылок на эту переменную, сколько мы хотим. По умолчанию в Rust используется неизменяемость, поэтому даже такие функции, как trim, написаны таким образом, что результатом является ссылка на исходную строку: 

```rust
fn main() {
   let name = " Herman ".to_string();
   let trimmed_name = name.trim(); // == &[1..n-1]
}
```

Единственное предостережение: я больше не могу перемещать переменную имени. Если я попытаюсь переместить имя, компилятор выдаст мне ошибку: невозможно переместить имя, потому что оно заимствовано. 

```rust
fn main() {
   let name = " Herman ".to_string();
   let trimmed_name = name.trim();

   let owned_name = name; // move error
}
```

Компилятор знает, что trimmed_name - это ссылка на name. Пока trimmed_name все еще находится в области видимости, компилятор не позволит нам передать имя функции, переназначить ее или выполнить любую другую операцию перемещения. Мы могли бы clone() имя переменной, а затем обрезать ее, но на самом деле мы просто хотим, чтобы компилятор знал, когда мы закончили заимствовать имя. Ключевое слово здесь - масштаб. Если ссылка на имя выходит за пределы области видимости, компилятор позволит нам переместить имя, потому что оно больше не заимствуется. Давайте заключим вызов trim() в фигурные скобки, чтобы обозначить другую область видимости. 

```rust
fn main() {
   let name = " Herman ".to_string();

   {
      let trimmed_name = name.trim();
   }

   let owned_name = name;
}
```

Это достаточно просто, но давайте сделаем еще один шаг. Предположим, мы хотим вернуть длину обрезанной строки из нашей области видимости. Если мы сделаем это внутри фигурных скобок, то trimmed_name_len больше не будет существовать, как только мы выйдем из этой области видимости. 

```rust
fn main() {
   let name = " Herman ".to_string();

   {
      let trimmed_name = name.trim();
      let trimmed_name_len = trimmed_name.len();
   }

   println!("Length of trimmed string is {}", trimmed_name_len); // no such variable error
   let owned_name = name;
}
```

## Стратегии

Есть несколько способов справиться с этим. Все они выглядят очень похоже, но имеют разные компромиссы. Мы можем вернуть значение из блока кода с ограниченной областью видимости: 

```rust
fn main() {
   let name = " Herman ".to_string();

   let trimmed_name_len = {
      let trimmed_name = name.trim();
      trimmed_name.len()
   };

   println!("Length of trimmed string is {}", trimmed_name_len);
   let owned_name = name;
}
```

Это дешевый и быстрый способ вывести ссылку за пределы области видимости. При этом не требуется указывать параметры или их типы, а также не требуется указывать тип возвращаемого значения. Однако его нельзя использовать повторно. Мы можем получить больше повторного использования, если воспользуемся анонимной функцией (или закрытием): 

```rust
fn main() {
   let name = " Herman ".to_string();

   let f = |name: &str| {
      let trimmed_name = name.trim();
      trimmed_name.len()
   };

   let trimmed_name_len = f(&name);

   println!("Length of trimmed string is {}", trimmed_name_len);
   let owned_name = name;
}
```

Замыкание требует, чтобы мы указали параметры и их типы, но делает указание типа возвращаемого значения необязательным. Как это написано, анонимная функция f может использоваться только в пределах функции. Если нам нужна полная возможность повторного использования, мы можем использовать обычную функцию: 

```rust
fn len_of_trimmed_string(name: &str) -> usize {
      let trimmed_name = name.trim();
      trimmed_name.len()
}

fn main() {
   let name = " Herman ".to_string();

   let trimmed_name_len = len_of_trimmed_string(name.as_ref());

   println!("Length of trimmed string is {}", trimmed_name_len);
   let owned_name = name;
}
```

Эти стратегии работают, только если мы вызываем неизменяемые функции. Мы временно сохраняем ссылку, чтобы получить другую информацию. Это действительно хорошо работает, поскольку информация как бы реализует трэйту Copy, например числа или логические значения. Если мы хотим сделать что-то вроде удаления всех пробелов в строке, например «H e r m a n», то мы изменяем строку. Нам пришлось бы вызвать name.clone(), чтобы позже переместить исходную переменную имени.
Замыкания без параметров

Вы могли задаться вопросом, действительно ли нам нужно было указывать параметры при использовании замыкания. Если мы попытаемся получить доступ к переменной name из замыкания, она создаст ссылку во время компиляции. Эта ссылка будет продолжать существовать, даже если мы попытаемся удалить замыкание f из области видимости. Пример: 

```rust
fn main() {
   let name = " Herman ".to_string();

   let f = || {
      let trimmed_name = name.trim();
      trimmed_name.len()
   };

   let trimmed_name_len = f();

   println!("Length of trimmed string is {}", trimmed_name_len);
   let owned_name = name; // move error
}
```
```
error: cannot move out of `name` because it is borrowed
   let owned_name = name;
               ^~~~~~~~~~
note: borrow of `name` occurs here
    let f = || {
       let trimmed_name = name.trim();
       trimmed_name.len()
    };
note: in expansion of closure expansion
```

## Пример из реального мира

Приведенные выше примеры довольно надуманы. Однако вы столкнетесь с этим, когда разбиваете функции на более мелкие части. В этом примере ниже я использовал функцию find_matches, которая требовала ввода типа &str. Учитывая PathBuf, мне нужно было вызвать на нем неизменяемый метод file_name(), а затем преобразовать его в &str, вызвав to_str() перед вызовом find_matches (file_name). Чтобы вернуть кортеж из (p, match), мне нужно было убедиться, что ссылка, созданная с помощью file_name, выходит за рамки. Я решил использовать функцию, но мог бы использовать фигурные скобки или закрытие, как мы обсуждали выше. 

```rust
fn find_matches(s: &str) -> f64 {
   // ...
}

fn count_filename_matches(path: &Path) -> f64 {
    let file_name = path.file_name()
        .and_then(|f| f.to_str())
        .unwrap_or_else(|| {
            debug!("Unable to determine filename for {:?}", path);
            ""
        });

    find_matches(file_name)
}

fn find_filename_matches_in_path(path: &str) -> Vec<(PathBuf, f64)> {
    fs::read_dir(path).unwrap()
        .map(|p| p.unwrap().path())
        .map(|p| {
            let matches = count_filename_matches(p.as_ref(), cmd);
            (p, matches)
        })
        .filter(|&(ref _p, matches)| {
            matches > 0.0
        })
        .collect()
}

```