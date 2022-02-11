+++
title = "Добавление функциональности"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

В этой части мы добавим функциональность в наш бюллетень, чтобы:

- Люди могут голосовать за предложения
- Люди могут делегировать свои голоса
- Председатель может назначать право голоса

### Контрактная функциональность <a id="contract-functionality"></a>

**Конструктор:**

Давайте сначала обновим конструктор нашего контракта. Как вы можете видеть в примере кода справа, конструктор теперь принимает параметр `Option<Vector<String>>`. Конструктор ожидает на входе вектор строк. Нам нужно обновить конструктор, чтобы предоставленные имена предложений использовались для создания объектов «Предложение» и добавлялись в наше хранилище для бюллетеней. Чтобы проверить, предоставлен ли вектор, содержащий строки:

```rust
    if proposal_name.is_some() {
        names = proposal_name.unwrap()
        // do somethiing with names 

    }
```

**Предоставить право голоса:**

В предыдущей части мы создали функцию, позволяющую пользователям добавлять себя в качестве избирателей. Мы инициализировали их структуру избирателя значением `voter.weight=0`, поскольку при создании избирателя он/она по умолчанию не имеет права голоса. Итак, давайте создадим функцию `give_voting_right`, которая позволит только председателю обновить вес до 1 для любого данного избирателя. Функция будет выглядеть примерно так:

```rust
    // assuming that the caller is the chair person
    pub fn give_voting_right(&mut self, voter_id: AccountId) {
        let voter_opt = self.voters.get_mut(&voter_id);
        if voter_opt.is_some() {
            let voter = voter.unwrap()
            // assuming that the voter has not already voted
            voter.weight = 1
        }
    }
```

**Голосование:**

Теперь давайте реализуем функцию, которая позволит пользователям отдавать свои голоса. Эта функция будет принимать индекс предложения в качестве входных данных. Если «вызывающий» является действительным избирателем и еще не отдал свой голос, обновите предложение в индексе «i» с весом избирателя, обновите «voter.voted» до «истина» и установите «voter.vote» на индекс `и`.

**Получить выигрышное предложение:**

Теперь, когда голоса поданы, мы реализуем функцию, которая получит имя выигравшего предложения. На повторных выборах победитель объявляется по истечении времени голосования. Мы оставим вам такую реализацию. На данный момент мы позволим любому пользователю вызывать эту функцию и получать имя выигравшего предложения. Давайте реализуем функцию для возврата индекса предложения с максимальным количеством голосов:

```rust
    fn winning_proposal(&self) -> Option<usize> {
        let mut winning_vote_vount:u32 = 0;
        let mut winning_index: Option<usize> = None;
        let mut index: usize = 0;

        for val in self.proposals.iter() {
            if val.vote_count > winning_vote_vount {
                winning_vote_vount = val.vote_count;
                winning_index = Some(index);
            }
            index += 1

        }
        return winning_index
    }
```

Обратите внимание, что эта функция возвращает `Option<usize>`, а не `usize`, так как возможно, что в бюллетене нет предложений. Эту функцию можно использовать для поиска названия выигравшего предложения.

**Делегация:**

В нашей структуре избирателя есть поле `делегата`, определенное как `Option<AccountId>`, позволяющее избирателям делегировать свой голос кому-то другому. Этого можно добиться с помощью следующей функции:

```rust
    #[ink(message)]
    pub fn delegate(&mut self, to: AccountId)  {

        // account id of the person who invoked the function
        let sender_id = self.env().caller();
        let sender_weight;
        // self delegation is not allowd
        assert_ne!(to,sender_id, "Self-delegation is disallowed.");

        {
            let sender_opt =  self.voters.get_mut(&sender_id);
            // the voter invoking the function should exist in our ballot
            assert_eq!(sender_opt.is_some(),true, "Caller is not a valid voter");
            let sender = sender_opt.unwrap();

            // the voter must not have already casted their vote
            assert_eq!(sender.voted,false, "You have already voted");

            sender.voted = true;
            sender.delegate = Some(to);
            sender_weight = sender.weight;
        }

        {
            let delegate_opt = self.voters.get_mut(&to);
            // the person to whom the vote is being delegated must be a valid voter
            assert_eq!(delegate_opt.is_some(),true, "The delegated address is not valid");

            let delegate = delegate_opt.unwrap();

            // the voter should not have already voted
            if delegate.voted {
                // If the delegate already voted,
                // directly add to the number of votes
                let voted_to = delegate.vote.unwrap() as usize;
                self.proposals[voted_to].vote_count += sender_weight;
            } else {
                // If the delegate did not vote yet,
                // add to her weight.
                delegate.weight += sender_weight;
            }
        }
    }
```

Вы увидите, что в приведенной выше функции делегирования мы обновляем поля `sender.voted` и `sender.delegate` перед проверкой, является ли делегируемое лицо действительным избирателем. Функция запаникует, если делегированное лицо не является действительным избирателем, и отменит изменения, внесенные в поля `sender.voted` и `sender.delegate`.

## Твоя очередь! <a id="your-turn"></a>

На этом урок заканчивается. Практикуйте полученные знания с помощью следующих упражнений:

- Обновите функцию «конструктор», чтобы при указании вектора имен предложений создавался новый объект предложения и добавлялся в «ballot.proposal».
- Определите функцию `give_voting_right` в соответствии с инструкциями.
- Добавьте функцию «голосования» и обновите бюллетень в соответствии с требованиями шаблона.
- Обновите функциональность `get_winning_proposal_name`, чтобы вернуть имя выигравшего предложения.

{% tabs %}
{% tab title="🔨Starting Point" %}

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
        pub fn new(proposal_names: Option<Vec<String>> ) -> Self {

            // get chair person address
            let chair_person =  Self::env().caller();

            // create empty propsal and voters
            let mut proposals: Vec<Proposal> = Vec::new();
            let mut voters = HashMap::new();

            // initialize chair person's vote
            voters.insert(chair_person, Voter{
                weight:1,
                voted:false,
                delegate: None,
                vote: None,
            });

            // ACTION : Check if proposal names are provided.
            //        * If yes then create and push proposal objects to proposals vector

            Self {
                chair_person,
                voters,
                proposals,
            }
        }

        /// default constrcutor
        #[ink(constructor)]
        pub fn default() -> Self {
            Self::new(Default::default())
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

        #[ink(message)]
        pub fn give_voting_right(&mut self, voter_id: AccountId) {
            let caller = self.env().caller();
            let voter_opt = self.voters.get_mut(&voter_id);
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
        pub fn new(proposal_names: Option<Vec<String>> ) -> Self {

            // get chair person address
            let chair_person =  Self::env().caller();

            // create empty propsal and voters
            let mut proposals: Vec<Proposal> = Vec::new();
            let mut voters = HashMap::new();

            // initialize chair person's vote
            voters.insert(chair_person, Voter{
                weight:1,
                voted:false,
                delegate: None,
                vote: None,
            });


            // ACTION : Check if proposal names are provided.
            //        * If yes then create and push proposal objects to proposals vector
            // if proposals are provided
            if proposal_names.is_some() {
                // store the provided propsal names
                let names = proposal_names.unwrap();
                for name in &names {
                    proposals.push(
                        Proposal{
                        name: String::from(name),
                        vote_count: 0,
                    });
                }
            }

            Self {
                chair_person,
                voters,
                proposals,
            }
        }

        /// default constrcutor
        #[ink(constructor)]
        pub fn default() -> Self {
            Self::new(Default::default())
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
        /// Give `voter` the right to vote on this ballot.
        /// Should only be called by `chairperson`.
        #[ink(message)]
        pub fn give_voting_right(&mut self, voter_id: AccountId) {
            let caller = self.env().caller();
            let voter_opt = self.voters.get_mut(&voter_id);
            // ACTION: check if the caller is the chair_person
            //         * check if the voter_id exists in ballot
            //         * check if voter has not already voted
            //         * if everything alright update voters weight to 1    // only chair person can give right to vote
            assert_eq!(caller,self.chair_person, "only chair person can give right to vote");    // the voter does not exists
            assert_eq!(voter_opt.is_some(),true, "provided voterId does not exist");    let voter = voter_opt.unwrap();    // the voter should not have already voted
            assert_eq!(voter.voted,false, "the voter has already voted");    voter.weight = 1;
        }
    }
}
```

{% endtab %}
{% endtabs %}
