+++
title = "Своевременный поток данных: основные концепции"
description = "Своевременный поток данных: основные концепции"
weight = 1
hash = "57f47f33"
+++

[Перевод](http://www.frankmcsherry.org/dataflow/naiad/2014/12/29/TD_time_summaries.html) | Автор оригинала: Frank McSherry

Это второй пост в серии о своевременном потоке данных. В первом посте был дан обзор некоторого контекста для своевременного потока данных и предложений по новым направлениям. В этом посте мы начнем подробно рассказывать о некоторых подвижных частях нового иерархического подхода.

Наш план состоит в том, чтобы организовать своевременные графы потоков данных иерархически во вложенные области, каждая из которых может быть реализована другими своевременными графами потоков данных. Это дает несколько преимуществ, в основном из-за того, что уровень абстракции скрывает детали, которые другим частям внешнего графика своевременного потока данных знать не обязательно. С другой стороны, нам нужно будет тщательно продумать границу абстракции, чтобы не терять точность.

## Подход

В то время как Naiad сохранил плоское представление графика своевременного потока данных с подсчетами для каждой пары (местоположение, временная метка), указывающей на незавершенную работу, нам нужно будет использовать другой подход. Вместо того, чтобы представлять такие мелкие детали о том, где находится незавершенная работа, всем участникам, мы воспользуемся иерархической структурой, чтобы упростить информацию.

Каждая область будет проецировать незавершенную работу на свои выходы, сообщая для каждого выхода временные метки, в которые могут появиться сообщения. По мере выполнения работы он будет сообщать о ходе выполнения внешнему графику своевременного потока данных в виде изменений в счетчиках для пар (выход, временная метка).

Точно так же незавершенная работа и прогресс, достигнутый в другом месте графика, сначала проецируются на входные данные области, а затем передаются в подграф.

## Пример

В качестве примера мы заимствуем огромную цифру из статьи Naiad.

![Мой полезный скриншот](/imgs/posts/57f47f33_01.png)

В этом примере у нас есть два контекста, или «области видимости», как мы их будем называть: внешняя «потоковая» область видимости и внутренняя область «цикла». В то время как область внутреннего цикла может быстро прогрессировать, вершина D внешней потоковой области должна быть проинформирована только тогда, когда будет достигнут некоторый внешне видимый прогресс. В частности, когда выходные данные области внутреннего цикла больше не будут создавать сообщения с определенной меткой времени.

Фактически, внешней потоковой области не нужно ничего знать об области внутреннего цикла, кроме того, что у нее есть один вход и один выход, а также всякий раз, когда он делает внешне видимый прогресс.

Наш план состоит в том, чтобы просто структурировать механизмы отслеживания прогресса в соответствии с этими принципами, сообщая о прогрессе в восходящем направлении, прогнозируя все нерелевантные детали и объединяя (а часто и отменяя) результаты.

Как правило, есть и другие вещи, о которых внешнему прицелу будет полезно знать, что этот рисунок не особенно хорошо демонстрирует. Если бы во внутренней области было несколько входов и выходов, было бы полезно знать, какие входы связаны с какими выходами. Если бы у внутренней области был экзогенный источник входных данных, ей нужно было бы предупредить внешнюю область, чтобы ожидать этого. Мы разберемся в этих деталях.

## Основные трэйты

В основе нашего подхода к своевременному потоку данных лежат три характеристики: Timestamp, PathSummary и Scope. У каждой области есть свои собственные типы сводки времени и пути, реализующие соответствующие трэйты.

### Свойство отметки времени

Типаж Timestamp представляет тип, подходящий для использования в качестве отметки времени сообщения. В Naiad эту роль играла последовательность беззнаковых целых чисел. Мы планируем использовать более общие временные метки, но с некоторыми ограничениями, в частности (из кода):

```rust
trait Timestamp: PartialOrd+Eq+PartialEq+Copy+Default+Hash+Show+Send+'static { }
```

Это что-то вроде беспорядка, и, строго говоря, не все ограничения необходимы. Просто полезно.

Наиболее подходящим ограничением является PartialOrd, который предоставляет методы le, lt, ge, gt для сравнения двух элементов одного типа. Типы, реализующие Timestamp, должны быть частично упорядочены.

### Свойство PathSummary

Типаж PathSummary параметризуется типом, реализующим Timestamp. Он указывает, как следует ожидать продвижения метки времени по мере ее перемещения из одного места в другое на графике своевременного потока данных. Он также обеспечивает поддержку для объединения двух сводок (резюмируя конкатенированные пути). 

```rust
trait PathSummary<T> : PartialOrd+Eq+Copy+Clone+Show+Default+'static
{
    fn results_in(&self, timestamp: &T) -> T;       // advances a timestamp
    fn followed_by(&self, other: &Self) -> Self;    // composes two summaries
}
```

Сводные данные о путях позволяют нам переводить события в каком-то удаленном месте в локальные последствия события. Если удаленная вершина завершает обработку последнего из своих входов и не производит выходных данных, какая временная метка (или временные метки) должна быть уменьшена локально?

Чтобы суммировать все пути из одного места в другое, мы используем тот факт, что типы, реализующие PathSummary, частично упорядочены, и что нас интересуют самые ранние временные метки, которые могут появиться. Любой набор сводок путей может быть сокращен до тех элементов, которые не строго меньше, чем какой-либо другой элемент набора, что мы упрощаем с помощью структуры `Antichain <S>`.

Вероятно, что свойство будет обновляться по мере обнаружения дополнительных ограничений. Например, очень вероятно, что results_in должен возвращать типы Option<T> или Iterator<T>, а не отдельные элементы. В противном случае может быть трудно приспособить ограниченные циклы, которые отбрасывают сообщения, когда их временная метка превышает границу цикла. Вероятно, существуют и другие подобные неподдерживаемые шаблоны.

### Свойство Scope

Трэйта Scope - это то, где кусочки начинают сходиться воедино. Область видимости представляет собой элемент на графике своевременного потока данных, видимый из внешнего мира (или его родительской области). Область параметризуется типом временной метки T и типом сводки пути S. Для реализации трэйта тип должен предоставлять несколько методов, которые мы скоро подробно опишем (некоторые методы диагностики опущены). 

```rust
trait Scope<T: Timestamp, S: PathSummary<T>> : 'static
{
    fn inputs(&self) -> uint;   // number of inputs
    fn outputs(&self) -> uint;  // number of outputs

    // get and set summary information as part of set-up.
    fn get_internal_summary(&mut self) -> (Vec<Vec<Antichain<S>>>,
                                           Vec<Vec<(T, i64)>>) ->();
    fn set_external_summary(&mut self, summaries: Vec<Vec<Antichain<S>>>,
                                       external: &Vec<Vec<(T, i64)>>) ->();

    // push and pull progress information at run-time.
    fn push_external_progress(&mut self, external: &Vec<Vec<(T, i64)>>) ->();
    fn pull_internal_progress(&mut self, internal: &mut Vec<Vec<(T, i64)>>,
                                         consumed: &mut Vec<Vec<(T, i64)>>,
                                         produced: &mut Vec<Vec<(T, i64)>>) -> bool;
}
```

Это определение звучит сложно, но я надеюсь, что к концу поста причины для каждой из частей станут более ясными.

#### Входы и выходы

Каждая область видимости для внешнего мира представляет собой вершину, которая потребляет и производит сообщения с отметкой времени T. Первое, что нужно знать внешнему миру, - это форма области: сколько входов у нее есть и сколько выходов она делает. имеют. Хотя это и не является концептуально глубоким, все дальнейшее общение между осциллографом и внешним миром будет происходить в терминах этих входов и выходов.

#### Инициализация

Два метода используются как часть инициализации вычислений: область действия суммирует себя с внешним миром, а внешний мир суммирует себя обратно в область действия. Из-за иерархической структуры области сначала суммируются до своей родительской области, которая в конечном итоге получает достаточно информации, чтобы суммировать структуру вокруг области до самой области.

Эти сводки включают как то, что область действия (и внешний мир) способна делать с сообщениями, полученными на их входах, с точки зрения сообщений, созданных на их выходах, так и любые начальные подсчеты сообщений с отметками времени на каждом выходе.

Первый метод - get_internal_summary, который запрашивает у области видимости сводки путей для каждой из пар (вход, выход), а также начальные подсчеты на каждом из выходов для каждой временной метки. 

```rust
fn get_internal_summary(&mut self) -> (Vec<Vec<Antichain<S>>>,
                                       Vec<Vec<(T, i64)>>) ->();
```

Результатом является просто пара сводок для каждого входа к каждому выходу и список для каждого выхода приращений к счетчикам для каждой временной метки.

Второй метод - set_internal_summary, который сообщает области видимости сводные данные о пути от каждого из своих выходов обратно к своим входам, а также начальные подсчеты для каждого из своих входов. 

```rust
fn set_external_summary(&mut self, summaries: Vec<Vec<Antichain<S>>>,
                                   external: &Vec<Vec<(T, i64)>>) ->();
```

Два аргумента являются аналогами возвращенных значений выше: сводные данные о путях от каждого выхода области к каждому из входов области и начальные подсчеты на каждом из входов области.

### Время выполнения

Как только вычисление началось, внешний мир взаимодействует с осциллографом двумя способами: он уведомляет объем прогресса, достигнутого во внешнем мире, и запрашивает информацию о прогрессе, достигнутом в самой области. Это методы push_external_progress и pull_internal_progress.

Первый из этих двух методов относительно прост: он передает в область видимости список обновлений счетчиков временных меток для каждого из своих входов. Эти обновления сообщают о прогрессе во внешнем мире, прогнозируемом вплоть до входов осциллографа. 

```rust
fn push_external_progress(&mut self, external: &Vec<Vec<(T, i64)>>) ->();
```

Метод ничего не возвращает, что отражает наше желание, чтобы это было асинхронным взаимодействием: уведомление не связано с ответом, оно просто отправляется в область видимости из внешнего мира с надеждой, что прогресс будет достигнут в будущем.

Второй способ немного сложнее, по крайней мере, сигнатурно. Его роль заключается в получении информации из области видимости о достигнутом прогрессе, чтобы система в целом могла развиваться. 

```rust
fn pull_internal_progress(&mut self, internal: &mut Vec<Vec<(T, i64)>>,
                                     consumed: &mut Vec<Vec<(T, i64)>>,
                                     produced: &mut Vec<Vec<(T, i64)>>) -> bool;
```

Аргументы - это векторы, предназначенные для заполнения, указывающие (соответственно) изменения в любой внутренней работе, проецируемой на выходы области, количество сообщений, потребляемых на каждом из входов области, и количество сообщений, созданных на каждом из выходов области. Метод возвращает логическое значение, указывающее, есть ли незарегистрированная работа, которая может не привести к выходным сообщениям, которые должны предотвратить завершение вычислений (например, вершины с побочными эффектами, но без выходных данных).

Этот метод собирает три типа обновлений вместе, потому что для корректности важно, чтобы два типа обновлений происходили вместе:

- Использование входящего сообщения приводит к увеличению внутренней работы (потребление -> внутренняя).
- Создание выходного сообщения, что приводит к меньшему количеству внутренней работы (внутренняя -> производство).

Если любой из этих двух разделится, возможно, внешний мир может иметь неполное (и неправильное) представление о состоянии области видимости. Например, если область действия подтверждает получение входного сообщения, но не указывает на новую внутреннюю работу, внешняя область может разумно предполагать, что ее нет, и сообщать другим неверную информацию о ходе выполнения. Точно так же, если внутренняя работа прекращается, создавая выходное сообщение, нет никаких оснований полагать, что внешний мир будет знать о сообщении своевременно, если только сам объем не сообщил об этом.

Хотя мы могли бы разбить вышеперечисленное на два метода pull_internal, существует хорошая симметрия в наличии одного метода для каждого направления информационного потока. Более того, получение из области видимости только части информации о ходе выполнения пока не дает никаких преимуществ. Область действия может сообщать то, что она хочет сообщить.

## Следующие шаги

Мы описали существующие интерфейсы для нашей иерархической реализации своевременного потока данных. Еще многое предстоит сказать о том, как реализовать области видимости, особенно об общей логике для области действия, которая объединяет связанную коллекцию других областей. Мы поговорим об этом дальше и почти наверняка затронем детали, которые все еще обсуждаются. 