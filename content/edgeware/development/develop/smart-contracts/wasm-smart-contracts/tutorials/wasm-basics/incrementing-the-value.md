+++
title = "Увеличение значения"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Пришло время позволить нашим пользователям изменять хранилище!

## Изменяемые и неизменяемые функции <a id="mutable-and-immutable-functions"></a>

Вы могли заметить, что шаблоны функций включают `self` в качестве первого параметра контрактных функций. Именно через `self` вы получаете доступ ко всем вашим контрактным функциям и элементам хранилища.

Если вы просто _читаете_ из хранилища контрактов, вам нужно только передать `&self`. Но если вы хотите _модифицировать_ элементы хранилища, вам нужно явно пометить их как изменяемые, `&mut self`.

```rust
impl MyContract {
    #[ink(message)]
    pub fn my_getter(&self) -> u32 {
        self.my_number
    }

    #[ink(message)]
    pub fn my_setter(&mut self, new_value: u32) {
        self.my_number = new_value;
    }
}
```

## Ленивые значения хранения <a id="lazy-storage-values"></a>

Существует [ленивый тип](https://paritytech.github.io/ink/ink_storage/struct.Lazy.html), который можно использовать для чернил! значения хранения, которые не нужно загружать в некоторых или большинстве случаев. Потому что они не соответствуют этому критерию, многие простые чернила! примеры, в том числе и в этом семинаре, не требуют использования «ленивых» значений. Поскольку существуют некоторые накладные расходы, связанные со значениями `Lazy`, их следует использовать только там, где это необходимо.

```rust
#[ink(storage)]
pub struct MyContract {
    // Store some number
    my_number: ink_storage::Lazy<u32>,
}

impl MyContract {
    #[ink(constructor)]
    pub fn new(init_value: i32) -> Self {
        Self {
            my_number: Default::default(),
        }
    }

    #[ink(message)]
    pub fn my_setter(&mut self, new_value: u32) {
        ink_storage::Lazy::<u32>::set(&mut self.my_number, new_value);
    }

    #[ink(message)]
    pub fn my_adder(&mut self, add_value: u32) {
        let my_number = &mut self.my_number;
        let cur = ink_storage::Lazy::<u32>::get(my_number);
        ink_storage::Lazy::<u32>::set(my_number, cur + add_value);
    }
}
```

## Твоя очередь <a id="your-turn"></a>

Следуйте `ACTION`s в коде шаблона.

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
        pub fn get(&self) -> i32 {
            self.value
        }

        #[ink(message)]
        pub fn inc(&mut self, by: i32) {
            // ACTION: Simply increment `value` by `by`
        }
    }

    #[cfg(test)]
    mod tests {
        use super::*;

        #[test]
        fn default_works() {
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
        pub fn get(&self) -> i32 {
            self.value
        }

        #[ink(message)]
        pub fn inc(&mut self, by: i32) {
            self.value += by;
        }
    }

    #[cfg(test)]
    mod tests {
        use super::*;

        #[test]
        fn default_works() {
```

{% endtab %}
{% endtabs %}
