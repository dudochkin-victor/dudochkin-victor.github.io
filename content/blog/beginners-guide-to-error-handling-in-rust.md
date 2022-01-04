+++
title = "Руководство для начинающих по обработке ошибок в Rust"
description = "Руководство для начинающих по обработке ошибок в Rust"
weight = 1
+++

[Перевод](https://www.sheshbabu.com/posts/rust-error-handling/)

Обработка ошибок в Rust сильно отличается, если вы переходите с других языков. В таких языках, как Java, JS, Python и т.д., Вы обычно генерируете исключения и возвращаете успешные значения. В Rust вы возвращаете то, что называется Result.

Тип Result<T, E> - это перечисление, которое имеет два варианта - Ok(T) для успешного значения или Err(E) для значения ошибки: 

```rust
enum Result<T, E> {
   Ok(T),
   Err(E),
}
```

Возврат ошибок вместо их выдачи - это сдвиг парадигмы в обработке ошибок. Если вы новичок в Rust, сначала возникнут некоторые трения, так как это потребует от вас другого подхода к ошибкам.

В этом посте я рассмотрю некоторые распространенные шаблоны обработки ошибок, чтобы вы постепенно познакомились с тем, как это делается в Rust:

- Не обращайте внимания на ошибку
- Завершить программу
- Используйте запасное значение
- Взрыв ошибки
- Множество ошибок в пузырях
- Ошибки в коробке
- Библиотеки против приложений
- Создавать собственные ошибки
- Пузырьки пользовательских ошибок
- Соответствие пользовательским ошибкам

## Игнорировать ошибку

Начнем с простейшего сценария, при котором мы просто игнорируем ошибку. Это звучит небрежно, но имеет несколько законных вариантов использования:

- Мы создаем прототип нашего кода и не хотим тратить время на обработку ошибок.
- Мы уверены, что ошибки не будет.

Допустим, мы читаем файл, который, как мы уверены, присутствует: 

```rust
use std::fs;

fn main() {
  let content = fs::read_to_string("./Cargo.toml").unwrap();
  println!("{}", content)
}
```

Несмотря на то, что мы знаем, что файл будет присутствовать, компилятор не имеет возможности узнать об этом. Поэтому мы используем unwrap, чтобы сообщить компилятору, что он нам доверяет и возвращает значение внутри. Если функция read_to_string возвращает значение Ok(), unwrap получит содержимое Ok и назначит его переменной содержимого. Если он вернет ошибку, он «запаникует». Panic либо завершает программу, либо выходит из текущего потока.

Обратите внимание, что развёртка используется во многих примерах Rust, чтобы пропустить обработку ошибок. Это в основном сделано для удобства и не должно использоваться в реальном коде.

## Завершить программу

Некоторые ошибки невозможно исправить или исправить. В этих случаях лучше быстро потерпеть неудачу, прервав программу.

Давайте возьмем тот же пример, что и выше - мы читаем файл, который обязательно присутствует. Представим, что для этой программы абсолютно важен этот файл, без которого он не будет работать должным образом. Если по каким-то причинам этот файл отсутствует, лучше прекратить работу программы.

Мы можем использовать развёртку, как раньше, или ожидать - это то же самое, что разворачивание, но позволяет добавить дополнительное сообщение об ошибке. 

```rust
use std::fs;

fn main() {
  let content = fs::read_to_string("./Cargo.toml").expect("Can't read Cargo.toml");
  println!("{}", content)
}
```

См. Также: panic!

## Используйте резервное значение

В некоторых случаях вы можете исправить ошибку, вернувшись к значению по умолчанию.

Например, предположим, что мы пишем сервер, и порт, который он слушает, можно настроить с помощью переменной среды. Если переменная среды не установлена, доступ к этому значению приведет к ошибке. Но мы можем легко справиться с этим, вернувшись к значению по умолчанию. 

```rust
use std::env;

fn main() {
  let port = env::var("PORT").unwrap_or("3000".to_string());
  println!("{}", port);
}
```

Здесь мы использовали вариант разворачивания под названием unwrap_or, который позволяет нам указывать значения по умолчанию.

См. Также: unwrap_or_else, unwrap_or_default

## Разбейте ошибку

Если у вас недостаточно контекста для обработки ошибки, вы можете передать ошибку в вызывающую функцию.

Вот надуманный пример, который использует веб-сервис для получения текущего года: 

```rust
use std::collections::HashMap;

fn main() {
  match get_current_date() {
    Ok(date) => println!("We've time travelled to {}!!", date),
    Err(e) => eprintln!("Oh noes, we don't know which era we're in! :( \n  {}", e),
  }
}

fn get_current_date() -> Result<String, reqwest::Error> {
  let url = "https://postman-echo.com/time/object";
  let result = reqwest::blocking::get(url);

  let response = match result {
    Ok(res) => res,
    Err(err) => return Err(err),
  };

  let body = response.json::<HashMap<String, i32>>();

  let json = match body {
    Ok(json) => json,
    Err(err) => return Err(err),
  };

  let date = json["years"].to_string();

  Ok(date)
}
```

Внутри функции get_current_date есть два вызова функций (get и json), которые возвращают значения Result. Поскольку get_current_date не имеет контекста, что делать, когда они возвращают ошибки, он использует сопоставление с образцом для передачи ошибок в main.

Использование сопоставления с образцом для обработки множественных или вложенных ошибок может сделать ваш код «зашумленным». Вместо этого мы можем переписать приведенный выше код с помощью символа? оператор: 

```rust
use std::collections::HashMap;

fn main() {
  match get_current_date() {
    Ok(date) => println!("We've time travelled to {}!!", date),
    Err(e) => eprintln!("Oh noes, we don't know which era we're in! :( \n  {}", e),
  }
}

fn get_current_date() -> Result<String, reqwest::Error> {
  let url = "https://postman-echo.com/time/object";
  let res = reqwest::blocking::get(url)?.json::<HashMap<String, i32>>()?;
  let date = res["years"].to_string();

  Ok(date)
}
```

Это выглядит намного чище!

? Оператор похож на разворачивание, но вместо паники он передает ошибку вызывающей функции. Следует иметь в виду, что мы можем использовать оператор? только для функций, возвращающих тип результата или параметра.

## Сверните несколько ошибок

В предыдущем примере функции get и json возвращают ошибку reqwest::Error, которую мы передали с помощью? оператор. Но что, если вызов другой функции вернул другое значение ошибки?

Давайте расширим предыдущий пример, вернув отформатированную дату вместо года: 

```rust
+ use chrono::NaiveDate;
  use std::collections::HashMap;

  fn main() {
    match get_current_date() {
      Ok(date) => println!("We've time travelled to {}!!", date),
      Err(e) => eprintln!("Oh noes, we don't know which era we're in! :( \n  {}", e),
    }
  }

  fn get_current_date() -> Result<String, reqwest::Error> {
    let url = "https://postman-echo.com/time/object";
    let res = reqwest::blocking::get(url)?.json::<HashMap<String, i32>>()?;
-   let date = res["years"].to_string();
+   let formatted_date = format!("{}-{}-{}", res["years"], res["months"] + 1, res["date"]);
+   let parsed_date = NaiveDate::parse_from_str(formatted_date.as_str(), "%Y-%m-%d")?;
+   let date = parsed_date.format("%Y %B %d").to_string();

    Ok(date)
  }
```

Приведенный выше код не компилируется, поскольку parse_from_str возвращает ошибку chrono::format::ParseError, а не reqwest::Error.

Мы можем исправить это, упаковав ошибки: 

```rust
  use chrono::NaiveDate;
  use std::collections::HashMap;

  fn main() {
    match get_current_date() {
      Ok(date) => println!("We've time travelled to {}!!", date),
      Err(e) => eprintln!("Oh noes, we don't know which era we're in! :( \n  {}", e),
    }
  }

- fn get_current_date() -> Result<String, reqwest::Error> {
+ fn get_current_date() -> Result<String, Box<dyn std::error::Error>> {
    let url = "https://postman-echo.com/time/object";
    let res = reqwest::blocking::get(url)?.json::<HashMap<String, i32>>()?;

    let formatted_date = format!("{}-{}-{}", res["years"], res["months"] + 1, res["date"]);
    let parsed_date = NaiveDate::parse_from_str(formatted_date.as_str(), "%Y-%m-%d")?;
    let date = parsed_date.format("%Y %B %d").to_string();

    Ok(date)
  }
```

Возврат объекта-трэйта Box<dyn std::error::Error> очень удобен, когда мы хотим вернуть несколько ошибок!

Также: во всяком случае, эйр

## Ошибки, помещенные в рамки

Пока что мы только распечатали ошибки в основной функции, но не обработали их. Если мы хотим обрабатывать и восстанавливать ошибки в штучной упаковке, нам нужно «уменьшить» их: 

```rust
  use chrono::NaiveDate;
  use std::collections::HashMap;

  fn main() {
    match get_current_date() {
      Ok(date) => println!("We've time travelled to {}!!", date),
-     Err(e) => eprintln!("Oh noes, we don't know which era we're in! :( \n  {}", e),
+     Err(e) => {
+       eprintln!("Oh noes, we don't know which era we're in! :(");
+       if let Some(err) = e.downcast_ref::<reqwest::Error>() {
+         eprintln!("Request Error: {}", err)
+       } else if let Some(err) = e.downcast_ref::<chrono::format::ParseError>() {
+         eprintln!("Parse Error: {}", err)
+       }
+     }
    }
  }

  fn get_current_date() -> Result<String, Box<dyn std::error::Error>> {
    let url = "https://postman-echo.com/time/object";
    let res = reqwest::blocking::get(url)?.json::<HashMap<String, i32>>()?;

    let formatted_date = format!("{}-{}-{}", res["years"], res["months"] + 1, res["date"]);
    let parsed_date = NaiveDate::parse_from_str(formatted_date.as_str(), "%Y-%m-%d")?;
    let date = parsed_date.format("%Y %B %d").to_string();

    Ok(date)
  }
```

Обратите внимание, как нам нужно знать детали реализации (различные ошибки внутри) get_current_date, чтобы иметь возможность понижать их внутри main.

См. Также: downcast, downcast_mut

## Приложения против библиотек

Как упоминалось ранее, обратная сторона упакованных ошибок заключается в том, что если мы хотим обрабатывать лежащие в основе ошибки, нам необходимо знать детали реализации. Когда мы возвращаем что-то как Box<dyn std::error::Error>, информация о конкретном типе стирается. Чтобы обрабатывать различные ошибки по-разному, нам нужно преобразовать их в конкретные типы, и это приведение может завершиться ошибкой во время выполнения.

Однако говорить, что что-то является «обратной стороной», без контекста бесполезно. Хорошее практическое правило - задать вопрос, является ли код, который вы пишете, «приложением» или «библиотекой»:

### Применение

- Код, который вы пишете, будет использоваться конечными пользователями.
- Большинство ошибок, генерируемых кодом приложения, не обрабатываются, а регистрируются в журнале или сообщаются пользователю.
- Это нормально, если ошибки помещены в рамки.

### Библиотека

- Код, который вы пишете, будет использоваться другим кодом. «Библиотекой» могут быть крэйти с открытым исходным кодом, внутренние библиотеки и т.д.
- Ошибки являются частью API вашей библиотеки, поэтому ваши потребители знают, каких ошибок ожидать и от которых следует исправляться.
- Ошибки из вашей библиотеки часто обрабатываются вашими потребителями, поэтому они должны быть структурированы и просты для выполнения исчерпывающего сопоставления.
- Если вы возвращаете ошибки в коробках, ваши потребители должны знать об ошибках, созданных вашим кодом, вашими зависимостями и т.д.!
- Вместо ошибок в штучной упаковке мы можем возвращать пользовательские ошибки.

## Создание пользовательских ошибок

Для кода библиотеки мы можем преобразовать все ошибки в наши собственные ошибки и распространять их вместо ошибок в коробках. В нашем примере в настоящее время есть две ошибки - reqwest::Error и chrono::format::ParseError. Мы можем преобразовать их в MyCustomError::HttpError и MyCustomError::ParseError соответственно.

Начнем с создания перечисления для хранения двух наших вариантов ошибок: 

```rust
// error.rs

pub enum MyCustomError {
  HttpError,
  ParseError,
}
```

Трэйта Error требует, чтобы мы реализовали трэйты Debug и Display: 

```rust
// error.rs

use std::fmt;

#[derive(Debug)]
pub enum MyCustomError {
  HttpError,
  ParseError,
}

impl std::error::Error for MyCustomError {}

impl fmt::Display for MyCustomError {
  fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
    match self {
      MyCustomError::HttpError => write!(f, "HTTP Error"),
      MyCustomError::ParseError => write!(f, "Parse Error"),
    }
  }
}
```

Мы создали собственную ошибку!

Очевидно, что это простой пример, поскольку варианты ошибки не содержат много информации об ошибке. Но этого должно быть достаточно в качестве отправной точки для создания более сложных и реалистичных пользовательских ошибок. Вот несколько примеров из реальной жизни: ripgrep, reqwest, csv и serde_json.

См. Также: thiserror, snafu

## Создание пузырей пользовательских ошибок

Давайте обновим наш код, чтобы он возвращал только что созданные пользовательские ошибки: 

```rust
  // main.rs

+ mod error;

  use chrono::NaiveDate;
+ use error::MyCustomError;
  use std::collections::HashMap;

  fn main() {
    // skipped, will get back later
  }

- fn get_current_date() -> Result<String, Box<dyn std::error::Error>> {
+ fn get_current_date() -> Result<String, MyCustomError> {
    let url = "https://postman-echo.com/time/object";
-   let res = reqwest::blocking::get(url)?.json::<HashMap<String, i32>>()?;
+   let res = reqwest::blocking::get(url)
+     .map_err(|_| MyCustomError::HttpError)?
+     .json::<HashMap<String, i32>>()
+     .map_err(|_| MyCustomError::HttpError)?;

    let formatted_date = format!("{}-{}-{}", res["years"], res["months"] + 1, res["date"]);
-   let parsed_date = NaiveDate::parse_from_str(formatted_date.as_str(), "%Y-%m-%d")?;
+   let parsed_date = NaiveDate::parse_from_str(formatted_date.as_str(), "%Y-%m-%d")
+     .map_err(|_| MyCustomError::ParseError)?;
    let date = parsed_date.format("%Y %B %d").to_string();

    Ok(date)
  }
```

Обратите внимание, как мы используем map_err для преобразования ошибки из одного типа в другой.

Но в результате все стало многословным - наша функция завалена этими вызовами map_err. Мы можем реализовать трейт From для автоматического определения типов ошибок, когда мы используем? оператор: 

```rust
  // error.rs

  use std::fmt;

  #[derive(Debug)]
  pub enum MyCustomError {
    HttpError,
    ParseError,
  }

  impl std::error::Error for MyCustomError {}

  impl fmt::Display for MyCustomError {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
      match self {
        MyCustomError::HttpError => write!(f, "HTTP Error"),
        MyCustomError::ParseError => write!(f, "Parse Error"),
      }
    }
  }

+ impl From<reqwest::Error> for MyCustomError {
+   fn from(_: reqwest::Error) -> Self {
+     MyCustomError::HttpError
+   }
+ }

+ impl From<chrono::format::ParseError> for MyCustomError {
+   fn from(_: chrono::format::ParseError) -> Self {
+     MyCustomError::ParseError
+   }
+ }

  // main.rs

  mod error;

  use chrono::NaiveDate;
  use error::MyCustomError;
  use std::collections::HashMap;

  fn main() {
    // skipped, will get back later
  }

  fn get_current_date() -> Result<String, MyCustomError> {
    let url = "https://postman-echo.com/time/object";
-   let res = reqwest::blocking::get(url)
-     .map_err(|_| MyCustomError::HttpError)?
-     .json::<HashMap<String, i32>>()
-     .map_err(|_| MyCustomError::HttpError)?;
+   let res = reqwest::blocking::get(url)?.json::<HashMap<String, i32>>()?;

    let formatted_date = format!("{}-{}-{}", res["years"], res["months"] + 1, res["date"]);
-   let parsed_date = NaiveDate::parse_from_str(formatted_date.as_str(), "%Y-%m-%d")
-     .map_err(|_| MyCustomError::ParseError)?;
+   let parsed_date = NaiveDate::parse_from_str(formatted_date.as_str(), "%Y-%m-%d")?;
    let date = parsed_date.format("%Y %B %d").to_string();

    Ok(date)
  }
```

Мы удалили map_err, и код выглядит намного чище!

Однако трейт From не является магическим, и бывают случаи, когда нам нужно использовать map_err. В приведенном выше примере мы переместили преобразование типа из функции get_current_date в From<X> для реализации MyCustomError. Это хорошо работает, когда информацию, необходимую для преобразования одной ошибки в MyCustomError, можно получить из исходного объекта ошибки. Если нет, нам нужно использовать map_err внутри get_current_date.

## Соответствие пользовательским ошибкам

До сих пор мы игнорировали изменения в main. Вот как мы можем обрабатывать пользовательские ошибки: 

```rust
  // main.rs

  mod error;

  use chrono::NaiveDate;
  use error::MyCustomError;
  use std::collections::HashMap;

  fn main() {
    match get_current_date() {
      Ok(date) => println!("We've time travelled to {}!!", date),
      Err(e) => {
        eprintln!("Oh noes, we don't know which era we're in! :(");
-       if let Some(err) = e.downcast_ref::<reqwest::Error>() {
-         eprintln!("Request Error: {}", err)
-       } else if let Some(err) = e.downcast_ref::<chrono::format::ParseError>() {
-         eprintln!("Parse Error: {}", err)
-       }
+       match e {
+         MyCustomError::HttpError => eprintln!("Request Error: {}", e),
+         MyCustomError::ParseError => eprintln!("Parse Error: {}", e),
+       }
      }
    }
  }

  fn get_current_date() -> Result<String, MyCustomError> {
    let url = "https://postman-echo.com/time/object";
    let res = reqwest::blocking::get(url)?.json::<HashMap<String, i32>>()?;

    let formatted_date = format!("{}-{}-{}", res["years"], res["months"] + 1, res["date"]);
    let parsed_date = NaiveDate::parse_from_str(formatted_date.as_str(), "%Y-%m-%d")?;
    let date = parsed_date.format("%Y %B %d").to_string();

    Ok(date)
  }
```

Обратите внимание, как, в отличие от ошибок в штучной упаковке, мы можем сопоставить варианты внутри перечисления MyCustomError.

## Вывод

Спасибо за чтение! Надеюсь, этот пост был полезен для ознакомления с основами обработки ошибок в Rust. Я добавил примеры в репозиторий на GitHub, который вы можете использовать на практике. Если у вас возникнут дополнительные вопросы, свяжитесь со мной по адресу sheshbabu [at] gmail.com. Не стесняйтесь подписываться на меня в Twitter, чтобы увидеть больше подобных сообщений :) 
