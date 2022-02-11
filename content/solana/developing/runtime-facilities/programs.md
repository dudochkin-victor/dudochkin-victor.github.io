+++
title = "Нативные программы"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Solana содержит небольшое количество нативных программ, которые необходимы для запуска узлов валидатора. В отличие от сторонних программ, собственные программы являются частью реализации валидатора и могут быть обновлены в рамках обновления кластера.
Обновления могут выполняться для добавления функций, исправления ошибок или повышения производительности. Изменения интерфейса в отдельных инструкциях должны происходить редко, если вообще когда-либо. Вместо этого, когда необходимы изменения, добавляются новые инструкции, а предыдущие помечаются как устаревшие. Приложения могут обновляться по собственной временной шкале, не беспокоясь о сбоях при обновлении.

Для каждой собственной программы предоставляется идентификатор программы и описание каждой поддерживаемой инструкции. Транзакция может смешивать и сопоставлять инструкции из разных программ, а также включать инструкции из сетевых программ.

## Системная программа

Создавайте новые учетные записи, распределяйте данные учетных записей, назначайте учетные записи программам-владельцам, переносите лампы с учетных записей, принадлежащих системной программе, и оплачивайте комиссию за транзакцию.

- Идентификатор программы: `11111111111111111111111111111111`
- Инструкции: [SystemInstruction](https://docs.rs/solana-sdk/VERSION_FOR_DOCS_RS/solana_sdk/system_instruction/enum.SystemInstruction.html)

## Программа конфигурации

Добавьте данные конфигурации в цепочку и список открытых ключей, которым разрешено ее изменять.

- Идентификатор программы: `Config1111111111111111111111111111111111111`
- Инструкции: [config_instruction](https://docs.rs/solana-config-program/VERSION_FOR_DOCS_RS/solana_config_program/config_instruction/index.html)

В отличие от других программ, программа Config не определяет никаких отдельных инструкций. Он имеет только одну неявную инструкцию, инструкцию «сохранить». Его данные инструкции представляют собой набор ключей, которые ограничивают доступ к учетной записи, и данные для хранения в ней.

## Программа ставок

Создавайте и управляйте учетными записями, представляющими ставки и вознаграждения для делегаций.
валидаторы.

- Идентификатор программы: `Stake11111111111111111111111111111111111111`
- Инструкции: [StakeInstruction](https://docs.rs/solana-sdk/VERSION_FOR_DOCS_RS/solana_sdk/stake/instruction/enum.StakeInstruction.html)

## Программа голосования

Создавайте и управляйте учетными записями, которые отслеживают состояние голосования и вознаграждения валидатора.

- Идентификатор программы: `Vote111111111111111111111111111111111111111`
- Инструкции: [VoteInstruction](https://docs.rs/solana-vote-program/VERSION_FOR_DOCS_RS/solana_vote_program/vote_instruction/enum.VoteInstruction.html)

## Загрузчик BPF

Развертывает, обновляет и выполняет программы в цепочке.

- Идентификатор программы: `BPFLoaderUpgradeab1e11111111111111111111111`
- Инструкции: [Инструкция по загрузке](https://docs.rs/solana-sdk/VERSION_FOR_DOCS_RS/solana_sdk/loader_upgradeable_instruction/enum.UpgradeableLoaderInstruction.html)

Обновляемый загрузчик BPF помечает себя как «владельца» исполняемого файла и учетных записей программных данных, которые он создает для хранения вашей программы. Когда пользователь вызывает инструкцию через идентификатор программы, среда выполнения Solana загрузит как вашу программу, так и ее владельца, обновляемый загрузчик BPF. Затем среда выполнения передает вашу программу обновляемому загрузчику BPF для обработки инструкции.

[Подробнее о развертывании](cli/deploy-a-program/)

## Программа Ed25519

Проверьте программу подписи ed25519. Эта программа принимает подпись ed25519, открытый ключ и сообщение. Можно проверить несколько подписей. Если какая-либо из подписей не проходит проверку, возвращается ошибка.

- Идентификатор программы: `Ed25519SigVerify111111111111111111111111111`
- Инструкции: [new_ed25519_instruction](https://github.com/solana-labs/solana/blob/master/sdk/src/ed25519_instruction.rs#L45)

Программа ed25519 обрабатывает инструкцию. Первый `u8` — это количество проверяемых подписей, за которым следует однобайтовое заполнение. После этого сериализуется следующая структура, по одной для каждой проверяемой подписи.

```
struct Ed25519SignatureOffsets {
    signature_offset: u16,             // offset to ed25519 signature of 64 bytes
    signature_instruction_index: u16,  // instruction index to find signature
    public_key_offset: u16,            // offset to public key of 32 bytes
    public_key_instruction_index: u16, // instruction index to find public key
    message_data_offset: u16,          // offset to start of message data
    message_data_size: u16,            // size of message data
    message_instruction_index: u16,    // index of instruction data to get message data
}
```

Псевдокод операции:

```
process_instruction() {
    for i in 0..count {
        // i'th index values referenced:
        instructions = &transaction.message().instructions
        instruction_index = ed25519_signature_instruction_index != u16::MAX ? ed25519_signature_instruction_index : current_instruction;
        signature = instructions[instruction_index].data[ed25519_signature_offset..ed25519_signature_offset + 64]
        instruction_index = ed25519_pubkey_instruction_index != u16::MAX ? ed25519_pubkey_instruction_index : current_instruction;
        pubkey = instructions[instruction_index].data[ed25519_pubkey_offset..ed25519_pubkey_offset + 32]
        instruction_index = ed25519_message_instruction_index != u16::MAX ? ed25519_message_instruction_index : current_instruction;
        message = instructions[instruction_index].data[ed25519_message_data_offset..ed25519_message_data_offset + ed25519_message_data_size]
        if pubkey.verify(signature, message) != Success {
            return Error
        }
    }
    return Success
}
```

## Программа Secp256k1

Проверьте операции восстановления открытого ключа secp256k1 (ecrecover).

- Идентификатор программы: `KeccakSecp256k11111111111111111111111111111`
- Инструкции: [new_secp256k1_instruction](https://github.com/solana-labs/solana/blob/1a658c7f31e1e0d2d39d9efbc0e929350e2c2bcb/sdk/src/secp256k1_instruction.rs#L31)

Программа secp256k1 обрабатывает инструкцию, которая принимает в качестве первого байта счетчик следующей структуры, сериализованной в данных инструкции:

```
struct Secp256k1SignatureOffsets {
    secp_signature_key_offset: u16,        // offset to [signature,recovery_id,etherum_address] of 64+1+20 bytes
    secp_signature_instruction_index: u8,  // instruction index to find data
    secp_pubkey_offset: u16,               // offset to [signature,recovery_id] of 64+1 bytes
    secp_signature_instruction_index: u8,  // instruction index to find data
    secp_message_data_offset: u16,         // offset to start of message data
    secp_message_data_size: u16,           // size of message data
    secp_message_instruction_index: u8,    // index of instruction data to get message data
}
```

Псевдокод операции:

```
process_instruction() {
  for i in 0..count {
      // i'th index values referenced:
      instructions = &transaction.message().instructions
      signature = instructions[secp_signature_instruction_index].data[secp_signature_offset..secp_signature_offset + 64]
      recovery_id = instructions[secp_signature_instruction_index].data[secp_signature_offset + 64]
      ref_eth_pubkey = instructions[secp_pubkey_instruction_index].data[secp_pubkey_offset..secp_pubkey_offset + 32]
      message_hash = keccak256(instructions[secp_message_instruction_index].data[secp_message_data_offset..secp_message_data_offset + secp_message_data_size])
      pubkey = ecrecover(signature, recovery_id, message_hash)
      eth_pubkey = keccak256(pubkey[1..])[12..]
      if eth_pubkey != ref_eth_pubkey {
          return Error
      }
  }
  return Success
}
```

Это позволяет пользователю указать любые данные инструкции в транзакции для подписи и данных сообщения. Указав специальную инструкцию sysvar, можно также получить данные из самой транзакции.

Стоимость транзакции будет учитывать количество подписей для проверки, умноженное на множитель стоимости проверки подписи.

### Примечания по оптимизации

Операция должна будет выполняться после (по крайней мере, частичной) десериализации, но все входные данные поступают из самих данных транзакции, это позволяет относительно легко выполнять ее параллельно с обработкой транзакции и проверкой PoH.
