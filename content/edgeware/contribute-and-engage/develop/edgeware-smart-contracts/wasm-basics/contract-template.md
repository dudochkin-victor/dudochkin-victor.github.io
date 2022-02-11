+++
title = "Шаблон контракта"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Давайте взглянем на высокий уровень, что доступно вам при разработке смарт-контракта с использованием ink!.

## ink! <a id="ink"></a>

ink! — это [eDSL](https://wiki.haskell.org/Embedded_domain_specific_language) для написания смарт-контрактов на основе WebAssembly на языке программирования Rust.

ink! это просто стандартный Rust в четко определенном «формате контракта» со специализированными макросами атрибутов `#[ink(...)]`. Эти макросы атрибутов сообщают ink! что представляют собой различные части вашего смарт-контракта Rust и, в конечном счете, позволяет использовать чернила! чтобы сделать всю магию, необходимую для создания байт-кодов Wasm, совместимых с Substrate!

## Твоя очередь! <a id="your-turn"></a>

Мы собираемся начать новый проект для контракта Incrementer, который мы создадим в этой главе.

Итак, зайдите в свой рабочий каталог и запустите:

```
cargo contract new incrementer
```

Как и раньше, это создаст новую папку проекта с именем `incrementer`, которую мы будем использовать до конца этой главы.

```
cd incrementer/
```

В файле `lib.rs` замените исходный код контракта "Flipper" приведенным здесь кодом шаблона.

Быстро проверьте, что он компилируется и тривиальный тест проходит:

```
cargo +nightly test
```

Также убедитесь, что вы можете создать файл Wasm, запустив:

```
cargo +nightly contract build
```

Если все выглядит хорошо, то мы готовы приступить к программированию!

{% tabs %}
{% tab title="✅Potential Solution" %}
{% code title="lib.rs" %}

```rust
#![cfg_attr(not(feature = "std"), no_std)]

use ink_lang as ink;

#[ink::contract]
mod incrementer {

    #[ink(storage)]
    pub struct Incrementer {
        // Storage Declaration
    }

    impl Incrementer {
        #[ink(constructor)]
        pub fn new(init_value: i32) -> Self {
            // Contract Constructor
            Self{}
        }

        #[ink(message)]
        pub fn get(&self) {
            // Contract Message
        }
    }

    #[cfg(test)]
    mod tests {
        #[test]
        fn default_works() {
            // Test Your Contract
        }
    }
}
```

{% endcode %}
{% endtab %}
{% endtabs %}
