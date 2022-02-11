+++
title = "Получение значения"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Теперь, когда мы создали и инициализировали значение хранилища, мы начнем взаимодействовать с ним!

## Контрактные функции <a id="contract-functions"></a>

Как видно из шаблона контракта, все функции контракта являются частью модуля контракта.

```rust
impl MyContract {
    // Public and Private functions can go here
}
```

### Публичные и приватные функции <a id="public-and-private-functions"></a>

В Rust вы можете сделать столько реализаций, сколько захотите. В качестве стилистического выбора мы рекомендуем разделить определения реализации на частные и общедоступные функции:

```rust
impl MyContract {
    /// Public function
    #[ink(message)]
    pub fn my_public_function(&self) {
        /* --snip-- */
    }

    /// Private function
    fn my_private_function(&self) {
        /* --snip-- */
    }

    /* --snip-- */
}
```

Вы также можете разделить вещи, как это наиболее понятно для вашего проекта.

Обратите внимание, что все общедоступные функции должны использовать атрибут `#[ink(message)]`.

## API ценности хранилища <a id="storage-value-api"></a>

Не вдаваясь в подробности, скажем, что значения хранения являются частью базовых чернил! основной слой. В фоновом режиме они используют более примитивный тип `cell`, который содержит `Option<T>`. Когда мы пытаемся получить значение из хранилища, мы «разворачиваем» значение, поэтому оно паникует, если оно не инициализировано!

```rust
impl<T> Value<T>
where
    T: scale::Codec,
{
    /// Returns an immutable reference to the wrapped value.
    pub fn get(&self) -> &T {
        self.cell.get().unwrap()
    }

    /// Returns a mutable reference to the wrapped value.
    pub fn get_mut(&mut self) -> &mut T {
        self.cell.get_mut().unwrap()
    }

    /// Sets the wrapped value to the given value.
    pub fn set(&mut self, val: T) {
        self.cell.set(val);
    }
}
```

В том же файле вы можете найти другие API, предоставляемые значениями хранилища, однако эти три используются чаще всего.

## Получение значения <a id="getting-a-value-1"></a>

Мы уже показали вам, как инициализировать значение хранилища. Получить значение так же просто:

```rust
impl MyContract {
    #[ink(message)]
    fn my_getter(&self) -> u32 {
        self.number
    }
}
```

В Rust, если в последнем выражении функции нет точки с запятой, то это будет возвращаемое значение.

## Твоя очередь! <a id="your-turn"></a>

Следуйте `ACTION`s в предоставленном шаблоне кода.

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
                value: init_value
            }
        }

        #[ink(constructor)]
        pub fn default() -> Self {
            Self {
                value: 0
            }
        }

        #[ink(message)]
        // ACTION: Update this function to return an i32.
        pub fn get(&self) {
            // ACTION: Return the `value`.
        }
    }

    #[cfg(test)]
    mod tests {
        use super::*;

        #[test]
        fn default_works() {
            let contract = Incrementer::default();
            assert_eq!(contract.get(), 0);
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
                value: init_value
            }
        }

        #[ink(constructor)]
        pub fn default() -> Self {
            Self {
                value: 0
            }
        }

        #[ink(message)]
        pub fn get(&self) -> i32 {
            self.value
        }
    }

    #[cfg(test)]
    mod tests {
        use super::*;

        #[test]
        fn default_works() {
            let contract = Incrementer::default();
            assert_eq!(contract.get(), 0);
        }
    }
}
```

{% endtab %}
{% endtabs %}
