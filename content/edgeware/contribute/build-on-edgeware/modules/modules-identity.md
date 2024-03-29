+++
title = "Модули: Идентификация (Устарело)"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

_Начиная с testnet V0.4.0, эта страница устарела. Пожалуйста, обратитесь к коду для справки._

## edge\_identity

В настоящее время модуль идентификации обрабатывает регистрацию, аттестацию и проверку внешних удостоверений в Edgeware. Типы удостоверений, которые могут быть интересны для регистрации через этот модуль, включают другие адреса блокчейна или открытые ключи, имена пользователей Github, адреса электронной почты и даже номера телефонов. Цель этого модуля — обеспечить максимальную расширяемость для внешних удостоверений в реальном мире или мире блокчейна для взаимодействия с Edgeware.

## Настройка

Установите rust или обновитесь до последних версий.

```
curl https://sh.rustup.rs -sSf | sh
rustup update nightly
rustup target add wasm32-unknown-unknown --toolchain nightly
rustup update stable
cargo install --git https://github.com/alexcrichton/wasm-gc
```

Вам также потребуется установить следующие пакеты:

Linux:

```
sudo apt install cmake pkg-config libssl-dev git clang libclang-dev
```

Mac:

```
brew install cmake pkg-config openssl git llvm
```

## Жизненный цикл удостоверения

Удостоверения в Edgeware проходят через простой конечный автомат:

1. «Зарегистрировано»
2. «Засвидетельствовано»
3. «Проверено»

### Регистрация

Внешнее удостоверение в Edgeware сначала запускается в процессе регистрации. Пользователь должен иметь действительный открытый ключ Edgeware и иметь возможность подписывать и отправлять транзакцию на подходящий узел Edgeware. Чтобы зарегистрировать удостоверение, нам требуется только передать тип удостоверения и удостоверение, отформатированное в виде байтовых массивов `Vec<u8>`, в функцию `register`. Как только транзакция включается в цепочку Edgeware, эта идентификация становится «Зарегистрированной».

### Аттестовано

Подтверждение внешнего удостоверения в Edgeware является убедительным доказательством того, что регистратор удостоверения действительно владеет указанным удостоверением. Мы обозначаем регистратора как владельца закрытого ключа Edgeware, отправившего регистрацию внешнего удостоверения. Если аттестующий и регистратор — одно и то же лицо, у них не должно возникнуть проблем с убеждением активного набора «верификаторов», что они на самом деле контролируют внешнюю идентификацию. Аттестация отформатирована как массив байтов `Vec<u8>`, который должен надежно указывать на URL-адрес или другое приемлемое доказательство контроля.

Отправитель регистрации и аттестации должен быть одним и тем же. Это помогает смягчить ложные аттестации. Таким образом, блокчейн служит единственным источником достоверной информации о регистрации личности. Верификатор должен только сверяться с доказательством, хранящимся в цепочке, чтобы решить, является ли оно действительным или нет, а не с какой-либо другой формой или представлением аттестации.

Примеры аттестаций для разных настроек:

1. **Гитхаб**
   
   Идентификацией является «имя пользователя» Github. Регистратор после регистрации имени пользователя должен опубликовать Gist под учетной записью «имя пользователя» со ссылкой на регистрацию в Edgeware.

2. **Эфириум**
   
   Идентификатор представляет собой открытый ключ или адрес Ethereum. Регистратор после регистрации имени пользователя может отправить транзакцию со значением 0 на адрес записи, символизирующий открытый ключ соответствующей учетной записи Edgeware, и отправить хэш транзакции в качестве подтверждения. После проверки у верификатора должно быть достаточно доказательств того, что, если владелец учетной записи Ethereum не владеет также учетной записью Edgeware получателя \ (представленной как целевой адрес в Ethereum \), то он не будет выполнять такую ​​транзакцию.

### Проверено

Окончательное и постоянное состояние удостоверения в Edgeware в его нынешнем виде — это подтвержденное удостоверение. Поскольку люди по-прежнему могут регистрироваться и подтверждать личность, которую они не контролируют, отправляя ложные доказательства аттестации, они не смогут обмануть активный набор верификаторов. Работа активных верификаторов состоит в том, чтобы проверять доказательства аттестации и голосовать за или против проверки.

Проверка личности принимается или отклоняется после того, как 2/3 активных верификаторов проголосовали за соответствующий результат. Однажды принятые идентификационные данные остаются проверенными навсегда, если в будущем не будут разработаны процедуры управления для изменения логики.
