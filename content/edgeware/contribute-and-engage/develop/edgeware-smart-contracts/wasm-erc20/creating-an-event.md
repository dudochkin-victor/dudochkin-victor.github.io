+++
title = "Создание события"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Напомним, что вызовы контрактов не могут напрямую возвращать значение во внешний мир при отправке транзакции. Однако часто нам нужно указать внешнему миру, что что-то произошло (например, произошла транзакция или было достигнуто определенное состояние). Мы можем предупредить других о том, что это произошло, используя `событие`.

## Объявление событий <a id="declaring-events"></a>

Событие может передавать произвольное количество данных, определенных аналогично `структуре`. События должны быть объявлены с использованием атрибута `#[ink(event)]`.

Например,

```rust
#[ink(event)]
pub struct Transfer {
    #[ink(topic)]
    from: Option<AccountId>,
    #[ink(topic)]
    to: Option<AccountId>,
    value: Balance,
}
```

Это событие `Transfer` будет содержать три части данных: значение типа `Balance` и две переменные `AccountId`, заключенные в Option, указывающие на счета `from` и `to`. Для более быстрого доступа к данным о событиях они могут иметь _индексированные поля_. Мы можем сделать это, используя тег атрибута `#[ink(topic)]` в этом поле.

Одним из способов извлечения данных из переменной Option является использование функции `.unwrap_or()`. Вы можете вспомнить использование этого в функциях `my_value_or_zero()` и `balance_of_or_zero()` в этом проекте и проекте Incrementer.

## Отправка событий <a id="emitting-events"></a>

Теперь, когда мы определили, какие данные будут содержаться в событии и как их объявить, пришло время фактически генерировать некоторые события. Мы делаем это, вызывая `self.env().emit_event` и включаем событие в качестве единственного аргумента в вызов метода.

Помните, что поскольку поля «от» и «до» являются опциональными, мы не можем просто установить для них определенные значения. Предположим, мы хотим установить значение 100 для начального развертывателя. Это значение не получено ни от какой другой учетной записи, поэтому значение «от» должно быть «None».

```rust
self.env()
    .emit_event(
        Transfer {
            from: None,
            to: Some(self.env().caller()),
            value: 100,
        });
```

>Примечание: **`значение`** не нуждается в **`Some()`**, так как значение не хранится в **`Option`**.

Мы хотим генерировать событие Transfer каждый раз, когда происходит передача. В шаблоне ERC-20, над которым мы работали, это происходит в двух местах: во-первых, во время «нового» вызова, а во-вторых, каждый раз, когда вызывается «transfer_from_to».

## Твоя очередь! <a id="your-turn"></a>

Следуйте ДЕЙСТВИЯМ в коде шаблона, чтобы генерировать событие `Transfer` каждый раз, когда происходит передача токена.

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

    #[ink(event)]
    pub struct Transfer {
    //  ACTION: Create a `Transfer` event with:
    //          * from: Option<AccountId>
    //          * to: Option<AccountId>
    //          * value: Balance
    }

    impl Erc20 {
        #[ink(constructor)]
        pub fn new(initial_supply: Balance) -> Self {
            let mut balances = ink_storage::collections::HashMap::new();
            balances.insert(Self::env().caller(), initial_supply);

            // ACTION: Call `self.env().emit_event` with the `Transfer` event
            //   HINT: Since we use `Option<AccountId>`, you need to wrap accounts in `Some()`

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

        #[ink(message)]
        pub fn transfer(&mut self, to: AccountId, value: Balance) -> bool {
            self.transfer_from_to(self.env().caller(), to, value)
        }

        fn transfer_from_to(&mut self, from: AccountId, to: AccountId, value: Balance) -> bool {
            let from_balance = self.balance_of_or_zero(&from);
            if from_balance < value {
                return false
            }

            // Update the sender's balance.
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

    #[ink(event)]
    pub struct Transfer {
        #[ink(topic)]
        from: Option<AccountId>,
        #[ink(topic)]
        to: Option<AccountId>,
        #[ink(topic)]
        value: Balance,
    }

    impl Erc20 {
        #[ink(constructor)]
        pub fn new(initial_supply: Balance) -> Self {
            let caller = Self::env().caller();
            let mut balances = ink_storage::collections::HashMap::new();
            balances.insert(caller, initial_supply);

            Self::env().emit_event(Transfer {
                from: None,
                to: Some(caller),
                value: initial_supply,
            });

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

        #[ink(message)]
        pub fn transfer(&mut self, to: AccountId, value: Balance) -> bool {
            self.transfer_from_to(self.env().caller(), to, value)
        }

        fn transfer_from_to(&mut self, from: AccountId, to: AccountId, value: Balance) -> bool {
            let from_balance = self.balance_of_or_zero(&from);
            if from_balance < value {
                return false
            }

            // Update the sender's balance.
            self.balances.insert(from, from_balance - value);

            // Update the receiver's balance.
            let to_balance = self.balance_of_or_zero(&to);
            self.balances.insert(to, to_balance + value);

            self.env().emit_event(Transfer {
                from: Some(from),
                to: Some(to),
                value,
            });

            true
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

        #[ink::test]
        fn transfer_works() {
            let mut contract = Erc20::new(100);
            assert_eq!(contract.balance_of(AccountId::from([0x1; 32])), 100);
            assert!(contract.transfer(AccountId::from([0x0; 32]), 10));
            assert_eq!(contract.balance_of(AccountId::from([0x0; 32])), 10);
            assert!(!contract.transfer(AccountId::from([0x0; 32]), 100));
        }
    }
}
```

{% endtab %}
{% endtabs %}
