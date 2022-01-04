+++
title = "Как создать простой REST API в Rust"
description = "Как создать простой REST API в Rust"
weight = 1
+++

[Перевод](https://morioh.com/p/d4b5c0ca1174)

## Введение

Rust - это язык программирования с несколькими парадигмами, ориентированный на производительность и безопасность, особенно на безопасный параллелизм. Rust синтаксически похож на C++, но обеспечивает безопасность памяти без использования сборки мусора.

Язык программирования Rust помогает писать более быстрое и надежное программное обеспечение. Эргономика высокого уровня и управление на низком уровне часто противоречат друг другу при разработке языков программирования; Rust бросает вызов этому конфликту. Благодаря уравновешиванию мощных технических возможностей и отличного опыта разработчика, Rust дает вам возможность управлять низкоуровневыми деталями (такими как использование памяти) без всех хлопот, традиционно связанных с таким контролем.
Узнайте, как создать REST API в Rust - пошаговое руководство

Для этого мы собираемся использовать фреймворк Rocket для API и фреймворк Diesel ORM для сохранения функций. Этот фреймворк будет охватывать все, что упомянуто ниже. Так что это будет намного проще, чем реализовать с нуля.

- Запустите веб-сервер и откройте ПОРТ.
- Слушайте запросы по этому ПОРТУ.
- Если поступает запрос, посмотрите Путь в заголовке HTTP.
- Направьте запрос обработчику в соответствии с Path.
- Помогу извлечь информацию из запроса.
- Упакуйте сгенерированные данные (созданные вами) и сформируйте ответ.
- Отправить ответ обратно отправителю.

## Установка Rust Nightly

Поскольку Rocket широко использует расширения синтаксиса Rust и другие продвинутые, нестабильные функции, мы должны устанавливать его каждую ночь. 

```
rustup default nightly
```

Если вы предпочитаете устанавливать каждую ночь только в каталоге вашего проекта, вы можете использовать следующее: 

```
rustup override set nightly
```

## Зависимости 

```toml
[dependencies]
rocket = "0.4.4"
rocket_codegen = "0.4.4"
diesel = { version = "1.4.0", features = ["postgres"] }
dotenv = "0.9.0"
r2d2-diesel = "1.0"
r2d2 = "0.8"
serde = "1.0"
serde_derive = "1.0"
serde_json = "1.0"
custom_derive ="0.1.7"

[dependencies.rocket_contrib]
version = "*"
default-features = false
features = ["json"]
```

Я объясню эти крэйти, когда мы их воспользуемся.

## Настройка дизельного топлива

Итак, следующее, что я собираюсь сделать, это настроить Дизель. Diesel предоставляет собственный интерфейс командной строки, поэтому сначала необходимо его установить. (Предполагая, что вы используете PostgreSQL.) 

```
cargo install diesel_cli — no-default-features — features postgre
```

Затем вам нужно сообщить Diesel, какие у вас учетные данные в БД. Эта команда сгенерирует файл .env. 

```
echo DATABASE_URL=postgres://username:password@localhost:port/diesel_demo > .env
```

После этого запустите эту команду: 

```
diesel setup
```

Это создаст нашу базу данных (если она еще не существует) и создаст пустой каталог миграции, который мы можем использовать для управления нашей схемой (подробнее об этом позже).

При выполнении приведенного выше кода может быть несколько ошибок. 

```
= note: LINK : fatal error LNK1181: cannot open input file ‘libpq.lib’
```

Вы можете легко это исправить, добавив путь к папке PG lib в переменные среды. 

```
setx PQ_LIB_DIR “[path to pg lib folder]”
```

Я удивлен, что эти ошибки не упоминаются в документации Diesel.

Я настоятельно рекомендую запускать эти команды в CMD или Powershell. Если вы используете терминал IDE, у вас не будет таких ошибок, и вы потратите два часа, пытаясь понять, что, черт возьми, происходит.

Картина: Некоторые

Чтобы исправить это, вы можете добавить путь к папке bin PG в переменные Path. Проблема решена? Хорошо!

Давайте создадим таблицу пользователей и создадим для этого миграцию: 

```
diesel migration generate users
```

После выполнения этой команды вы увидите, что в папке миграции сгенерированы два файла.

Далее мы напишем SQL для миграций: 

up.sql
```sql
CREATE TABLE users
(
    id         SERIAL PRIMARY KEY,
    username   VARCHAR NOT NULL,
    password   VARCHAR NOT NULL,
    first_name VARCHAR NOT NULL
)
```

down.sql

```sql
DROP TABLE users
```

Чтобы применить нашу миграцию, вы можете использовать: 

```
diesel migration run
```

Хорошо убедиться, что down.sql верен. Вы можете быстро убедиться, что ваш down.sql правильно откатывает миграцию, повторив миграцию: 

```
diesel migration redo
```

Как видите, в вашей БД есть таблица пользователей. Правильно!

Я забыл упомянуть, если вы заметили, что после запуска программы установки Diesel создается файл с именем schema.rs. Должно получиться вот так. 

```
table! {
    users (id) {
        id -> Int4,
        username -> Varchar,
        password -> Varchar,
        first_name -> Varchar,
    }
}
```

## Круто, вот и часть Rust

Поскольку мы собираемся использовать ORM, очевидно, что нам нужно сопоставить пользовательскую таблицу с чем-то в Rust. В Java мы используем Class для сопоставления таблиц. В Java **, ** мы обычно называем их Бобами. В Rust мы используем структуры. Давайте создадим структуру. 

```rust
use diesel;
use diesel::pg::PgConnection;
use diesel::prelude::*;
use super::schema::users;
use super::schema::users::dsl::users as all_users;
// this is to get users from the database
#[derive(Serialize, Queryable)] 
pub struct User {
    pub id: i32,
    pub username: String,
    pub password: String,
    pub first_name: String,
}
```

Теперь вы можете задаться вопросом, что это за аннотации, как это определение структуры выше.

Они называются производными. Итак, эта строка будет выводить сериализованные и запрашиваемые трэйты. #[derive(Serialize)] и #[derive(Deserialize)] используются для сопоставления данных с ответом и запросом.

Теперь я собираюсь создать еще две структуры. Вы получите их позже. 

```rust
// decode request data
#[derive(Deserialize)] 
pub struct UserData {
    pub username: String,
}
// this is to insert users to database
#[derive(Serialize, Deserialize, Insertable)]
#[table_name = "users"]
pub struct NewUser {
    pub username: String,
    pub password: String,
    pub first_name: String,
}
```

Следующее, что мы собираемся сделать, это реализовать User. Таким образом, у него будет несколько методов для операций с базой данных.

Здесь, как вы можете видеть, мы передали соединение методу и вернули вектор пользователей. Мы получаем все строки в пользовательской таблице и сопоставляем их со структурой User.

Конечно, мы ждем ошибок. В случае паники будет распечатано сообщение «ошибка». 

```rust
impl User {

    pub fn get_all_users(conn: &PgConnection) -> Vec<User> {
        all_users
            .order(users::id.desc())
            .load::<User>(conn)
            .expect("error!")
    }

    pub fn insert_user(user: NewUser, conn: &PgConnection) -> bool {
        diesel::insert_into(users::table)
            .values(&user)
            .execute(conn)
            .is_ok()
    }

    pub fn get_user_by_username(user: UserData, conn: &PgConnection) -> Vec<User> {
        all_users
            .filter(users::username.eq(user.username))
            .load::<User>(conn)
            .expect("error!")
    }
}
```

Теперь мы создали таблицу и структуры для сопоставления этой таблицы. Следующее, что мы собираемся сделать, это создать методы для его использования. Итак, мы собираемся создать файл маршрутов. Обычно мы называем это обработчиком. 

```rust
use super::db::Conn as DbConn;
use rocket_contrib::json::Json;
use super::models::{User, NewUser};
use serde_json::Value;
use crate::models::UserData;

#[post("/users", format = "application/json")]
pub fn get_all(conn: DbConn) -> Json<Value> {
    let users = User::get_all_users(&conn);
    Json(json!({
        "status": 200,
        "result": users,
    }))
}

#[post("/newUser", format = "application/json", data = "<new_user>")]
pub fn new_user(conn: DbConn, new_user: Json<NewUser>) -> Json<Value> {
    Json(json!({
        "status": User::insert_user(new_user.into_inner(), &conn),
        "result": User::get_all_users(&conn).first(),
    }))
}

#[post("/getUser", format = "application/json", data = "<user_data>")]
pub fn find_user(conn: DbConn, user_data: Json<UserData>) -> Json<Value> {
    Json(json!({
        "status": 200,
        "result": User::get_user_by_username(user_data.into_inner(), &conn),
    }))
}
```

Теперь все, что нам нужно сделать, это настроить пул соединений. Вот краткое объяснение пула соединений из документации Rocket.

> Rocket включает встроенную, не зависящую от ORM поддержку баз данных. В частности, Rocket предоставляет процедурный макрос, который позволяет легко подключать приложение Rocket к базам данных через пулы соединений. Пул соединений с базой данных - это структура данных, которая поддерживает активные соединения с базой данных для последующего использования в приложении. 

```rust
use diesel::pg::PgConnection;
use r2d2;
use r2d2_diesel::ConnectionManager;
use rocket::http::Status;
use rocket::request::{self, FromRequest};
use rocket::{Outcome, Request, State};
use std::ops::Deref;

pub type Pool = r2d2::Pool<ConnectionManager<PgConnection>>;

pub fn init_pool(db_url: String) -> Pool {
    let manager = ConnectionManager::<PgConnection>::new(db_url);
    r2d2::Pool::new(manager).expect("db pool failure")
}

pub struct Conn(pub r2d2::PooledConnection<ConnectionManager<PgConnection>>);

impl<'a, 'r> FromRequest<'a, 'r> for Conn {
    type Error =();

    fn from_request(request: &'a Request<'r>) -> request::Outcome<Conn,()> {
        let pool = request.guard::<State<Pool>>()?;
        match pool.get() {
            Ok(conn) => Outcome::Success(Conn(conn)),
            Err(_) => Outcome::Failure((Status::ServiceUnavailable,())),
        }
    }
}

impl Deref for Conn {
    type Target = PgConnection;

    #[inline(always)]
    fn deref(&self) -> &Self::Target {
        &self.0
    }
}
```

Наконец, нам нужно запустить наш сервер в основном файле. 

```rust
#![feature(plugin, const_fn, decl_macro, proc_macro_hygiene)]
#![allow(proc_macro_derive_resolution_fallback, unused_attributes)]

#[macro_use] extern crate diesel;

#[macro_use] extern crate rocket;
#[macro_use] extern crate serde_derive;
#[macro_use] extern crate serde_json;

use dotenv::dotenv;
use std::env;
use routes::*;
use std::process::Command;

mod db;
mod models;
mod routes;
mod schema;

fn rocket() -> rocket::Rocket {
    dotenv().ok();

    let database_url = env::var("DATABASE_URL").expect("set DATABASE_URL");

    let pool = db::init_pool(database_url);
    rocket::ignite()
        .manage(pool)
        .mount(
            "/api/v1/",
            routes![get_all, new_user, find_user],
        )
}

fn main() {
    let _output = if cfg!(target_os = "windows") {
        Command::new("cmd")
            .args(&["/C", "cd ui && npm start"])
            .spawn()
            .expect("Failed to start UI Application")
    } else {
        Command::new("sh")
            .arg("-c")
            .arg("cd ui && npm start")
            .spawn()
            .expect("Failed to start UI Application")
    };
    rocket().launch();
}
```

Внутри моего проекта я также добавил интерфейс Angular. Я также буду использовать нашу серверную часть Rust, чтобы обслуживать его.

Для запуска приложения → Cargo run.

Картина: Некоторые

Давайте протестируем наш сервер с помощью Insomnia.

Картина: Некоторые

Надеюсь, это поможет. Ваше здоровье! 