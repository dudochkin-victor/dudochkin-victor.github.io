+++
title = "Бесстрашный параллелизм с Rust"
description = "Бесстрашный параллелизм с Rust"
weight = 1
+++

[Перевод](https://blog.rust-lang.org/2015/04/10/Fearless-Concurrency.html) | Автор оригинала: Aaron Turon

Проект Rust был инициирован для решения двух сложных проблем:

- Как вы занимаетесь безопасным системным программированием?
- Как сделать параллелизм безболезненным?

Первоначально эти проблемы казались ортогональными, но, к нашему удивлению, решение оказалось идентичным: те же инструменты, которые делают Rust безопасным, также помогают вам напрямую бороться с параллелизмом.

Ошибки безопасности памяти и ошибки параллелизма часто сводятся к тому, что код обращается к данным, когда этого не следует. Секретное оружие Rust - владение, дисциплина управления доступом, которой стараются следовать системные программисты, но компилятор Rust проверяет ее статически.

Для безопасности памяти это означает, что вы можете программировать без сборщика мусора и не опасаясь ошибок сегментации, потому что Rust отловит ваши ошибки.

Для параллелизма это означает, что вы можете выбирать из множества парадигм (передача сообщений, общее состояние, без блокировки, чисто функциональный), и Rust поможет вам избежать распространенных ошибок.

Вот пример параллелизма в Rust:

- Канал передает право собственности на сообщения, отправленные по нему, поэтому вы можете отправлять указатель из одного потока в другой, не опасаясь, что потоки позже будут пытаться получить доступ через этот указатель. Каналы Rust обеспечивают изоляцию потоков.

- Блокировка знает, какие данные она защищает, и Rust гарантирует, что доступ к данным возможен только тогда, когда блокировка удерживается. Состояние никогда не делится случайно. "Блокировать данные, а не код" принудительно в Rust.

- Каждый тип данных знает, можно ли безопасно пересылать его между несколькими потоками или получать к ним доступ, и Rust обеспечивает такое безопасное использование; нет никаких гонок данных, даже для структур данных без блокировок. Безопасность потоков - это не просто документация; это закон.

- Вы даже можете обмениваться кадрами стека между потоками, и Rust будет статически гарантировать, что кадры остаются активными, пока другие потоки их используют. Даже самые смелые формы совместного использования гарантированно безопасны в Rust.

Все эти преимущества проистекают из модели владения Rust, и фактически блокировки, каналы, структуры данных без блокировок и так далее определяются в библиотеках, а не в основном языке. Это означает, что подход Rust к параллелизму является открытым: новые библиотеки могут охватывать новые парадигмы и отлавливать новые ошибки, просто добавляя API, которые используют функции владения Rust.

Цель этой публикации - дать вам некоторое представление о том, как это делается. 

## Предыстория: владение

     Мы начнем с обзора систем владения и заимствования Rust. Если вы уже знакомы с ними, вы можете пропустить два «фоновых» раздела и сразу перейти к параллелизму. Если вы хотите более глубокого знакомства, я не могу рекомендовать пост Иегуды Каца достаточно высоко. И в книге Rust есть все подробности.

В Rust каждое значение имеет «область действия», и передача или возврат значения означает передачу владения («перемещение» его) в новую область. Значения, которые все еще принадлежат, когда область действия заканчивается, автоматически уничтожаются в этой точке.

Давайте посмотрим на несколько простых примеров. Предположим, мы создаем вектор и помещаем в него какие-то элементы: 

```rust
fn make_vec() {
    let mut vec = Vec::new(); // owned by make_vec's scope
    vec.push(0);
    vec.push(1);
    // scope ends, `vec` is destroyed
}
```

Область, создающая ценность, также изначально владеет ею. В этом случае тело make_vec является областью владения для vec. Владелец может делать с vec все, что угодно, в том числе изменять его путем нажатия. В конце области vec по-прежнему принадлежит, поэтому он автоматически освобождается.

Все становится интереснее, если вектор возвращается или передается: 

```rust
fn make_vec() -> Vec<i32> {
    let mut vec = Vec::new();
    vec.push(0);
    vec.push(1);
    vec // transfer ownership to the caller
}

fn print_vec(vec: Vec<i32>) {
    // the `vec` parameter is part of this scope, so it's owned by `print_vec`

    for i in vec.iter() {
        println!("{}", i)
    }

    // now, `vec` is deallocated
}

fn use_vec() {
    let vec = make_vec(); // take ownership of the vector
    print_vec(vec);       // pass ownership to `print_vec`
}
```

Теперь, непосредственно перед тем, как закончится область видимости make_vec, vec удаляется путем его возврата; он не разрушен. Вызывающий объект, такой как use_vec, затем получает право владения вектором.

С другой стороны, функция print_vec принимает параметр vec, и право собственности на вектор передается ей вызывающей стороной. Поскольку print_vec больше не передает право собственности, в конце своей области вектор уничтожается.

После передачи права собственности ценность больше не может быть использована. Например, рассмотрим этот вариант use_vec: 

```rust
fn use_vec() {
    let vec = make_vec();  // take ownership of the vector
    print_vec(vec);        // pass ownership to `print_vec`

    for i in vec.iter() {  // continue using `vec`
        println!("{}", i * 2)
    }
}
```

Если вы скармливаете эту версию компилятору, вы получите сообщение об ошибке: 

```
error: use of moved value: `vec`

for i in vec.iter() {
         ^~~
```

Компилятор сообщает, что vec больше не доступен; право собственности было передано в другое место. И это очень хорошо, потому что на этом этапе вектор уже освобожден!

Катастрофа предотвращена.

## Предыстория: заимствование

История до сих пор не совсем удовлетворительна, потому что наша цель print_vec не уничтожать данный вектор. На самом деле мы хотим предоставить print_vec временный доступ к вектору, а затем продолжить его использование.

Здесь на помощь приходит заимствование. Если у вас есть доступ к значению в Rust, вы можете предоставить этот доступ вызываемым функциям. Rust проверит, что эти договоры аренды не переживут взятый в аренду объект.

Чтобы заимствовать значение, вы делаете ссылку на него (своего рода указатель) с помощью оператора &: 

```rust
fn print_vec(vec: &Vec<i32>) {
    // the `vec` parameter is borrowed for this scope

    for i in vec.iter() {
        println!("{}", i)
    }

    // now, the borrow ends
}

fn use_vec() {
    let vec = make_vec();  // take ownership of the vector
    print_vec(&vec);       // lend access to `print_vec`
    for i in vec.iter() {  // continue using `vec`
        println!("{}", i * 2)
    }
    // vec is destroyed here
}
```

Теперь print_vec берет ссылку на вектор, а use_vec одалживает вектор, написав &vec. Поскольку заимствования являются временными, use_vec сохраняет право собственности на вектор; он может продолжать использовать его после возврата из вызова print_vec (и истечения срока его аренды vec).

Каждая ссылка действительна для ограниченной области, которую компилятор определит автоматически. Ссылки бывают двух видов:

- Неизменяемые ссылки &T, которые позволяют делиться, но не изменяют. Одновременно может быть несколько ссылок &T на одно и то же значение, но значение нельзя изменить, пока эти ссылки активны.

- Изменяемые ссылки и mut T, которые разрешают мутации, но не делятся. Если есть ссылка &mut T на значение, в это время не может быть других активных ссылок, но значение может быть изменено.

Rust проверяет эти правила во время компиляции; заимствование не требует дополнительных затрат времени выполнения.

Почему есть два типа ссылок? Рассмотрим такую функцию, как: 

```rust
fn push_all(from: &Vec<i32>, to: &mut Vec<i32>) {
    for i in from.iter() {
        to.push(*i);
    }
}
```

Эта функция выполняет итерацию по каждому элементу одного вектора, подталкивая его к другому. Итератор сохраняет указатель на вектор в текущей и конечной позициях, шагая друг к другу.

Что, если бы мы вызвали эту функцию с одним и тем же вектором для обоих аргументов? 

```rust
push_all(&vec, &mut vec)
```

Это будет катастрофой! По мере того как мы помещаем элементы в вектор, время от времени ему потребуется изменить размер, выделить новый кусок памяти и скопировать в него свои элементы. У итератора останется висящий указатель на старую память, что приведет к небезопасности памяти (с сопутствующими ошибками сегментирования или хуже).

К счастью, Rust гарантирует, что всякий раз, когда изменяемое заимствование является активным, никакие другие заимствования объекта не будут активными, и будет выдано сообщение: 

```rust
error: cannot borrow `vec` as mutable because it is also borrowed as immutable
push_all(&vec, &mut vec);
                    ^~~
```
Катастрофа предотвращена.

## Передача сообщений

Теперь, когда мы рассмотрели базовую историю владения в Rust, давайте посмотрим, что это значит для параллелизма.

Параллельное программирование имеет множество стилей, но особенно простым является передача сообщений, когда потоки или субъекты общаются, отправляя друг другу сообщения. Сторонники стиля подчеркивают, что он связывает воедино обмен и общение:

     Не общайтесь, разделяя память; вместо этого поделитесь памятью, общаясь.

     - Эффективный Go

Владение Rust позволяет легко превратить этот совет в правило, проверяемое компилятором. Рассмотрим следующий API каналов (каналы в стандартной библиотеке Rust немного отличаются): 

```rust
fn send<T: Send>(chan: &Channel<T>, t: T);
fn recv<T: Send>(chan: &Channel<T>) -> T;
```

Каналы являются общими по типу передаваемых данных (часть API <T: Send>). Часть «Отправить» означает, что T следует считать безопасным для передачи между потоками; мы вернемся к этому позже в посте, но пока достаточно знать, что Vec<i32> - это Отправить.

Как всегда в Rust, передача T функции send означает передачу права собственности на нее. Этот факт имеет серьезные последствия: это означает, что код, подобный приведенному ниже, вызовет ошибку компилятора. 

```rust
// Suppose chan: Channel<Vec<i32>>

let mut vec = Vec::new();
// do some computation
send(&chan, vec);
print_vec(&vec);
```

Здесь поток создает вектор, отправляет его другому потоку, а затем продолжает его использовать. Поток, получающий вектор, может изменить его по мере продолжения работы этого потока, поэтому вызов print_vec может привести к состоянию гонки или, если на то пошло, ошибке использования после освобождения.

Вместо этого компилятор Rust выдаст сообщение об ошибке при вызове print_vec: 

```rust
Error: use of moved value `vec`
```

Катастрофа предотвращена.

## Замки

Другой способ справиться с параллелизмом - связать потоки через пассивное разделяемое состояние.

У параллелизма с общим состоянием плохая репутация. Легко забыть установить блокировку или иным образом изменить неправильные данные в неподходящее время, что приведет к плачевным результатам - настолько легко, что многие вообще отказываются от этого стиля.

Вот что говорит Руст:

1. Параллелизм с разделяемым состоянием, тем не менее, является фундаментальным стилем программирования, необходимым для системного кода, для максимальной производительности и для реализации других стилей параллелизма.

2. Проблема действительно в случайно разделенном состоянии.

Rust стремится предоставить вам инструменты для непосредственного преодоления параллелизма с разделяемым состоянием, независимо от того, используете ли вы методы блокировки или без блокировки.

В Rust потоки автоматически «изолированы» друг от друга по причине владения. Запись может происходить только тогда, когда поток имеет изменяемый доступ, либо владея данными, либо имея их изменяемое заимствование. В любом случае, поток гарантированно будет единственным, имеющим доступ в данный момент. Чтобы увидеть, как это работает, давайте посмотрим на блокировки.

Помните, что изменяемые заимствования не могут происходить одновременно с другими заимствованиями. Блокировки обеспечивают такую же гарантию («взаимное исключение») посредством синхронизации во время выполнения. Это приводит к API-интерфейсу блокировки, который напрямую подключается к системе владения Rust.

Вот упрощенная версия (стандартная библиотека более эргономична): 

```rust
// create a new mutex
fn mutex<T: Send>(t: T) -> Mutex<T>;

// acquire the lock
fn lock<T: Send>(mutex: &Mutex<T>) -> MutexGuard<T>;

// access the data protected by the lock
fn access<T: Send>(guard: &mut MutexGuard<T>) -> &mut T;
```

Этот API блокировки необычен во многих отношениях.

Во-первых, тип Mutex является универсальным для типа T данных, защищенных блокировкой. Когда вы создаете мьютекс, вы передаете право собственности на эти данные в мьютекс, немедленно отказываясь от доступа к нему. (Замки разблокируются при первом создании.)

Позже вы можете заблокировать поток, чтобы заблокировать поток, пока блокировка не будет получена. Эта функция также необычна тем, что предоставляет возвращаемое значение MutexGuard<T>. MutexGuard автоматически снимает блокировку при ее разрушении; отдельной функции разблокировки нет.

Единственный способ получить доступ к блокировке - использовать функцию доступа, которая превращает изменяемое заимствование охранника в изменяемое заимствование данных (с более короткой арендой): 

```rust
fn use_lock(mutex: &Mutex<Vec<i32>>) {
    // acquire the lock, taking ownership of a guard;
    // the lock is held for the rest of the scope
    let mut guard = lock(mutex);

    // access the data by mutably borrowing the guard
    let vec = access(&mut guard);

    // vec has type `&mut Vec<i32>`
    vec.push(3);

    // lock automatically released here, when `guard` is destroyed
}
```

Здесь есть два ключевых ингредиента:

- Изменяемая ссылка, возвращаемая access, не может пережить MutexGuard, из которого она заимствована.

- Блокировка снимается только после уничтожения MutexGuard.

В результате Rust применяет дисциплину блокировки: он не позволит вам получить доступ к данным, защищенным блокировкой, кроме случаев, когда блокировка удерживается. Любая попытка сделать иначе приведет к ошибке компилятора. Например, рассмотрим следующий «рефакторинг» с ошибками: 

```rust
fn use_lock(mutex: &Mutex<Vec<i32>>) {
    let vec = {
        // acquire the lock
        let mut guard = lock(mutex);

        // attempt to return a borrow of the data
        access(&mut guard)

        // guard is destroyed here, releasing the lock
    };

    // attempt to access the data outside of the lock.
    vec.push(3);
}
```

Rust сгенерирует ошибку, определяющую проблему: 

```rust
error: `guard` does not live long enough
access(&mut guard)
            ^~~~~
```

Катастрофа предотвращена.

## Безопасность потоков и «Отправить»

Типично различать одни типы данных как «потокобезопасные», а другие нет. Потокобезопасные структуры данных используют достаточно внутренней синхронизации для одновременного безопасного использования несколькими потоками.

Например, Rust поставляется с двумя видами «умных указателей» для подсчета ссылок:

- Rc<T> обеспечивает подсчет ссылок посредством обычных операций чтения / записи. Это не потокобезопасный.

- Arc<T> обеспечивает подсчет ссылок с помощью атомарных операций. Это потокобезопасный.

Аппаратные атомарные операции, используемые Arc, более дороги, чем обычные операции, используемые Rc, поэтому лучше использовать Rc, чем Arc. С другой стороны, очень важно, чтобы Rc<T> никогда не переходил из одного потока в другой, потому что это может привести к условиям гонки, которые повреждают счетчик.

Обычно единственным выходом является тщательная документация; в большинстве языков нет семантического различия между потокобезопасными и небезопасными типами.

В Rust мир разделен на два типа типов данных: те, которые являются типами данных Send, что означает, что их можно безопасно перемещать из одного потока в другой, и те, которые имеют! Send, что означает, что это может быть небезопасно. Если все компоненты типа являются Send, то же самое относится и к этому типу, который охватывает большинство типов. Однако некоторые базовые типы по своей сути не являются потокобезопасными, поэтому также можно явно пометить такой тип, как Arc, как Send, сказав компилятору: «Поверьте мне, здесь я проверил необходимую синхронизацию».

Естественно, Arc - это Send, а Rc - нет.

Мы уже видели, что API-интерфейсы Channel и Mutex работают только с данными отправки. Поскольку они являются точкой, в которой данные пересекают границы потока, они также являются точкой принудительного применения для отправки.

Собирая все это вместе, программисты Rust могут с уверенностью воспользоваться преимуществами Rc и других небезопасных для потоков типов, зная, что если они когда-нибудь случайно попытаются отправить один в другой поток, компилятор Rust скажет: 

```
`Rc<Vec<i32>>` cannot be sent between threads safely
```

Катастрофа предотвращена.

## Совместное использование стека: "scoped"

Упомянутый здесь API - старый, который был перемещен из стандартной библиотеки. Вы можете найти эквивалентную функциональность в crossbeam (документация для scope()) и scoped_threadpool (документация для scoped())

До сих пор все шаблоны, которые мы видели, включают создание структур данных в куче, которые совместно используются потоками. Но что, если бы мы захотели запустить несколько потоков, использующих данные, находящиеся в нашем стековом фрейме? Это может быть опасно: 

```rust
fn parent() {
    let mut vec = Vec::new();
    // fill the vector
    thread::spawn(|| {
        print_vec(&vec)
    })
}
```

Дочерний поток принимает ссылку на vec, которая, в свою очередь, находится в стековом кадре родительского потока. Когда родительский процесс завершается, кадр стека выталкивается, но дочерний поток не в этом разбирается. Ой!

Чтобы исключить такую небезопасность памяти, базовый API порождения потоков в Rust выглядит примерно так: 

```rust
fn spawn<F>(f: F) where F: 'static, ...
```

Статическое ограничение - это способ грубо сказать, что заимствованные данные не допускаются в закрытии. Это означает, что функция, подобная parent выше, вызовет ошибку: 

```
error: `vec` does not live long enough
```

по сути, улавливая возможность выскакивания кадра родительского стека. Катастрофа предотвращена.

Но есть еще один способ гарантировать безопасность: убедиться, что кадр родительского стека остается на месте до завершения дочернего потока. Это шаблон программирования с ответвлением-соединением, часто используемый для параллельных алгоритмов «разделяй и властвуй». Rust поддерживает это, предоставляя вариант создания потоков с ограниченным объемом: 

```rust
fn scoped<'a, F>(f: F) -> JoinGuard<'a> where F: 'a, ...
```

Есть два ключевых отличия от API спауна, описанного выше:

- Используйте параметр a, а не static. Этот параметр представляет область, которая охватывает все заимствования в рамках замыкания f.

- Возвращаемое значение - JoinGuard. Как следует из названия, JoinGuard гарантирует, что родительский поток присоединяется (ожидает) к своему дочернему, выполняя неявное соединение в своем деструкторе (если оно еще не произошло явно).

Включение 'a в JoinGuard гарантирует, что JoinGuard не сможет выйти из области каких-либо данных, заимствованных при закрытии. Другими словами, Rust гарантирует, что родительский поток ожидает завершения дочернего процесса, прежде чем выталкивать любые кадры стека, к которым дочерний процесс может иметь доступ.

Таким образом, изменив наш предыдущий пример, мы можем исправить ошибку и удовлетворить компилятор: 

```rust
fn parent() {
    let mut vec = Vec::new();
    // fill the vector
    let guard = thread::scoped(|| {
        print_vec(&vec)
    });
    // guard destroyed here, implicitly joining
}
```

Таким образом, в Rust вы можете свободно заимствовать данные стека в дочерние потоки, будучи уверенными, что компилятор проверит достаточную синхронизацию.

## Гонки за данные

На данный момент мы увидели достаточно, чтобы решительно заявить о подходе Rust к параллелизму: компилятор предотвращает все гонки данных.

    Гонка за данными - это любой несинхронизированный одновременный доступ к данным, включающий запись.

Синхронизация здесь включает в себя такие низкоуровневые вещи, как атомарные инструкции. По сути, это способ сказать, что вы не можете случайно «разделить состояние» между потоками; весь (изменяющийся) доступ к состоянию должен быть обеспечен некоторой формой синхронизации.

Гонки данных - это всего лишь один (очень важный) вид состояния гонки, но, предотвращая их, Rust часто помогает предотвратить и другие, более тонкие гонки. Например, часто важно, чтобы обновления в разных местах происходили атомарно: другие потоки видят либо все обновления, либо ни одного из них. В Rust одновременный доступ &mut к соответствующим местоположениям гарантирует атомарность их обновлений, поскольку ни один другой поток не может иметь одновременный доступ для чтения.

Стоит ненадолго остановиться и подумать об этой гарантии в более широком контексте языков. Многие языки обеспечивают безопасность памяти за счет сборки мусора. Но сборка мусора не помогает предотвратить скачки данных.

Вместо этого Rust использует владение и заимствование для обеспечения двух своих ключевых ценностных предложений:

- Безопасность памяти без сборки мусора.
- Параллелизм без гонок данных.

## Будущее

Когда Rust только начинался, он врезал каналы прямо в язык, занимая очень жесткую позицию в отношении параллелизма.

В сегодняшнем Rust параллелизм - это полностью библиотечное дело; все, что описано в этом посте, включая Send, определено в стандартной библиотеке и вместо этого может быть определено во внешней библиотеке.

И это очень интересно, потому что это означает, что история параллелизма в Rust может бесконечно развиваться, расширяться, охватывать новые парадигмы и выявлять новые классы ошибок. Такие библиотеки, как syncbox и simple_parallel, делают некоторые из первых шагов, и мы ожидаем, что в следующие несколько месяцев мы вложим значительные средства в это пространство. Быть в курсе! 
