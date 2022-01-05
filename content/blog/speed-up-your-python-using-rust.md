+++
title = "Ускорьте свой Python с помощью Rust"
description = "Ускорьте свой Python с помощью Rust"
weight = 1
+++

[Перевод](https://developers.redhat.com/blog/2017/11/16/speed-python-using-rust) | Автор оригинала: Bruno Rocha

## Что такое Rust?
Rust - это язык системного программирования, который работает невероятно быстро, предотвращает сбои и гарантирует безопасность потоков.

### С участием

- абстракции с нулевой стоимостью
- семантика перемещения
- гарантированная сохранность памяти
- потоки без гонок данных
- дженерики на основе трэйтов
- сопоставление с образцом
- вывод типа
- минимальное время работы
- эффективные привязки C

Описание взято с сайта rust-lang.org.

### Почему это важно для разработчика Python?

Лучшее описание Rust я услышал от Элиаса (члена группы Telegram Rust Brazil).

- Rust - это язык, который позволяет создавать абстракции высокого уровня, но без отказа от низкоуровневого контроля, то есть контроля того, как данные представлены в памяти, контроля над тем, какую потоковую модель вы хотите использовать и т.д.
- Rust - это язык, который обычно может обнаруживать во время компиляции наихудшие ошибки параллелизма и управления памятью (такие как доступ к данным в разных потоках без синхронизации или использование данных после того, как они были освобождены), но дает вам возможность избежать ошибок в этом случае. вы действительно знаете, что делаете.
- Rust - это язык, который, поскольку у него нет среды выполнения, можно использовать для интеграции с любой средой выполнения; вы можете написать собственное расширение на Rust, которое вызывается программой node.js, или программой python, или программой на ruby, lua и т.д., и, однако, вы можете написать сценарий программы на Rust, используя эти языки. - «Элиас Габриэль Амарал да Силва»

Существует множество пакетов Rust, которые помогут вам расширить Python с помощью Rust.

Я могу упомянуть Milksnake, созданную Армином Ронахером (создателем Flask), а также привязки PyO3 The Rust для интерпретатора Python.

#### См. Полный список литературы внизу этой статьи.

Посмотрим на это в действии

Для этого поста я собираюсь использовать Rust Cpython, это единственный, который я тестировал, он совместим со стабильной версией Rust и нашел его простым в использовании.

> PyO3 - это форк rust-cpython, поставляется со многими улучшениями, но работает только с ночной версией Rust, поэтому я предпочел использовать стабильную версию для этого сообщения, в любом случае приведенные здесь примеры должны работать также с PyO3.

Плюсы: легко писать функции на Rust и импортировать их из Python, и, как вы увидите по тестам, это стоит того с точки зрения производительности.

Минусы: для распространения вашего проекта / библиотеки / фреймворка потребуется, чтобы модуль Rust был скомпилирован в целевой системе из-за различий в среде и архитектуре, будет этап компиляции, которого у вас нет при установке библиотек Pure Python, вы может упростить использование rust-setuptools или MilkSnake для встраивания двоичных данных в Python Wheels.

### Python иногда работает медленно

Да, Python известен тем, что в некоторых случаях он «медленный», и хорошая новость заключается в том, что это не имеет особого значения в зависимости от целей и приоритетов вашего проекта. Для большинства проектов эта деталь не будет иметь большого значения.

Однако вы можете столкнуться с редким случаем, когда отдельная функция или модуль занимает слишком много времени и определяется как узкое место в производительности вашего проекта, что часто происходит при синтаксическом анализе строк и обработке изображений.

### Пример

Допустим, у вас есть функция Python, которая выполняет обработку строк, возьмите следующий простой пример подсчета пар повторяющихся символов, но имейте в виду, что этот пример можно воспроизвести с другими функциями обработки строк или любым другим обычно медленным процессом в Python. 

```
# How many subsequent-repeated group of chars are in the given string? 
abCCdeFFghiJJklmnopqRRstuVVxyZZ... {millions of chars here}
  1   2    3        4    5   6
```

Python медленно обрабатывает большие строки, поэтому вы можете использовать pytest-benchmark для сравнения функции Pure Python (с Iterator Zipping) с реализацией Regexp. 

```
# Using a Python3.6 environment
$ pip3 install pytest pytest-benchmark
```

Затем напишите новую программу Python с именем doubles.py 

```py
import re
import string
import random

# Python ZIP version
def count_doubles(val):
    total = 0
    # there is an improved version later on this post
    for c1, c2 in zip(val, val[1:]):
        if c1 == c2:
            total += 1
    return total


# Python REGEXP version
double_re = re.compile(r'(?=(.)\1)')

def count_doubles_regex(val):
    return len(double_re.findall(val))


# Benchmark it
# generate 1M of random letters to test it
val = ''.join(random.choice(string.ascii_letters) for i in range(1000000))

def test_pure_python(benchmark):
    benchmark(count_doubles, val)

def test_regex(benchmark):
    benchmark(count_doubles_regex, val)
```

Запустите pytest для сравнения: 

```
$ pytest doubles.py                                                                                                           
=============================================================================
platform linux -- Python 3.6.0, pytest-3.2.3, py-1.4.34, pluggy-0.4.
benchmark: 3.1.1 (defaults: timer=time.perf_counter disable_gc=False min_roun
rootdir: /Projects/rustpy, inifile:
plugins: benchmark-3.1.1
collected 2 items

doubles.py ..


-----------------------------------------------------------------------------
Name (time in ms)         Min                Max               Mean          
-----------------------------------------------------------------------------
test_regex            24.6824 (1.0)      32.3960 (1.0)      27.0167 (1.0)    
test_pure_python      51.4964 (2.09)     62.5680 (1.93)     52.8334 (1.96)   
-----------------------------------------------------------------------------
```

Для сравнения возьмем Среднее:

- Regexp - 27.0167 <- меньше значит лучше
- Python Zip - 52,8334

## Расширение Python с помощью Rust

### Создайте новый крэйт

crate - так мы называем Rust Packages.

После установки Rust (рекомендуемый способ - https://www.rustup.rs/) Rust также доступен в репозиториях Fedora и RHEL с помощью набора инструментов Rust.

Я использовал rustc 1.21.0

В этой же папке запустите: 

```
cargo new pyext-myrustlib
```

Он создает новый проект Rust в той же папке под названием pyext-myrustlib, содержащий Cargo.toml (Cargo - это менеджер пакетов Rust), а также src/lib.rs (где мы пишем реализацию нашей библиотеки).

### Редактировать Cargo.toml

Он будет использовать крэйт rust-cpython в качестве зависимости и укажет Cargo создать dylib для импорта из Python. 

```toml
[package]
name = "pyext-myrustlib"
version = "0.1.0"
authors = ["Bruno Rocha <rochacbruno@gmail.com>"]

[lib]
name = "myrustlib"
crate-type = ["dylib"]

[dependencies.cpython]
version = "0.1"
features = ["extension-module"]
```

### Редактировать src/lib.rs

Что нам нужно сделать:

1. Импортируйте все макросы из cpython crate.
2. Возьмите типы Python и PyResult из CPython в нашу библиотеку.
3. Напишите реализацию функции count_doubles в Rust, обратите внимание, что она очень похожа на версию Pure Python, за исключением:
    - Он принимает Python в качестве первого аргумента, который является ссылкой на интерпретатор Python и позволяет Rust использовать Python GIL.
    - Получает значение типа &str в качестве ссылки.
    - Возвращает PyResult, который является типом, допускающим возникновение исключений Python.
    - Возвращает объект PyResult в Ok(всего) (Result - это тип перечисления, который представляет либо успех (Ok), либо неудачу (Err)), и поскольку наша функция должна вернуть PyResult, компилятор позаботится о том, чтобы обернуть наш Ok на этом тип. (обратите внимание, что наш PyResult ожидает возвращаемого значения u64).
4. Использование py_module_initializer! Мы регистрируем новые атрибуты в библиотеке, включая __doc__, а также добавляем атрибут count_doubles, ссылающийся на нашу реализацию функции в Rust.
    - Внимание к именам libmyrustlib, initlibmyrustlib и PyInit.
    - Мы тоже пробуем! макрос, который эквивалентен Python'stry .. за исключением.
    - Return Ok(()) -() - это пустой кортеж результатов, эквивалент None в Python. 

```rs
#[macro_use] extern crate cpython;

use cpython::{Python, PyResult};

fn count_doubles(_py: Python, val: &str) -> PyResult<u64> {
    let mut total = 0u64;

    // There is an improved version later on this post
    for (c1, c2) in val.chars().zip(val.chars().skip(1)) {
        if c1 == c2 {
            total += 1;
        }
    }

    Ok(total)
}

py_module_initializer!(libmyrustlib, initlibmyrustlib, PyInit_myrustlib, |py, m | {
    try!(m.add(py, "__doc__", "This module is implemented in Rust"));
    try!(m.add(py, "count_doubles", py_fn!(py, count_doubles(val: &str))));
    Ok(())
});
```

### А теперь построим его с cargo 

```
$ cargo build --release
    Finished release [optimized] target(s) in 0.0 secs

$ ls -la target/release/libmyrustlib*
target/release/libmyrustlib.d
target/release/libmyrustlib.so*  <-- Our dylib is here
```

Теперь давайте скопируем сгенерированную .so lib в ту же папку, где находится наш doubles.py.

> В Fedora вы должны получить. Поэтому в другой системе вы можете получить .dylib, и вы можете переименовать его, изменив расширение на .so. 

```
$ cd ..
$ ls
doubles.py pyext-myrustlib/

$ cp pyext-myrustlib/target/release/libmyrustlib.so myrustlib.so

$ ls
doubles.py myrustlib.so pyext-myrustlib/
```

Наличие myrustlib.so в той же папке или добавление к вашему пути Python позволяет напрямую импортировать его, прозрачно, как это был модуль Python.

### Импорт из Python и сравнение результатов

Отредактируйте свой doubles.py, импортировав нашу версию, реализованную в Rust, и добавив для нее тест. 

```py
import re
import string
import random
import myrustlib   #  <-- Import the Rust implemented module (myrustlib.so)


def count_doubles(val):
    """Count repeated pair of chars ins a string"""
    total = 0
    for c1, c2 in zip(val, val[1:]):
        if c1 == c2:
            total += 1
    return total


double_re = re.compile(r'(?=(.)\1)')


def count_doubles_regex(val):
    return len(double_re.findall(val))


val = ''.join(random.choice(string.ascii_letters) for i in range(1000000))


def test_pure_python(benchmark):
    benchmark(count_doubles, val)


def test_regex(benchmark):
    benchmark(count_doubles_regex, val)


def test_rust(benchmark):   #  <-- Benchmark the Rust version
    benchmark(myrustlib.count_doubles, val)
```

### Бенчмарк

```
$ pytest doubles.py
==============================================================================
platform linux -- Python 3.6.0, pytest-3.2.3, py-1.4.34, pluggy-0.4.
benchmark: 3.1.1 (defaults: timer=time.perf_counter disable_gc=False min_round
rootdir: /Projects/rustpy, inifile:
plugins: benchmark-3.1.1
collected 3 items

doubles.py ...


-----------------------------------------------------------------------------
Name (time in ms)         Min                Max               Mean          
-----------------------------------------------------------------------------
test_rust              2.5555 (1.0)       2.9296 (1.0)       2.6085 (1.0)    
test_regex            25.6049 (10.02)    27.2190 (9.29)     25.8876 (9.92)   
test_pure_python      52.9428 (20.72)    56.3666 (19.24)    53.9732 (20.69)  
-----------------------------------------------------------------------------
```

Для сравнения возьмем Среднее: 

- Rust - 2.6085 <-- чем меньше тем лучше 
- Regexp - 25.8876
- Python Zip - 53.9732

Реализация Rust может быть в 10 раз быстрее, чем Python Regex, и в 21 раз быстрее, чем версия Pure Python.

Интересно, что версия Regex всего в 2 раза быстрее Pure Python :)

     Эти числа имеют смысл только для этого конкретного сценария, для других случаев сравнение может быть другим.

 
## Обновления и улучшения

После публикации этой статьи я получил несколько комментариев о r / python, а также о r / rust.

Вклады поступали в виде запросов на извлечение, и вы можете отправить новый, если считаете, что функции можно улучшить.

Благодаря: Джошу Стоуну мы получили лучшую реализацию для Rust, которая выполняет итерацию строки только один раз, а также эквивалент Python.

Благодаря: Purple Pixie мы получили реализацию Python с использованием itertools, однако эта версия не работает лучше и все еще требует улучшений.

### Итерация только один раз 

```rs
fn count_doubles_once(_py: Python, val: &str) -> PyResult<u64> {
    let mut total = 0u64;

    let mut chars = val.chars();
    if let Some(mut c1) = chars.next() {
        for c2 in chars {
            if c1 == c2 {
                total += 1;
            }
            c1 = c2;
        }
    }

    Ok(total)
}
```

```py
def count_doubles_once(val):
    total = 0
    chars = iter(val)
    c1 = next(chars)
    for c2 in chars:
        if c1 == c2:
            total += 1
        c1 = c2
    return total
```

### Python with itertools

```py
import itertools

def count_doubles_itertools(val):
    c1s, c2s = itertools.tee(val)
    next(c2s, None)
    total = 0
    for c1, c2 in zip(c1s, c2s):
        if c1 == c2:
            total += 1
    return total
```

### Почему не C/C++ / Nim / Go / Ĺua / PyPy / {другой язык}?

Хорошо, это не цель этого поста, этот пост никогда не был о сравнении Rust X с другим языком, этот пост был специально о том, как использовать Rust для расширения и ускорения Python, и тем самым это означает, что у вас есть веская причина для выбора Rust, а не другой язык, или его экосистема, или его безопасность, и инструменты, или просто для того, чтобы следить за шумихой, или просто потому, что вам нравится Rust, не имеет значения, по какой причине, этот пост здесь, чтобы показать, как использовать его с Python.

Я (лично) могу сказать, что Rust является более перспективным, поскольку он новый, и в нем будет много улучшений, в том числе из-за его экосистемы, инструментов и сообщества, а также потому, что я чувствую себя комфортно с синтаксисом Rust, мне он очень нравится!

Итак, как и ожидалось, люди начали жаловаться на использование других языков, и это стало своего рода эталоном, и я думаю, что это круто!

Так что в рамках моего запроса на улучшения некоторые люди из Hacker News также прислали идеи, martinxyz отправил реализацию с использованием C и SWIG, которая работает очень хорошо.

Код C (шаблон swig опущен) 

```cpp
uint64_t count_byte_doubles(char * str) {
  uint64_t count = 0;
  while (str[0] &&str[1]) {
    if (str[0] == str[1]) count++;
    str++;
  }
  return count;
}
```

А наш товарищ из Red Hatter Джош Стоун снова улучшил реализацию Rust, заменив символы байтами, так что это честная конкуренция с C, поскольку C сравнивает байты вместо символов Unicode. 

```rs
fn count_doubles_once_bytes(_py: Python, val: &str) -> PyResult<u64> {
    let mut total = 0u64;

    let mut chars = val.bytes();
    if let Some(mut c1) = chars.next() {
        for c2 in chars {
            if c1 == c2 {
                total += 1;
            }
            c1 = c2;
        }
    }

    Ok(total)
}
```

Есть также идеи сравнить понимание списка Python и numpy, поэтому я включил сюда 

Numpy:

```py
import numpy as np

def count_double_numpy(val):
    ng=np.fromstring(val,dtype=np.byte)
    return np.sum(ng[:-1]==ng[1:])
```

Понимание списка 

```py
def count_doubles_comprehension(val):
    return sum(1 for c1, c2 in zip(val, val[1:]) if c1 == c2)
```

Полный тестовый пример находится в файле test_all.py репозитория.

### Новые результаты

     Имейте в виду, что сравнение было выполнено в той же среде и может иметь некоторые отличия при запуске в другой среде с использованием другого компилятора и/или других тегов. 

```
-------------------------------------------------------------------------------------------------
Name (time in us)                     Min                    Max                   Mean          
-------------------------------------------------------------------------------------------------
test_rust_bytes_once             476.7920 (1.0)         830.5610 (1.0)         486.6116 (1.0)    
test_c_swig_bytes_once           795.3460 (1.67)      1,504.3380 (1.81)        827.3898 (1.70)   
test_rust_once                   985.9520 (2.07)      1,483.8120 (1.79)      1,017.4251 (2.09)   
test_numpy                     1,001.3880 (2.10)      2,461.1200 (2.96)      1,274.8132 (2.62)   
test_rust                      2,555.0810 (5.36)      3,066.0430 (3.69)      2,609.7403 (5.36)   
test_regex                    24,787.0670 (51.99)    26,513.1520 (31.92)    25,333.8143 (52.06)  
test_pure_python_once         36,447.0790 (76.44)    48,596.5340 (58.51)    38,074.5863 (78.24)  
test_python_comprehension     49,166.0560 (103.12)   50,832.1220 (61.20)    49,699.2122 (102.13) 
test_pure_python              49,586.3750 (104.00)   50,697.3780 (61.04)    50,148.6596 (103.06) 
test_itertools                56,762.8920 (119.05)   69,660.0200 (83.87)    58,402.9442 (120.02) 
-------------------------------------------------------------------------------------------------
```

- Новая реализация Rust, сравнивающая байты, в 2 раза лучше, чем старая, сравнивающая символы Unicode.
- Версия Rust по-прежнему лучше, чем C с использованием SWIG
- Rust, сравнивающий символы Unicode, по-прежнему лучше, чем numpy
- Однако Numpy лучше, чем первая реализация Rust, в которой была проблема двойной итерации по символам Unicode.
- Использование понимания списка не имеет существенного значения, чем использование чистого Python

Если вы хотите предложить изменения или улучшения, отправьте PR здесь: https://github.com/rochacbruno/rust-python-example/

## Вывод

Возвращаясь к цели этого поста «Как ускорить ваш Python с помощью Rust», мы начали с:

- Чистая функция Python, занимающая 102 мс.
- Улучшено с помощью Numpy (который реализован в C) до 3 мс.
- Закончилось тем, что Rust занял 1 мс.

В этом примере Rust работал в 100 раз быстрее, чем наш Pure Python.

Rust не спасет вас волшебным образом, вы должны знать язык, чтобы иметь возможность реализовать умное решение, и после правильного внедрения оно стоит столько же, сколько C с точки зрения производительности, а также поставляется с потрясающими инструментами, экосистемой, сообществом и бонусами безопасности.

Возможно, Rust еще не является предпочтительным языком общего назначения по уровню сложности и, возможно, не лучший выбор для написания общих простых приложений, таких как веб-сайты и сценарии автоматизации тестирования.

Однако для определенных частей проекта, где Python, как известно, является узким местом, и ваш естественный выбор будет заключаться в реализации расширения C/C++, написание этого расширения на Rust кажется простым и более удобным в обслуживании.

В Rust еще много улучшений и множество других крэйтов, предлагающих интеграцию Python <--> Rust. Даже если вы не добавляете язык в свой пояс с инструментами прямо сейчас, действительно стоит смотреть в будущее!

## Использованная литература

Фрагменты кода для показанных здесь примеров доступны в репозитории GitHub: https://github.com/rochacbruno/rust-python-example.

Примеры в этой публикации вдохновлены докладом «Расширение Python с помощью Rust» Самуэля Кормье-Ииджимы из Pycon Canada. видео здесь: https://www.youtube.com/watch?v=-ylbuEzkG4M.

Также My Python - это маленький Rust-y Дэна Каллахана из Pycon Montreal. видео здесь: https://www.youtube.com/watch?v=3CwJ0MH-4MA.

Другие ссылки: 

- https://github.com/mitsuhiko/snaek
- https://github.com/PyO3/pyo3
- https://pypi.python.org/pypi/setuptools-rust
- https://github.com/mckaymatt/cookiecutter-pypackage-rust-cross-platform-publish
- http://jakegoulding.com/rust-ffi-omnibus/
- https://github.com/urschrei/polylabel-rs/blob/master/src/ffi.rs
- https://bheisler.github.io/post/calling-rust-in-python/
- https://github.com/saethlin/rust-lather

#### Присоединяйтесь к сообществу:

Присоединяйтесь к сообществу Rust, вы можете найти ссылки на группы в https://www.rust-lang.org/en-US/community.html.

Если вы говорите по-португальски, я рекомендую вам присоединиться к https://t.me/rustlangbr, а на Youtube есть http://bit.ly/canalrustbr.

### Автор

Бруно Роча

- Старший инженер по качеству Red Hat
- Обучение Python и Flask на CursoDePython.com.br
- Сотрудник Python Software Foundation
- Член исследовательской группы RustBR
- Дополнительная информация: http://about.me/rochacbruno и http://brunorocha.org 