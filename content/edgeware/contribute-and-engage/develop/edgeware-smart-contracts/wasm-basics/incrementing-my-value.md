+++
title = "Сохранение значения"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Первое, что мы собираемся сделать с шаблоном контракта, — ввести некоторые значения для хранения.

Вот как вы могли бы хранить некоторые простые значения в хранилище:

```rust
#[ink(storage)]
pub struct MyContract {
    // Store a bool
    my_bool: bool,
    // Store some number
    my_number: u32,
}
/* --snip-- */
```

## Поддерживаемые типы <a id="supported-types"></a>

Контракт может хранить типы, которые кодируются и декодируются с помощью [кодека четности](https://github.com/paritytech/parity-codec), который включает наиболее распространенные типы, такие как `bool`, `u{8,16,32, 64,128}`, `i{8,16,32,64,128}`, `String`, кортежи и массивы.

ink! предоставляет смарт-контракты. Типы субстрата, такие как «AccountId», «Balance» и «Hash», как если бы они были примитивными типами. Также ink! предоставляет типы хранения для более сложных взаимодействий с хранилищем через модуль хранения:

```rust
use ink_storage::collections::{Vec, HashMap, Stash, Bitvec};
```

Вот пример того, как вы будете хранить `AccountId` и `Balance`:

```rust
// We are importing the default ink! types
use ink_lang as ink;

#[ink::contract]
mod MyContract {

    // Our struct will use those default ink! types
    #[ink(storage)]
    pub struct MyContract {
        // Store some AccountId
        my_account: AccountId,
        // Store some Balance
        my_balance: Balance,
    }
    /* --snip-- */
}
```

Вы можете найти все поддерживаемые типы субстратов в [`crates/storage/src/lib.rs`](https://github.com/paritytech/ink/blob/master/crates/storage/src/lib.rs).

## Развертывание контракта <a id="contract-deployment"></a>

Каждая ink! смарт-контракт должен иметь конструктор, который запускается один раз при создании контракта. ink! смарт-контракты могут иметь несколько конструкторов:

```rust
use ink_lang as ink;

#[ink::contract]
mod mycontract {

    #[ink(storage)]
    pub struct MyContract {
        number: u32,
    }

    impl MyContract {
        /// Constructor that initializes the `u32` value to the given `init_value`.
        #[ink(constructor)]
        pub fn new(init_value: u32) -> Self {
            Self {
                number: init_value,
            }
        }

        /// Constructor that initializes the `u32` value to the `u32` default.
        ///
        /// Constructors can delegate to other constructors.
        #[ink(constructor)]
        pub fn default() -> Self {
            Self {
                number: Default::default(),
            }
        }
    /* --snip-- */
    }
}
```

## [Твоя очередь!](https://contracts.edgewa.re/#/1/storing-a-value?id=your-turn) <a id="your-turn"></a>

Следуйте ДЕЙСТВИЯМ в шаблоне.

Не забудьте запустить `cargo +nightly test`, чтобы проверить свою работу.

{% tabs %}
{% tab title="🔨Starting Point" %}

```rust
#![cfg_attr(not(feature = "std"), no_std)]

use ink_lang as ink;

#[ink::contract]
mod incrementer {

    #[ink(storage)]
    pub struct Incrementer {
        // ACTION: Create a storage value called `value` which holds a `i32`
    }

    impl Incrementer {
        #[ink(constructor)]
        pub fn new(init_value: i32) -> Self {
            // ACTION: Create a new `Incrementer` and set its `value` to `init_value`
        }

        // ACTION: Create a second constructor function named `default`.
        //         It has no input, and creates a new `Incrementer` with its `value`
        //         set to `0`.

        #[ink(message)]
        pub fn get(&self) {
            // Contract Message
        }
    }

    #[cfg(test)]
    mod tests {
        use super::*;

        #[test]
        fn default_works() {
            Incrementer::default();
        }
    }
}
```

{% endtab %}

{% tab title="✅Potential Solution" %}

```rust
#![cfg_attr(not(feature = "std"), no_std)]

use ink_lang as ink;

#[ink::contract]
mod incrementer {
    #[ink(storage)]
    pub struct Incrementer {
        value: i32,
    }

    impl Incrementer {
        #[ink(constructor)]
        pub fn new(init_value: i32) -> Self {
            Self {
                value: init_value,
            }
        }

        #[ink(constructor)]
        pub fn default() -> Self {
            Self {
                value: 0,
            }
        }

        #[ink(message)]
        pub fn get(&self) {
            // Contract Message
        }
    }

    #[cfg(test)]
    mod tests {
        use super::*;

        #[test]
        fn default_works() {
            Incrementer::default();
        }
    }
}
```

{% endtab %}
{% endtabs %}
