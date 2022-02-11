+++
title = "Передача токенов"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Итак, на данный момент у нас есть один пользователь, которому принадлежат все токены для контракта. Тем не менее, это не _действительно_ полезный токен, если вы не можете передать его другим людям...

Давайте сделаем это!

## Функция передачи <a id="the-transfer-function"></a>

Функция «перевод» делает именно то, что вы могли ожидать: она позволяет пользователю, вызывающему контракт, перевести некоторые средства, которыми он владеет, другому пользователю.

Вы заметите, что в коде нашего шаблона есть общедоступная функция «transfer» и внутренняя функция «transfer_from_to». Мы сделали это, потому что в будущем мы будем повторно использовать логику для передачи токенов, когда мы разрешаем сторонние разрешения и расходы от имени.

### transfer\_from\_to\(\) <a id="transfer_from_to"></a>

```rust
fn transfer_from_to(&mut self, from: AccountId, to: AccountId, value: Balance) -> bool {/* --snip-- */}
```

Функция `transfer_from_to` будет построена без каких-либо проверок _авторизации_. Поскольку это внутренняя функция, мы полностью контролируем ее вызов. Тем не менее, он будет иметь все логические проверки для управления балансами между счетами.

На самом деле нам просто нужно проверить одну вещь: убедиться, что на счете «от» достаточно средств для отправки на счет «на»:

```rust
if balance_from < value {
    return false
}
```

Помните, что функция «передача» и другие общедоступные функции возвращают логическое значение, указывающее на успех. Если на счете «от» недостаточно баланса для выполнения перевода, мы выйдем досрочно и вернем false, не внося никаких изменений в состояние контракта. Наш `transfer_from_to` просто перенаправит "успех" `bool` в функцию, которая его вызывает.

### transfer\(\) <a id="transfer"></a>

```rust
#[ink(message)] 
pub fn transfer(&mut self, to: AccountId, value: Balance) -> bool {/* --snip-- */}
```

Наконец, функция «transfer» просто вызовет «transfer_from_to» с параметром «from», автоматически установленным на «self.env().caller()». Это наша «проверка авторизации», поскольку вызывающий контракт всегда имеет право перемещать свои собственные средства.

## Передача математики <a id="transfer-math"></a>

На самом деле мало что можно сказать о простой математике, выполняемой при передаче токена.

1. Сначала мы получаем текущий баланс как `от`, так и `до` счета, обязательно используя наш геттер `balance_of_or_zero()`.
2. Затем мы выполняем логическую проверку, упомянутую выше, чтобы убедиться, что на балансе «от» достаточно средств для отправки «значения».
3. Наконец, мы вычитаем это «значение» из баланса «от», добавляем его к балансу «до» и вставляем эти новые значения обратно.

## Твоя очередь! <a id="your-turn"></a>

Следуйте «ДЕЙСТВИЯМ» в коде шаблона, чтобы создать передаточную функцию.

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

        #[ink(message)]
        pub fn transfer(&mut self, to: AccountId, value: Balance) -> bool {
            // ACTION: Call the `transfer_from_to` with `from` as `self.env().caller()`
        }

        fn transfer_from_to(&mut self, from: AccountId, to: AccountId, value: Balance) -> bool {
            // ACTION: Get the balance for `from` and `to`
            //   HINT: Use the `balance_of_or_zero` function to do this
            // ACTION: If `from_balance` is less than `value`, return `false`
            // ACTION: Insert new values for `from` and `to`
            //         * from_balance - value
            //         * to_balance + value
            // ACTION: Return `true`
        }

        fn balance_of_or_zero(&self, owner: &AccountId) -> Balance {
            *self.balances.get(owner).unwrap_or(&0)
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

            true
        }

        fn balance_of_or_zero(&self, owner: &AccountId) -> Balance {
            *self.balances.get(owner).unwrap_or(&0)
        }
```

{% endtab %}
{% endtabs %}
