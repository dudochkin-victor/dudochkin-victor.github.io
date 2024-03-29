+++
title = "Возврат данных из программ BPF"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

## Проблема

В языке Solidity разрешено возвращать любое количество значений из функции, например, может быть возвращена строка переменной длины:

```
function foo1() public returns (string) {
    return "Hello, world!\n";
}
```

Допускаются также множественные значения, массивы и структуры.

```
struct S {
    int f1;
    bool f2
};

function foo2() public returns (string, int[], S) {
    return (a, b, c);
}
```

Все возвращаемые значения кодируются eth abi в массив байтов переменной длины.

В Эфириуме тоже могут быть возвращены ошибки:

```
function withdraw() public {
    require(msg.sender == owner, "Permission denied");
}

function failure() public {
    revert("I afraid I can't do that dave");
}
```

Эти ошибки помогают разработчику отладить любую проблему, с которой они столкнулись, а также могут быть обнаружены в блоке `try` .. `catch` Solidity. Вне блока `try` .. `catch` любой из них приведет к сбою транзакции или rpc.

## Существующее решение

Существующее решение, которое использует Solang, записывает возвращаемые данные в данные учетной записи вызываемого абонента.
Учетная запись вызывающего абонента не может быть использована, так как вызываемый объект может быть не той же программой BPF, поэтому у него не будет разрешения на запись данных учетной записи вызываемого абонента.

Другим решением было бы иметь единую учетную запись данных возврата, которая передается через CPI. Опять же, это не работает для CPI, поскольку вызываемый объект может не иметь разрешения на запись в него.

Проблема с этим решением заключается в следующем:

- Не работает для вызовов RPC
- Это очень гоночный; клиент должен отправить Tx, а затем получить данные учетной записи. Это не атомарно, поэтому возвращаемые данные могут быть перезаписаны другой транзакцией.

## Требования к решению

Он должен работать для:

- RPC: RPC должен иметь возможность возвращать любое количество значений без записи данных учетной записи.
- Транзакция: транзакция должна иметь возможность возвращать любое количество значений без необходимости записи в них данных учетной записи.
- CPI: вызываемый объект должен "установить" возвращаемое значение, а вызывающий должен иметь возможность получить его.

## Обзор других сетей

### Эфириум (EVM)

Код операции `RETURN` позволяет контракту установить буфер в качестве возвращаемых данных. Этот код операции принимает указатель на память и размер. Опкод `REVERT` работает аналогично, но сигнализирует о том, что вызов не удался, и все изменения данных учетной записи должны быть отменены.

Для CPI вызывающая сторона может получить возвращенные данные вызываемого объекта с помощью кода операции `RETURNDATASIZE`, который возвращает длину, и кода операции `RETURNDATACOPY`, который принимает указатель места назначения в памяти, смещение в возвращаемых данных и аргумент длины.

Ethereum хранит возвращаемые данные в блоках.

### Parity Substrate

Возвращаемые данные можно установить с помощью системного вызова `seal_return (флаги u32, указатель u32, размер u32)`.

- Флаги могут быть 1 для возврата, 0 для успеха (больше ничего не определено)
- Функция не возвращается

CPI: системный вызов `seal_call()` принимает указатель на буфер и указатель на размер буфера, куда возвращаются данные.

- Существует ограничение в 32 КБ для возвращаемых данных.

Parity Substrate не записывает возвращаемые данные в блоки.

## Отклонено Решение

Для решения этой проблемы была предложена концепция эфемерных счетов. Это определенно сработает для случая CPI, но не сработает для случая RPC или транзакции.

## Предложенное решение

Вызываемая сторона может установить возвращаемые данные с помощью нового системного вызова sol_set_return_data(buf: *const u8, length: u64)`.
Для возвращаемых данных существует ограничение в 1024 байта. Эту функцию можно вызывать несколько раз, и
просто перезапишет то, что было написано при последнем вызове.

Возвращаемые данные можно получить с помощью `sol_get_return_data(buf: *mut u8, length: u64, program_id: *mut Pubkey) -> u64`.
Эта функция копирует буфер возврата и идентификатор программы, который устанавливает возвращаемые данные, и возвращает длину возвращаемых данных или `0`, если возвращаемые данные не установлены. В этом случае program_id не устанавливается.

Когда инструкция вызывает sol_invoke(), возвращаемые данные вызываемого объекта копируются в возвращаемые данные текущей инструкции. Это означает, что любые возвращаемые данные автоматически передаются вверх по стеку вызовов вызываемому объекту текущей инструкции (или вызову RPC).

Обратите внимание, что sol_invoke() очищает возвращаемые данные перед вызовом вызываемого, так что любые возвращаемые данные из предыдущего вызова не используются повторно, если вызываемый не может установить возвращаемые данные. Например:

- А вызывает Б
- Перед входом в B данные возврата очищаются.0
- B устанавливает некоторые возвращаемые данные и возвращает
- А вызывает С
- Перед входом в C возвращаемые данные очищаются.
- C не устанавливает возвращаемые данные и возвращает
- A проверяет возвращаемые данные и находит их пустыми

Другой сценарий для рассмотрения:

- А вызывает Б
- B вызывает C
- C устанавливает возвращаемые данные и возвращает
- B не трогает возвращаемые данные и возвращает
- A получает возвращаемые данные от C
- A не касается возвращаемых данных
- Возврат данных из транзакции - это то, что установил C.

Затраты на вычисления рассчитываются для получения и установки возвращаемых данных с помощью системных вызовов.

Для обычного RPC или транзакции возвращаемые данные имеют кодировку base64 и хранятся вместе со строками sol_log в [стабильном журнале](https://github.com/solana-labs/solana/blob/95292841947763bdd47ef116b40fc34d0585bca8/sdk/src/process_instruction .rs#L275-L281).

## Примечание о возврате ошибок

Solidity на Ethereum позволяет контракту возвращать ошибку в возвращаемых данных. В этом случае все изменения данных учетной записи для учетной записи должны быть отменены. В Solana любой ненулевой код выхода для программы BPF означает сбой всей транзакции. Мы не хотим поддерживать возврат ошибки, возвращая успех, а затем возвращая ошибку в возвращаемых данных. Это означало бы, что нам придется поддерживать отмену изменений данных учетной записи; это слишком дорого как на стороне VM, так и на стороне контракта BPF.

Об ошибках будет сообщаться через sol_log.
