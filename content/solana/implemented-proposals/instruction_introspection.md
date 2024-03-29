+++
title = "Интроспекция инструкций"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

## Проблема

Некоторые программы смарт-контрактов могут захотеть проверить наличие другой инструкции в данном сообщении, поскольку эта инструкция может выполнять проверку определенных данных в предварительно скомпилированной функции. (См. secp256k1_instruction для примера).

## Решение

Добавьте новую системную переменную Sysvar1nstructions11111111111111111111111111, на которую программа может ссылаться и получать данные инструкции сообщения внутри, а также индекс текущей инструкции.

Можно использовать две вспомогательные функции для извлечения этих данных:

```
fn load_current_index(instruction_data: &[u8]) -> u16;
fn load_instruction_at(instruction_index: usize, instruction_data: &[u8]) -> Result<Instruction>;
```

Среда выполнения распознает эту специальную инструкцию, сериализует для нее данные инструкции Message, а также запишет текущий индекс инструкции, после чего программа bpf сможет извлечь оттуда необходимую информацию.

Примечание: используется пользовательская сериализация инструкций, поскольку бинкод примерно в 10 раз медленнее в собственном коде и превышает текущие ограничения инструкций BPF.
