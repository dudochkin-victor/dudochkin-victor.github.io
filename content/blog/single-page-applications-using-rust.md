+++
title = "Одностраничные приложения, использующие Rust"
description = "Одностраничные приложения, использующие Rust"
weight = 1
hash = "ebe0fe80"
+++

[Перевод](https://www.sheshbabu.com/posts/rust-wasm-yew-single-page-application/) | Автор оригинала: Shesh

WebAssembly (wasm) позволяет запускать в браузерах код, написанный на языках, отличных от JavaScript. Если вы не обращали внимания, все основные браузеры поддерживают wasm, и во всем мире более 90% пользователей имеют браузеры, которые могут запускать wasm.

Поскольку Rust компилируется в wasm, возможно ли создавать SPA (одностраничные приложения) исключительно на Rust и без написания ни одной строчки JavaScript? Краткий ответ - да! Читайте дальше, чтобы узнать больше, или посетите демонстрационный сайт, если не можете сдержать волнение!

Мы будем создавать простой сайт электронной коммерции под названием «RustMart», который будет иметь две страницы:

- Домашняя страница - список всех продуктов, которые покупатель может добавить в корзину.
- ProductDetailPage - показывать подробную информацию о продукте при нажатии на карточку продукта.

![](/imgs/posts/ebe0fe80_01.png)

Я использую этот пример, поскольку он проверяет минимальный набор возможностей, необходимых для создания современных SPA:

- Перемещение между несколькими страницами без перезагрузки страницы
- Делайте сетевые запросы без перезагрузки страницы
- Возможность повторно использовать компоненты пользовательского интерфейса на нескольких страницах.
- Обновление компонентов на разных уровнях иерархии пользовательского интерфейса

## Настройка

Перейдите по этой ссылке, чтобы установить Rust, если вы еще этого не сделали.

Установите эти инструменты Rust: 

```bash
$ cargo install wasm-pack          # Compile Rust to Wasm and generate JS interop code
$ cargo install cargo-make         # Task runner
$ cargo install simple-http-server # Simple server to serve assets
```

Создайте новый проект: 

```bash
$ cargo new --lib rustmart && cd rustmart
```

Мы будем использовать библиотеку Yew для создания компонентов пользовательского интерфейса. Давайте добавим это и зависимости wasm в Cargo.toml: 

```toml
[lib]
crate-type = ["cdylib", "rlib"]

[dependencies]
yew = "0.17"
wasm-bindgen = "0.2"
```

Создайте новый файл с именем Makefile.toml и добавьте это: 

```toml
[tasks.build]
command = "wasm-pack"
args = ["build", "--dev", "--target", "web", "--out-name", "wasm", "--out-dir", "./static"]
watch = { ignore_pattern = "static/*" }

[tasks.serve]
command = "simple-http-server"
args = ["-i", "./static/", "-p", "3000", "--nocache", "--try-file", "./static/index.html"]
```

Запустите задачу сборки: 

```bash
$ cargo make build
```

Если вы новичок в Rust, я написал несколько руководств для начинающих, которые помогут вам лучше следить за этим постом.

## Привет мир

Начнем с простого примера "привет, мир":

Создайте static / index.html и добавьте это: 

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <title>RustMart</title>
    <script type="module">
      import init from "/wasm.js";
      init();
    </script>
    <link rel="shortcut icon" href="#" />
  </head>
  <body></body>
</html>
```

Добавьте это в src/lib.rs: 

```rust
// src/lib.rs
use wasm_bindgen::prelude::*;
use yew::prelude::*;

struct Hello {}

impl Component for Hello {
    type Message =();
    type Properties =();

    fn create(_: Self::Properties, _: ComponentLink<Self>) -> Self {
        Self {}
    }

    fn update(&mut self, _: Self::Message) -> ShouldRender {
        true
    }

    fn change(&mut self, _: Self::Properties) -> ShouldRender {
        true
    }

    fn view(&self) -> Html {
        html! { <span>{"Hello World!"}</span> }
    }
}

#[wasm_bindgen(start)]
pub fn run_app() {
    App::<Hello>::new().mount_to_body();
}
```

Многое происходит, но вы видите, что мы создаем новый компонент с именем «Hello», который отображает <span> Hello World! </span> в DOM. Мы узнаем больше о компонентах Yew позже.

Запустите задачу обслуживания в новом терминале и загрузите http: // localhost: 3000 в свой браузер. 

```bash
$ cargo make serve
```

![](/imgs/posts/ebe0fe80_02.png)

Оно работает!! Это всего лишь «привет, мир», но это полностью написано на Rust.

Прежде чем продолжить, давайте узнаем о компонентах и других концепциях SPA.

## Мышление компонентами

Создание пользовательского интерфейса путем компоновки компонентов и однонаправленной передачи данных - это сдвиг парадигмы в мире внешнего интерфейса. Это огромное улучшение в том, как мы рассуждаем о пользовательском интерфейсе, и очень сложно вернуться к императивным манипуляциям с DOM, когда вы к этому привыкнете.

Компонент в таких библиотеках, как React, Vue, Yew, Flutter и т.д., Имеет следующие особенности:

- Возможность составлять из более крупных компонентов
- Props - передавать данные и обратные вызовы от этого компонента к его дочерним компонентам.
- Состояние - манипулировать состоянием, локальным для этого компонента.
- AppState - управлять глобальным состоянием.
- Слушайте события жизненного цикла, такие как «Создан», «Установлен в DOM» и т.д.
- Выполнять побочные эффекты, такие как получение удаленных данных, манипулирование локальным хранилищем и т.д.

Компонент обновляется (повторно отображается), когда происходит одно из следующих событий:

- Родительский компонент перерисован
- Изменения реквизита
- Изменения состояния
- Изменения в AppState

Таким образом, вместо обязательного обновления пользовательского интерфейса при взаимодействии с пользователем, сетевых запросах и т.д. Мы обновляем данные (свойства, состояние, AppState), и пользовательский интерфейс обновляется на основе этих данных. Это то, что имеют в виду, когда говорят: «UI - это функция состояния».

Точные детали различаются в разных библиотеках, но это должно дать вам общее представление. Если вы новичок в этом, возможно, потребуется время, чтобы «щелкнуть» и привыкнуть к такому образу мышления.

## Домашняя страница

Давайте сначала создадим домашнюю страницу. Мы будем строить HomePage как монолитный компонент, а позже разложим его на более мелкие повторно используемые компоненты.

Создадим следующие файлы: 

```rust
// src/pages/home.rs
use yew::prelude::*;

pub struct Home {}

impl Component for Home {
    type Message =();
    type Properties =();

    fn create(_: Self::Properties, _: ComponentLink<Self>) -> Self {
        Self {}
    }

    fn update(&mut self, _: Self::Message) -> ShouldRender {
        true
    }

    fn change(&mut self, _: Self::Properties) -> ShouldRender {
        true
    }

    fn view(&self) -> Html {
        html! { <span>{"Home Sweet Home!"}</span> }
    }
}

// src/pages/mod.rs
mod home;

pub use home::Home;
```

Давайте обновим src/lib.rs, чтобы импортировать компонент HomePage: 

```rust
  // src/lib.rs
+ mod pages;

+ use pages::Home;
  use wasm_bindgen::prelude::*;
  use yew::prelude::*;

- struct Hello {}

- impl Component for Hello {
-     type Message =();
-     type Properties =();

-     fn create(_: Self::Properties, _: ComponentLink<Self>) -> Self {
-         Self {}
-     }

-     fn update(&mut self, _: Self::Message) -> ShouldRender {
-         true
-     }

-     fn change(&mut self, _: Self::Properties) -> ShouldRender {
-         true
-     }

-     fn view(&self) -> Html {
-         html! { <span>{"Hello World!"}</span> }
-     }
- }

  #[wasm_bindgen(start)]
  pub fn run_app() {
-   App::<Hello>::new().mount_to_body();
+   App::<Home>::new().mount_to_body();
  }
```

Теперь вы должны увидеть «Дом, милый дом!» вместо «Hello World!» отображается в вашем браузере.

Приступим к проектированию состояния этого компонента:

- Нам нужно хранить список продуктов, полученных с сервера
- Храните продукты, добавленные пользователем в корзину.

Мы создаем простую структуру для хранения сведений о продукте: 

```rust
struct Product {
    name: String,
    description: String,
    image: String,
    price: f64,
}
```

Затем мы создаем новую структуру State с полем products для хранения продуктов с сервера: 

```rust
struct State {
    products: Vec<Product>,
}
```

Вот полный список изменений в компоненте HomePage: 

```rust
  use yew::prelude::*;

+ struct Product {
+     id: i32,
+     name: String,
+     description: String,
+     image: String,
+     price: f64,
+ }

+ struct State {
+     products: Vec<Product>,
+ }

- pub struct Home {}
+ pub struct Home {
+     state: State,
+ }

  impl Component for Home {
      type Message =();
      type Properties =();

      fn create(_: Self::Properties, _: ComponentLink<Self>) -> Self {
+       let products: Vec<Product> = vec![
+           Product {
+               id: 1,
+               name: "Apple".to_string(),
+               description: "An apple a day keeps the doctor away".to_string(),
+               image: "/products/apple.png".to_string(),
+               price: 3.65,
+           },
+           Product {
+               id: 2,
+               name: "Banana".to_string(),
+               description: "An old banana leaf was once young and green".to_string(),
+               image: "/products/banana.png".to_string(),
+               price: 7.99,
+           },
+       ];

-       Self {}
+       Self {
+           state: State {
+               products,
+           },
+       }
      }

      fn update(&mut self, _: Self::Message) -> ShouldRender {
          true
      }

      fn change(&mut self, _: Self::Properties) -> ShouldRender {
          true
      }

      fn view(&self) -> Html {
+        let products: Vec<Html> = self
+            .state
+            .products
+            .iter()
+            .map(|product: &Product| {
+                html! {
+                  <div>
+                    <img src={&product.image}/>
+                    <div>{&product.name}</div>
+                    <div>{"$"}{&product.price}</div>
+                  </div>
+                }
+            })
+            .collect();
+
+        html! { <span>{products}</span> }
-        html! { <span>{"Home!"}</span> }
      }
  }
```

Метод создания жизненного цикла вызывается при создании компонента, и именно здесь мы устанавливаем начальное состояние. На данный момент мы создали фиктивный список продуктов и присвоили его продуктам внутри штата в качестве начального значения. Позже мы получим этот список с помощью сетевого запроса.

Метод жизненного цикла представления вызывается при визуализации компонента. Здесь мы перебирали продукты внутри состояния для создания карточек продуктов. Если вы знакомы с React, это то же самое, что метод рендеринга и HTML! макрос похож на JSX.

Сохраните несколько случайных изображений как static / products / apple.png и static / products / banana.png, и вы получите следующий интерфейс:

![](/imgs/posts/ebe0fe80_03.png)

Давайте реализуем функцию «добавить в корзину»:

- Мы отслеживаем все товары, добавленные в корзину, в новом поле состояния под названием cart_products.
- Оформляем кнопку «добавить в корзину» для каждого товара.
- Добавить логику для обновления состояния cart_products при нажатии кнопки «добавить в корзину». 

```rust
  use yew::prelude::*;

+ #[derive(Clone)]
  struct Product {
      id: i32,
      name: String,
      description: String,
      image: String,
      price: f64,
  }

+ struct CartProduct {
+     product: Product,
+     quantity: i32,
+ }

  struct State {
      products: Vec<Product>,
+     cart_products: Vec<CartProduct>,
  }

  pub struct Home {
      state: State,
+     link: ComponentLink<Self>,
  }

+ pub enum Msg {
+     AddToCart(i32),
+ }

  impl Component for Home {
-   type Message =();
+   type Message = Msg;
    type Properties =();

-   fn create(_: Self::Properties, _: ComponentLink<Self>) -> Self {
+   fn create(_: Self::Properties, link: ComponentLink<Self>) -> Self {
        let products: Vec<Product> = vec![
            Product {
                id: 1,
                name: "Apple".to_string(),
                description: "An apple a day keeps the doctor away".to_string(),
                image: "/products/apple.png".to_string(),
                price: 3.65,
            },
            Product {
                id: 2,
                name: "Banana".to_string(),
                description: "An old banana leaf was once young and green".to_string(),
                image: "/products/banana.png".to_string(),
                price: 7.99,
            },
        ];
+       let cart_products = vec![];

        Self {
            state: State {
                products,
+               cart_products,
            },
+           link,
        }
    }

-   fn update(&mut self, _: Self::Message) -> ShouldRender {
+   fn update(&mut self, message: Self::Message) -> ShouldRender {
+       match message {
+           Msg::AddToCart(product_id) => {
+               let product = self
+                   .state
+                   .products
+                   .iter()
+                   .find(|p: &&Product| p.id == product_id)
+                   .unwrap();
+               let cart_product = self
+                   .state
+                   .cart_products
+                   .iter_mut()
+                   .find(|cp: &&mut CartProduct| cp.product.id == product_id);
+
+               if let Some(cp) = cart_product {
+                   cp.quantity += 1;
+               } else {
+                   self.state.cart_products.push(CartProduct {
+                       product: product.clone(),
+                       quantity: 1,
+                   })
+               }
+               true
+           }
+       }
-       true
    }

    fn change(&mut self, _: Self::Properties) -> ShouldRender {
        true
    }

    fn view(&self) -> Html {
        let products: Vec<Html> = self
            .state
            .products
            .iter()
            .map(|product: &Product| {
+              let product_id = product.id;
                html! {
                  <div>
                    <img src={&product.image}/>
                    <div>{&product.name}</div>
                    <div>{"$"}{&product.price}</div>
+                   <button onclick=self.link.callback(move |_| Msg::AddToCart(product_id))>{"Add To Cart"}</button>
                  </div>
                }
            })
            .collect();

+       let cart_value = self
+           .state
+           .cart_products
+           .iter()
+           .fold(0.0, |acc, cp| acc + (cp.quantity as f64 * cp.product.price));

-       html! { <span>{products}</span> }
+       html! {
+         <div>
+           <span>{format!("Cart Value: {:.2}", cart_value)}</span>
+           <span>{products}</span>
+         </div>
+       }
      }
  }
```

- clone - Мы получили трэйту Clone в структуре Product, чтобы мы могли сохранять клонированный продукт в CartProduct всякий раз, когда пользователь добавляет его в корзину.
- update - этот метод является местом, где существует логика для обновления состояния компонента или выполнения побочных эффектов (например, сетевых запросов). Он вызывается с помощью перечисления Message, которое содержит все действия, поддерживаемые компонентом. Когда мы возвращаем true из этого метода, компонент перерисовывается. В приведенном выше коде, когда пользователь нажимает кнопку «Добавить в корзину», мы отправляем сообщение Msg::AddToCart для обновления. Внутри обновления оно либо добавляет продукт в cart_product, если его не существует, либо увеличивает количество.
- ссылка - это позволяет нам регистрировать обратные вызовы, которые могут запускать наш метод жизненного цикла обновления.

Если вы раньше использовали Redux, обновление похоже на Reducer (для обновлений состояния) и Action Creator (для побочных эффектов), Message аналогично Action, а ссылка аналогична Dispatch.

![](/imgs/posts/ebe0fe80_04.png)

Вот как выглядит пользовательский интерфейс. Попробуйте нажать кнопку «Добавить в корзину» и увидеть изменения в «Стоимость корзины»:

![](/imgs/posts/ebe0fe80_05.png)

## Получение данных

Мы переместим данные о товарах из функции create в static / products / products.json и запросим их с помощью API выборки. 

```json
[
  {
    "id": 1,
    "name": "Apple",
    "description": "An apple a day keeps the doctor away",
    "image": "/products/apple.png",
    "price": 3.65
  },
  {
    "id": 2,
    "name": "Banana",
    "description": "An old banana leaf was once young and green",
    "image": "/products/banana.png",
    "price": 7.99
  }
]
```

Yew предоставляет общие API-интерфейсы браузера, такие как fetch, localstorage и т.д., Через так называемые «службы». Мы можем использовать FetchService для выполнения сетевых запросов. Требуются все равно и серде крэйти, давайте их установим: 

```toml
  [package]
  name = "rustmart"
  version = "0.1.0"
  authors = ["sheshbabu <sheshbabu@gmail.com>"]
  edition = "2018"

  [lib]
  crate-type = ["cdylib", "rlib"]

  [dependencies]
  yew = "0.17"
  wasm-bindgen = "0.2"
+ anyhow = "1.0.32"
+ serde = { version = "1.0", features = ["derive"] }
```

Давайте извлечем Product и CartProduct в src/types.rs, чтобы мы могли использовать их в нескольких файлах: 

```rust
use serde::{Deserialize, Serialize};

#[derive(Deserialize, Serialize, Clone, Debug)]
pub struct Product {
    pub id: i32,
    pub name: String,
    pub description: String,
    pub image: String,
    pub price: f64,
}

#[derive(Clone, Debug)]
pub struct CartProduct {
    pub product: Product,
    pub quantity: i32,
}
```

Мы сделали обе структуры и их поля общедоступными и унаследовали трэйты Deserialize и Serialize.

Мы воспользуемся шаблоном модуля API и создадим отдельный модуль с именем src/api.rs для хранения нашей логики выборки: 

```rust
// src/api.rs
use crate::types::Product;
use anyhow::Error;
use yew::callback::Callback;
use yew::format::{Json, Nothing};
use yew::services::fetch::{FetchService, FetchTask, Request, Response};

pub type FetchResponse<T> = Response<Json<Result<T, Error>>>;
type FetchCallback<T> = Callback<FetchResponse<T>>;

pub fn get_products(callback: FetchCallback<Vec<Product>>) -> FetchTask {
    let req = Request::get("/products/products.json")
        .body(Nothing)
        .unwrap();

    FetchService::fetch(req, callback).unwrap()
}
```

API-интерфейс FetchService немного неудобен - он принимает объект запроса и обратный вызов в качестве аргументов и возвращает нечто, называемое «FetchTask». Одна удивительная проблема заключается в том, что сетевой запрос прерывается, если эта «FetchTask» отбрасывается. Итак, мы возвращаем это и сохраняем в нашем компоненте.

Давайте обновим lib.rs, чтобы добавить эти новые модули в дерево модулей: 

```rust
  // src/lib.rs
+ mod api;
+ mod types;
  mod pages;

  use pages::Home;
  use wasm_bindgen::prelude::*;
  use yew::prelude::*;

  #[wasm_bindgen(start)]
  pub fn run_app() {
      App::<Home>::new().mount_to_body();
  }
```

Наконец, давайте обновим наш компонент HomePage: 

```rust
+ use crate::api;
+ use crate::types::{CartProduct, Product};
+ use anyhow::Error;
+ use yew::format::Json;
+ use yew::services::fetch::FetchTask;
  use yew::prelude::*;

- #[derive(Clone)]
- struct Product {
-     id: i32,
-     name: String,
-     description: String,
-     image: String,
-     price: f64,
- }

- struct CartProduct {
-     product: Product,
-     quantity: i32,
- }

  struct State {
      products: Vec<Product>,
      cart_products: Vec<CartProduct>,
+     get_products_error: Option<Error>,
+     get_products_loaded: bool,
  }

  pub struct Home {
      state: State,
      link: ComponentLink<Self>,
+     task: Option<FetchTask>,
  }

  pub enum Msg {
      AddToCart(i32),
+     GetProducts,
+     GetProductsSuccess(Vec<Product>),
+     GetProductsError(Error),
  }

  impl Component for Home {
      type Message = Msg;
      type Properties =();

      fn create(_: Self::Properties, link: ComponentLink<Self>) -> Self {
-         let products: Vec<Product> = vec![
-             Product {
-                 id: 1,
-                 name: "Apple".to_string(),
-                 description: "An apple a day keeps the doctor away".to_string(),
-                 image: "/products/apple.png".to_string(),
-                 price: 3.65,
-             },
-             Product {
-                 id: 2,
-                 name: "Banana".to_string(),
-                 description: "An old banana leaf was once young and green".to_string(),
-                 image: "/products/banana.png".to_string(),
-                 price: 7.99,
-             },
-         ];
+         let products = vec![];
          let cart_products = vec![];

+         link.send_message(Msg::GetProducts);

          Self {
              state: State {
                  products,
                  cart_products,
+                 get_products_error: None,
+                 get_products_loaded: false,
              },
              link,
+             task: None,
          }
      }

      fn update(&mut self, message: Self::Message) -> ShouldRender {
          match message {
+             Msg::GetProducts => {
+                 self.state.get_products_loaded = false;
+                 let handler =
+                     self.link
+                         .callback(move |response: api::FetchResponse<Vec<Product>>| {
+                             let (_, Json(data)) = response.into_parts();
+                             match data {
+                                 Ok(products) => Msg::GetProductsSuccess(products),
+                                 Err(err) => Msg::GetProductsError(err),
+                             }
+                         });
+                 self.task = Some(api::get_products(handler));
+                 true
+             }
+             Msg::GetProductsSuccess(products) => {
+                 self.state.products = products;
+                 self.state.get_products_loaded = true;
+                 true
+             }
+             Msg::GetProductsError(error) => {
+                 self.state.get_products_error = Some(error);
+                 self.state.get_products_loaded = true;
+                 true
+             }
              Msg::AddToCart(product_id) => {
                  let product = self
                      .state
                      .products
                      .iter()
                      .find(|p: &&Product| p.id == product_id)
                      .unwrap();
                  let cart_product = self
                      .state
                      .cart_products
                      .iter_mut()
                      .find(|cp: &&mut CartProduct| cp.product.id == product_id);

                  if let Some(cp) = cart_product {
                      cp.quantity += 1;
                  } else {
                      self.state.cart_products.push(CartProduct {
                          product: product.clone(),
                          quantity: 1,
                      })
                  }
                  true
              }
          }
      }

      fn change(&mut self, _: Self::Properties) -> ShouldRender {
          true
      }

      fn view(&self) -> Html {
          let products: Vec<Html> = self
              .state
              .products
              .iter()
              .map(|product: &Product| {
                  let product_id = product.id;
                  html! {
                    <div>
                      <img src={&product.image}/>
                      <div>{&product.name}</div>
                      <div>{"$"}{&product.price}</div>
                      <button onclick=self.link.callback(move |_| Msg::AddToCart(product_id))>{"Add To Cart"}</button>
                    </div>
                  }
              })
              .collect();

          let cart_value = self
              .state
              .cart_products
              .iter()
              .fold(0.0, |acc, cp| acc + (cp.quantity as f64 * cp.product.price));

+         if !self.state.get_products_loaded {
+             html! {
+               <div>{"Loading ..."}</div>
+             }
+         } else if let Some(_) = self.state.get_products_error {
+             html! {
+               <div>
+                 <span>{"Error loading products! :("}</span>
+               </div>
+             }
+         } else {
              html! {
                <div>
                  <span>{format!("Cart Value: {:.2}", cart_value)}</span>
                  <span>{products}</span>
                </div>
              }
+         }
      }
  }
```

Достаточно много изменений, но вы должны уметь разбираться в большинстве из них.

- Мы заменили жестко запрограммированный список продуктов в create пустым массивом. Мы также отправляем сообщение Msg::GetProducts для обновления, которое вызывает метод get_products в модуле api. Возвращенный FetchTask сохраняется в task.
- При успешном выполнении сетевого запроса вызывается сообщение Msg::GetProductsSuccess со списком продуктов или Msg::GetProductsError с ошибкой.
- Эти два сообщения устанавливают поля products и get_products_error в состояние соответственно. Они также устанавливают для состояния get_products_loaded значение true после выполнения запроса.
- В методе представления мы использовали условную визуализацию для визуализации представления загрузки, представления ошибок или представления продуктов в зависимости от состояния компонента.

![](/imgs/posts/ebe0fe80_06.png)

## Разделение на повторно используемые компоненты

Давайте извлечем компонент «карточка продукта» в отдельный модуль, чтобы мы могли повторно использовать его на других страницах. 

```rust
// src/components/product_card.rs
use crate::types::Product;
use yew::prelude::*;

pub struct ProductCard {
    props: Props,
}

#[derive(Properties, Clone)]
pub struct Props {
    pub product: Product,
    pub on_add_to_cart: Callback<()>,
}

impl Component for ProductCard {
    type Message =();
    type Properties = Props;

    fn create(props: Self::Properties, _link: ComponentLink<Self>) -> Self {
        Self { props }
    }

    fn update(&mut self, _msg: Self::Message) -> ShouldRender {
        true
    }

    fn change(&mut self, _props: Self::Properties) -> ShouldRender {
        true
    }

    fn view(&self) -> Html {
        let onclick = self.props.on_add_to_cart.reform(|_|());

        html! {
          <div>
            <img src={&self.props.product.image}/>
            <div>{&self.props.product.name}</div>
            <div>{"$"}{&self.props.product.price}</div>
            <button onclick=onclick>{"Add To Cart"}</button>
          </div>
        }
    }
}

// src/components/mod.rs
mod product_card;

pub use product_card::ProductCard;

  // src/lib.rs
  mod api;
+ mod components;
  mod pages;
  mod types;

  // No changes

  // src/pages/home.rs

  use crate::api;
+ use crate::components::ProductCard;
  use crate::types::{CartProduct, Product};
  use anyhow::Error;
  use yew::format::Json;
  use yew::prelude::*;
  use yew::services::fetch::FetchTask;

  // No changes

  impl Component for Home {
      // No changes

      fn view(&self) -> Html {
          let products: Vec<Html> = self
              .state
              .products
              .iter()
              .map(|product: &Product| {
                  let product_id = product.id;
                  html! {
-                   <div>
-                     <img src={&product.image}/>
-                     <div>{&product.name}</div>
-                     <div>{"$"}{&product.price}</div>
-                     <button onclick=self.link.callback(move |_| Msg::AddToCart(product_id))>{"Add To Cart"}</button>
-                   </div>
+                   <ProductCard product={product} on_add_to_cart=self.link.callback(move |_| Msg::AddToCart(product_id))/>
                  }
              })
              .collect();

          // No changes
      }
  }
```

Довольно просто, за исключением свойств, обратного вызова и реформы.

- Свойства. Как упоминалось в начале сообщения, компоненту вводятся «Свойства» или «Свойства». Если вы думаете о компонентах как о функциях, то Props - это аргументы функции.
- Для компонента ProductCard мы передаем структуру Product, а также обратный вызов on_add_to_cart. Этот компонент не содержит никакого состояния, поэтому, когда пользователь нажимает кнопку «Добавить в корзину», этот компонент вызывает родительский компонент для обновления состояния cart_products. Этот обратный вызов представлен с использованием типа Callback<T>, и чтобы вызвать его из дочернего компонента, мы либо используем методы emit, либо методы преобразования для обратного вызова.

![](/imgs/posts/ebe0fe80_07.png)

## Стиль

Пользовательский интерфейс выглядит просто, поскольку мы не добавляли никаких стилей.

![](/imgs/posts/ebe0fe80_08.png)

Мы можем использовать либо атрибут class, либо встроенные стили с Yew. Давайте добавим несколько стилей, чтобы интерфейс выглядел хорошо.

Давайте создадим новый файл CSS static / styles.css, добавим его в static / index.html, а затем мы сможем начать использовать классы в наших компонентах. 

```rust
  // src/pages/home.rs

  html! {
    <div>
-     <span>{format!("Cart Value: {:.2}", cart_value)}</span>
-     <span>{products}</span>
+     <div class="navbar">
+         <div class="navbar_title">{"RustMart"}</div>
+         <div class="navbar_cart_value">{format!("${:.2}", cart_value)}</div>
+     </div>
+     <div class="product_card_list">{products}</div>
    </div>
  }
```
```rust
  // src/components/product_card.rs
  html! {
-   <div>
-     <img src={&self.props.product.image}/>
-     <div>{&self.props.product.name}</div>
-     <div>{"$"}{&self.props.product.price}</div>
-     <button onclick=onclick>{"Add To Cart"}</button>
-   </div>
+   <div class="product_card_container">
+     <img class="product_card_image" src={&self.props.product.image}/>
+     <div class="product_card_name">{&self.props.product.name}</div>
+     <div class="product_card_price">{"$"}{&self.props.product.price}</div>
+     <button class="product_atc_button" onclick=onclick>{"Add To Cart"}</button>
+   </div>
  }
```

После добавления стилей и еще нескольких продуктов, вот как выглядит пользовательский интерфейс:

![](/imgs/posts/ebe0fe80_09.png)

Изменения CSS выходят за рамки этой публикации, обратитесь к репозиторию GitHub.

## Маршрутизация

На страницах, отображаемых на сервере (Jinja, ERB, JSP и т.д.), Каждая страница, которую видит пользователь, отображается в отдельный файл шаблона. Например, когда пользователь переходит к «/ login», он отображается на сервере с использованием «login.html», а когда пользователь переходит к «/ settings», он отображается с использованием «settings.html». Использование уникальных URL-адресов для разных страниц пользовательского интерфейса также полезно для создания закладок и обмена.

Поскольку в SPA есть только одна html-страница («Одна страница» в SPA), мы должны иметь возможность воспроизвести описанное выше поведение. Это делается с помощью маршрутизатора. Маршрутизатор сопоставляет разные URL-адреса (с параметрами запроса, фрагментами и т.д.) С разными компонентами страницы и помогает перемещаться между несколькими страницами без перезагрузки.

Для нашего приложения мы будем использовать это сопоставление: 

```
/            => HomePage
/product/:id => ProductDetailPage
```

Установим тис-роутер: 

```toml
  [package]
  name = "rustmart"
  version = "0.1.0"
  authors = ["sheshbabu <sheshbabu@gmail.com>"]
  edition = "2018"

  [lib]
  crate-type = ["cdylib", "rlib"]

  [dependencies]
  yew = "0.17"
+ yew-router = "0.14.0"
  wasm-bindgen = "0.2"
  log = "0.4.6"
  wasm-logger = "0.2.0"
  anyhow = "1.0.32"
  serde = { version = "1.0", features = ["derive"] }
```

Давайте добавим маршруты в специальный файл, чтобы было легче увидеть все доступные маршруты: 

```rust
// src/route.rs
use yew_router::prelude::*;

#[derive(Switch, Debug, Clone)]
pub enum Route {
    #[to = "/"]
    HomePage,
}
```

Пока у него только один маршрут. Мы добавим больше позже.

Давайте создадим новый файл с именем src/app.rs, чтобы заменить HomePage в качестве нового корневого компонента: 

```rust
use yew::prelude::*;
use yew_router::prelude::*;

use crate::pages::Home;
use crate::route::Route;

pub struct App {}

impl Component for App {
    type Message =();
    type Properties =();

    fn create(_: Self::Properties, _link: ComponentLink<Self>) -> Self {
        Self {}
    }

    fn update(&mut self, _msg: Self::Message) -> ShouldRender {
        true
    }

    fn change(&mut self, _: Self::Properties) -> ShouldRender {
        false
    }

    fn view(&self) -> Html {
        let render = Router::render(|switch: Route| match switch {
            Route::HomePage => html! {<Home/>},
        });

        html! {
            <Router<Route,()> render=render/>
        }
    }
}
```

Сделаем соответствующее изменение в lib.rs: 


```rust
  mod api;
+ mod app;
  mod components;
  mod pages;
+ mod route;
  mod types;

- use pages::Home;
  use wasm_bindgen::prelude::*;
  use yew::prelude::*;

  #[wasm_bindgen(start)]
  pub fn run_app() {
      wasm_logger::init(wasm_logger::Config::default());
-     App::<Home>::new().mount_to_body();
+     App::<app::App>::new().mount_to_body();
  }
```

Вот как выглядит наша иерархия компонентов на данный момент:

![](/imgs/posts/ebe0fe80_10.png)

## ProductDetailPage

Теперь, когда у нас есть маршрутизатор, давайте будем использовать его для перехода с одной страницы на другую. Поскольку это SPA, нам следует избегать перезагрузки страницы во время навигации.

Давайте добавим маршрут для ProductDetailPage в / product /: id. Когда пользователь нажимает на ProductCard, он переходит на страницу сведений с идентификатором в маршруте, переданным как Prop. 

```rust
  // src/route.rs
  use yew_router::prelude::*;

  #[derive(Switch, Debug, Clone)]
  pub enum Route {
+     #[to = "/product/{id}"]
+     ProductDetail(i32),
      #[to = "/"]
      HomePage,
  }
```

Обратите внимание, что порядок приведенных выше маршрутов определяет, какая страница будет отображаться первой. Например, url / product / 2 соответствует как / product / {id}, так и /, но, поскольку мы сначала написали / product / {id}, страница ProductDetail отображается вместо Home.

Добавьте этот маршрут в app.rs: 

```rust
  use yew::prelude::*;
  use yew_router::prelude::*;

- use crate::pages::{Home};
+ use crate::pages::{Home, ProductDetail};
  use crate::route::Route;

  pub struct App {}

  impl Component for App {
      // No changes

      fn view(&self) -> Html {
          let render = Router::render(|switch: Route| match switch {
+             Route::ProductDetail(id) => html! {<ProductDetail id=id/>},
              Route::HomePage => html! {<Home/>},
          });

          html! {
              <Router<Route,()> render=render/>
          }
      }
  }
```

Давайте обновим ProductCard, чтобы при нажатии на изображение, название или цену продукта мы попадали на эту новую страницу: 

```rust
  // src/components/product_card.rs
+ use crate::route::Route;
  use crate::types::Product;
  use yew::prelude::*;
+ use yew_router::components::RouterAnchor;

  // No changes

  impl Component for ProductCard {
      // No changes

      fn view(&self) -> Html {
+         type Anchor = RouterAnchor<Route>;
          let onclick = self.props.on_add_to_cart.reform(|_|());

          html! {
              <div class="product_card_container">
+                 <Anchor route=Route::ProductDetail(self.props.product.id) classes="product_card_anchor">
                      <img class="product_card_image" src={&self.props.product.image}/>
                      <div class="product_card_name">{&self.props.product.name}</div>
                      <div class="product_card_price">{"$"}{&self.props.product.price}</div>
+                 </Anchor>
                  <button class="product_atc_button" onclick=onclick>{"Add To Cart"}</button>
              </div>
          }
      }
  }
```

Обратите внимание, как мы использовали классы вместо класса для якоря.

Мы создадим файлы с именами static / products / 1.json, static / products / 2.json и т.д. С фиктивными данными: 

```json
{
  "id": 1,
  "name": "Apple",
  "description": "An apple a day keeps the doctor away",
  "image": "/products/apple.png",
  "price": 3.65
}
```

Давайте обновим модуль api.rs новым маршрутом: 

```rust
  use crate::types::Product;
  use anyhow::Error;
  use yew::callback::Callback;
  use yew::format::{Json, Nothing};
  use yew::services::fetch::{FetchService, FetchTask, Request, Response};

  pub type FetchResponse<T> = Response<Json<Result<T, Error>>>;
  type FetchCallback<T> = Callback<FetchResponse<T>>;

  pub fn get_products(callback: FetchCallback<Vec<Product>>) -> FetchTask {
      let req = Request::get("/products/products.json")
          .body(Nothing)
          .unwrap();

      FetchService::fetch(req, callback).unwrap()
  }

+ pub fn get_product(id: i32, callback: FetchCallback<Product>) -> FetchTask {
+     let req = Request::get(format!("/products/{}.json", id))
+         .body(Nothing)
+         .unwrap();
+
+     FetchService::fetch(req, callback).unwrap()
+ }
```

Наконец, вот компонент страницы ProductDetail:

```rust
// src/pages/product_detail.rs
use crate::api;
use crate::types::Product;
use anyhow::Error;
use yew::format::Json;
use yew::prelude::*;
use yew::services::fetch::FetchTask;

struct State {
    product: Option<Product>,
    get_product_error: Option<Error>,
    get_product_loaded: bool,
}

pub struct ProductDetail {
    props: Props,
    state: State,
    link: ComponentLink<Self>,
    task: Option<FetchTask>,
}

#[derive(Properties, Clone)]
pub struct Props {
    pub id: i32,
}

pub enum Msg {
    GetProduct,
    GetProductSuccess(Product),
    GetProductError(Error),
}

impl Component for ProductDetail {
    type Message = Msg;
    type Properties = Props;

    fn create(props: Self::Properties, link: ComponentLink<Self>) -> Self {
        link.send_message(Msg::GetProduct);

        Self {
            props,
            state: State {
                product: None,
                get_product_error: None,
                get_product_loaded: false,
            },
            link,
            task: None,
        }
    }

    fn update(&mut self, message: Self::Message) -> ShouldRender {
        match message {
            Msg::GetProduct => {
                let handler = self
                    .link
                    .callback(move |response: api::FetchResponse<Product>| {
                        let (_, Json(data)) = response.into_parts();
                        match data {
                            Ok(product) => Msg::GetProductSuccess(product),
                            Err(err) => Msg::GetProductError(err),
                        }
                    });

                self.task = Some(api::get_product(self.props.id, handler));
                true
            }
            Msg::GetProductSuccess(product) => {
                self.state.product = Some(product);
                self.state.get_product_loaded = true;
                true
            }
            Msg::GetProductError(error) => {
                self.state.get_product_error = Some(error);
                self.state.get_product_loaded = true;
                true
            }
        }
    }

    fn change(&mut self, _: Self::Properties) -> ShouldRender {
        false
    }

    fn view(&self) -> Html {
        if let Some(ref product) = self.state.product {
            html! {
                <div class="product_detail_container">
                    <img class="product_detail_image" src={&product.image}/>
                    <div class="product_card_name">{&product.name}</div>
                    <div style="margin: 10px 0; line-height: 24px;">{&product.description}</div>
                    <div class="product_card_price">{"$"}{&product.price}</div>
                    <button class="product_atc_button">{"Add To Cart"}</button>
                </div>
            }
        } else if !self.state.get_product_loaded {
            html! {
                <div class="loading_spinner_container">
                    <div class="loading_spinner"></div>
                    <div class="loading_spinner_text">{"Loading ..."}</div>
                </div>
            }
        } else {
            html! {
                <div>
                    <span>{"Error loading product! :("}</span>
                </div>
            }
        }
    }
}
```

Очень похоже на компонент HomePage. Давайте также добавим этот файл в дерево модулей: 

```rust
  // src/pages/mod.rs
  mod home;
+ mod product_detail;

  pub use home::Home;
+ pub use product_detail::ProductDetail;
```

Вот как это выглядит:

![](/imgs/posts/ebe0fe80_11.png)

Теперь мы можем перемещаться между несколькими страницами, не обновляя страницу!

## Государственное управление

На странице ProductDetail вы могли заметить одну вещь: нажатие кнопки «Добавить в корзину» не приводит к обновлению корзины. Это связано с тем, что состояние, содержащее список продуктов в корзине cart_products, в настоящее время находится внутри компонента домашней страницы:

![](/imgs/posts/ebe0fe80_12.png)

Чтобы разделить состояние между двумя компонентами, мы можем:

- Поднимите состояние к общему предку
- Переместить состояние в глобальное состояние приложения

Компонент App является общим предком как ProductDetail, так и Home. Мы можем переместить туда состояние cart_products и передать его в качестве реквизита в ProductDetail и Home.

![](/imgs/posts/ebe0fe80_13.png)

Это отлично работает для неглубоких иерархий компонентов, но когда у вас есть глубокая иерархия компонентов (что является обычным для больших SPA), вам нужно будет передать это состояние через несколько уровней компонентов (которые могут не использоваться для этой опоры), чтобы достичь желаемого. узел. Это называется «бурение опор».

Вы можете видеть, что cart_products теперь передается из приложения в компонент AddToCart через ProductDetail и Home, хотя они не используют это состояние. Представьте себе тот же сценарий с компонентами, имеющими многоуровневую глубину.

Это проблема, которую решает глобальное государство. Вот как это будет выглядеть:

![](/imgs/posts/ebe0fe80_14.png)

Обратите внимание на прямую связь между компонентами, которым требуется это состояние, и глобальным состоянием.

К сожалению, у Yew нет для этого хорошего решения. Рекомендуемое решение - использовать агентов для трансляции изменений состояния через pubsub. Это то, от чего я держусь подальше, так как это быстро становится грязным. Я надеюсь, что в будущем мы увидим что-то похожее на React’s Context, Redux или Mobx и т.д.

Давайте решим нашу проблему, подняв состояние.

## Состояние подъема

Мы проведем рефакторинг нашего кода, переместив состояние cart_products в приложение и извлекая Navbar и AtcButton как отдельные компоненты: 

![](/imgs/posts/ebe0fe80_15.png)

```rust
// src/components/navbar.rs
use crate::types::CartProduct;
use yew::prelude::*;

pub struct Navbar {
    props: Props,
}

#[derive(Properties, Clone)]
pub struct Props {
    pub cart_products: Vec<CartProduct>,
}

impl Component for Navbar {
    type Message =();
    type Properties = Props;

    fn create(props: Self::Properties, _link: ComponentLink<Self>) -> Self {
        Self { props }
    }

    fn update(&mut self, _msg: Self::Message) -> ShouldRender {
        true
    }

    fn change(&mut self, props: Self::Properties) -> ShouldRender {
        self.props = props;
        true
    }

    fn view(&self) -> Html {
        let cart_value = self
            .props
            .cart_products
            .iter()
            .fold(0.0, |acc, cp| acc + (cp.quantity as f64 * cp.product.price));

        html! {
            <div class="navbar">
                <div class="navbar_title">{"RustMart"}</div>
              <div class="navbar_cart_value">{format!("${:.2}", cart_value)}</div>
            </div>
        }
    }
}
```

Обратите внимание, как мы начали использовать методы жизненного цикла изменения в компоненте Navbar. Когда реквизиты, отправленные из родительского элемента, изменяются, нам нужно обновить реквизиты внутри компонента, чтобы пользовательский интерфейс отрисовался повторно. 

```rust
// src/components/atc_button.rs
use crate::types::Product;
use yew::prelude::*;

pub struct AtcButton {
    props: Props,
    link: ComponentLink<Self>,
}

#[derive(Properties, Clone)]
pub struct Props {
    pub product: Product,
    pub on_add_to_cart: Callback<Product>,
}

pub enum Msg {
    AddToCart,
}

impl Component for AtcButton {
    type Message = Msg;
    type Properties = Props;

    fn create(props: Self::Properties, link: ComponentLink<Self>) -> Self {
        Self { props, link }
    }

    fn update(&mut self, msg: Self::Message) -> ShouldRender {
        match msg {
            Msg::AddToCart => self.props.on_add_to_cart.emit(self.props.product.clone()),
        }
        true
    }

    fn change(&mut self, props: Self::Properties) -> ShouldRender {
        self.props = props;
        true
    }

    fn view(&self) -> Html {
        let onclick = self.link.callback(|_| Msg::AddToCart);

        html! {
          <button class="product_atc_button" onclick=onclick>{"Add To Cart"}</button>
        }
    }
}

  // src/components/mod.rs
+ mod atc_button;
+ mod navbar;
  mod product_card;

+ pub use atc_button::AtcButton;
+ pub use navbar::Navbar;
  pub use product_card::ProductCard;

Use the new AtcButton in ProductCard and ProductDetail:

  // src/components/product_card.rs
+ use crate::components::AtcButton;
  use crate::route::Route;
  use crate::types::Product;
  use yew::prelude::*;
  use yew_router::components::RouterAnchor;

  pub struct ProductCard {
      props: Props,
  }

  #[derive(Properties, Clone)]
  pub struct Props {
      pub product: Product,
-     pub on_add_to_cart: Callback<()>,
+     pub on_add_to_cart: Callback<Product>,
  }

  impl Component for ProductCard {
      // No changes

      fn view(&self) -> Html {
          type Anchor = RouterAnchor<Route>;
-         let onclick = self.props.on_add_to_cart.reform(|_|());

          html! {
              <div class="product_card_container">
                  <Anchor route=Route::ProductDetail(self.props.product.id) classes="product_card_anchor">
                      <img class="product_card_image" src={&self.props.product.image}/>
                      <div class="product_card_name">{&self.props.product.name}</div>
                      <div class="product_card_price">{"$"}{&self.props.product.price}</div>
                  </Anchor>
-                 <button class="product_atc_button" onclick=onclick>{"Add To Cart"}</button>
+                 <AtcButton product=self.props.product.clone() on_add_to_cart=self.props.on_add_to_cart.clone() />
              </div>
          }
      }
  }

  // src/pages/product_detail.rs
  use crate::api;
+ use crate::components::AtcButton;
  use crate::types::Product;
  use anyhow::Error;
  use yew::format::Json;
  use yew::prelude::*;
  use yew::services::fetch::FetchTask;

  // No changes

  #[derive(Properties, Clone)]
  pub struct Props {
      pub id: i32,
+     pub on_add_to_cart: Callback<Product>,
  }

  impl Component for ProductDetail {
      // No changes

      fn view(&self) -> Html {
          if let Some(ref product) = self.state.product {
              html! {
                  <div class="product_detail_container">
                      <img class="product_detail_image" src={&product.image}/>
                      <div class="product_card_name">{&product.name}</div>
                      <div style="margin: 10px 0; line-height: 24px;">{&product.description}</div>
                      <div class="product_card_price">{"$"}{&product.price}</div>
-                     <button class="product_atc_button">{"Add To Cart"}</button>
+                     <AtcButton product=product.clone() on_add_to_cart=self.props.on_add_to_cart.clone() />
                  </div>
              }
          }

          // No changes
      }
  }
```

Наконец, переместите состояние cart_products из Home в приложение: 

```rust
  // src/app.rs
+ use crate::components::Navbar;
+ use crate::types::{CartProduct, Product};
  use yew::prelude::*;
  use yew_router::prelude::*;

  use crate::pages::{Home, ProductDetail};
  use crate::route::Route;

+ struct State {
+     cart_products: Vec<CartProduct>,
+ }

- pub struct App {}
+ pub struct App {
+     state: State,
+     link: ComponentLink<Self>,
+ }

+ pub enum Msg {
+     AddToCart(Product),
+ }

  impl Component for App {
-     type Message =();
+     type Message = Msg;
      type Properties =();

-     fn create(_: Self::Properties, _link: ComponentLink<Self>) -> Self {
+     fn create(_: Self::Properties, link: ComponentLink<Self>) -> Self {
+         let cart_products = vec![];

-         Self {}
+         Self {
+             state: State { cart_products },
+             link,
+         }
      }

-     fn update(&mut self, _msg: Self::Message) -> ShouldRender {
+     fn update(&mut self, message: Self::Message) -> ShouldRender {
+         match message {
+             Msg::AddToCart(product) => {
+                 let cart_product = self
+                     .state
+                     .cart_products
+                     .iter_mut()
+                     .find(|cp: &&mut CartProduct| cp.product.id == product.id);

+                 if let Some(cp) = cart_product {
+                     cp.quantity += 1;
+                 } else {
+                     self.state.cart_products.push(CartProduct {
+                         product: product.clone(),
+                         quantity: 1,
+                     })
+                 }
+                 true
+             }
+         }
-         true
      }

      fn change(&mut self, _: Self::Properties) -> ShouldRender {
          false
      }

      fn view(&self) -> Html {
+         let handle_add_to_cart = self
+             .link
+             .callback(|product: Product| Msg::AddToCart(product));
+         let cart_products = self.state.cart_products.clone();

-         let render = Router::render(|switch: Route| match switch {
-           Route::ProductDetail(id) => html! {<ProductDetail id=id/>},
-           Route::HomePage => html! {<Home/>},
+         let render = Router::render(move |switch: Route| match switch {
+             Route::ProductDetail(id) => {
+                 html! {<ProductDetail id=id on_add_to_cart=handle_add_to_cart.clone() />}
+             }
+             Route::HomePage => {
+                 html! {<Home cart_products=cart_products.clone() on_add_to_cart=handle_add_to_cart.clone()/>}
+             }
          });

          html! {
+             <>
+                 <Navbar cart_products=self.state.cart_products.clone() />
                  <Router<Route,()> render=render/>
+             </>
          }
      }
  }

  // src/pages/home.rs
  // No changes

  struct State {
      products: Vec<Product>,
-     cart_products: Vec<CartProduct>,
      get_products_error: Option<Error>,
      get_products_loaded: bool,
  }

+ #[derive(Properties, Clone)]
+ pub struct Props {
+     pub cart_products: Vec<CartProduct>,
+     pub on_add_to_cart: Callback<Product>,
+ }

  pub struct Home {
+     props: Props,
      state: State,
      link: ComponentLink<Self>,
      task: Option<FetchTask>,
  }

  pub enum Msg {
-     AddToCart(i32),
      GetProducts,
      GetProductsSuccess(Vec<Product>),
      GetProductsError(Error),
  }

  impl Component for Home {
      type Message = Msg;
-     type Properties =();
+     type Properties = Props;

-     fn create(_: Self::Properties, link: ComponentLink<Self>) -> Self {
+     fn create(props: Self::Properties, link: ComponentLink<Self>) -> Self {
          let products = vec![];
-         let cart_products = vec![];

          link.send_message(Msg::GetProducts);

          Self {
              props,
              state: State {
                  products,
-                 cart_products,
                  get_products_error: None,
                  get_products_loaded: false,
              },
              link,
              task: None,
          }
      }

      fn update(&mut self, message: Self::Message) -> ShouldRender {
          match message {
              Msg::GetProducts => {
                  self.state.get_products_loaded = false;
                  let handler =
                      self.link
                          .callback(move |response: api::FetchResponse<Vec<Product>>| {
                              let (_, Json(data)) = response.into_parts();
                              match data {
                                  Ok(products) => Msg::GetProductsSuccess(products),
                                  Err(err) => Msg::GetProductsError(err),
                              }
                          });

                  self.task = Some(api::get_products(handler));
                  true
              }
              Msg::GetProductsSuccess(products) => {
                  self.state.products = products;
                  self.state.get_products_loaded = true;
                  true
              }
              Msg::GetProductsError(error) => {
                  self.state.get_products_error = Some(error);
                  self.state.get_products_loaded = true;
                  true
              }
-             Msg::AddToCart(product_id) => {
-                 let product = self
-                     .state
-                     .products
-                     .iter()
-                     .find(|p: &&Product| p.id == product_id)
-                     .unwrap();
-                 let cart_product = self
-                     .state
-                     .cart_products
-                     .iter_mut()
-                     .find(|cp: &&mut CartProduct| cp.product.id == product_id);
-                 if let Some(cp) = cart_product {
-                     cp.quantity += 1;
-                 } else {
-                     self.state.cart_products.push(CartProduct {
-                         product: product.clone(),
-                         quantity: 1,
-                     })
-                 }
-                 true
-             }
          }
      }

-     fn change(&mut self, _: Self::Properties) -> ShouldRender {
+     fn change(&mut self, props: Self::Properties) -> ShouldRender {
+         self.props = props;
          true
      }

      fn view(&self) -> Html {
          let products: Vec<Html> = self
              .state
              .products
              .iter()
              .map(|product: &Product| {
-                 let product_id = product.id;
                  html! {
-                   <ProductCard product={product} on_add_to_cart=self.link.callback(move |_| Msg::AddToCart(product_id))/>
+                   <ProductCard product={product} on_add_to_cart=self.props.on_add_to_cart.clone()/>
                  }
              })
              .collect();

-        let cart_value = self
-            .state
-            .cart_products
-            .iter()
-            .fold(0.0, |acc, cp| acc + (cp.quantity as f64 * cp.product.price));

          if !self.state.get_products_loaded {
              // No changes
          } else if let Some(_) = self.state.get_products_error {
              // No changes
          } else {
              html! {
-               <div>
-                 <div class="navbar">
-                     <div class="navbar_title">{"RustMart"}</div>
-                     <div class="navbar_cart_value">{format!("${:.2}", cart_value)}</div>
-                 </div>
                  <div class="product_card_list">{products}</div>
-               </div>
              }
          }
      }
  }
```

Теперь мы, наконец, можем добавить в корзину со страницы ProductDetail, и мы также можем видеть панель навигации на всех страницах.

![](/imgs/posts/ebe0fe80_16.png)

![](/imgs/posts/ebe0fe80_17.png)

Мы успешно построили SPA полностью на Rust!

Я разместил здесь демонстрацию, а код находится в этом репозитории GitHub. Если у вас есть вопросы или предложения, свяжитесь со мной по адресу sheshbabu [at] gmail.com.

## Вывод

Сообщество Yew проделало хорошую работу по разработке абстракций, таких как html !, Component и т.д., Так что кто-то вроде меня, знакомый с React, может сразу начать продуктивно. У него определенно есть некоторые шероховатости, такие как FetchTask, отсутствие предсказуемого управления состоянием и скудная документация, но он может стать хорошей альтернативой React, Vue и т.д., Как только эти проблемы будут исправлены.

Спасибо за чтение! Не стесняйтесь подписываться на меня в Twitter, чтобы увидеть больше подобных сообщений :) 