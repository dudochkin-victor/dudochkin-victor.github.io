+++
title = "Создание шаблона ERC20"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Мы собираемся начать еще одну краску! проект по созданию контракта токена ERC20.

Вернувшись в свой рабочий каталог, запустите:

```
cargo contract new erc20
```

Опять же, мы заменим содержимое файла `lib.rs` шаблоном, представленным на этой странице.

Вы заметите, что шаблон токена ERC20 ОЧЕНЬ похож на контракт Инкрементера. \(Совпадение? ¯\_\(ツ\)\_/¯\)

Хранилище \(пока что\) состоит из:

- Хранилище «Стоимость»: представляет общее количество токенов в нашем контракте.
- Хранилище `HashMap`: представляет индивидуальный баланс каждой учетной записи.

## Развертывание ERC20 <a id="erc20-deployment"></a>

Самый простой контракт токена ERC20 — это токен с фиксированной поставкой. Во время развертывания контракта все токены будут автоматически переданы создателю контракта. Затем этот пользователь должен распространять эти токены среди других пользователей по своему усмотрению.

Конечно, это не единственный способ чеканки и распространения токенов, но самый простой, чем мы и займемся.

Так что не забудьте `установить` общий баланс и `вставить` баланс `Self::env().caller()`

## Твоя очередь! <a id="your-turn"></a>

Эта глава должна быть не чем иным, как кратким повторением того содержания, которое вы уже изучили.

Тебе надо:

- Настройте функцию конструктора, которая инициализирует два элемента хранилища.
- Создать геттеры для обоих элементов хранилища
- Создайте функцию `balance_of_or_zero` для обработки значений чтения из HashMap.

Не забудьте запустить `cargo +nightly test`, чтобы проверить свою работу.

{% tabs %}
{% tab title="🔨Starting Point" %}

```rust
#![cfg_attr(not(feature = "std"), no_std)]

use ink_lang as ink;

#[ink::contract]
mod erc20 {
    #[cfg(not(feature = "ink-as-dependency"))]
    #[ink(storage)]
    pub struct Erc20 {
        /// The total supply.
        total_supply: Balance,
        /// The balance of each user.
        balances: ink_storage::collections::HashMap<AccountId, Balance>,
    }

    impl Erc20 {
        #[ink(constructor)]
        pub fn new(initial_supply: Balance) -> Self {
            // ACTION: `set` the total supply to `init_value`
            // ACTION: `insert` the `init_value` as the `caller` balance
        }

        #[ink(message)]
        pub fn total_supply(&self) -> Balance {
            // ACTION: Return the total supply
        }

        #[ink(message)]
        pub fn balance_of(&self, owner: AccountId) -> Balance {
            // ACTION: Return the balance of `owner`
            //   HINT: Use `balance_of_or_zero` to get the `owner` balance
        }

        fn balance_of_or_zero(&self, owner: &AccountId) -> Balance {
            // ACTION: `get` the balance of `owner`, then `unwrap_or` fallback to 0
            // ACTION: Return the balance
        }
    }

    #[cfg(test)]
    mod tests {
        use super::*;

        use ink_lang as ink;

        #[ink::test]
        fn new_works() {
            let contract = Erc20::new(777);
            assert_eq!(contract.total_supply(), 777);
        }

        #[ink::test]
        fn balance_works() {
            let contract = Erc20::new(100);
            assert_eq!(contract.total_supply(), 100);
            assert_eq!(contract.balance_of(AccountId::from([0x1; 32])), 100);
            assert_eq!(contract.balance_of(AccountId::from([0x0; 32])), 0);
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
mod erc20 {
    #[cfg(not(feature = "ink-as-dependency"))]
    #[ink(storage)]
    pub struct Erc20 {
        /// The total supply.
        total_supply: Balance,
        /// The balance of each user.
        balances: ink_storage::collections::HashMap<AccountId, Balance>,
    }

    impl Erc20 {
        #[ink(constructor)]
        pub fn new(initial_supply: Balance) -> Self {
            let mut balances = ink_storage::collections::HashMap::new();
            balances.insert(Self::env().caller(), initial_supply);
            Self {
                total_supply: initial_supply,
                balances
            }
        }

        #[ink(message)]
        pub fn total_supply(&self) -> Balance {
            self.total_supply
        }

        #[ink(message)]
        pub fn balance_of(&self, owner: AccountId) -> Balance {
            self.balance_of_or_zero(&owner)
        }

        fn balance_of_or_zero(&self, owner: &AccountId) -> Balance {
            *self.balances.get(owner).unwrap_or(&0)
        }
    }

    #[cfg(test)]
    mod tests {
        use super::*;

        use ink_lang as ink;

        #[ink::test]
        fn new_works() {
            let contract = Erc20::new(777);
            assert_eq!(contract.total_supply(), 777);
        }

        #[ink::test]
        fn balance_works() {
            let contract = Erc20::new(100);
            assert_eq!(contract.total_supply(), 100);
            assert_eq!(contract.balance_of(AccountId::from([0x1; 32])), 100);
            assert_eq!(contract.balance_of(AccountId::from([0x0; 32])), 0);
        }
```

{% endtab %}
{% endtabs %}
