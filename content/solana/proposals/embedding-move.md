+++
title = "Встраивание языка перемещения"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

## Проблема

Solana позволяет разработчикам писать on-chain программы на языках программирования общего назначения, таких как C или Rust, но эти программы содержат специфичные для Solana механизмы. Например, нет другой цепочки, которая просит разработчиков создать модуль Rust с функцией `process_instruction(KeyedAccounts)`. Когда это целесообразно, Solana должна предлагать разработчикам приложений более портативные варианты.

До недавнего времени ни один популярный блокчейн не предлагал языка, который мог бы раскрыть ценность массивно-параллельной [среды выполнения] Соланы (../validator/runtime/). Контракты Solidity, например, не отделяют ссылки на общие данные от кода контракта и поэтому должны выполняться последовательно, чтобы обеспечить детерминированное поведение. На практике мы видим, что все наиболее агрессивно оптимизированные блокчейны на основе EVM, по-видимому, достигают пика около 1200 TPS — небольшая часть того, что может сделать Solana. С другой стороны, проект Libra разработал сетевой язык программирования под названием Move, который больше подходит для параллельного выполнения. Как и среда выполнения Solana, программы Move зависят от учетных записей для всего общего состояния.

Самая большая разница в дизайне между средой выполнения Solana и Move VM Libra заключается в том, как они управляют безопасными вызовами между модулями. Solana применила подход к операционным системам, а Libra — подход к предметно-ориентированному языку. Во время выполнения модуль должен вернуться в среду выполнения, чтобы убедиться, что модуль вызывающего объекта не записывает данные, принадлежащие вызываемому. Аналогичным образом, когда вызываемый объект завершает работу, он должен снова вернуться к среде выполнения, чтобы гарантировать, что вызываемый объект не записывает данные, принадлежащие вызывающему объекту. Move, с другой стороны, включает расширенную систему типов, которая позволяет выполнять эти проверки с помощью верификатора байт-кода. Поскольку байт-код Move можно проверить, стоимость проверки оплачивается только один раз, когда модуль загружается в цепочку. Во время выполнения стоимость оплачивается каждый раз, когда транзакция проходит между модулями. Разница по духу аналогична разнице между языком с динамической типизацией, таким как Python, и языком со статической типизацией, например Java. Среда выполнения Solana позволяет писать приложения на языках программирования общего назначения, но это сопряжено со стоимостью проверок во время выполнения при переходе между программами.

В этом предложении делается попытка определить способ внедрения виртуальной машины Move таким образом, чтобы:

- Межмодульные вызовы в Move не требуют межпрограммных проверок среды выполнения.

- Программы Move могут использовать функциональность других программ Solana и наоборот.

- Параллелизм во время выполнения Solana подвергается пакетным транзакциям с перемещением и без него.

## Предложенное решение

### Переместить ВМ как загрузчик Solana

Виртуальная машина Move должна быть встроена в качестве загрузчика Solana под идентификатором «MOVE_PROGRAM_ID», чтобы модули Move могли быть помечены как «исполняемые», а виртуальная машина — в качестве «владельца». Это позволит модулям загружать зависимости модулей, а также позволит параллельно выполнять сценарии Move.

Все учетные записи данных, принадлежащие модулям Move, должны указать своих владельцев для загрузчика `MOVE_PROGRAM_ID`. Поскольку модули Move инкапсулируют данные своей учетной записи так же, как программы Solana инкапсулируют свои данные, владелец модуля Move должен быть встроен в данные учетной записи. Среда выполнения предоставит доступ на запись к Move VM, а Move предоставит доступ к учетным записям модуля.

### Взаимодействие с программами Solana

Чтобы вызывать инструкции в программах, отличных от Move, Солане потребуется расширить виртуальную машину Move с помощью системного вызова `process_instruction()`. Это будет работать так же, как `process_instruction()` программы Rust BPF.