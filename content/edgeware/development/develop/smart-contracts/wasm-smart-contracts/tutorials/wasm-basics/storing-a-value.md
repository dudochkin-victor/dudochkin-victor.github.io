+++
title = "Сохранение сопоставления"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

## Сохранение сопоставления <a id="storing-a-mapping"></a>

Теперь давайте расширим наш Incrementer, чтобы управлять не только одним номером, но и управлять одним номером для каждого пользователя!

### Хеш-карта хранилища <a id="storage-hashmap"></a>

Помимо хранения отдельных значений, ink! также поддерживает `HashMap`, который позволяет вам хранить элементы в сопоставлении ключ-значение.

Вот пример сопоставления пользователя с числом:

```rust
#[ink(storage)]
pub struct MyContract {
    // Store a mapping from AccountIds to a u32
    my_number_map: ink_storage::collections::HashMap<AccountId, u32>,
}
```

Это означает, что для данного ключа вы можете хранить уникальный экземпляр типа значения. В этом случае каждый «пользователь» получает свой номер, и мы можем построить логику так, чтобы только они могли изменять свой номер.

### API HashMap хранилища <a id="storage-hashmap-api"></a>

Вы можете найти полный API HashMap в [crates/storage/src/collections/hashmap](https://github.com/paritytech/ink/blob/master/crates/storage/src/collections/hashmap/mod.rs ) часть ink!.

Вот некоторые из наиболее распространенных функций, которые вы можете использовать:

```rust
    /// Inserts a key-value pair into the map.
    ///
    /// Returns the previous value associated with the same key if any.
    /// If the map did not have this key present, `None` is returned.
    pub fn insert(&mut self, key: K, new_value: V) -> Option<V> {/* --snip-- */}

    /// Removes the key/value pair from the map associated with the given key.
    ///
    /// - Returns the removed value if any.
    pub fn take<Q>(&mut self, key: &Q) -> Option<V> {/* --snip-- */}

    /// Returns a shared reference to the value corresponding to the key.
    ///
    /// The key may be any borrowed form of the map's key type,
    /// but `Hash` and `Eq` on the borrowed form must match those for the key type.
    pub fn get<Q>(&self, key: &Q) -> Option<&V> {/* --snip-- */}

    /// Returns a mutable reference to the value corresponding to the key.
    ///
    /// The key may be any borrowed form of the map's key type,
    /// but `Hash` and `Eq` on the borrowed form must match those for the key type.
    pub fn get_mut<Q>(&mut self, key: &Q) -> Option<&mut V> {/* --snip-- */}

    /// Returns `true` if there is an entry corresponding to the key in the map.
    pub fn contains_key<Q>(&self, key: &Q) -> bool {/* --snip-- */}

    /// Converts the OccupiedEntry into a mutable reference to the value in the entry
    /// with a lifetime bound to the map itself.
    pub fn into_mut(self) -> &'a mut V {/* --snip-- */}

    /// Gets the given key's corresponding entry in the map for in-place manipulation.
    pub fn entry(&mut self, key: K) -> Entry<K, V> {/* --snip-- */}Copy to clipboardErrorCopied
```

### Инициализация HashMap <a id="initializing-a-hashmap"></a>

Как уже упоминалось, отсутствие инициализации хранилища перед его использованием является распространенной ошибкой, которая может нарушить ваш смарт-контракт. Для каждого ключа в значении хранилища значение необходимо установить, прежде чем вы сможете его использовать. Для этого мы создадим приватную функцию, которая обрабатывает, когда значение установлено, а когда нет, и убедимся, что мы никогда не работаем с неинициализированным хранилищем.

Итак, учитывая `my_number_map`, представьте, что мы хотели, чтобы значение по умолчанию для любого заданного ключа было `0`. Мы можем построить такую функцию:

```rust
#![cfg_attr(not(feature = "std"), no_std)]

use ink_lang as ink;

#[ink::contract]
mod mycontract {

    #[ink(storage)]
    pub struct MyContract {
        // Store a mapping from AccountIds to a u32
        my_number_map: ink_storage::collections::HashMap<AccountId, u32>,
    }

    impl MyContract {
        /// Private function.
        /// Returns the number for an AccountId or 0 if it is not set.
        fn my_number_or_zero(&self, of: &AccountId) -> u32 {
            let balance = self.my_number_map.get(of).unwrap_or(&0);
            *balance
        }
    }
}
```

Здесь мы видим, что после того, как мы `получили` значение из `my_number_map`, мы вызываем `unwrap_or`, который либо `развернет` значение, хранящееся в хранилище, либо, если значения нет, вернет какое-то известное значение. Затем при создании функций, которые взаимодействуют с этим HashMap, вам нужно всегда помнить о вызове этой функции, а не о получении значения непосредственно из хранилища.

Вот пример:

```rust
#![cfg_attr(not(feature = "std"), no_std)]

use ink_lang as ink;

#[ink::contract]
mod mycontract {

    #[ink(storage)]
    pub struct MyContract {
        // Store a mapping from AccountIds to a u32
        my_number_map: ink_storage::collections::HashMap<AccountId, u32>,
    }

    impl MyContract {
        // Get the value for a given AccountId
        #[ink(message)]
        pub fn get(&self, of: AccountId) -> u32 {
            self.my_number_or_zero(&of)
        }

        // Get the value for the calling AccountId
        #[ink(message)]
        pub fn get_my_number(&self) -> u32 {
            let caller = self.env().caller();
            self.my_number_or_zero(&caller)
        }

        // Returns the number for an AccountId or 0 if it is not set.
        fn my_number_or_zero(&self, of: &AccountId) -> u32 {
            let value = self.my_number_map.get(of).unwrap_or(&0);
            *value
        }
    }
}
```

### Вызывающий контракт <a id="contract-caller"></a>

Как вы могли заметить в приведенном выше примере, мы используем специальную функцию `self.env().caller()`. Эта функция доступна во всей логике контракта и всегда будет возвращать вам вызывающую сторону контракта.

> **ПРИМЕЧАНИЕ.** Контрактный вызывающий объект отличается от исходного вызывающего. Если пользователь инициирует контракт, который затем вызывает последующий контракт, `self.env().caller()` во втором контракте будет адресом первого контракта, а не исходного пользователя.

`self.env().caller()` можно использовать разными способами. В приведенных выше примерах мы в основном создаем уровень «управления доступом», который позволяет пользователю изменять свое собственное значение, но никому другому. Вы также можете делать такие вещи, как определение владельца контракта во время развертывания контракта:

```rust
#![cfg_attr(not(feature = "std"), no_std)]

use ink_lang as ink;

#[ink::contract]
mod mycontract {

    #[ink(storage)]
    pub struct MyContract {
        // Store a contract owner
        owner: AccountId,
    }

    impl MyContract {
        #[ink(constructor)]
        pub fn new(init_value: i32) -> Self {
            Self {
                owner: Self::env().caller();
            }
        }
        /* --snip-- */
    }
}
```

Затем вы можете написать разрешенные функции, которые проверяют, что текущий вызывающий объект является владельцем контракта.

### Твоя очередь! <a id="your-turn"></a>

Следуйте `ДЕЙСТВИЯМ` в коде шаблона, чтобы ввести карту хранилища в ваш контракт.

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
        // ACTION: Add a `HashMap` from `AccountId` to `u64` named `my_value`
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

        #[ink(message)]
        pub fn get_mine(&self) -> u64 {
            // ACTION: Get `my_value` using `my_value_or_zero` on `&self.env().caller()`
            // ACTION: Return `my_value`
        }

        fn my_value_or_zero(&self, of: &AccountId) -> u64 {
            // ACTION: `get` and return the value of `of` and `unwrap_or` return 0
        }
    }

    #[cfg(test)]
    mod tests {
        use super::*;

        // Alias `ink_lang` so we can use `ink::test`.
        use ink_lang as ink;

        #[test]
        fn default_works() {
            let contract = Incrementer::default();
            assert_eq!(contract.get(), 0);
        }

        #[test]
        fn it_works() {
            let mut contract = Incrementer::new(42);
            assert_eq!(contract.get(), 42);
            contract.inc(5);
            assert_eq!(contract.get(), 47);
            contract.inc(-50);
            assert_eq!(contract.get(), -3);
        }

        // Use `ink::test` to initialize accounts.
        #[ink::test]
        fn my_value_works() {
            let contract = Incrementer::new(11);
            assert_eq!(contract.get(), 11);
            assert_eq!(contract.get_mine(), 0);
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

        fn my_value_or_zero(&self, of: &AccountId) -> u64 {
            *self.my_value.get(of).unwrap_or(&0)
        }
    }

    #[cfg(test)]
    mod tests {
        use super::*;

        use ink_lang as ink;

        #[test]
        fn default_works() {
            let contract = Incrementer::default();
            assert_eq!(contract.get(), 0);
        }

        #[test]
        fn it_works() {
            let mut contract = Incrementer::new(42);
            assert_eq!(contract.get(), 42);
            contract.inc(5);
            assert_eq!(contract.get(), 47);
            contract.inc(-50);
            assert_eq!(contract.get(), -3);
        }

        #[ink::test]
        fn my_value_works() {
            let contract = Incrementer::new(11);
            assert_eq!(contract.get(), 11);
            assert_eq!(contract.get_mine(), 0);
        }
    }
}
```

{% endtab %}
{% endtabs %}
