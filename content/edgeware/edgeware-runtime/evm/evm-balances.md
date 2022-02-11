+++
title = "Балансы EVM"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Модуль «EVM» в Substrate обеспечивает поддержку выполнения контрактов Ethereum в цепочке субстратов. Чтобы выполнять какие-либо действия, связанные с газом или балансом на EVM, на вызывающем счете должен быть баланс. Как работают эти балансы?

## Преобразование баланса

Чтобы использовать контракты Ethereum в цепочке Substrate, цепочка должна иметь протокол, поддерживающий следующие предположения:

1. \(32-байтовый\) адрес субстрата должен иметь соответствующий \(20-байтовый\) адрес Ethereum.
2. Каждый \(20-байтовый\) адрес Ethereum должен иметь свой баланс.

Модуль EVM выполняет шаг 1, просто усекая исходный адрес субстрата в адрес Ethereum, взяв первые 20 байтов. Чтобы выполнить шаг 2, цепочка использует ранее существовавший модуль балансов Substrate для управления каждым адресом Ethereum путем преобразования адресов Ethereum обратно в «адреса EVM», которые представляют собой 32-байтовые адреса Substrate. **Обратите внимание, что эти адреса EVM не имеют внутренней связи с исходным усеченным адресом субстрата.**

Возьмем пример:

- Рассмотрим 32-байтовый адрес субстрата: `0x1234567890ABCDEF1234567890ABCDEF1234567890ABCDEF1234567890ABCDEF`.
- Усеките это, чтобы создать 20-байтовый адрес Ethereum: `0x1234567890ABCDEF1234567890ABCDEF12345678`
- Баланс этого адреса Ethereum исходит из состояния модуля баланса субстрата для соответствующего адреса EVM, полученного путем хэширования вышеуказанных байтов с префиксом evm:`\(`0x65766D3A`\). Поэтому мы выполняем `Hash(0x65766D3A1234567890ABCDEF1234567890ABCDEF1234)` = `0xAF8536395A1EEC8EDA6FB9CF36739ECF75BECF6FEA04CEEC108BBB6AA15B7CB3`, чей баланс в модуле Balances будет использоваться для операций, связанных с EVM.
  - Точная используемая хеш-функция определяется в файле времени выполнения цепочки субстратов. В случае с Edgeware используется алгоритм хеширования Blake2, но возможен и Keccak.

**Обратите внимание, что эти действия необратимы: мы не можем преобразовать адрес EVM обратно в его адрес Ethereum, а также мы не можем преобразовать адрес Ethereum обратно в его «исходный» адрес субстрата.**

## Управление балансами Ethereum на Substrate

Возможны две операции: мы можем «внести» средства с учетной записи Substrate на соответствующую учетную запись Ethereum, и мы можем «снять» средства со учетной записи Ethereum обратно на исходную учетную запись Substrate.

### Депозиты

Поскольку адрес EVM, поддерживающий адрес Ethereum, вычисляется детерминировано, как указано выше, мы можем выполнить стандартный перевод баланса с нашей исходной учетной записи Substrate на адрес EVM, чтобы пополнить счет Ethereum средствами.

Это можно выполнить, вызвав `balances::transfer(prefixAndHash(truncate(account)).signAndSend(account)`, где `account` — исходная учетная запись субстрата, `truncate` берет первые 20 байтов в качестве адреса Ethereum, а `prefixAndHash` применяет префикс `evm:` и берет хэш as для преобразования обратно в 32-байтовый адрес субстрата.

### Снятие

Поскольку адрес EVM вычисляется детерминистически, у нас нет для него приватного ключа, поэтому мы не можем выполнить перенос баланса с него обычными способами. В результате модуль EVM предоставляет специальную функцию «вывод» для перевода средств обратно с учетной записи Ethereum на исходную учетную запись Substrate.

Это можно выполнить, вызвав evm::withdraw(truncate(account), value).signAndSend(account)`, где `account` — исходная учетная запись Substrate, а `truncate` берет первые 20 байтов в качестве адреса Ethereum.

### Балансы Эфириума

Балансы Ethereum обрабатываются так, как если бы они работали в любой цепочке Ethereum: газ вычитается из баланса (количество использованного газа можно узнать из квитанции транзакции, возвращаемой модулем EVM через web3 или truffle\), а переводы работают как ожидал.

Баланс 20-байтового адреса Ethereum и 32-байтового адреса EVM должен быть идентичен при сравнении: `web3.eth.getBalance(ethAddress)` должен равняться `system::balances(prefixAndHash(ethAddress)).freeBalance`, где ethAddress — это 20-байтовый адрес Ethereum, а prefixAndHash применяет префикс evm: и берет хеш, как описано выше.

**Обратите внимание, что коэффициент преобразования газа EVM в токены субстрата определяется в среде выполнения субстрата как «FeeCalculator», который в случае Edgeware настроен на сопоставление токена wei-to-Substrate 1-к-1.**

При преобразовании также обратите внимание, что в вызове EVM указывается `gasPrice`, как коэффициент преобразования из поля `gasUsed` в квитанции в wei.

\(ниже оригинальная техническая запись\)

### Адреса:

- Преобразование адреса субстрата в адрес EVM: `address[0..20]` — взять первые 20 байт.

- Преобразование адреса EVM в адрес субстрата: добавьте к данным префикс evm: и возьмите хэш. **ЭТО НЕОБРАТИМО**.
  
  - Видеть:[https://gitlab.parity.io/parity/substrate/-/blob/16fdfc4a80a14a26221d17b8a1b9a95421a1576c/frame/evm/src/lib.rs\#L167](https://gitlab.parity.io/parity/substrate/-/blob/16fdfc4a80a14a26221d17b8a1b9a95421a1576c/frame/evm/src/lib.rs#L167)
* * ```
    fn into_account_id(address: H160) -> AccountId32 {
        let mut data = [0u8; 24];
        data[0..4].copy_from_slice(b"evm:");
        data[4..24].copy_from_slice(&address[..]);
        let hash = H::hash(&data);
    
        AccountId32::from(Into::<[u8; 32]>::into(hash))
    }
    ```

* **Эффективно: каждый адрес субстрата сопоставляется с конкретным адресом EVM, который** _** поддерживается так, как если бы он был адресом субстрата**_ **\(см. ниже\)**_**.**_ ** Я называю \(32-байтовый\) адрес субстрата, созданный из \(20-байтового\) адреса EVM, "эквивалентом субстрата-эфириума".**
  
  - В качестве примера рассмотрим \(фальшивый\) 32-байтовый адрес субстрата\(он использует `AccountId32`, который представляет собой массив `[u8, 32]`, представленный здесь как 64-символьная шестнадцатеричная строка\): `0x1234567890ABCDEF1234567890ABCDEF1234567890ABCDEF1234567890ABCDEF `
  - Сначала мы преобразуем это в 20-байтовый адрес EVM \ (представленный здесь как шестнадцатеричная строка из 40 символов \): `0x1234567890ABCDEF1234567890ABCDEF1234`
    Затем мы конвертируем его в новый адрес субстрата: `Hash('evm:'+0x1234567890ABCDEF1234567890ABCDEF1234)` = `Hash(0x65766D3A1234567890ABCDEF1234567890ABCDEF1234)`, где `Hash` определяется во время выполнения как Blake или Keccak. Edgeware использует Blake \(см.: [https://github.com/hicommonwealth/edgeware-node/blob/edg-frontier-up-1/node/runtime/src/lib.rs\#L857](https:// github.com/hicommonwealth/edgeware-node/blob/edg-frontier-up-1/node/runtime/src/lib.rs#L857)\), поэтому окончательный вывод будет в шестнадцатеричном формате: `0xAF8536395A1EEC8EDA6FB9CF36739ECF75BECF6FEA04CEEC108BBB6AA15B7CB3`
  - Таким образом, 32-байтовая «субстратная учетная запись» `0x12345...` сопоставляется с 20-байтовой «учетной записью эфириума» `0x12345...1234`, которая сопоставляется с 32-байтовым «эквивалентом субстрата-эфириума» `0xAF8536. ..`, который функционирует как действительный адрес субстрата. **Это основной адрес, используемый для поддержания остатков на счетах для соответствующей учетной записи EVM.**

### Балансы:

- Адресу EVM может быть предоставлен баланс при генезисе или путем отправки баланса непосредственно на «эквивалент субстрата-эфириума», как обычно отправляет баланс между учетными записями.
  
  > «Кажется, нельзя использовать собственные адреса субстрата по умолчанию для взаимодействия с EVM, можете ли вы рассказать об этом подробнее? Что должен делать пользователь, если у него нулевой баланс при генезисе»
  
  - Каждый адрес субстрата детерминированно сопоставляется с адресом EVM.
    
    - В границе evm-address.js есть утилита, которая предназначена для выполнения этого преобразования, но, похоже, делает это неправильно на основе обновленной палитры evm (утилита не обновлялась 4 месяца).
    - См. реализации следующих утилит в проекте frontier-tester: [https://github.com/hicommonwealth/frontier-tester/blob/master/utils.js\#L46](https://github.com/hicommonwealth /frontier-tester/blob/master/utils.js#L46)
    - Это выполнимо следующим образом:
* * ```
    import { decodeAddress } from '@polkadot/util-crypto';
    
    const convertToEvmAddress = (substrateAddress) => {
      const addressBytes = decodeAddress(substrateAddress);
      return '0x' + Buffer.from(addressBytes.subarray(0, 20)).toString('hex');
    }
    ```
    
    * В Rust \(вероятно, есть более простой способ сделать это с помощью refs\):
    
    ```
    fn convertToEvmAddress(address: H256) -> H160 {
      let mut evmAddress: [u8; 20] = Default::default();
        evmAddress[0..20].copy_from_slice(&address[0..20]);
        evmAddress
    }
    ```
    
    * **Исходный адрес подложки не может быть восстановлен только по адресу EVM.**
  
  * Каждый адрес EVM детерминистически сопоставляется с другим адресом-субстратом, который поддерживает свой баланс и на который можно отправлять средства напрямую через базовый перевод. Этот адрес субстрата вычисляется следующим образом:
    
    ```
    import { encodeAddress, blake2AsHex } from '@polkadot/util-crypto';
    
    const convertToSubstrateAddress = (evmAddress, prefix = 42) => {
      const addressBytes = Buffer.from(evmAddress.slice(2), 'hex');
      const prefixBytes = Buffer.from('evm:');
      const convertBytes = Uint8Array.from(Buffer.concat([ prefixBytes, addressBytes ]));
      const finalAddressHex = blake2AsHex(convertBytes, 256);
      return encodeAddress(finalAddressHex, prefix);
    }
    ```
    
    * В Rust:
    
    ```
    fn convertToSubstrateAddress(address: H160) -> AccountId32 {
        HashedAddressMapping<BlakeTwo256>::into_account_id(address)
    }
    ```
  
  * **Этот сгенерированный адрес подложки эквивалентен адресу EVM во всех смыслах и целях и является детерминированным вычислением исходного адреса подложки — его можно свободно использовать в других поддонах в качестве «прокси» для адреса EVM. Однако у него нет закрытого ключа, поэтому он не может подписывать.**

* Средства должны быть сняты со счета EVM с помощью функции `pallet_evm::withdraw`, поскольку «эквивалент субстрата-эфириума» не имеет известного закрытого ключа для отправки транзакций.
  
  - [https://github.com/paritytech/substrate/blob/master/frame/evm/src/lib.rs\#L304](https://github.com/paritytech/substrate/blob/master/frame/ evm/src/lib.rs#L304)
  - Вызывающий должен указать \(20-байтовый\) адрес EVM, который соответствует его адресу субстрата — это проверяется здесь: [https://github.com/paritytech/substrate/blob/master/frame/evm/src/ lib.rs\#L148](https://github.com/paritytech/substrate/blob/master/frame/evm/src/lib.rs#L148).
  - Функция преобразует этот адрес EVM в «эквивалент субстрата-эфириума», с которого она переводит средства.
- `free_balance` «эквивалента субстрата-эфириума» используется в качестве баланса учетной записи ethereum: [https://github.com/paritytech/substrate/blob/master/frame/evm/src/lib.rs\ # L471](https://github.com/paritytech/substrate/blob/master/frame/evm/src/lib.rs#L471)

- Изменения в балансе учетной записи ethereum, например, после выполнения либо вызов `slash`, либо `deposit_creating` на «эквивалент субстрата-ethereum» в зависимости от того, был ли добавлен или удален баланс: [https://github.com/paritytech/ субстрат/blob/master/frame/evm/src/lib.rs\#L440](https://github.com/paritytech/substrate/blob/master/frame/evm/src/lib.rs#L440)

- **Для примера балансов в действии на Javascript см. этот тестовый файл в frontier-tester:** [**https://github.com/hicommonwealth/frontier-tester/blob/master/web3tests/testSubstrateBalances.ts* *](https://github.com/hicommonwealth/frontier-tester/blob/master/web3tests/testSubstrateBalances.ts)

### Gas:

- Цены на различные функции в газе определяются в `EVM_CONFIG` во время выполнения.
  - Текущая конфигурация Edgeware использует клон конфигурации ISTANBUL с повышенным значением CreateContractLimit.
  - См.: [https://github.com/rust-blockchain/evm/blob/master/runtime/src/lib.rs\#L245](https://github.com/rust-blockchain/evm/blob/ мастер/среда выполнения/источник/lib.rs#L245)
- Скорость преобразования газа в токены субстрата определяется во время выполнения как FeeCalculator, который в случае Edgeware настроен на сопоставление 1-к-1.
- Газ "изымается" из StackExecutor перед выполнением: [https://github.com/paritytech/substrate/blob/master/frame/evm/src/lib.rs\#L608](https://github.com /paritytech/substrate/blob/master/frame/evm/src/lib.rs#L608)
- Изменения баланса, включая газ \(выполненные вышеуказанным выводом\), записываются обратно на баланс «эквивалента субстрата-эфириума» после того, как выполнение EVM было применено к серверной части: [https://github.com /paritytech/substrate/blob/master/frame/evm/src/backend.rs\#L144](https://github.com/paritytech/substrate/blob/master/frame/evm/src/backend.rs#L144 )
