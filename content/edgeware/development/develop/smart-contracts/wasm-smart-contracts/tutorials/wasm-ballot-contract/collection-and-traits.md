+++
title = "Коллекция и трэйты"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

В этой части мы будем использовать структуры, созданные ранее, и напишем общедоступные функции для добавления и извлечения данных из нашего контракта.

## Коллекции <a id="collections"></a>

Для этого контракта мы собираемся хранить наших избирателей в HashMap с `AccountId` в качестве ключа и экземпляром `Voter` в качестве значения. HashMap можно импортировать из ящика ink_storage следующим образом:

```rust
use ink_storage::collections::HashMap;
```

Предложения будут храниться в коллекции «Vec», которую можно импортировать из ящика «ink_prelude». ink_prelude — это набор структур данных, которые работают с памятью контракта во время выполнения контракта. Вектор можно импортировать:

```rust
    use ink_prelude::vec::Vec;
```

Векторы могут быть созданы так же, как HashMap. Новые объекты можно добавлять или ссылаться на них из вектора, используя:

```rust
    let proposals: Vec<proposals> = Vec::new();
    // adding a new proposal
    proposals.push(
        Proposal{
            name:String::from("Proposal # 1"),
            vote_count: 0,
        });
    // returns the proposal at index 0 if exists else returns None
    let proposal = self.proposals.get(0).unwrap();
```

Помните, что `vector.get` возвращает `Option`, а не фактический объект!

## Черты <a id="traits"></a>

Черта сообщает компилятору Rust о функциональности определенного типа и может использоваться совместно с другими типами. Подробнее о них можно прочитать [здесь](https://doc.rust-lang.org/book/ch10-02-traits.html). Прежде чем использовать настраиваемые структуры внутри хранилища «Бюллетень», необходимо реализовать определенные черты для структур «Избиратель» и «Предложение». Эти черты включают в себя:

- `Отладка`: разрешает форматирование отладки в строках формата.
- `Clone`: эта черта позволяет вам создать глубокую копию объекта.
- `Копировать`: Черты копирования позволяют копировать значение поля.
- `PackedLayout`: типы, которые можно сохранять и загружать из одной ячейки хранилища контрактов.
- `SpreadLayout`: типы, которые можно сохранять и загружать из хранилища контрактов.

Вы можете узнать больше об этих трейтах по [здесь](https://doc.rust-lang.org/book/appendix-03-derivable-traits.html) и [здесь](https://paritytech.github.io /ink/ink_storage/traits/index.html). Эти черты реализуются с помощью атрибута `derive`:

```rust
    #[derive(Clone, Copy, Debug, scale::Encode, scale::Decode, SpreadLayout, PackedLayout, scale_info::TypeInfo)]
    struct XYZ {
        ...
        ...
    }
```

## Твоя очередь! <a id="your-turn"></a>

Тебе надо:

- Создайте предложение Vec и избирателей HashMap в структуре голосования.
- Обновите конструктор, чтобы он инициализировал Vec предложений и HashMap избирателей. Также обновите HashMap избирателей, чтобы включить председателя в качестве избирателя.
- Создайте геттеры для обоих элементов хранилища.
- Напишите функцию `add_voter` для создания избирателя по заданному `AccountId` и вставьте его в HashMap избирателей.
- Напишите функцию `add_proposal`, которая создает объект `Proposal` и вставляет его в вектор предложений.

Не забудьте запустить `cargo +nightly test`, чтобы проверить свою работу.

{% tabs %}
{% tab title="🔨Starting Point" %}

```rust
#![cfg_attr(not(feature = "std"), no_std)]

use ink_lang as ink;

#[ink::contract]    
mod ballot {
    use ink_storage::collections::HashMap;
    use ink_prelude::vec::Vec;
    use ink_storage::traits::{PackedLayout, SpreadLayout};

    // Structure to store Proposal information
    #[derive(Clone, Debug, scale::Encode, scale::Decode, SpreadLayout, PackedLayout,scale_info::TypeInfo)]
    struct Proposal {
        name: String,
        vote_count: u32, 
    }

    // Structure to store Proposal information
    #[derive(Clone, Debug, scale::Encode, scale::Decode, SpreadLayout, PackedLayout,scale_info::TypeInfo)]
    pub struct Voter {
        weight: u32,
        voted: bool,
        delegate: Option<AccountId>, 
        vote: Option<i32>, 
    }

    /// Defines the storage of your contract.
    /// Add new fields the struct in order
    /// to add new static storage fields to your contract.
    #[ink(storage)]
    pub struct Ballot {
        chair_person: AccountId,
        //  ACTION: create a voters hash map with account id as key and Voter as value
        voters: 

        //  ACTION: create a proposals vector
        proposals: 
    }

    impl Ballot {
        #[ink(constructor)]
        pub fn new() -> Self {
            // get chair person address
            let chair_person =  Self::env().caller();

            //  ACTION: create empty proposals and voters variables
            //          * let proposals = 
            //          * let mut voters =

            // ACTION: add chair persons voter object in voters hash map
            // HINT: Use hashmap.insert(key,value)

            Self {
                chair_person,
                voters,
                proposals,
            }
        }

        // return chair person id
        #[ink(message)]
        pub fn get_chairperson(&self) -> AccountId {
            self.chair_person
        }

        // return the provided voter object
        pub fn get_voter(&self, voter_id: AccountId) -> Option<&Voter>{
        }

        // return the count of voters
        pub fn get_voter_count(&self) -> usize{
        }


        /// the function adds the provided voter id into possible
        /// list of voters. By default the voter has no voting right,
        /// the contract owner must approve the voter before he can cast a vote
        #[ink(message)]
        pub fn add_voter(&mut self, voter_id: AccountId) -> bool{
            // ACTION: check if voter already exits, if yes return false       
            //      * if not exists, create an entry in hash map 
            //      * with default weight set to 0 and voted to false
            //      * and return true

            // HINT: use hashmap.get() to get voter
            //        and use options.some() to check if voter exists
        }

        /// given an index returns the name of the proposal at that index
        pub fn get_proposal_name_at_index(&self, index:usize) -> &String {
        }

        /// returns the number of proposals in ballet
        pub fn get_proposal_count(&self) -> usize {
        }


        /// adds the given proposal name in ballet
        pub fn add_proposal(&mut self, proposal_name: String){
        }




    }

    /// Unit tests in Rust are normally defined within such a `#[cfg(test)]`
    /// module and test functions are marked with a `#[test]` attribute.
    /// The below code is technically just normal Rust code.
    #[cfg(test)]
    mod tests {
        /// Imports all the definitions from the outer scope so we can use them here.
        use super::*;

        // Alias `ink_lang` so we can use `ink::test`.
        use ink_lang as ink;

        #[ink::test]
        fn new_works() {
            let mut proposal_names: Vec<String> = Vec::new();
            proposal_names.push(String::from("Proposal # 1"));  
            let ballot = Ballot::new();
            assert_eq!(ballot.get_voter_count(),1);
        }

        #[ink::test]
        fn adding_proposals_works() {
            let mut ballot = Ballot::new();
            ballot.add_proposal(String::from("Proposal #1"));
            assert_eq!(ballot.get_proposal_count(),1);
        }

        #[ink::test]
        fn adding_voters_work() {
            let mut ballot = Ballot::new();
            let account_id = AccountId::from([0x0; 32]);
            assert_eq!(ballot.add_voter(account_id),true);
            assert_eq!(ballot.add_voter(account_id),false);
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
mod ballot {
    // use Hash
    use ink_storage::collections::HashMap;
    use ink_prelude::vec::Vec;
    use ink_storage::traits::{PackedLayout, SpreadLayout};

    // Structure to store Proposal information
    #[derive(Clone, Debug, scale::Encode, scale::Decode, SpreadLayout, PackedLayout,scale_info::TypeInfo)]
    struct Proposal {
        name: String,
        vote_count: u32, 
    }

    // Structure to store Proposal information
    #[derive(Clone, Debug, scale::Encode, scale::Decode, SpreadLayout, PackedLayout,scale_info::TypeInfo)]
    pub struct Voter {
        weight: u32,
        voted: bool,
        delegate: Option<AccountId>, 
        vote: Option<i32>, 
    }

    /// Defines the storage of your contract.
    /// Add new fields to the below struct in order
    /// to add new static storage fields to your contract.
    #[ink(storage)]
    pub struct Ballot {
        chair_person: AccountId,
        voters: HashMap<AccountId, Voter>,
        proposals: Vec<Proposal>    
    }

    impl Ballot {
        #[ink(constructor)]
        pub fn new() -> Self {
            // get chair person address
            let chair_person =  Self::env().caller();

            // create empty propsal and voters
            let proposals: Vec<Proposal> = Vec::new();
            let mut voters = HashMap::new();

            // initialize chair person's vote
            voters.insert(chair_person, Voter{
                weight:1,
                voted:false,
                delegate: None,
                vote: None,
            });

            Self {
                chair_person,
                voters,
                proposals,
            }
        }


        #[ink(message)]
        pub fn get_chairperson(&self) -> AccountId {
            self.chair_person
        }



        pub fn get_voter(&self, voter_id: AccountId) -> Option<&Voter>{
            self.voters.get(&voter_id)
        }

        pub fn get_voter_count(&self) -> usize{
            self.voters.len() as usize
        }

                /// the function adds the provided voter id into possible
        /// list of voters. By default the voter has no voting right,
        /// the contract owner must approve the voter before he can cast a vote
        #[ink(message)]
        pub fn add_voter(&mut self, voter_id: AccountId) -> bool{

            let voter_opt = self.voters.get(&voter_id);
            // the voter does not exists
            if voter_opt.is_some() {
                return false
            }

            self.voters.insert(voter_id, Voter{
                weight:0,
                voted:false,
                delegate: None,
                vote: None,
            });
            return true
        }



        /// given an index returns the name of the proposal at that index
        pub fn get_proposal_name_at_index(&self, index:usize) -> &String {
            let proposal = self.proposals.get(index).unwrap();
            return &proposal.name
        }

        /// returns the number of proposals in ballet
        pub fn get_proposal_count(&self) -> usize {
            return self.proposals.len()
        }

        /// adds the given proposal name in ballet
        /// to do: check unqiueness of proposal,
        pub fn add_proposal(&mut self, proposal_name: String){
            self.proposals.push(
                Proposal{
                    name:String::from(proposal_name),
                    vote_count: 0,
            });
        }

    }

    /// Unit tests in Rust are normally defined within such a `#[cfg(test)]`
    /// module and test functions are marked with a `#[test]` attribute.
    /// The below code is technically just normal Rust code.
    #[cfg(test)]
    mod tests {
        /// Imports all the definitions from the outer scope so we can use them here.
        use super::*;

        // Alias `ink_lang` so we can use `ink::test`.
        use ink_lang as ink;

        #[ink::test]
        fn new_works() {
            let mut proposal_names: Vec<String> = Vec::new();
            proposal_names.push(String::from("Proposal # 1"));  
            let ballot = Ballot::new();
            assert_eq!(ballot.get_voter_count(),1);
        }
    }

}
```

{% endtab %}
{% endtabs %}
