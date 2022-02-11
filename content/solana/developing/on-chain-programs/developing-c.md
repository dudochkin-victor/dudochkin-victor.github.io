+++
title = "Разработка на C"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Solana поддерживает написание сетевых программ с использованием языков программирования C и C++.

## Макет проекта

C-проекты расположены следующим образом:

```
/src/<program name>
/makefile
```

Makefile должен содержать следующее:

```bash
OUT_DIR := <path to place to resulting shared object>
include ~/.local/share/solana/install/active_release/bin/sdk/bpf/c/bpf.mk
```

Bpf-sdk может находиться не в точном месте, указанном выше, но если вы настроите свою среду в соответствии с Как собрать, тогда он должен быть.

Взгляните на [helloworld] (https://github.com/solana-labs/example-helloworld/tree/master/src/program-c) для примера программы C.

## Как построить

Сначала настройте среду:

- Установите последнюю стабильную версию Rust с https://rustup.rs
- Установите последние инструменты командной строки Solana с https://docs.solana.com/cli/install-solana-cli-tools.

Затем соберите с помощью make:

```bash
make -C <program directory>
```

## Как тестировать

Solana использует среду тестирования [Criterion](https://github.com/Snaipe/Criterion), и тесты выполняются каждый раз при сборке программы Как построить.

Чтобы добавить тесты, создайте новый файл рядом с исходным файлом с именем `test_<имя программы>.c` и заполните его тестовыми примерами критериев. Пример см. в [тестах helloworld C](https://github.com/solana-labs/example-helloworld/blob/master/src/program-c/src/helloworld/test_helloworld.c) или в [документах Criterion ](https://criterion.readthedocs.io/en/master) для получения информации о том, как написать тестовый пример.

## Точка входа в программу

Программы экспортируют известный символ точки входа, который среда выполнения Solana ищет и вызывает при вызове программы. Solana поддерживает несколько [версий загрузчика BPF](overview.md#versions), и точки входа могут различаться между ними. Программы должны быть написаны и развернуты для одного и того же загрузчика. Подробнее см. [обзор](обзор#загрузчики).

В настоящее время поддерживаются два загрузчика [BPF Loader](https://github.com/solana-labs/solana/blob/7ddf10e602d2ed87a9e3737aa8c32f1db9f909d8/sdk/program/src/bpf_loader.rs#L17) и [загрузчик BPF устарел](https: //github.com/solana-labs/solana/blob/7ddf10e602d2ed87a9e3737aa8c32f1db9f909d8/sdk/program/src/bpf_loader_deprecated.rs#L14).

Они оба имеют одно и то же необработанное определение точки входа, ниже приведен необработанный символ, который ищет и вызывает среда выполнения:

```c
extern uint64_t entrypoint(const uint8_t *input)
```

Эта точка входа принимает общий массив байтов, который содержит сериализованные параметры программы (идентификатор программы, учетные записи, данные инструкций и т. д.). Для десериализации параметров каждый загрузчик содержит свою вспомогательную функцию.

См. [использование точки входа в helloworld](https://github.com/solana-labs/example-helloworld/blob/bc0b25c0ccebeff44df9760ddb97011558b7d234/src/program-c/src/helloworld/helloworld.c#L37) в качестве примера как вещи сочетаются друг с другом.

### Сериализация

См. [Helloworld использует функцию десериализации](https://github.com/solana-labs/example-helloworld/blob/bc0b25c0ccebeff44df9760ddb97011558b7d234/src/program-c/src/helloworld/helloworld.c#L43).

Каждый загрузчик предоставляет вспомогательную функцию, которая десериализует входные параметры программы в типы C:

- [Десериализация загрузчика BPF](https://github.com/solana-labs/solana/blob/d2ee9db2143859fa5dc26b15ee6da9c25cc0429c/sdk/bpf/c/inc/solana_sdk.h#L304)
- [Загрузчик BPF устарел от десериализации](https://github.com/solana-labs/solana/blob/8415c22b593f164020adc7afe782e8041d756ddf/sdk/bpf/c/inc/deserialize_deprecated.h#L25)

Некоторым программам может потребоваться выполнить десериализацию самостоятельно, и они могут это сделать, предоставив собственную реализацию необработанной точки входа.
Обратите внимание, что предоставленные функции десериализации сохраняют ссылки на сериализованный массив байтов для переменных, которые программа может изменять (lamports, данные учетной записи). Причина этого в том, что по возвращении загрузчик прочитает эти изменения, чтобы их можно было зафиксировать. Если программа реализует свою собственную функцию десериализации, она должна гарантировать, что любые изменения, которые программа хочет зафиксировать, должны быть записаны обратно во входной массив байтов.

Подробности о том, как загрузчик сериализует входные данные программы, можно найти в документах [Сериализация входных параметров](overview.md#input-parameter-serialization).

## Типы данных

Вспомогательная функция десериализации загрузчика заполняет структуру [SolParameters](https://github.com/solana-labs/solana/blob/8415c22b593f164020adc7afe782e8041d756ddf/sdk/bpf/c/inc/solana_sdk.h#L276):

```c
/**
 * Structure that the program's entrypoint input data is deserialized into.
 */
typedef struct {
  SolAccountInfo* ka; /** Pointer to an array of SolAccountInfo, must already
                          point to an array of SolAccountInfos */
  uint64_t ka_num; /** Number of SolAccountInfo entries in `ka` */
  const uint8_t *data; /** pointer to the instruction data */
  uint64_t data_len; /** Length in bytes of the instruction data */
  const SolPubkey *program_id; /** program_id of the currently executing program */
} SolParameters;
```

«ka» — это упорядоченный массив учетных записей, на которые ссылается инструкция и представленный в виде [SolAccountInfo](https://github.com/solana-labs/solana/blob/8415c22b593f164020adc7afe782e8041d756ddf/sdk/bpf/c/inc/solana_sdk. h#L173) структуры. Место счета в массиве указывает на его значение, например, при передаче лэмпортов инструкция может определить первый счет как источник, а второй как место назначения.

Члены структуры `SolAccountInfo` доступны только для чтения, за исключением `lamports` и `data`. Оба могут быть изменены программой в соответствии с [политикой применения во время выполнения] (developing/programming-model/accounts.md#policy). Когда инструкция ссылается на одну и ту же учетную запись несколько раз, в массиве могут быть повторяющиеся записи `SolAccountInfo`, но обе они указывают на исходный массив входных байтов. Программа должна деликатно обрабатывать эти случаи, чтобы избежать перекрывающихся операций чтения/записи в один и тот же буфер. Если программа реализует собственную функцию десериализации, следует позаботиться о правильной обработке повторяющихся учетных записей.

`data` - это массив байтов общего назначения из [данных инструкции инструкции] (developing/programming-model/transactions.md#instruction-data), которые обрабатываются.

`program_id` - это открытый ключ выполняемой в данный момент программы.

## Куча

Программы на C могут выделять память с помощью системного вызова [`calloc`](https://github.com/solana-labs/solana/blob/c3d2d2134c93001566e1e56f691582f379b5ae55/sdk/bpf/c/inc/solana_sdk.h#L245) или реализовывать свои собственную кучу поверх области кучи размером 32 КБ, начиная с виртуального адреса x300000000. Область кучи также используется `calloc`, поэтому, если программа реализует свою собственную кучу, она не должна также вызывать `calloc`.

## Логирование

Среда выполнения предоставляет два системных вызова, которые принимают данные и регистрируют их в журналах программы.

- [`sol_log(const char*)`](https://github.com/solana-labs/solana/blob/d2ee9db2143859fa5dc26b15ee6da9c25cc0429c/sdk/bpf/c/inc/solana_sdk.h#L128)
- [`sol_log_64(uint64_t, uint64_t, uint64_t, uint64_t, uint64_t)`](https://github.com/solana-labs/solana/blob/d2ee9db2143859fa5dc26b15ee6da9c25cc0429c/sdk/bpf/c/inc/solana_sd3k4)#1sd3k4)

Раздел [debugging](debugging.md#logging) содержит дополнительную информацию о работе с журналами программы.

## Расчет бюджета

Для регистрации сообщения, оставшееся количество вычислительных единиц, которые программа может использовать, прежде чем выполнение будет остановлено

См. [вычислить бюджет](developing/programming-model/runtime.md#compute-budget) для получения дополнительной информации.

## ЭЛЬФ Дамп

Внутреннее устройство общего объекта BPF можно сбросить в текстовый файл, чтобы получить более полное представление о составе программы и о том, что она может делать во время выполнения. Дамп будет содержать как информацию об ELF, так и список всех символов и инструкций, которые их реализуют. Некоторые из сообщений журнала ошибок загрузчика BPF будут ссылаться на определенные номера инструкций, в которых произошла ошибка. Эти ссылки можно найти в дампе ELF, чтобы идентифицировать нарушающую инструкцию и ее контекст.

Чтобы создать файл дампа:

```bash
$ cd <program directory>
$ make dump_<program name>
```

## Примеры

Репозиторий [Solana Program Library github](https://github.com/solana-labs/solana-program-library/tree/master/examples/c) содержит коллекцию примеров C
