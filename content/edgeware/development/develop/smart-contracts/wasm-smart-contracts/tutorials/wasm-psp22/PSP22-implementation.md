+++
title = "Внедрение PSP22 в контракт"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Для получения последней версии см. [Документацию OpenBrush](https://docs.openbrush.io/)

В этом примере показано, как можно повторно использовать реализацию
[psp22](https://github.com/Supercolony-net/openbrush-contracts/tree/main/contracts/token/psp22) токен (таким же образом вы можете повторно использовать
[psp721](https://github.com/Supercolony-net/openbrush-contracts/tree/main/contracts/token/psp721) и [psp1155](https://github.com/Supercolony-net/openbrush-contracts /tree/main/contracts/token/psp1155)). Кроме того, в этом примере показано, как можно настроить
логика, например, для отказа от передачи токенов на `hated_account`.

## Шаг 1. Включите зависимости

Включите зависимости от `psp22` и `brush` в файл груза.

```toml
[dependencies]
ink_primitives = { tag = "v3.0.0-rc4", git = "https://github.com/Supercolony-net/ink", default-features = false }
ink_metadata = { tag = "v3.0.0-rc4", git = "https://github.com/Supercolony-net/ink", default-features = false, features = ["derive"], optional = true }
ink_env = { tag = "v3.0.0-rc4", git = "https://github.com/Supercolony-net/ink", default-features = false }
ink_storage = { tag = "v3.0.0-rc4", git = "https://github.com/Supercolony-net/ink", default-features = false }
ink_lang = { tag = "v3.0.0-rc4", git = "https://github.com/Supercolony-net/ink", default-features = false }
ink_prelude = { tag = "v3.0.0-rc4", git = "https://github.com/Supercolony-net/ink", default-features = false }

scale = { package = "parity-scale-codec", version = "2.1", default-features = false, features = ["derive"] }
scale-info = { version = "0.6.0", default-features = false, features = ["derive"], optional = true }

# These dependencies
psp22 = { tag = "v1.0.0", git = "https://github.com/Supercolony-net/openbrush-contracts", default-features = false }
brush = { tag = "v1.0.0", git = "https://github.com/Supercolony-net/openbrush-contracts", default-features = false }

[features]
default = ["std"]
std = [
   "ink_primitives/std",
   "ink_metadata",
   "ink_metadata/std",
   "ink_env/std",
   "ink_storage/std",
   "ink_lang/std",
   "scale/std",
   "scale-info",
   "scale-info/std",

   # These dependencies   
   "psp22/std",
   "brush/std",
]
```

## Шаг 2. Добавьте импорт

Замените макрос ink::contract на кисть::contract.
Импортируйте **все** из `psp22::traits`.

```rust
#[brush::contract]
pub mod my_psp22 {
   use psp22::traits::*;
   use ink_storage::Lazy;
   use ink_prelude::{string::String, vec::Vec};
```

## Шаг 3. Определите хранилище

Объявить структуру хранилища и объявить поля, связанные с PSP22Storage и PSP22MetadataStorage.
черты. Затем вам нужно получить черты PSP22Storage и PSP22MetadataStorage и отметить соответствующие поля.
с атрибутами `#[PSP22StorageField]` и `#[PSP22MetadataStorageField]`. Получение этих признаков позволяет повторно использовать
реализация по умолчанию «PSP22» и «PSP22Metadata».

```rust
#[ink(storage)]
#[derive(Default, PSP22Storage, PSP22MetadataStorage)]
pub struct MyPSP22 {
    #[PSP22StorageField]
    psp22: PSP22Data,
    #[PSP22MetadataStorageField]
    metadata: PSP22MetadataData,
}
```

## Шаг 4: Наследовать логику

Наследовать реализации трейтов PSP22 и PSP22Metadata. Вы можете настроить (переопределить) методы в этом блоке impl.

```rust
impl PSP22 for MyPSP22 {}

impl PSP22Metadata for MyPSP22 {}
```

## Шаг 5: Определите конструктор

Определить конструктор. Ваша базовая версия контракта `PSP22` готова!

```rust
impl MyPSP22 {
   #[ink(constructor)]
   pub fn new(_total_supply: Balance, name: Option<String>, symbol: Option<String>, decimal: u8) -> Self {
      let mut instance = Self::default();
      Lazy::set(&mut instance.metadata.name, name);
      Lazy::set(&mut instance.metadata.symbol,symbol);
      Lazy::set(&mut instance.metadata.decimals,decimal);
      instance._mint(instance.env().caller(), _total_supply);
      instance
   }
}
```

## Шаг 6: Настройте свой контракт

Настройте его, добавив ненавистную логику аккаунта. Он будет содержать два общедоступных метода set_hated_account и get_hated_account. Также мы будем
переопределить метод _before_token_transfer в реализации PSP22. И добавим в структуру поле `hated_account:AccountId`.

```rust
#[ink(storage)]
#[derive(Default, PSP22Storage, PSP22MetadataStorage)]
pub struct MyPSP22 {
   #[PSP22StorageField]
   psp22: PSP22Data,
   #[PSP22MetadataStorageField]
   metadata: PSP22MetadataData,
   // fields for hater logic
   hated_account: AccountId,
}

impl PSP22 for MyPSP22 {
   // Let's override method to reject transactions to bad account
   fn _before_token_transfer(&mut self, _from: AccountId, _to: AccountId, _amount: Balance) {
      assert!(_to != self.hated_account, "{}", PSP22Error::Custom(String::from("I hate this account!")).as_ref());
   }
}

impl PSP22Metadata for MyPSP22 {}

impl MyPSP22 {
   #[ink(constructor)]
   pub fn new(_total_supply: Balance, name: Option<String>, symbol: Option<String>, decimal: u8) -> Self {
      let mut instance = Self::default();
      Lazy::set(&mut instance.metadata.name, name);
      Lazy::set(&mut instance.metadata.symbol,symbol);
      Lazy::set(&mut instance.metadata.decimals,decimal);
      instance._mint(instance.env().caller(), _total_supply);
      instance
   }

   #[ink(message)]
   pub fn set_hated_account(&mut self, hated: AccountId) {
      self.hated_account = hated;
   }

   #[ink(message)]
   pub fn get_hated_account(&self) -> AccountId {
      self.hated_account.clone()
   }
}
```

{% endtab %}
{% endtabs %}
