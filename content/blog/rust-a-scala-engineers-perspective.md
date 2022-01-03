+++
title = "Rust: взгляд инженера Scala"
description = "Rust: взгляд инженера Scala"
weight = 1
+++

[Перевод](https://beachape.com/blog/2017/05/24/rust-from-scala/)

Приближается 1-я годовщина моей первой строчки кода на Rust, и вот уже пять лет с тех пор, как я написал свою первую строчку кода на Scala. Я подумал, что было бы неплохо резюмировать мой окрашенный в Scala взгляд на The Rust Experience TM через год.

Rust винтовая лестница - Яно де Чезаре

Это не объективное сравнение языка и языка. Я написал этот пост как отчасти для дампа опыта, отчасти как ориентир для других разработчиков Scala, которые изучают или думают об изучении Rust.

## Немного обо мне

Я написал несколько библиотек / инструментов для Rust, а также для Scala. Во всех смыслах я инженер Scala: мне платят за это, и это, безусловно, мой самый сильный язык. Я использовал Rust в нескольких своих побочных проектах (библиотеки и небольшие утилиты).

Что касается Scala, то я являюсь автором enumeratum, который привносит гибкие перечисления и перечисления значений в Scala в виде библиотеки. Я также пробовал писать библиотеки на основе макросов, чтобы сделать такие вещи, как Free Monads и Tagless Final, более удобными в использовании.

Что касается Rust, я написал frunk, набор инструментов для функционального программирования на Rust, который представляет собой примерно порт Shapeless с добавлением котов / скаляза, который делает некоторые довольно забавные вещи с системой типов, о которой я писал в блоге ( 1, 2, 3, 4). Я также написал порт requestb.in на Rust под названием rusqbin на основе Hyper и небольшой асинхронный клиент WIP для служб Microsoft Cognitive под названием cogs.

### Предупреждение

    Я предвзято отношусь к Scala, и я в основном привык к особенностям Scala. Тем не менее, я стараюсь оставаться как можно более нейтральным.
    Когда я говорю о Rust, я имею в виду стабильную версию Rust. Это потому, что я использую только стабильную версию Scala.
    Некоторые вещи, о которых я пишу в отношении Rust, могли измениться к тому времени, когда вы это прочитали. В конце концов, существует постоянная инициатива в области эргономики.

## Обзор

- Вещи, которыми я доволен
    - Батареи в комплекте
    - Система типов
    - Макросы
    - Оптимизация во время компиляции
    - Синтаксис
    - Взаимодействие с C
    - Текущий дух времени
- То, к чему я привык
    - точка с запятой
    - Модель владения: стек против кучи
- Вещи, которые я хотел бы, были разными
    - Асинхронный ввод-вывод
    - струны
    - Кросс-компиляция
    - Странные головные уборы
    - Дай мне
- Вывод

## Вещи, которыми я доволен

### Батарейки в комплекте

Опыт настройки среды разработки с Rust просто потрясающий. Сообщество Rust постаралось максимально упростить начало работы с Rust, и это видно. Буквально одна команда оболочки установит все, что вам нужно.

- rustup для управления вашими инструментами Rust (разные версии / каналы Rust)
- Cargo для управления вашей сборкой и для публикации на crates.io, который, среди прочего, включает:
    - Подкоманда test для запуска тестов
    - Подкоманда Bench для запуска тестов
- rustfmt для форматирования вашего кода (запускается в cargo проектах через Cargo fmt)
- rustdoc для создания красивых сайтов документации.
    - Этот инструмент поддерживает тесты документации без дополнительной настройки / настройки (запускается как часть cargo теста)

Исходя из Scala, то, что все это настроено без суеты прямо на пороге, - это глоток свежего воздуха и большой выигрыш для производительности. Я знаю, что есть причины для более модульного подхода Scala, но я думаю, было бы неплохо, если бы часть этого была перенесена на Scala на других языках. 

#### Редактор / IDE

Когда я впервые начал работать с Rust, я использовал IntelliJ и его плагин Rust, но позже переключился на Microsoft Studio Code с плагином Rust, который очень хорошо взаимодействует с Rust Language Server (устанавливается как компонент инструментальной цепочки rustup). Он кажется очень легким и предлагает всю необходимую мне помощь.

### Система типов

Если вы больше склоняетесь к парадигме функционального программирования Scala, то вам, вероятно, понравится следующее о системе типов Rust:

- Нет наследования для типов данных (есть нижний тип, но он используется гораздо экономнее)
- Нет всеобщего равенства
- Нет нулей
- Трэйты - это в основном классы типов Haskell.
- Еще много основных типов (вместо Int есть i8, i16, i32, i64, isize, а также u8, u16…)

По сути, Rust имеет много хороших черт в системе типов Scala. Одна вещь, которой сейчас не хватает в Rust, - это первоклассная поддержка высокодородных типов (HKT), которую, честно говоря, я не упускаю слишком много, потому что:

1. Есть способы до некоторой степени подражать этому.
2. Модель владения / памяти Rust имеет тенденцию подталкивать вас к более детальному размышлению о ваших ценностях / ссылках, что, возможно, противоречит типу программирования, обычно использующему абстракции на основе HKT.

Если это все еще звучит неприемлемо, просто знайте, что вы можете довольно далеко продвинуться в создании повторно используемых абстракций, используя трэйты Rust + связанные типы, а порт быстрой проверки BurnSushi доступен для написания и применения законов.

Также в разработке есть несколько интересных вещей:

1. Высший родственный полиморфизм
2. Пи (значения) типы

Добавление функциональности с использованием свойств Rust должно быть привычной областью, если вы писали на Scala вещи, похожие на классы типов. Фактически, система свойств Rust намного больше похожа на систему классов типов Haskell, чем на Scala, что имеет свои плюсы и минусы (например, отсутствие определения реализаций для данного типа). Я написал введение / руководство по системе трейтов Rust в другом посте.

#### Вывод типа

И Rust, и Scala имеют локальный вывод типов, и в целом они работают примерно одинаково. В обоих из них вам нужно написать типы для параметров вашей функции. В Scala вы можете оставить возвращаемый тип отключенным и позволить компилятору вывести его за вас, в Rust вы не можете этого сделать (если вы его не укажете, предполагается, что это(), unit).

### Макросы

Макросистема Rust, хотя и менее мощная, чем Scala, весьма полезна для сохранения СУХОГО кода и, что немаловажно, очень хорошо интегрируется с остальным языком. Фактически, он включен и доступен из коробки без каких-либо дополнительных зависимостей / флагов.

По сравнению с макросами Scala макросы Rust кажутся очень естественной частью языка, и вы довольно часто будете сталкиваться с ними при чтении / использовании библиотек Rust. В базах кода Rust вы часто будете видеть макросы, объявленные и немедленно используемые для генерации кода (например, получение реализаций трэйтов для списка числовых типов или для кортежей до N элементов), что обычно делают пользователи Scala. внеполосный », подключившись к SBT и используя другой шаблонизатор или инструмент на основе AST.

С другой стороны, в Scala обычно звучит рефрен «не пишите макросы, если вам не нужно». Когда я сравниваю подходы, выбранные двумя языками, я чувствую, что Scala, возможно, был чрезмерно амбициозным с точки зрения предоставления разработчикам возможностей, что привело к устареванию API-интерфейсов, которые нельзя поддерживать из-за сложности. Действительно, набор инструментов для метапрограммирования Scala переживает еще одну реформу с переходом на Scalameta.

Из-за своей простоты (макросы работают на основе ряда шаблонов) API макросов Rust сначала может показаться ограничивающим, но если вы будете его придерживаться, вы, вероятно, обнаружите, что можете добиться большего, чем вы изначально думали. Например, тот факт, что вы можете рекурсивно создавать / реструктурировать аргументы макроса (!) И снова вызывать макрос (или даже вызывать другой макрос), является довольно мощным инструментом.

При этом, в дополнение к устаревшей системе макросов, Rust скоро получит процедурные макросы, которые больше похожи на то, что разработчики Scala привыкли видеть. Вы можете получить представление о том, на что похожи процедурные макросы, посмотрев на настраиваемые производные, которые я использовал для реализации производных для LabelledGeneric в Rust. 

### Оптимизация во время компиляции

Думаю, ни для кого не новость, что Rust быстр и эффективен. На домашней странице официального сайта говорится, что он работает «невероятно быстро» и содержит «абстракции с нулевой стоимостью», а сторонники Rust громко опровергли поражение Rust над GCC-C в k-нуклеотиде несколько месяцев назад. Даже если вы не полностью согласны с частью «быстрее, чем C», не будет большим скачком сказать, что производительность Rust находится на том же уровне, что и C, или, по крайней мере, нет причин, по которым это не так (да , язык и реализация разные, компиляторы имеют значение и т. д.).

Меня особенно впечатлила способность компилятора Rust (хотя я не уверен, что это LLVM?) Возможность компилировать абстракции, так что выполняемые ими операции не имеют накладных расходов. В качестве личного анекдота, когда я писал LabelledGeneric на frunk, я ожидал, что будет некоторая разница в производительности между использованием этой абстракции для преобразований между структурами и записью преобразований вручную (с использованием From). В конце концов, есть немаловажные различия в его версии Shapeless в мире Scala (тестовый код): 

```
// JMH benchmark results

[info] Benchmark                               Mode  Cnt     Score     Error  Units
[info] Benchmarks.from24FieldsManual           avgt   30    33.626 ±   1.032  ns/op
[info] Benchmarks.from24FieldsShapeless        avgt   30  4443.018 ± 101.612  ns/op
[info] Benchmarks.from25FieldsManual           avgt   30    33.066 ±   0.650  ns/op
[info] Benchmarks.from25FieldsShapeless        avgt   30  4859.432 ± 104.763  ns/op
```

К моему удивлению, Rust сумел скомпилировать основанное на LabelledGeneric, нетривиальное, многошаговое, неоптимизированное (кроме использования стека, никаких усилий) преобразование структур между структурами в абстракцию с нулевой стоимостью. То есть использование LabelledGeneric для преобразования добавляет нулевые накладные расходы по сравнению с записью преобразования вручную (тестовый код): 

```
// Cargo benchmark results

test from_24fields_manual           ... bench:         109 ns/iter (+/- 49)
test from_24fields_labelledgeneric  ... bench:         104 ns/iter (+/- 24)
test from_25fields_manual           ... bench:         129 ns/iter (+/- 9)
test from_25fields_labelledgeneric  ... bench:         131 ns/iter (+/- 13)
```

Picture: ['Mind Blown']

Примечание: тесты Rust и Scala LabelledGeneric не являются полностью однозначными (версия Rust должна создавать экземпляры новых исходных объектов при каждом запуске из-за семантики перемещения), но они иллюстрируют разницу в производительности между преобразованием на основе LabelledGeneric и рукописным преобразованием в двух языков.

### Синтаксис

В целом синтаксис Rust очень похож на синтаксис Scala. Конечно, кое-где есть небольшие корректировки (пусть и пусть mut vs var и val, вы будете использовать угловые скобки вместо квадратных и т. Д.), Но в целом языки кажутся очень похожими, потому что они оба являются C-подобными языками. которые в значительной степени вдохновлены машинным обучением.

Люди Scala, вероятно, будут радоваться таким вещам, как доступность перечисления (скоро появится в Scala через Dotty), а также частичное деструктурирование (например, при условии, что структура Point {x: i32, y: 32}, вы можете позволить Point {x, .. } = p;).

Есть несколько вещей, которые вначале вы упустите только из-за мышечной памяти, но они либо реализованы в виде библиотек, либо немного по-другому, например, ленивые значения (Rust-ленивый или ленивый-статический) и такие методы, как опция foreach. (попробуйте if let Some(x) = myOption {/ * вместо этого используйте x * /}). Другие просто отсутствуют, такие как параметры по имени (для меня не слишком большая проблема), для понимания / do и аргументы ключевого слова (последние два вредны).

О, в Rust типы и трейты называются так же, как в Scala, в CamelCase, но идентификаторы (привязки и методы) используют snake_case, который, как мне кажется, заставляет код выглядеть длиннее, но это не большая проблема. Вы найдете ссылки, которые могут помочь, если вы не уверены, и, скорее всего, вы все равно почерпнете это из чтения кода библиотеки.

Как и в случае со Swift, мне не удалось найти убедительных доказательств или признания того, что Scala оказала какое-либо влияние на Rust ...

### Взаимодействие с C

Rust максимально упрощает работу с C, придерживаясь при этом своей мантры - обеспечивать безопасность. Для справки взгляните на раздел в книге Rust, посвященный FFI. 

```rust
// Taken from the Rust book
#[link(name = "snappy")]
extern {
    fn snappy_max_compressed_length(source_length: size_t) -> size_t;
}

let x = unsafe { snappy_max_compressed_length(100) };
```

Синтаксис может показаться знакомым тем, кто играл со Scala.Native. 

```scala
// Taken from Scala Native homepage
@extern object stdlib {
  def malloc(size: CSize): Ptr[Byte] = extern
}

val ptr = stdlib.malloc(32)
```

Поскольку вызов C-кода может быть небезопасным (с учетом памяти, потокобезопасности), Rust требует, чтобы вы обернули ваши C-вызовы в небезопасные. Если вы хотите скрыть это от пользователей, вы можете обернуть эти вызовы в другую функцию. 

```rust
// Taken from the Rust book
pub fn validate_compressed_buffer(src: &[u8]) -> bool {
    unsafe {
        snappy_validate_compressed_buffer(src.as_ptr(), src.len() as size_t) == 0
    }
}
```

Вызов кода Rust из C также очень удобен, что в Scala Native еще предстоит реализовать.

### Текущий дух времени

Текущее «ощущение» Rust и его сообщества (или сообществ, поскольку библиотеки / фреймворки могут иметь свои собственные) очень гостеприимны и полезны. Кроме того, это очень сложно дать количественную оценку, поэтому я просто перечислю несколько наблюдений:

- Стабилке Rust всего 2 года, и все же существует официальная инициатива по эргономике для уменьшения трения
- Я задавал несколько вопросов о StackOverflow и каждый раз получал быстрые и полезные ответы.
- Rust - язык №1 «самый любимый» в опросе StackOverflow за 2017 год.
- Rust чувствует себя очень управляемым сообществом: у него очень живое репозиторий RFC, и с тех пор, как я начал возиться с ним, я увидел, как минимум 3 RFC, встроенных в язык (макросы типа, пользовательские производные и синтаксис? Для Trys).

## То, к чему я привык

### Точка с запятой

В Scala точки с запятой не обязательны, и почти все является выражением и, следовательно, возвращает значения. 

```scala
3 // returns 3

val x = 3 // assignment, returns unit

// certain things don't return anything though, such as import
// statements, and blocks

import com.beachape._ // returns nothing
object Hello {} // returns nothing
```

В Rust точки с запятой не являются обязательными и важны. Операторы, которые заканчиваются точкой с запятой return() (unit), и те, которые не превращаются в выражения и, таким образом, возвращают значение. 

```rust
// taken from the Rust book

let x = 5u32; // this is a statement

let y = {
    let x_squared = x * x;
    let x_cube = x_squared * x;

    // This expression will be assigned to `y`
    x_cube + x_squared + x
};

let z = {
    // The semicolon suppresses this expression and `()` is assigned to `z`
    2 * x;
};
```

### Модель владения: стек против кучи

Модель памяти / владения Rust для меня - его главная фишка-убийца; это дает вам более жесткий контроль над тем, как ваша программа потребляет память, сохраняя при этом безопасность памяти, и все это без необходимости поставлять сборщик мусора со средой выполнения. Вы сами решаете, передавать ли вещи по значению или по ссылке, а также изменяемость привязок (в том числе при сопоставлении с образцом).

Существует также вопрос о том, где размещаются вещи. В Scala (и, возможно, в большинстве языков на основе JVM) существует набор правил, которые определяют, будет ли что-то помещено в стек или в кучу (и, таким образом, повлечет за собой будущие затраты на сборку мусора). В общем, единственный раз, когда что-то выделяется в стеке, - это примитивы, которые не экранируют методы как поля объектов, и ссылки на объекты, которые сами выделяются в куче. В среде выполнения могут быть забавные уловки, такие как анализ экранирования, но в целом вы не можете выбирать.

В Rust вы можете размещать объекты в куче, создавая их экземпляры внутри (или передавая право собственности) структурам данных, таким как Boxes, Vecs и т. Д. Или вы можете работать с простыми значениями. Вы можете выбрать свою абстракцию в зависимости от стоимости, которую вы хотите заплатить за функции, и гарантий, которые они предлагают, таких как безопасный многопоточный доступ (эта страница является отличным ориентиром). В любом случае система владения Rust во время компиляции будет следить за тем, чтобы у вас не возникало скачков данных, вызванных, например, изменением голых значений в разных потоках без контроля доступа.

Scala не предоставляет пользователям такой же уровень контроля, поэтому, естественно, необходимо внести некоторые изменения. Однако, вопреки опыту некоторых других, я не обнаружил, что вопросы владения слишком сложны для понимания и привыкания. Опыт работы с системой богатых типов Scala означал, что с аннотациями времени жизни было довольно легко справиться. Может быть, изучение C и C ++ на курсах Comsci в университете тоже помогло.

- Примечание: если вы человек наполовину налитый стаканом, я думаю, вы можете сказать, что Rust вынуждает вас взять на себя управление, а не дает вам контроль. Все дело в перспективе ...
- Примечание 2: если вы обнаружите, что делаете много .clone(), чтобы избавиться от компилятора, возможно, вы делаете что-то не совсем правильно.

#### Изменчивость

Отдельно стоит упомянуть мутабильность. Если вы пришли из многолетнего использования Scala (или почти любого другого языка, который подчеркивает неизменность и ссылочную прозрачность как путь к просветлению), написание вашего первого let mut или &mut self может показаться грязным.

Мне потребовалось время, чтобы привыкнуть к этой идее, но эй, когда в Риме, верно? Если это помогает, помните, что Rust ориентирован на скорость и эффективность за счет (почти или фактически) абстракций с нулевой стоимостью и что благодаря его строгой модели владения гонка данных из-за изменчивости не является проблемой. 

## Вещи, которые я хотел бы, были разными

### Асинхронный ввод-вывод

В Scala большинство фреймворков, которые имеют дело с любым типом ввода-вывода, включают неблокирующий ввод-вывод, используя какой-то тип данных оболочки, такой как Future [A], Task [A] или IO [A] (обычно монада), который отделяет описание вашей программы от ее выполнения и определяет по типу эффект общения с пугающим и грязным внешним миром. Это позволяет вам не блокировать выполняющийся поток в ожидании каких-либо событий (например, возврата данных), выбирая подходящую стратегию выполнения.

В стране Rust большинство широко используемых библиотек, которые я видел, таких как клиент Redis и Hyper (и все различные вещи, построенные на нем, такие как Rusoto, Rocket и т. Д.), Все блокируют. Хотя это нормально работает для таких вещей, как однопользовательские утилиты, это неоптимально для приложений, которые требуют большого количества операций ввода-вывода и должны обслуживать большое количество одновременных пользователей, потому что потоки вашего приложения могут быть связаны, просто ожидая данных, что делает его неспособным обслуживать другие Запросы. Или вы получите потенциально огромные пулы потоков (в стиле старых приложений Java Servlet ...), что, похоже, идет вразрез с духом эффективности Rust.

При этом я знаю, что в этой области делаются успехи:

- Tokio, «токенизированный ввод-вывод», фреймворк асинхронного ввода-вывода, который предоставляет API на основе будущего, добивается большого прогресса. Выглядит готовым к производству.
- Hyper, структура HTTP-клиент-сервер по умолчанию, скоро вырастет до 0.11, что принесет с собой Futures-based API, основанный на Tokio. Это, скорее всего, (я надеюсь) перейдет к любым библиотекам, основанным на Hyper.

Кроме того, на данный момент трудно преобразовывать и возвращать Futures из функций, потому что каждое преобразование приводит к тому, что конкретный тип вашего объекта связывается и помечается произвольным типом замыкания. Поскольку запись типа результата не является необязательной в Rust, текущее решение состоит в том, чтобы объявить ваш возвращаемый тип как Box<Future <A>>, но он менее эффективен во время выполнения, поскольку упакованные в штучные объекты типажи требуют динамической отправки и распределения кучи. Надеюсь, что скоро будет выпущен «impl Trait» для решения этой проблемы (отслеживание RFC)

### Строки

В Rust есть несколько способов представления строк. Вот несколько:

- Строковое значение строки времени выполнения, содержимое которого размещено в куче
- &'строка с временем жизни
    - &'статическая строка str со статическим временем жизни (запеченная в вашем двоичном файле)
- Vec <u8>

Хотя к настоящему времени я в основном привык к этому и понимаю цель наличия каждого из них, я надеюсь, что инициатива в области эргономики поможет лучше понять эту ситуацию, поскольку строки настолько распространены. Как? Понятия не имею ... может, я просто разглагольствую.

### Кросс-компиляция

Очевидно, что разработчики Scala привыкли один раз компилировать и запускать одни и те же двоичные файлы повсюду благодаря JVM (в основном: p). Хотя я не ожидаю того же от Rust, потому что он компилируется в собственный машинный код, я бы хотел, чтобы инструменты кросс-компиляции были лучше из коробки (например, как в Golang).

На данный момент, в зависимости от целевой платформы, кросс-компиляция для Rust немного задействована, и есть несколько вариантов:

1. Добавление целевой цепочки инструментов через Rustup и, возможно, установка еще нескольких пакетов специально для вашей целевой платформы (как в этом руководстве)
2. Использование предварительно созданного контейнера Docker, который содержит все настройки / переменные среды / установки, необходимые для компиляции на вашу целевую платформу (см. Rust-on-raspberry-docker)
3. Использование кросс-грузового инструмента, который, кажется, автоматизирует 2.

Мой вариант использования строится для моего Raspberry Pi, и я пробовал только первые два, но последний выглядит здесь победителем, и было бы здорово увидеть что-то подобное, включенное по умолчанию как часть rustup или cargo. 

### Странные головные уборы

Вот несколько вещей, которые я до сих пор не совсем понимаю:

#### Нужен ли реф?

На мой взгляд, ref излишне сбивает с толку. Насколько я могу судить, он в основном используется для привязки указателей во время сопоставления с образцом. 

```scala
match some_int {
  // Why not Some(&s) => ... ???
  Some(ref s) => println!("{}",s),
  None => unreachable!()
}
```

#### &mut

Почему мне нужно делать &mut вместо просто &? 

```rust
// This uses mut for no reason other than to prove a point.
fn non_empty(s: &mut String) -> bool { s.len() > 0 }

let mut string = "hello".to_string();
hello(&mut string); // why can't this just be hello(&string) ??
```

#### Определение продолжительности жизни с брекетами

Мне каким-то образом удалось закодировать свой путь в тупик при использовании RWLock, потому что поведение фигурных скобок {} при использовании фигурных скобок {} при использовании с сопоставлением с образцом, на мой взгляд, не является интуитивно понятным. Если интересно, подробнее в этом выпуске.

### Дай мне

Я знаю, что эти вещи находятся в разработке, но я бы хотел, чтобы они были в Rust вчера:

1. Высшие родственные типы
2. «Специализация», то есть поиск наиболее конкретной реализации трэйта в соответствии с типом значения в месте вызова. Прямо сейчас, если вы реализуете типаж Rust для A, он конфликтует с любой другой реализацией, которую вы пишете. Специализация должна исправить это (отслеживание RFC)
3. REPL. Есть Русти, но я думаю, что Rust упускает хитрость, не поставляя ни одной готовой игры, особенно когда у нее такая сильная игра для разработки и настройки.
4. Что-то вроде do или для понимания работы с типами контейнеров (есть библиотеки, но встроенные было бы неплохо)

## Вывод

На этом я завершаю свой взгляд на использование Rust с точки зрения разработчика Scala через год, в 2017 году. В целом я очень рад, что год назад я решил изучить Rust. Это была веселая и захватывающая поездка: какое-то время мне казалось, что каждые несколько месяцев я получал новые игрушки, которые я мог немедленно использовать: макросы типов и настраиваемые производные изменили правила игры, потому что они сделали эргономичным писать типы Hlist вручную и сделали Generic / LabelledGeneric Practical соответственно.

В целом, я считаю, что в Rust есть много чего, что может понравиться инженерам Scala. Сообщество дружелюбное и разнообразное, так что вы можете легко найти библиотеку, которая вам интересна и в которой можно поучаствовать (бесстыдный плагин: вклад в frunk всегда приветствуется). Или вы можете создать свой собственный побочный проект и написать небольшую системную утилиту или запрограммировать микроконтроллер; онлайн-ресурсы очень легко найти. В любом случае, с Rust действительно сложно начать! 