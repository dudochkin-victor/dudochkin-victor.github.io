+++
title = "Поддержка подтверждений и передачи от (Transfer From)"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Мы почти на месте! Наш токен-контракт теперь может переводить средства от пользователя к пользователю и сообщать внешнему миру, что происходит, когда это происходит. Все, что осталось сделать, это ввести функции `approve` и `transfer_from`.

## Передача третьей стороне <a id="third-parity-transfers"></a>

Этот раздел посвящен добавлению возможности для других учетных записей безопасно тратить некоторое количество ваших токенов.

Немедленный вопрос должен звучать так: «Какого черта мне это нужно?»

Одним из таких сценариев является поддержка децентрализованных бирж. По сути, другие смарт-контракты могут позволить вам обмениваться токенами с другими пользователями, обычно токенами одного типа на другой. Однако эти «заявки» не всегда выполняются сразу. Может быть, вы хотите получить действительно хорошую сделку по обмену токенами и продержитесь до тех пор, пока эта сделка не будет удовлетворена.

Что ж, вместо того, чтобы передавать свои токены напрямую в контракт \(депонирование\), вы можете просто «одобрить» их, чтобы они потратили часть ваших токенов от вашего имени! Это означает, что в то время, пока вы ожидаете исполнения сделки, вы все еще можете контролировать и тратить свои средства, если это необходимо. Более того, вы можете одобрить несколько разных контрактов или пользователей для доступа к вашим средствам, поэтому, если один контракт предлагает лучшую сделку, вам не нужно извлекать средства из другого и перемещать их, что иногда является дорогостоящим и трудоемким процессом.

Итак, надеюсь, вы понимаете, почему подобная функция была бы полезна, но как мы можем сделать это безопасно?

Мы используем двухэтапный процесс: **Утвердить** и **Перенести из**.

### Одобрить <a id="approve"></a>

Утверждение другой учетной записи для расходования ваших средств — это первый шаг в процессе передачи третьей стороне. Владелец токена может указать другую учетную запись и любое произвольное количество токенов, которое он может потратить от имени владельца. Владельцу не нужно, чтобы все его средства были одобрены для использования другими; в ситуации, когда средств недостаточно, утвержденный счет может потратить до утвержденной суммы с баланса владельца.

Когда учетная запись несколько раз вызывает «утвердить», утвержденное значение просто перезаписывает любое существующее значение, которое было утверждено в прошлом. По умолчанию утвержденное значение между любыми двумя учетными записями равно «0», и пользователь всегда может вызвать подтверждение для «0», чтобы отозвать доступ к своим средствам с другой учетной записи.

Чтобы хранить утверждения в нашем контракте, нам нужно использовать немного причудливую `HashMap`.

Поскольку каждая учетная запись может иметь разную сумму, одобренную для использования любой другой учетной записью, нам нужно использовать кортеж в качестве нашего ключа, который просто указывает на значение баланса. Вот пример того, как это будет выглядеть:

```rust
pub struct Erc20 {
    /// Balances that are spendable by non-owners: (owner, spender) -> allowed
    allowances: ink_storage::collections::HashMap<(AccountId, AccountId), Balance>,
}
```

Здесь мы определили кортеж для представления `(владелец, транжира)`, чтобы мы могли посмотреть, сколько «расходующий» может потратить из баланса «владельца», используя `AccountId` в этом кортеже. Помните, что нам нужно будет снова создать функцию `allowance_of_or_zero`, которая поможет нам получить разрешение учетной записи, когда она не инициализирована, и функцию-получатель, называемую `allowance`, для поиска текущего значения для любой пары учетных записей.

```rust
/// Approve the passed AccountId to spend the specified amount of tokens
/// on the behalf of the message's sender.
#[ink(message)] 
pub fn approve(&mut self, spender: AccountId, value: Balance) -> bool {/* --snip-- */}
```

Когда вы вызываете функцию «одобрить», вы просто вставляете указанное «значение» в хранилище. «Владелец» всегда является «self.env().caller()», гарантируя, что вызов функции всегда авторизован.

### Перевод из <a id="transfer-from"></a>

Наконец, после того как мы настроили одобрение для одной учетной записи расходов от имени другой, нам нужно создать специальную функцию «transfer_from», которая позволяет утвержденному пользователю переводить эти средства.

Как упоминалось ранее, мы воспользуемся частной функцией `transfer_from_to`, чтобы выполнить большую часть нашей логики передачи. Все, что нам нужно, это снова представить логику _authorization_.

Так что же значит иметь право вызывать эту функцию?

1. У `self.env().caller()` должны быть определенные возможности для расходования средств со счета `from`.
2. Надбавка не должна быть меньше стоимости, которую пытаются перевести.

В коде это легко представить так:

```rust
let allowance = self.allowance_of_or_zero(&from, &self.env().caller());
if allowance < value {
    return false
}
/* --snip-- */
true
```

Опять же, мы выходим раньше и возвращаем false, если наша авторизация не проходит.

Однако, если все выглядит хорошо, мы просто «вставляем» обновленный допуск в HashMap «разрешения» \(`let new_allowance = Allowance - value`\) и вызываем «transfer_from_to» между указанными учетными записями «from» и «to». .

## Будь осторожен! <a id="be-careful"></a>

Если вы слишком быстро проясните логику этой функции, вы можете внести ошибку в свой смарт-контракт. Помните, что при вызове `transfer_from`, `self.env().caller()` и учетная запись `from` используются для поиска текущего пособия, но функция `transfer_from` вызывается между `from` и `to `учетная запись указана.

При вызове `transfer_from` в игре задействованы три переменные учетной записи, и вы должны убедиться, что используете их правильно! Надеюсь, наш тест уловит любую вашу ошибку.

## Твоя очередь! <a id="your-turn"></a>

Вы почти там! Это последняя часть контракта токена ERC20.

Следуйте «ДЕЙСТВИЯМ» в шаблоне контракта, чтобы завершить внедрение ERC20.

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
        /// Approval spender on behalf of the message's sender.
        allowances: ink_storage::collections::HashMap<(AccountId, AccountId), Balance>,
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

    #[ink(event)]
    pub struct Approval {
        #[ink(topic)]
        owner: AccountId,
        #[ink(topic)]
        spender: AccountId,
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
                balances,
                allowances: ink_storage::collections::HashMap::new(),
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
        pub fn approve(&mut self, spender: AccountId, value: Balance) -> bool {
            // Record the new allowance.
            let owner = self.env().caller();
            self.allowances.insert((owner, spender), value);

            // Notify offchain users of the approval and report success.
            self.env().emit_event(Approval {
                owner,
                spender,
                value,
            });
            true
        }

        #[ink(message)]
        pub fn allowance(&self, owner: AccountId, spender: AccountId) -> Balance {
            self.allowance_of_or_zero(&owner, &spender)
        }

        #[ink(message)]
        pub fn transfer_from(&mut self, from: AccountId, to: AccountId, value: Balance) -> bool {
            // Ensure that a sufficient allowance exists.
            let caller = self.env().caller();
            let allowance = self.allowance_of_or_zero(&from, &caller);
            if allowance < value {
                return false;
            }

            // Decrease the value of the allowance and transfer the tokens.
            self.allowances.insert((from, caller), allowance - value);
            self.transfer_from_to(from, to, value)
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
```

{% endtab %}

{% tab title="✅Potential Solution" %}

```rust
#![cfg_attr(not(feature = "std"), no_std)]

use ink_lang as ink;

#[ink::contract(version = "0.1.0")]
mod erc20 {
    use ink_core::storage;

    #[ink(storage)]
    struct Erc20 {
        /// The total supply.
        total_supply: storage::Value<Balance>,
        /// The balance of each user.
        balances: storage::HashMap<AccountId, Balance>,
        /// Approval spender on behalf of the message's sender.
        allowances: storage::HashMap<(AccountId, AccountId), Balance>,
    }

    #[ink(event)]
    struct Transfer {
        #[ink(topic)]
        from: Option<AccountId>,
        #[ink(topic)]
        to: Option<AccountId>,
        #[ink(topic)]
        value: Balance,
    }

    #[ink(event)]
    struct Approval {
        #[ink(topic)]
        owner: AccountId,
        #[ink(topic)]
        spender: AccountId,
        #[ink(topic)]
        value: Balance,
    }

    impl Erc20 {
        #[ink(constructor)]
        fn new(&mut self, initial_supply: Balance) {
            let caller = self.env().caller();
            self.total_supply.set(initial_supply);
            self.balances.insert(caller, initial_supply);
            self.env().emit_event(Transfer {
                from: None,
                to: Some(caller),
                value: initial_supply,
            });
        }

        #[ink(message)]
        fn total_supply(&self) -> Balance {
            *self.total_supply
        }

        #[ink(message)]
        fn balance_of(&self, owner: AccountId) -> Balance {
            self.balance_of_or_zero(&owner)
        }

        #[ink(message)]
        fn approve(&mut self, spender: AccountId, value: Balance) -> bool {
            let owner = self.env().caller();
            self.allowances.insert((owner, spender), value);
            self.env().emit_event(Approval {
                owner,
                spender,
                value,
            });
            true
        }

        #[ink(message)]
        fn allowance(&self, owner: AccountId, spender: AccountId) -> Balance {
            self.allowance_of_or_zero(&owner, &spender)
        }

        #[ink(message)]
        fn transfer_from(&mut self, from: AccountId, to: AccountId, value: Balance) -> bool {
            let caller = self.env().caller();
            let allowance = self.allowance_of_or_zero(&from, &caller);
            if allowance < value {
                return false
            }
            self.allowances.insert((from, caller), allowance - value);
            self.transfer_from_to(from, to, value)
        }

        #[ink(message)]
        fn transfer(&mut self, to: AccountId, value: Balance) -> bool {
            let from = self.env().caller();
            self.transfer_from_to(from, to, value)
        }

        fn transfer_from_to(&mut self, from: AccountId, to: AccountId, value: Balance) -> bool {
            let from_balance = self.balance_of_or_zero(&from);
            if from_balance < value {
                return false
            }
            let to_balance = self.balance_of_or_zero(&to);
            self.balances.insert(from, from_balance - value);
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
```

{% endtab %}
{% endtabs %}
