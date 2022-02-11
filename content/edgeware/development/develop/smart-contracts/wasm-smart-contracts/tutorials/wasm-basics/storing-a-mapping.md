+++
title = "Увеличение моего значения"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Последним шагом в нашем контракте с инкрементом является предоставление каждому пользователю возможности обновлять собственное значение инкремента.

## Изменение HashMap <a id="modifying-a-hashmap"></a>

Внесение изменений в значение HashMap так же важно, как и получение значения. Если вы попытаетесь изменить какое-либо значение до того, как оно будет инициализировано, ваш контракт запаникует!

Но не бойтесь, мы можем продолжать использовать функцию `my_number_or_zero`, которую мы создали для защиты от таких ситуаций!

```rust
impl MyContract {

    /* --snip-- */

    // Set the value for the calling AccountId
    #[ink(message)]
    pub fn set_my_number(&mut self, value: u32) {
        let caller = self.env().caller();
        self.my_number_map.insert(caller, value);
    }

    // Add a value to the existing value for the calling AccountId
    #[ink(message)]
    pub fn add_my_number(&mut self, value: u32) {
        let caller = self.env().caller();
        let my_number = self.my_number_or_zero(&caller);
        self.my_number_map.insert(caller, my_number + value);
    }

    /// Returns the number for an AccountId or 0 if it is not set.
    fn my_number_or_zero(&self, of: &AccountId) -> u32 {
        *self.my_number_map.get(of).unwrap_or(&0)
    }
}
```

Здесь мы написали два вида функций, которые изменяют HashMap. Один просто вставляет значение непосредственно в хранилище, без необходимости сначала читать значение, а другой изменяет существующее значение. Обратите внимание, что мы всегда можем `вставить` значение, не беспокоясь, так как это инициализирует значение в хранилище, но прежде чем вы сможете получить или изменить что-либо, нам нужно вызвать `my_number_or_zero`, чтобы убедиться, что мы работаем с реальным значением.

## Почувствуй боль \(необязательно\) <a id="feel-the-pain-optional"></a>

У нас не всегда будет существующее значение в хранилище нашего контракта. Мы можем воспользоваться преимуществом типа Rust `Option<T>` для решения этой задачи. Если в хранилище контракта нет значения, мы вставим новое; наоборот, если есть существующее значение, мы только обновим его.

ink! HashMaps предоставляет хорошо известный `entry` API, который мы можем использовать для достижения такого типа «расстроенного» поведения:

```rust
let caller = self.env().caller();
self.my_number_map
    .entry(caller)
    .and_modify(|old_value| old_value += by)
    .or_insert(by);
```

## Твоя очередь! <a id="your-turn"></a>

Следуйте «ДЕЙСТВИЯМ», чтобы завершить свой смарт-контракт Incrementer.

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
        my_value: ink_storage::collections::HashMap<AccountId, u64>,
    }

    impl Incrementer {
        #[ink(constructor)]
        pub fn new(init_value: i32) -> Self {
            Self {
                value: init_value,
                my_value: ink_storage::collections::HashMap::new(),
            }
        }

        #[ink(constructor)]
        pub fn default() -> Self {
            Self {
                value: 0,
                my_value: Default::default(),
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

        #[ink(message)]
        pub fn get_mine(&self) -> u64 {
            self.my_value_or_zero(&self.env().caller())
        }

        #[ink(message)]
        pub fn inc_mine(&mut self, by: u64) {
            // ACTION: Get the `caller` of this function.
            // ACTION: Get `my_value` that belongs to `caller` by using `my_value_or_zero`.
            // ACTION: Insert the incremented `value` back into the mapping.
        }

        fn my_value_or_zero(&self, of: &AccountId) -> u64 {
            *self.my_value.get(of).unwrap_or(&0)
        }
    }

    #[cfg(test)]
    mod tests {
        use super::*;
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
        my_value: ink_storage::collections::HashMap<AccountId, u64>,
    }

    impl Incrementer {
        #[ink(constructor)]
        pub fn new(init_value: i32) -> Self {
            Self {
                value: init_value,
                my_value: ink_storage::collections::HashMap::new(),
            }
        }

        #[ink(constructor)]
        pub fn default() -> Self {
            Self {
                value: 0,
                my_value: Default::default(),
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

        #[ink(message)]
        pub fn get_mine(&self) -> u64 {
            self.my_value_or_zero(&self.env().caller())
        }

        #[ink(message)]
        pub fn inc_mine(&mut self, by: u64) {
            let caller = self.env().caller();
            let my_value = self.my_value_or_zero(&caller);
            self.my_value.insert(caller, my_value + by);
        }

        fn my_value_or_zero(&self, of: &AccountId) -> u64 {
            *self.my_value.get(of).unwrap_or(&0)
        }
    }

    #[cfg(test)]
    mod tests {
        use super::*;
```

{% endtab %}
{% endtabs %}
