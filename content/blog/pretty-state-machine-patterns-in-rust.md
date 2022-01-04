+++
title = "Симпатичные шаблоны конечного автомата в Rust"
description = "Симпатичные шаблоны конечного автомата в Rust"
weight = 1
+++

[Перевод](https://hoverbear.org/blog/rust-state-machine-pattern/) | Автор оригинала: xxxx

В последнее время я много думал о шаблонах и структурах, с помощью которых мы программируем. Действительно замечательно начать изучать проект и увидеть знакомые шаблоны и стили, которые вы уже использовали раньше. Это упрощает понимание проекта и дает вам возможность быстрее начать работу над ним.

Иногда вы работаете над новым проектом и понимаете, что вам нужно сделать что-то так же, как вы это делали в другом проекте. Это может быть не функциональность или библиотека, это может быть не то, что вы можете закодировать в какой-нибудь умный макрос или небольшой крэйт. Вместо этого это может быть просто шаблон или структурная концепция, которая хорошо решает проблему.

Один интересный паттерн, который обычно применяется к проблемам, - это паттерн «Конечный автомат». Давайте посмотрим, что именно мы имеем в виду, когда говорим это, и почему они интересны.

> В этом посте вы можете запускать все примеры на игровой площадке, я обычно по привычке использую Nightly.

## Основание наших концепций

В Интернете есть много ресурсов и тематических статей о конечных автоматах. Более того, существует множество реализаций конечных автоматов.

Чтобы попасть на эту веб-страницу, вы использовали ее. Вы можете смоделировать TCP как конечный автомат. Вы также можете моделировать HTTP-запросы. Вы можете смоделировать любой обычный язык, например регулярное выражение, как конечный автомат. Они повсюду, прячутся внутри вещей, которыми мы пользуемся каждый день.

Итак, конечный автомат - это любой «автомат», между которыми определен набор «состояний» и «переходов».

Когда мы говорим о машине, мы имеем в виду абстрактную концепцию чего-то, что что-то делает. Например, ваше «Hello World!» функция - это машина. Он запускается и в конечном итоге выводит то, что мы ожидаем. Некоторая модель, которую вы используете для взаимодействия с вашей базой данных, точно такая же. Мы будем рассматривать нашу самую простую машину просто как структуру, которую можно создавать и уничтожать. 

```rust
struct Machine;

fn main() {
  let my_machine = Machine; // Create.
  // `my_machine` is destroyed when it falls out of scope below.
}
```

Состояния - это способ понять, где находится машина в своем процессе. Например, в качестве примера можно представить машину для розлива бутылок. Машина находится в состоянии ожидания, когда ожидает новую бутылку. Как только он обнаруживает бутылку, он переходит в состояние «наполнения». Обнаружив, что бутылка заполнена, она переходит в состояние «готово». После того, как бутылка оставлена, автомат возвращается в состояние ожидания.

Ключевой вывод здесь заключается в том, что ни у одного из штатов нет информации, относящейся к другим штатам. Состояние «заполнение» не заботится о том, как долго ожидало состояние «ожидания». Состояние «Готово» не заботится о том, с какой скоростью была заполнена бутылка. У каждого государства есть отдельные обязанности и заботы. Эти варианты естественным образом рассматривать в виде перечисления. 

```rust
enum BottleFillerState {
  Waiting { waiting_time: std::time::Duration },
  Filling { rate: usize },
  Done,
}

struct BottleFiller {
  state: BottleFillerState,
}
```

Использование перечисления таким образом означает, что все состояния являются взаимоисключающими, вы можете быть только в одном за раз. «Толстые перечисления» Rust позволяют нам иметь каждое из этих состояний для переноса данных с собой. Что касается нашего текущего определения, все в порядке.

Но здесь есть небольшая проблема. Когда мы описывали нашу машину для розлива бутылок выше, мы описали три перехода: Ожидание -> Наполнение, Наполнение -> Готово и Готово -> Ожидание. Мы никогда не описывали «Ожидание» -> «Готово» или «Готово» -> «Заполнение», это не имеет смысла!

Это подводит нас к идее переходов. Одна из самых приятных особенностей настоящего конечного автомата - это то, что нам никогда не придется беспокоиться о том, что наш автомат для бутылок перейдет, например, из Готово -> Наполнение. Шаблон конечного автомата должен гарантировать, что этого никогда не может произойти. В идеале это должно быть сделано до того, как мы начнем запускать нашу машину, во время компиляции.

Давайте еще раз посмотрим на переходы, которые мы описали для нашего наполнителя бутылок на схеме: 

```
  +++++++++++   +++++++++++   ++++++++
  |         |   |         |   |      |
  | Waiting +-->+ Filling +-->+ Done |
  |         |   |         |   |      |
  ++++-++++-+   +++++++++++   +--+++++
       ^                         |
       +++++++++++++++++++++++++-+
```

Как мы видим, здесь существует конечное число состояний и конечное число переходов между этими состояниями. Теперь можно иметь допустимый переход между каждым состоянием и любым другим состоянием, но в большинстве случаев это неверно.

Это означает, что переход от состояния, такого как «Ожидание», к состоянию, например, «Заполнение», должен иметь определенную семантику. В нашем примере это можно определить как «Бутылка на месте». В случае потока TCP это может быть «Мы получили пакет FIN», что означает, что нам нужно завершить закрытие потока.

## Определение того, чего мы хотим

Теперь, когда мы знаем, что такое конечный автомат, как нам представить их в Rust? Сначала давайте подумаем, чего мы хотим от какого-нибудь паттерна.

В идеале мы хотели бы видеть следующие характеристики:

- Одновременно может находиться только в одном состоянии.
- Каждое состояние должно иметь свои собственные связанные значения, если требуется.
- Переход между состояниями должен иметь четко определенную семантику.
- Должна быть возможность иметь некоторый уровень общего состояния.
- Должны быть разрешены только явно определенные переходы.
- Переход из одного состояния в другое должно поглотить состояние, чтобы его больше нельзя было использовать.
- Нам не нужно выделять память для всех состояний. Конечно, не больше, чем самый крупный штат
- Любые сообщения об ошибках должны быть понятными.
- Для этого нам не нужно прибегать к выделению кучи. В стеке должно быть возможно все.
- Систему шрифтов нужно использовать с максимальной эффективностью.
- Во время компиляции должно быть как можно больше ошибок.

Так что, если бы у нас был шаблон дизайна, который позволял бы все эти вещи, было бы поистине фантастически. Было бы неплохо иметь шаблон, который позволял бы большинство из них.
Изучение возможных вариантов реализации

С такой мощной и гибкой системой типов, как Rusts, мы сможем это представить. Истина в том, что есть несколько способов попробовать, каждый из них обладает ценными качествами, и каждый преподает нам уроки.

## Второй снимок с перечислениями

Как мы видели выше, наиболее естественный способ попробовать это перечисление, но мы уже отметили, что вы не можете контролировать, какие переходы фактически разрешены в этом случае. Так можем ли мы просто обернуть это? Мы уверены, что сможем! Давайте взглянем:

```rust
enum State {
    Waiting { waiting_time: std::time::Duration },
    Filling { rate: usize },
    Done
}

struct StateMachine { state: State }

impl StateMachine {
    fn new() -> Self {
        StateMachine {
            state: State::Waiting { waiting_time: std::time::Duration::new(0, 0) }
        }
    }
    fn to_filling(&mut self) {
        self.state = match self.state {
            // Only Waiting -> Filling is valid.
            State::Waiting { .. } => State::Filling { rate: 1 },
            // The rest should fail.
            _ => panic!("Invalid state transition!"),
        }
    }
    // ...
}

fn main() {
    let mut state_machine = StateMachine::new();
    state_machine.to_filling();
}
```

На первый взгляд все нормально. Но заметили какие-то проблемы?

- Во время выполнения происходят недопустимые ошибки перехода, что ужасно!
- Это предотвращает только недопустимые переходы за пределы модуля, поскольку частными полями можно свободно манипулировать внутри модуля. Например, state_machine.state = State::Done совершенно корректно внутри модуля.
- Каждая функция, которую мы реализуем, которая работает с состоянием, должна включать оператор соответствия!

Однако у него есть некоторые хорошие характеристики:

- Память, необходимая для представления конечного автомата, составляет только размер самого большого состояния. Это связано с тем, что размер толстого перечисления равен размеру его самого большого варианта.
- Все происходит в стеке.
- Переход между состояниями имеет четко определенную семантику ... Он либо работает, либо вылетает!

Теперь вы можете подумать: «Hoverbear, вы могли бы полностью обернуть вывод to_filling() с помощью Result<T, E> или иметь вариант InvalidState!» Но давайте посмотрим правде в глаза: это не делает ничего лучше, если вообще делает. Даже если мы избавимся от сбоев во время выполнения, нам все равно придется иметь дело с большой неуклюжестью с операторами сопоставления, и наши ошибки все равно будут обнаруживаться только во время выполнения! Фу! Обещаю, мы сможем добиться большего.

Так что продолжим поиски!

## Структуры с переходами

Так что, если бы мы просто использовали набор структур? Мы могли бы заставить их всех реализовать трэйты, которые должны разделять все государства. Мы могли бы использовать специальные функции, которые переводили тип в новый тип! Как бы это выглядело? 

```rust
// This is some functionality shared by all of the states.
trait SharedFunctionality {
    fn get_shared_value(&self) -> usize;
}

struct Waiting {
    waiting_time: std::time::Duration,
    // Value shared by all states.
    shared_value: usize,
}
impl Waiting {
    fn new() -> Self {
        Waiting {
            waiting_time: std::time::Duration::new(0,0),
            shared_value: 0,
        }
    }
    // Consumes the value!
    fn to_filling(self) -> Filling {
        Filling {
            rate: 1,
            shared_value: 0,
        }
    }
}
impl SharedFunctionality for Waiting {
    fn get_shared_value(&self) -> usize {
        self.shared_value
    }
}

struct Filling {
    rate: usize,
    // Value shared by all states.
    shared_value: usize,
}
impl SharedFunctionality for Filling {
    fn get_shared_value(&self) -> usize {
        self.shared_value
    }
}

// ...

fn main() {
    let in_waiting_state = Waiting::new();
    let in_filling_state = in_waiting_state.to_filling();
}
```

Трэйт возьми, это банальный код! Идея заключалась в том, чтобы у всех государств были общие общие ценности, а также их собственные особые ценности. Как видно из функции to_filling(), мы можем использовать заданное состояние «Ожидание» и перевести его в состояние «Заполнение». Сделаем небольшое изложение:

- Ошибки перехода обнаруживаются во время компиляции! Например, вы даже не можете случайно создать состояние заполнения, не запустив сначала состояние ожидания. (Можно было бы специально, но это не относится к делу.)
- Правоприменение в переходный период происходит повсюду.
- Когда выполняется переход между состояниями, старое значение используется вместо только что измененного. Мы могли бы сделать это и с приведенным выше примером перечисления.
- Нам не обязательно все время совпадать.
- Потребление памяти по-прежнему невысокое, в любой момент времени размер такой же, как у состояния.

Однако есть и недостатки:

- Есть куча повторов кода. Вы должны реализовать одни и те же функции и характеристики для нескольких структур.
- Не всегда понятно, какие ценности разделяют все государства и только одно. - Из-за этого обновление кода позже могло быть проблемой.
- Поскольку размер состояния является переменным, нам приходится заключать его в перечисление, как указано выше, чтобы его можно было использовать, когда конечный автомат является просто одним из компонентов более сложной системы. Вот как это может выглядеть: 

```rust
enum State {
    Waiting(Waiting),
    Filling(Filling),
    Done(Done),
}

fn main() {
    let in_waiting_state = State::Waiting(Waiting::new());
    // This doesn't work since the `Waiting` struct is wrapped! We need to `match` to get it out.
    let in_filling_state = State::Filling(in_waiting_state.to_filling());
}
```

Как видите, это не очень эргономично. Однако мы приближаемся к тому, чего хотим. Идея перехода между разными типами кажется хорошим шагом вперед! Прежде чем мы попробуем что-то совершенно другое, давайте поговорим о простом способе изменить наш пример, который может пролить свет на дальнейшее мышление.

Стандартная библиотека Rust определяет две тесно связанные трэйты: From и Into, которые чрезвычайно полезны и заслуживают внимания. Важно отметить, что реализация одного из них автоматически реализует другой. В целом реализация From предпочтительнее, так как она немного более гибкая. Мы можем очень легко реализовать их в нашем примере, приведенном выше, вот так: 

```rust
// ...
impl From<Waiting> for Filling {
    fn from(val: Waiting) -> Filling {
        Filling {
            rate: 1,
            shared_value: val.shared_value,
        }
    }
}
// ...
```

Это не только дает нам общую функцию для перехода, но также приятно читать об этом в исходном коде! Это снижает умственную нагрузку на нас и облегчает понимание читателями. Вместо реализации пользовательских функций мы просто используем уже существующий шаблон. Построение нашего паттерна на основе уже существующих паттернов - отличный путь вперед.

Так что это круто, но как нам справиться со всем этим неприятным повторением кода и повторяющимися вещами shared_value? Давайте изучим еще немного!

## В целом изысканность

В этом приключении мы объединим уроки и идеи из первых двух, а также несколько новых идей, чтобы получить что-то более приятное. Суть этого в том, чтобы использовать возможности дженериков. Давайте посмотрим на довольно простую структуру, представляющую это: 

```rust
struct BottleFillingMachine<S> {
    shared_value: usize,
    state: S
}

// The following states can be the 'S' in StateMachine<S>

struct Waiting {
    waiting_time: std::time::Duration,
}

struct Filling {
    rate: usize,
}

struct Done;
```

Итак, здесь мы фактически встраиваем состояние в сигнатуру типа самой BottleFillingMachine. Конечный автомат в состоянии «Наполнение» - это BottleFillingMachine <Filling>, что просто замечательно, поскольку это означает, что когда мы видим его как часть сообщения об ошибке или чего-то еще, мы сразу знаем, в каком состоянии находится машина.

Оттуда мы можем продолжить и реализовать From<T> для некоторых из этих конкретных универсальных вариантов, например: 

```rust
impl From<BottleFillingMachine<Waiting>> for BottleFillingMachine<Filling> {
    fn from(val: BottleFillingMachine<Waiting>) -> BottleFillingMachine<Filling> {
        BottleFillingMachine {
            shared_value: val.shared_value,
            state: Filling {
                rate: 1,
            }
        }
    }
}

impl From<BottleFillingMachine<Filling>> for BottleFillingMachine<Done> {
    fn from(val: BottleFillingMachine<Filling>) -> BottleFillingMachine<Done> {
        BottleFillingMachine {
            shared_value: val.shared_value,
            state: Done,
        }
    }
}
```

Определение начального состояния машины выглядит так: 

```rust
impl BottleFillingMachine<Waiting> {
    fn new(shared_value: usize) -> Self {
        BottleFillingMachine {
            shared_value: shared_value,
            state: Waiting {
                waiting_time: std::time::Duration::new(0, 0),
            }
        }
    }
}
```

Итак, как это выглядит при переходе между двумя состояниями? Нравится: 

```rust
fn main() {
    let in_waiting = BottleFillingMachine::<Waiting>::new(0);
    let in_filling = BottleFillingMachine::<Filling>::from(in_waiting);
}
```

В качестве альтернативы, если вы делаете это внутри функции, сигнатура типа которой ограничивает возможные выходы, это может выглядеть так: 

```rust
fn transition_the_states(val: BottleFillingMachine<Waiting>) -> BottleFillingMachine<Filling> {
    val.into() // Nice right?
}
```

Как выглядят сообщения об ошибках времени компиляции? 

```
error[E0277]: the trait bound `BottleFillingMachine<Done>: std::convert::From<BottleFillingMachine<Waiting>>` is not satisfied
  --> <anon>:50:22
   |
50 |     let in_filling = BottleFillingMachine::<Done>::from(in_waiting);
   |                      ^^^^^^^^^^^^^^^^^^^^^^^^^^
   |
   = help: the following implementations were found:
   = help:   <BottleFillingMachine<Filling> as std::convert::From<BottleFillingMachine<Waiting>>>
   = help:   <BottleFillingMachine<Done> as std::convert::From<BottleFillingMachine<Filling>>>
   = note: required by `std::convert::From::from`
```

Совершенно ясно, что в этом плохого. Сообщение об ошибке даже намекает нам на некоторые допустимые переходы!

Так что же нам дает эта схема?

- Переходы гарантированы, чтобы быть действительными во время компиляции.
- Сообщения об ошибках о недопустимых переходах очень понятны и даже содержат список допустимых параметров.
- У нас есть «родительская» структура, с которой могут быть связаны характеристики и значения, которые не повторяются.
- После выполнения перехода старое состояние больше не существует, оно расходуется. Действительно, вся структура потребляется, поэтому, если есть побочные эффекты перехода на родительский элемент (например, изменение среднего времени ожидания), мы не можем получить доступ к устаревшим значениям.
- Потребление памяти скудное и все в стеке.

Есть еще несколько минусов:

- Наши реализации From<T> страдают от изрядного количества "типового шума". Однако это очень незначительная проблема.
- Каждый `BottleFillingMachine <S>` имеет другой размер, как в нашем предыдущем примере, поэтому нам нужно будет использовать перечисление. Однако благодаря нашей структуре мы можем делать это не совсем отстойным способом.

> Вы можете поиграть с этим примером здесь

## Беспокойство с родителями

Итак, как мы можем иметь некоторую родительскую структуру, удерживающую наш конечный автомат, чтобы взаимодействие с ней не было гигантской болью? Что ж, это возвращает нас к первоначальной идее перечисления.

Если вы помните, основная проблема с приведенным выше примером перечисления заключалась в том, что нам приходилось иметь дело с отсутствием возможности принудительно выполнять переходы, и единственные ошибки, которые мы получили, были во время выполнения, когда мы пытались. 

```rust
enum BottleFillingMachineWrapper {
    Waiting(BottleFillingMachine<Waiting>),
    Filling(BottleFillingMachine<Filling>),
    Done(BottleFillingMachine<Done>),
}
struct Factory {
    bottle_filling_machine: BottleFillingMachineWrapper,
}
impl Factory {
    fn new() -> Self {
        Factory {
            bottle_filling_machine: BottleFillingMachineWrapper::Waiting(BottleFillingMachine::new(0)),
        }
    }
}
```

Здесь ваша первая реакция, вероятно, будет: «Гоша, Hoverbear, посмотри на эту ужасную и длинную подпись!» Вы совершенно правы! Честно говоря, он довольно длинный, но я выбрал длинные, пояснительные названия типов! Вы сможете использовать все свои любимые загадочные сокращения и псевдонимы типов в своем собственном коде. Есть у! 

```rust
impl BottleFillingMachineWrapper {
    fn step(mut self) -> Self {
        match self {
            BottleFillingMachineWrapper::Waiting(val) => BottleFillingMachineWrapper::Filling(val.into()),
            BottleFillingMachineWrapper::Filling(val) => BottleFillingMachineWrapper::Done(val.into()),
            BottleFillingMachineWrapper::Done(val) => BottleFillingMachineWrapper::Waiting(val.into()),
        }
    }
}

fn main() {
    let mut the_factory = Factory::new();
    the_factory.bottle_filling_machine = the_factory.bottle_filling_machine.step();
}
```

Опять же, вы можете заметить, что это работает путем потребления, а не мутации. Использование match способом, описанным выше, перемещает val, чтобы его можно было использовать с .into(), который, как мы уже определили, должен использовать состояние. Если вы действительно хотите использовать мутацию, вы можете рассмотреть возможность использования ваших состояний #[derive(Clone)] или даже Copy, но это ваш выбор.

Несмотря на то, что это немного менее эргономично и приятно работать, чем мы могли бы пожелать, мы по-прежнему получаем строго принудительные переходы между состояниями и все гарантии, которые с ними связаны.

Вы заметите одну вещь: эта схема заставляет вас обрабатывать все возможные состояния при манипулировании машиной, и это имеет смысл. Вы входите в структуру с конечным автоматом и манипулируете им, вам необходимо определить действия для каждого состояния, в котором он находится.

Или вы можете просто паниковать!() Если вы действительно этого хотите. Но если вы просто хотели запаниковать!(), То почему вы просто не использовали первую попытку?

> Вы можете увидеть полностью рабочий пример этого примера Factory здесь

## Рабочие примеры

Всегда приятно иметь несколько примеров для таких вещей. Итак, ниже я собрал пару рабочих примеров с комментариями, которые вы можете изучить.

### Три состояния, два перехода

Этот пример очень похож на приведенную выше машину для розлива бутылок, но вместо этого он действительно работает, хотя и тривиальная работа. Он принимает строку и возвращает количество слов в ней.

Ссылка на игровую площадку 

```rust
fn main() {
    // The `<StateA>` is implied here. We don't need to add type annotations!
    let in_state_a = StateMachine::new("Blah blah blah".into());

    // This is okay here. But later once we've changed state it won't work anymore.
    in_state_a.some_unrelated_value;
    println!("Starting Value: {}", in_state_a.state.start_value);


    // Transition to the new state. This consumes the old state.
    // Here we need type annotations (since not all StateMachines are linear in their state).
    let in_state_b = StateMachine::<StateB>::from(in_state_a);

    // This doesn't work! The value is moved when we transition!
    // in_state_a.some_unrelated_value;
    // Instead, we can use the existing value.
    in_state_b.some_unrelated_value;

    println!("Interm Value: {:?}", in_state_b.state.interm_value);

    // And our final state.
    let in_state_c = StateMachine::<StateC>::from(in_state_b);

    // This doesn't work either! The state doesn't even contain this value.
    // in_state_c.state.start_value;

    println!("Final state: {}", in_state_c.state.final_value);
}

// Here is our pretty state machine.
struct StateMachine<S> {
    some_unrelated_value: usize,
    state: S,
}

// It starts, predictably, in `StateA`
impl StateMachine<StateA> {
    fn new(val: String) -> Self {
        StateMachine {
            some_unrelated_value: 0,
            state: StateA::new(val)
        }
    }
}

// State A starts the machine with a string.
struct StateA {
    start_value: String,
}
impl StateA {
    fn new(start_value: String) -> Self {
        StateA {
            start_value: start_value,
        }
    }
}

// State B goes and breaks up that String into words.
struct StateB {
    interm_value: Vec<String>,
}
impl From<StateMachine<StateA>> for StateMachine<StateB> {
    fn from(val: StateMachine<StateA>) -> StateMachine<StateB> {
        StateMachine {
            some_unrelated_value: val.some_unrelated_value,
            state: StateB {
                interm_value: val.state.start_value.split(" ").map(|x| x.into()).collect(),
            }
        }
    }
}

// Finally, StateC gives us the length of the vector, or the word count.
struct StateC {
    final_value: usize,
}
impl From<StateMachine<StateB>> for StateMachine<StateC> {
    fn from(val: StateMachine<StateB>) -> StateMachine<StateC> {
        StateMachine {
            some_unrelated_value: val.some_unrelated_value,
            state: StateC {
                final_value: val.state.interm_value.len(),
            }
        }
    }
}
```

## Пример плота

Если вы какое-то время следили за моими постами, то, возможно, знаете, что мне больше нравится думать о Raft. Raft и обсуждение с @argorak были основными мотивами всего этого исследования.

Raft немного сложнее, чем приведенные выше примеры, поскольку он не просто имеет линейные состояния, где A-> B-> C. Вот схема перехода: 

```
++++++++++-+    ++++++++++--+    +++++++--+
|          ++++->           |    |        |
| Follower |    | Candidate ++++-> Leader |
|          <+++-+           |    |        |
+++++++--^-+    ++++++++++--+    +-++++++++
         |                         |
         +++++++++++++++++++++++++-+
```

    Ссылка на игровую площадку 

```rust
// You can play around in this function.
fn main() {
    let is_follower = Raft::new(/* ... */);
    // Raft typically comes in groups of 3, 5, or 7. Just 1 for us. :)

    // Simulate this node timing out first.
    let is_candidate = Raft::<Candidate>::from(is_follower);

    // It wins! How unexpected.
    let is_leader = Raft::<Leader>::from(is_candidate);

    // Then it fails and rejoins later, becoming a Follower again.
    let is_follower_again = Raft::<Follower>::from(is_leader);

    // And goes up for election...
    let is_candidate_again = Raft::<Candidate>::from(is_follower_again);

    // But this time it fails!
    let is_follower_another_time = Raft::<Follower>::from(is_candidate_again);
}


// This is our state machine.
struct Raft<S> {
    // ... Shared Values
    state: S
}

// The three cluster states a Raft node can be in

// If the node is the Leader of the cluster services requests and replicates its state.
struct Leader {
    // ... Specific State Values
}

// If it is a Candidate it is attempting to become a leader due to timeout or initialization.
struct Candidate {
    // ... Specific State Values
}

// Otherwise the node is a follower and is replicating state it receives.
struct Follower {
    // ... Specific State Values
}

// Raft starts in the Follower state
impl Raft<Follower> {
    fn new(/* ... */) -> Self {
        // ...
        Raft {
            // ...
            state: Follower { /* ... */ }
        }
    }
}

// The following are the defined transitions between states.

// When a follower timeout triggers it begins to campaign
impl From<Raft<Follower>> for Raft<Candidate> {
    fn from(val: Raft<Follower>) -> Raft<Candidate> {
        // ... Logic prior to transition
        Raft {
            // ... attr: val.attr
            state: Candidate { /* ... */ }
        }
    }
}

// If it doesn't receive a majority of votes it loses and becomes a follower again.
impl From<Raft<Candidate>> for Raft<Follower> {
    fn from(val: Raft<Candidate>) -> Raft<Follower> {
        // ... Logic prior to transition
        Raft {
            // ... attr: val.attr
            state: Follower { /* ... */ }
        }
    }
}

// If it wins it becomes the leader.
impl From<Raft<Candidate>> for Raft<Leader> {
    fn from(val: Raft<Candidate>) -> Raft<Leader> {
        // ... Logic prior to transition
        Raft {
            // ... attr: val.attr
            state: Leader { /* ... */ }
        }
    }
}

// If the leader becomes disconnected it may rejoin to discover it is no longer leader
impl From<Raft<Leader>> for Raft<Follower> {
    fn from(val: Raft<Leader>) -> Raft<Follower> {
        // ... Logic prior to transition
        Raft {
            // ... attr: val.attr
            state: Follower { /* ... */ }
        }
    }
}
``` 

## Альтернативы по отзывам

Я видел интересный комментарий I-impv на Reddit, демонстрирующий этот подход, основанный на наших примерах выше. Вот что они сказали по этому поводу:

Мне нравится, как вы это сделали. Я сам сейчас работаю над довольно сложным автоматом, и сделал это немного по-другому.

Некоторые вещи я делал иначе:

- Я также смоделировал ввод для конечного автомата. Таким образом, вы можете моделировать свои переходы как соответствие (Состояние, Событие), каждая недопустимая комбинация обрабатывается шаблоном «по умолчанию».
- Вместо использования паники для недопустимых переходов я использовал состояние отказа, поэтому каждая недопустимая комбинация переходит в это состояние отказа

Мне очень нравится идея моделирования ввода в переходах!

## Заключительные мысли

Rust позволяет нам довольно хорошо представить State Machines. В идеальной ситуации мы могли бы создавать перечисления с ограниченными переходами между вариантами, но это не так. Вместо этого мы можем использовать возможности дженериков и системы владения, чтобы создать что-то выразительное, безопасное и понятное.

Если у вас есть отзывы или предложения по этой статье, я бы посоветовал проверить нижний колонтитул этой страницы для получения контактной информации. Я также болтаюсь в IRC Mozilla под именем Hoverbear. 