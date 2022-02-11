+++
title = "Шаблон контракта"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Начнем с создания новых чернил! проект по созданию избирательного контракта.

В вашем рабочем каталоге запустите:

```
cargo contract new ballot
```

Замените содержимое файла `lib.rs` шаблоном справа.

Хранилище контракта состоит из `AccountId`, который мы инициализируем идентификатором вызывающего абонента в конструкторе. Реализована функция `get_chair_person`, которая возвращает идентификатор председателя\_person\(владельца\) контракта.

## Структура <a id="struct"></a>

Возможно, вы встречали ключевое слово [struct](https://doc.rust-lang.org/book/ch05-01-defining-structs.html) в предыдущих руководствах, но до сих пор мы использовали структуры для определения хранилища. контрактов. В этом контракте мы используем его для определения следующих пользовательских типов, которые будут использоваться позже в качестве части хранилища для бюллетеней:

- `Proposal`: эта структура хранит информацию о предложении. Каждое предложение содержит:
  - `name`: поле для хранения названия предложения
  - `vote_count`: 32-битное целое число без знака для хранения количества голосов, полученных за предложение.
- «Избиратель»: для каждого избирателя в системе мы создаем структуру избирателя с помощью:
  - `вес`: вес без знака, указывающий вес избирателя. Вес избирателя может варьироваться в зависимости от параметров выборов/сети.
  - `voted`: по умолчанию установлено значение false, но после того, как избиратель проголосовал, оно устанавливается в значение true, чтобы тот же избиратель не мог снова отдать свой голос.
  - `делегат`: избиратель может делегировать свой голос кому-то другому. Поскольку избирателям не обязательно делегировать полномочия, это поле создается как «Опция».
  - `vote`: индекс предложения, за которое проголосовал пользователь. Это создается как «Опция», установленная на «Нет» по умолчанию.

В отличие от нашей структуры контракта `Ballot`, мы не используем макрос `ink(storage)` для наших настраиваемых структур, поскольку для контракта может быть только одна структура хранения. Кроме того, наши структуры не являются общедоступными, поскольку пользователям не нужно взаимодействовать с ними напрямую.

## Ink\_Prelude <a id="ink_prelude"></a>

`ink_pelude` crate предоставляет структуры данных, такие как `HashMap`, `Vector` и т. д., для работы с памятью контракта во время выполнения контракта. Мы будем импортировать эти коллекции в следующих частях этого руководства, поэтому, прежде чем двигаться дальше, обновите файл контракта cargo.toml со следующей зависимостью: `ink_prelude = { version = "3.0.0-rc2", default-features = false }`

## Компиляция и предупреждения <a id="compilaton-and-warnings"></a>

Вы можете построить контракт, используя `cargo +nightly build`, и запустить тесты, используя `cargo +nightly test`. Контракт успешно скомпилируется и пройдет все тесты, но компилятор rust выдаст вам следующие предупреждения:

```rust
warning: struct is never constructed: `Proposal`
  --> lib.rs:10:12
   |
10 |     struct Proposal {
   |            ^^^^^^^^
   |
   = note: `#[warn(dead_code)]` on by default

warning: struct is never constructed: `Voter`
  --> lib.rs:16:16
   |
16 |     pub struct Voter {
   |                ^^^^^

warning: 2 warnings emitted
```

Это связано с тем, что определенные нами структуры никогда не используются. Мы доберемся до этого в следующей части!

{% tabs %}
{% tab title="🔨Starting Point" %}

```rust
#![cfg_attr(not(feature = "std"), no_std)]

use ink_lang as ink;

#[ink::contract]
mod ballot {
    // Structure to store Proposal information
    struct Proposal {
        name: String,
        vote_count: i32, 
    }

    // Structure to store Proposal information
    pub struct Voter {
        weight: i32,
        voted: bool,
        delegate: Option<AccountId>, 
        vote: Option<i32>, 
    }

    /// Defines the storage of your contract.
    /// Add new fields to the below struct in order
    /// to add new static storage fields to your contract.
    #[ink(storage)]
    pub struct Ballot {
        chair_person: AccountId,
    }

    impl Ballot {
        #[ink(constructor)]
        pub fn new() -> Self {
            let owner = Self::env().caller();
            Self { 
                chair_person:owner,
              }
        }


        #[ink(message)]
        pub fn get_chairperson(&self) -> AccountId {
            self.chair_person
        }

    }

    /// Unit tests in Rust are normally defined within such a `#[cfg(test)]`
    /// module and test functions are marked with a `#[test]` attribute.
    /// The below code is technically just normal Rust code.
    #[cfg(test)]
    mod tests {
        /// Imports all the definitions from the outer scope so we can use them here.
        use super::*;

        // Alias `ink_lang` so we can use `ink::test`.
        use ink_lang as ink;

        #[ink::test]
        fn new_works() {
            let ballot = Ballot::new();
            assert_eq!(ballot.get_chairperson(),AccountId::from([0x1; 32]));
        }
    }
}
```

{% endtab %}
{% endtabs %}
