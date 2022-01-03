+++
title = "Изучение Rust и Go"
description = "Изучение Rust и Go"
weight = 1
+++

[Перевод](https://fettblog.eu/learning-rust-and-go/)

Мой блог - это хроника познания нового. Большинство статей, которые я пишу, представляют собой заметки о том, как я решал проблемы, с которыми сталкивался в повседневной работе. И то и дело мне приходится призывать узнавать что-то новое!

Из-за моей общей усталости от веб-разработки в целом я решил вернуться к тому, что делал 15-20 лет назад, и снова заняться программированием на уровне системы. Если вы читали мой информационный бюллетень, вы могли знать, что я недавно пробовал себя как в Rust, так и в Go.

Интересно, что и Go, и Rust попадают в одну категорию, но настолько принципиально отличаются друг от друга по дизайну, философии и исполнению.

Вот несколько советов по использованию обоих в течение длительного времени.

## Эй, пойдем!

Я увлекся Go, послушав Кармен Андох, которая сказала, что «Go делает программирование увлекательным!».

Go думает: «Что, если бы всего, что произошло после C ++, просто не произошло бы? Включая C ++! » - без занятий, без хлама. Никаких ОО-шаблонов. Сфокусированный код, скомпилированный в исходном коде. Просто, понятно. Продолжение C.

И мне нравится C!

Создатели Go умеют разыгрывать карту C. Один из его создателей - известный UNIX Кен Томпсон, входивший в ту же команду, что и первоначальный автор C Деннис Ричи. Книгу «Язык программирования Go» написал Брайан Керниган, который также написал «Язык программирования C», поскольку он был коллегой Ричи и Томпсона.

Но есть также влияния Паскаля, которые имеют большой смысл, если вы знаете историю создателей.

Они знают свое наследие. Они видели развитие языков программирования. Они хотят пойти по альтернативному пути, по которому вы получите большую часть преимуществ современных языков программирования, не теряя при этом ни внимания, ни производительности.

### Кодирование Детокс

Мой друг Райнер назвал Go «Coding Detox». И это очень подходящий термин, потому что вам придется отучиться от множества непонятных языков программирования, добавленных за последние пару лет. Ваш код становится более подробным, но неизбежно более читабельным.

И это правда! Даже если вы не умеете писать Go, если у вас есть немного знаний из других программ, вы сможете читать код Go. Это так просто. Да, Go иногда может быть немного многословным, но опять же: это делает его намного проще для чтения, поскольку каждый шаг очень преднамеренный.

Следующий фрагмент извлекает N случайных чисел из банка M. Мне не нужно было понимать большую часть внутренней механики Go, чтобы создать что-то вроде этого. 

```go
func makeRange(min int, max int) []int {
	numbers := make([]int, max-min)
	for i := range numbers {
		numbers[i] = min + i
	}
	return numbers
}

func takeNFromM(take int, from int) []int {
	source := rand.NewSource(time.Now().UnixNano())
	rando := rand.New(source)
	numbers := makeRange(1, from+1)
	rando.Shuffle(len(numbers), func(i, j int) {
		numbers[i], numbers[j] = numbers[j], numbers[i]
	})
	return numbers[0:take]
}
```

Мне очень нравится этот факт о го. То, как Go работает по дизайну, очень похоже на то, как я пишу JavaScript. Так что для меня это очень легко. Уменьшенный синтаксис позволяет сосредоточиться на действительно важных вещах: структуре, архитектуре, производительности.

### Инструменты

Я сразу заметил одну вещь: насколько хороши инструменты. Я знаю, что в последнее время мы далеко продвинулись в использовании инструментов JavaScript. Но это все равно ничто по сравнению с тем, что предлагает Go.

Бинарный файл Go поставляется со всеми инструментами, необходимыми для сборки, запуска, форматирования и тестирования вашего кода. И это супер быстро. Если вы установите расширения Go для VS Code, вы получите быстрое редактирование, которое превосходит все, что я видел до сих пор. Всего несколько щелчков мышью, и готово: автозаполнение, автоимпорт, автоформатирование, отладка. Все просто так!

Благодаря великолепному Intellisense мне нужно было лишь смутное представление о том, какой пакет из стандартной библиотеки я хотел импортировать. math / rand и время для правильных генераторов случайных чисел. Это можно сделать, набрав пару букв.

### любит

Есть некоторые вещи, которые мне очень нравятся:

1. ОО без БС. Никаких странных шаблонов наследования или использования абстракций там, где они не нужны. Структуры и методы, которые вы вызываете для структур. Если хотите, методы выглядят и работают как обычные функции. Так же, как я пишу свой JavaScript.
2. Стандартная библиотека огромна и заботится о множестве вещей, с которыми вы сталкиваетесь в повседневной жизни программирования.
3. Сильно самоуверен, не теряя выразительности.

### Беспокойства

При всем волнении меня беспокоит пара вещей:

1. Есть нулевые значения и указатели. Я знаю, что они ведут себя по-другому и намного безопаснее, чем то, что я знаю из C, но мне все равно кажется, что я могу сделать что-то не так, где я не должен, учитывая, что все остальное находится под контролем.
2. Я должен привыкнуть к загрузке пакетов с GitHub.
3. Время от времени мне не хватает абстракций более высокого уровня, таких как итераторы или более выразительная система типов, но эй. Это часть их философии!

### Как начать учиться

Брайан Кантрилл однажды сказал, что JavaScript - это «LISP в одежде Си». В этом много правды. Отношение к Java скорее случайное, чем преднамеренное. В JS есть много LISP-измов, которые доступны через знакомый синтаксис. В некотором смысле это делает JavaScript управляемым продолжением C.

Если я посмотрю с этой точки зрения, Go попадает в аналогичную категорию. Продолжение C, управляемое, очищенное, для современных приложений.

Я думаю, что хороший способ начать, если вы хотите вникнуть в некоторый готовый к производству код, - это преобразовать ваши Node-приложения в Go. Особенно веб-серверы и тому подобное, для чего вам обычно нужны Express или Fastify.

В Go есть потрясающий HTTP-пакет, в котором вы работаете с аналогичным API для создания своих серверов. Попробуйте это!

Если это не ваше дело, я думаю, все, что вам нужно, чтобы преобразовать JSON, XML или любой другой файл во что-то, - это хороший способ начать работать с Go. 

## От тряпки к тряпке, от Rust к Rust

Я впервые услышал о Rust от Райана Левика, который был гостем нашего подкаста Working Draft на Web Assembly, но не мог перестать восторгаться Rust!

За последние пару лет Rust стал довольно популярным, что мне до сих пор интересно, потому что по сравнению с любым другим современным языком программирования есть чему поучиться, если вы хотите продуктивно работать с Rust.

Rust поставляется с богатым синтаксисом, подобным C, который сначала кажется очень необходимым, но при более внимательном рассмотрении имеет много связей с функциональным программированием. Учитывая, что исходный компилятор был написан на OCaml, это не должно вызывать удивления.

Это дает вам как разработчику такие конструкции, как сопоставление с образцом, расширенные типы с помощью перечислений, итераторы, с которыми вы можете императивно работать, и так далее.

Как только вы освоите его, это будет исключительно хороший язык. Он элегантный, современный, прекрасно читается, учитывая, что вы работаете над задачами, которые так близки к металлу.

Следующий пример кода делает то же самое, что и приведенный выше фрагмент Go, но выглядит намного более плавным: 

```rust
fn take_n_from_m(take: usize, from: u64) -> Vec<u64> {
    let mut rng = rand::thread_rng();
    let mut nums: Vec<u64> = (1..=from).collect();
    nums.shuffle(&mut rng);
    nums[0..take].to_vec()
}
```

### Памяти

Так же, как и Go, Rust хочет знать, как бы выглядели языки программирования, если бы не было обходного пути C ++, Java и т. Д. Но вместо того, чтобы предоставить вам преимущества управляемой памяти, Rust обеспечивает безопасность памяти за счет синтаксиса и семантики. Во время компиляции.

Для меня это понятие совершенно новое. По сути, это говорит о том, что для каждого фрагмента памяти может быть только один владелец. Все остальное просто заимствует этот фрагмент памяти на какое-то время с гарантией, что они вернут его первоначальному владельцу или станут новым владельцем.

Это очень похоже на то, как брать книгу у кого-то. Эта аналогия работает исключительно хорошо. Это тоже то, чему нужно много учиться.

Оказывается, с момента появления языков программирования высокого уровня произошло множество абстракций, от простейшего умножения до переназначения и т. Д.

Внезапно вам нужно много думать о памяти. По сути, все время, пока шаблоны не станут для вас естественными.

И это исключительно хорошо. У вас есть ощущение, что вы полностью контролируете низкоуровневое управление памятью, совершенно не беспокоясь о том, что вы можете что-то сломать. Это потрясающе!

### Отладка во время компиляции

Это также приводит к философии, которую я когда-то слышал как «отладка во время компиляции». Вместо того, чтобы обнаруживать ошибки, когда они случаются, вы обнаруживаете многие из них во время разработки, когда пытаетесь скомпилировать свой код.

Вы будете много спорить со своим компилятором. Но компилятор хорош. Он дает вам подсказки о том, что вы можете попробовать, что вы могли иметь в виду. Замечательный диалог, почти в стиле парного программирования.

И внезапно вы начинаете понимать, что происходит с воспоминаниями внизу. И вы начинаете оптимизировать под это. И ваша программа становится намного быстрее, чем вы ожидали.

Для одной задачи сexperism.io был набор тестов, который занял более 45 секунд, учитывая мою плохую реализацию. Спустя пару оптимизаций весь пакет был выполнен менее чем за секунду.

Я чувствовал себя всемогущим!

### Абстракции с нулевой стоимостью

Идея абстракций с нулевой стоимостью присутствует повсюду. Например, использование итераторов, которые можно использовать как в функциональном стиле программирования, так и в императивном стиле.

Вы можете переключаться между обоими стилями по своему вкусу и при этом сохранять ощущение, что вы пишете производительный код. Это также дает вам лучшее представление о том, что происходит, когда вы вызываете итератор.

То же самое и с богатой системой шрифтов и их трэйтами. Вы понимаете, что делает число числом и как оно представлено в системе типов. И чем больше вы работаете с этим, вы видите, как эти типы помогают в основном предоставлять компилятору достаточно информации, чтобы он мог генерировать наиболее производительный ассемблерный код. 

### Любит

Что мне больше всего нравится в Rust?

1. Трэйты и типы. Это дает совершенно новый поворот в объектной ориентации, который я бы даже не осмелился назвать объектно-ориентированным.
2. Выражения! Все является выражением, даже если или за. Это дает место для множества красивых узоров!
3. В их системе пакетов (называемых крэйтами) есть несколько чрезвычайно полезных утилит, которые я бы полюбил на любом другом языке. Поскольку итераторы очень важны для всего, что вы делаете, я бы не хотел пропустить itertools.
4. Как и Go, Rust очень самоуверен, когда это важно!
5. Честно? Общество. Я организовываю Rust Linz с некоторыми людьми, и я был сбит с толку, насколько все гостеприимны и осторожны. Ребята из Rust обо всем позаботятся!
6. Rust приобретает все большее значение в отрасли. К счастью, это не находится в руках одной корпорации, но есть фонд, поддерживающий это.

### Беспокойства

Если есть что-то, что меня беспокоит, так это управление пакетами. крэйти и cargo очень приветствуются сообществом. Хорошие вещи, как и у всех JavaScript-разработчиков. Хорошо, что crates.io - это не другая упаковочная компания, как NPM, но я вижу некоторые из тех же шаблонов, которые в какой-то момент сработали в Node и NPM:

1. Слишком много важных пакетов в версии 0.x.
2. Большие деревья зависимостей, которые вы втягиваете, не зная.
3. Многие вещи делают то же самое!

Я надеюсь, что это всего лишь впечатление и не пойдет в том же направлении, что и Node. Я думаю, что это важно, так как многие функции, которые, как вы ожидаете, поставляются с языком в какой-то стандартной библиотеке, обычно извлекаются в крэйти: случайные числа, сеть TCP и т. Д. Вы много полагаетесь на крэйти.

Еще одна вещь, которая кажется мне немного странной, - это макросы. Они хороши и полезны, но теоретически у вас есть возможность создать с их помощью свой собственный метаязык. Кто-то даже создал макрос JSX в Rust. Само по себе это неплохо, но, учитывая, что большинство зависимостей - 0.x и что в языке уже есть довольно много синтаксиса и концепций, которые нужно изучить, я боюсь, что будет слишком много шума, из-за которого будет сложно сосредоточиться. и решить, что использовать для ваших производственных приложений.

Кроме этого, меня не волнует. Это красивый язык, и мне очень нравится его писать!

### Изучение Rust

Думаю, будет не так-то просто перенести некоторые приложения Node на Rust, даже несмотря на то, что есть крэйти, которые работают как Express. Например, ракета.

Я изучил Rust, посмотрев на сайтexperminism.io и выполнив несколько упражнений по программированию 101, в которых я мог сосредоточиться на изучении языка, его синтаксиса и семантики, не слишком беспокоясь о возникшей проблеме. Приятно иметь возможность генерировать числа Фибоначчи или находить простые числа.

Такой шанс важен. Rust - отнюдь не простой язык. И требуется время, чтобы разобраться в каждой концепции, которая позволяет создавать читаемый и целеустремленный код.

На полпути к курсу я понял, что мне нужен настоящий проект, над которым можно подумать. Я в основном работаю над распределением сетевых запросов другим сервисам и оркестровкой этих сервисов. Оказывается, это идеальный вариант использования Rust. Но я думаю, что и другие вещи тоже. Я действительно не вижу предела.

## Rust or Go? Rust и вперед!

Rust и Go для меня сейчас желанное отвлечение. Хорошо делать что-то совершенно отличное от того, что я делал раньше, и оба языка по-своему приводят меня к моим целям. Прямо сейчас я не мог сказать, какой из них я бы предпочел для какой задачи, поскольку оба они мне очень нравятся. Возможно, это решение придет, когда я столкнусь с проблемами, которые я смог бы легко решить на другом языке.

Но эй, а почему ты выбираешь? Может и дальше буду пользоваться обоими! 

```
Однажды я написал программу на ANSI-C
передача данных по TCP
Миллион маллоков, ни одного бесплатного,
ой, куча переполнена!

Скоро может наступить SEGFAULT,
Когда я отлаживаю, компилирую и запускаю.
Я спрашиваю себя, что я наделал.
И перепишите на Rust or Go.

Верно. Это была хижина! 
```