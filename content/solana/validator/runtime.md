+++
title = "Runtime"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

## Runtime

Среда выполнения представляет собой параллельный обработчик транзакций. Транзакции указывают свои зависимости данных заранее, а динамическое выделение памяти является явным. Отделив программный код от состояния, в котором он работает, среда выполнения может управлять одновременным доступом. Транзакции с доступом только к учетным записям только для чтения выполняются параллельно, тогда как транзакции с доступом к учетным записям с возможностью записи сериализуются. Среда выполнения взаимодействует с программой через точку входа с четко определенным интерфейсом. Данные, хранящиеся в учетной записи, представляют собой непрозрачный тип, массив байтов. Программа имеет полный контроль над своим содержимым.

Структура транзакции определяет список открытых ключей и подписей для этих ключей, а также последовательный список инструкций, которые будут выполняться в состояниях, связанных с ключами учетной записи. Чтобы транзакция была зафиксирована, все инструкции должны быть выполнены успешно; если произойдет какое-либо прерывание, вся транзакция не будет зафиксирована.

#### Структура аккаунта

Учетные записи поддерживают баланс лампы и память для конкретной программы.

## Механизм транзакций

Механизм сопоставляет открытые ключи с учетными записями и направляет их к точке входа программы.

### Исполнение

Транзакции группируются и обрабатываются в конвейере. TPU и TVU идут немного другим путем. Среда выполнения TPU гарантирует, что запись PoH произойдет до фиксации памяти.

Среда выполнения TVU гарантирует, что проверка PoH выполняется до того, как среда выполнения обработает какие-либо транзакции.

![Runtime pipeline](/img/runtime.svg)

На этапе _execute_ загружаемые аккаунты не имеют зависимостей данных, поэтому все программы могут выполняться параллельно.

Среда выполнения применяет следующие правила:

1. Только программа _owner_ может изменять содержимое учетной записи. Это означает, что при назначении вектор данных гарантированно равен нулю.
2. Суммарные остатки на всех счетах равны до и после совершения транзакции.
3. После выполнения транзакции балансы счетов только для чтения должны быть равны балансам до транзакции.
4. Все инструкции в транзакции выполняются атомарно. В случае сбоя все изменения учетной записи отбрасываются.

Выполнение программы включает сопоставление открытого ключа программы с точкой входа, которая принимает указатель на транзакцию и массив загруженных учетных записей.

### Интерфейс системной программы

Интерфейс лучше всего описывается `Instruction::data`, который кодирует пользователь.

- `CreateAccount` - позволяет пользователю создать учетную запись с выделенным массивом данных и назначить ее Программе.
- `CreateAccountWithSeed` – То же, что и `CreateAccount`, но адрес новой учетной записи получается из
  - публичный ключ счета финансирования,
  - мнемоническая строка (seed), и
  - публичный ключ Программы
- «Назначить» — позволяет пользователю назначить существующую учетную запись программе.
- `Перевод` - Переводы лампортов между аккаунтами.

### Государственная безопасность программы

Для правильной работы блокчейна программный код должен быть устойчивым к пользовательскому вводу. Вот почему в этом дизайне программный специфический код является единственным кодом, который может изменить состояние массива байтов данных в присвоенных ему Учетных записях. Это также причина, по которой `Assign` или `CreateAccount` должны обнулить данные. В противном случае у программы не было бы возможности отличить недавно назначенные данные учетной записи от естественно сгенерированного перехода состояния без некоторых дополнительных метаданных из среды выполнения, чтобы указать, что эта память назначена, а не сгенерирована изначально.

Для передачи сообщений между программами принимающая программа должна принять сообщение и скопировать состояние. Но на практике копия не нужна и нежелательна. Программа-получатель может прочитать состояние, принадлежащее другим Пользователям, не копируя его, и во время чтения имеет гарантию состояния программы-отправителя.

### Примечания

- Нет динамического выделения памяти. Клиенту необходимо использовать инструкции CreateAccount для создания памяти перед передачей ее другой программе. Эта инструкция может быть объединена в одну транзакцию с вызовом самой программы.
- `CreateAccount` и `Assign` гарантируют, что когда учетная запись назначается программе, данные учетной записи инициализируются нулями.
- Транзакции, которые назначают учетную запись программе или выделяют пространство, должны быть подписаны закрытым ключом адреса учетной записи, если только учетная запись не создается с помощью `CreateAccountWithSeed`, и в этом случае нет соответствующего закрытого ключа для адреса учетной записи/pubkey.
- После назначения в программу учетная запись не может быть переназначена.
- Среда выполнения гарантирует, что код программы является единственным кодом, который может изменять данные Пользователя, которому назначена Пользователя.
- Runtime гарантирует, что программа может тратить только лэмпорты, которые находятся в аккаунтах, которые ей назначены.
- Время выполнения гарантирует, что остатки, принадлежащие счетам, сбалансированы до и после транзакции.
- Среда выполнения гарантирует успешное выполнение всех инструкций при фиксации транзакции.

## Будущая работа

- [Продолжения и сигналы для длительных транзакций](https://github.com/solana-labs/solana/issues/1485)