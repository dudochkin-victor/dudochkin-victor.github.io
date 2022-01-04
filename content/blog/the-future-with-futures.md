+++
title = "Будущее с футурами"
description = "Будущее с футурами"
weight = 1
+++

[Перевод](https://hoverbear.org/blog/the-future-with-futures/) | Автор оригинала: xxxx

В последнее время в языке Rust был достигнут значительный прогресс в направлении создания надежного асинхронного стека. В этой статье мы рассмотрим, что это такое, познакомимся с тем, что доступно, поиграем с некоторыми примерами и поговорим о том, как эти части сочетаются друг с другом.

Для начала мы начнем с крэйта Futures, перейдем к futures_cpupool, а затем, в конечном итоге, к tokio. Мы предполагаем, что у вас есть некоторые познания в программировании и вы хотя бы думали о том, чтобы попробовать Rust раньше.

## Что такое Futures и Async?

При написании кода для выполнения некоторых действий, которые могут занять некоторое время, таких как запрос ресурса с удаленного хоста, не всегда желательно блокировать дальнейшее выполнение нашей программы. Это особенно верно в случае, когда мы пишем веб-сервер или выполняем большое количество сложных вычислений.

Один из способов справиться с этим - создать потоки и дискретно разделить задачи для распределения по потокам. Это не всегда так удобно или просто, как может показаться. Внезапно мы вынуждены выяснить, как лучше всего разделять задачи, как распределять ресурсы, программировать против условий гонки и управлять межпотоковой связью.

Как сообщество, мы изучили некоторые методы на протяжении многих лет, такие как пулы ЦП, которые позволяют нескольким потокам взаимодействовать при выполнении задачи, и «будущие» значения, которые разрешаются к их предполагаемому значению после того, как они завершают некоторые вычисления. Они предоставляют нам полезные и мощные инструменты, которые делают написание такого кода проще, безопаснее и увлекательнее.

Если вы когда-либо разрабатывали на Javascript, возможно, вы уже знакомы с асинхронным программированием и идеей промисов. Futures в Rust очень похожи на это, но нам предоставлено больше контроля и больше ответственности. С этим приходит большая сила.

## Как мы сюда попали

Большая часть этой асинхронной работы находилась в разработке после того, как зеленые потоки были удалены из Rust примерно в версии 0.7. Было несколько проектов, связанных с футурами и асинхронностью, с тех пор их влияние можно почувствовать по тому, что у нас есть сейчас и что находится на горизонте.

Сегодня большая часть истории async основана на идеях и уроках, извлеченных из зеленых потоков std, mio, сопрограмм и ротора. В частности, mio является основой почти всей асинхронной области Rust. Если вы заинтересованы в высококачественном системном коде, я настоятельно рекомендую уделить внимание этому проекту.

Сегодня токио и футуры находятся в центре внимания многих сообществ. Недавно Tokio анонсировала свой первый релиз, в то время как футуры оставались относительно стабильными в течение пары месяцев. Это отвергло большое количество разработок в сообществе, направленное на поддержку и использование этих новых возможностей.

## Начиная

крэйт Futures предлагает ряд структур и абстракций, позволяющих создавать асинхронный код. Сам по себе он довольно прост, и благодаря своему продуманному дизайну, по сути, представляет собой абстракцию с нулевыми затратами. Это важный момент, о котором следует помнить при работе с нашими примерами: написание кода с футурами не должно иметь накладных расходов на производительность по сравнению с кодом без.

При компиляции футуры сводятся к реальным машинам состояний, в то же время позволяя нам писать наш код в относительно знакомом шаблоне «стиля обратного вызова». Если вы уже использовали Rust, то работа с Futures будет очень похожа на работу итераторов.

Одна из вещей, которые мы обычно делаем в этом разделе, - это «немного поспать» в потоке, чтобы имитировать некоторые асинхронные действия. Для этого создадим небольшую функцию: 

```rust
use std::thread;
use std::time::Duration;
use rand::distributions::{Range, IndependentSample};

// This function sleeps for a bit, then returns how long it slept.
pub fn sleep_a_little_bit() -> u64 {
    let mut generator = rand::thread_rng();
    let possibilities = Range::new(0, 1000);

    let choice = possibilities.ind_sample(&mut generator);

    let a_little_bit = Duration::from_millis(choice);
    thread::sleep(a_little_bit);
    choice
}
```

Теперь, когда мы установили это, мы можем использовать его в нашем первом будущем. Мы воспользуемся одним выстрелом, чтобы делегировать задачу другому потоку, а затем забрать ее обратно в основной поток. OneShot - это, по сути, одноразовый канал, что-то может быть отправлено от отправителя к получателю, а затем канал закрывается. 

```rust
use std::thread;
use futures::Future;
use futures::sync::oneshot;

use fun_with_futures::sleep_a_little_bit;

fn main() {
    // This is a simple future built into the crate which feel sort of like
    // one-time channels. You get a (sender, receiver) when you invoke them.
    // Sending a value consumes that side of the channel, leaving only the reciever.
    let (tx, rx) = oneshot::channel();

    // We can spawn a thread to simulate an action that takes time, like a web
    // request. In this case it's just sleeping for a random time.
    thread::spawn(move || {
        println!("--> START");

        let waited_for = sleep_a_little_bit();
        println!("+++ WAITED {}", waited_for);
        // This consumes the sender, we can't use it afterwards.
        tx.complete(waited_for);

        println!("<-- END");
    });

    // Now we can wait for it to finish
    let result = rx.wait()
        .unwrap();

    // This value will be the same as the previous "WAITED" output.
    println!("{}", result);
}
```

Если мы запустим этот пример, мы увидим что-то вроде: 

```
--> START
+++ WAITED 542
<-- END
542
```

Результат - именно то, что можно было бы ожидать, если бы мы делали это через стандартные каналы. Код тоже выглядит похожим. На данный момент мы вообще почти не используем футуры, поэтому неудивительно, что здесь не происходит ничего удивительного. Будущее начинает проявляться в более сложных задачах.

Далее давайте посмотрим, как мы можем работать с набором футур. В этом примере мы создадим несколько потоков, заставим их выполнить какую-нибудь длительную задачу, а затем соберем все результаты в вектор. 

```rust
use std::thread;
use futures::Future;
use futures::future::join_all;

use fun_with_futures::sleep_a_little_bit;

const NUM_OF_TASKS: usize = 10;

fn main() {
    // We'll create a set to add a bunch of recievers to.
    let mut rx_set = Vec::new();

    // Next we'll spawn up a bunch of threads doing 'something' for a bit then sending a value.
    for index in 0..NUM_OF_TASKS {
        // Here we create a future, this is a `oneshot` value which is consumed after use.
        let (tx, rx) = futures::oneshot();
        // Add the reciever to the vector we created earlier so we can collect on it.
        rx_set.push(rx);

        // Spawning up a thread means things won't be executed sequentially, so this will actually
        // behave like an asynchronous value, so we can actually see how they work.
        thread::spawn(move || {
            println!("{} --> START", index);

            let waited_for = sleep_a_little_bit();
            println!("{} +++ WAITED {}", index, waited_for);

            // Here we send back the index (and consume the sender).
            tx.complete(index);

            println!("{} <-- END", index);
        });
    }

    // `join_all` lets us join together the set of futures.
    let result = join_all(rx_set)
        // Block until they all are resolved.
        .wait()
        // Check they all came out in the right order.
        .map(|values|
            values.iter()
                .enumerate()
                .all(|(index, &value)| index == value))
        // We'll be lazy and just unwrap the result.
        .unwrap();

    println!("Job is done. Values returned in order: {}", result);
}
```

Вызов карты ведет себя так же, как карта `Option<T>` или `Result<T, E>`. Он преобразует некоторое `Future<T>` в какое-то `Future <U>`. Запуск этого примера в выводе аналогичен следующему: 

```
0 --> START
1 --> START
3 --> START
2 --> START
4 --> START
5 --> START
6 --> START
7 --> START
8 --> START
9 --> START
4 +++ WAITED 124
4 <-- END
1 +++ WAITED 130
1 <-- END
0 +++ WAITED 174
0 <-- END
2 +++ WAITED 268
2 <-- END
6 +++ WAITED 445
6 <-- END
3 +++ WAITED 467
3 <-- END
9 +++ WAITED 690
9 <-- END
8 +++ WAITED 694
8 <-- END
5 +++ WAITED 743
5 <-- END
7 +++ WAITED 802
7 <-- END
Job is done. Values returned in order: true
```

В этом примере мы можем наблюдать, что все футуры запускались, ждали разное время, а затем заканчивались. Они не закончили по порядку, но получившийся вектор получился в правильном порядке. Опять же, этот результат не совсем удивителен, и мы могли бы сделать что-то очень похожее с каналами std.

     Помните, что футуры - это базовый строительный блок, а не решение, включающее батареи. Они предназначены для надстройки.

Мы рассмотрим еще один пример, который покажется довольно знакомым людям, которые использовали каналы из std, а затем мы начнем делать более интересные вещи.

Следующий примитив из Futures, который мы будем использовать, - это futures::sync::mpsc::channel, который ведет себя аналогично std::sync::mpsc::channel. Мы создадим канал, затем передадим отправитель tx другому потоку, а затем отправим серию сообщений по каналу. Затем сторона rx канала может использоваться аналогично итератору. 

```rust
use std::thread;
use futures::future::{Future, ok};
use futures::stream::Stream;
use futures::sync::mpsc;
use futures::Sink;

use fun_with_futures::sleep_a_little_bit;

const BUFFER_SIZE: usize = 10;

fn main() {
    // A channel represents a stream and will yield a series of futures.

    // We're using a bounded channel here with a limited size.
    let (mut tx, rx) = mpsc::channel(BUFFER_SIZE);

    thread::spawn(move || {
        println!("--> START THREAD");
        // We'll have the stream produce a series of values.
        for _ in 0..10 {

            let waited_for = sleep_a_little_bit();
            println!("+++ THREAD WAITED {}", waited_for);

            // When we `send()` a value it consumes the sender. Returning
            // a 'new' sender which we have to handle. In this case we just
            // re-assign.
            match tx.send(waited_for).wait() {
                // Why do we need to do this? This is how back pressure is implemented.
                // When the buffer is full `wait()` will block.
                Ok(new_tx) => tx = new_tx,
                Err(_) => panic!("Oh no!"),
            }

        }
        println!("<-- END THREAD");
        // Here the stream is dropped.
    });

    // We can `.fold()` like we would an iterator. In fact we can do many
    // things like we would an iterator.
    let sum = rx.fold(0, |acc, val| {
            // Notice when we run that this is happening after each item of
            // the stream resolves, like an iterator.
            println!("+++ FOLDING {} INTO {}", val, acc);
            // `ok()` is a simple way to say "Yes this worked."
            // `err()` also exists.
            ok(acc + val)
        })
        .wait()
        .unwrap();
    println!("SUM {}", sum);
}
```

Результат будет примерно таким: 

```
--> START THREAD
+++ THREAD WAITED 166
+++ FOLDING 166 INTO 0
+++ THREAD WAITED 554
+++ FOLDING 554 INTO 166
+++ THREAD WAITED 583
+++ FOLDING 583 INTO 720
+++ THREAD WAITED 175
+++ FOLDING 175 INTO 1303
+++ THREAD WAITED 136
+++ FOLDING 136 INTO 1478
+++ THREAD WAITED 155
+++ FOLDING 155 INTO 1614
+++ THREAD WAITED 90
+++ FOLDING 90 INTO 1769
+++ THREAD WAITED 986
+++ FOLDING 986 INTO 1859
+++ THREAD WAITED 830
+++ FOLDING 830 INTO 2845
+++ THREAD WAITED 21
<-- END THREAD
+++ FOLDING 21 INTO 3675
SUM 3696
```

Теперь, когда мы знакомы с этими основными идеями, мы можем перейти к работе с futures_cpupool.

## Погружаемся в пул ЦП

Ранее мы ссылались на идею использования футур с пулом ЦП, где нам не нужно было управлять тем, какой поток выполняет какую работу, но до сих пор в наших примерах нам все еще приходилось вручную раскручивать потоки. Так что давайте это исправим.

крэйт futures_cpupool предлагает нам эту функциональность. Давайте посмотрим на базовый пример, который делает почти то же самое, что и наш второй пример выше: 

```rust
use futures::future::{Future, join_all};
use futures_cpupool::Builder;

use fun_with_futures::sleep_a_little_bit;

// Feel free to change me!
const NUM_OF_TASKS: usize = 10;

fn main() {
    // Creates a CpuPool with workers equal to the cores on the machine.
    let pool = Builder::new()
        .create();

    // Create a batch of futures.
    let futures = (0..NUM_OF_TASKS)
        // Tell the pool to run a closure.
        .map(|index| pool.spawn_fn(move || {

            println!("{} --> START", index);
            let waited_for = sleep_a_little_bit();
            println!("{} <-- WAITED {}", index, waited_for);

            // We need to return a result!
            // Why? Futures were implemented with I/O in mind!
            let result: Result<_,()> = Ok(index);

            result
        })).collect::<Vec<_>>();

    // We wait on all the futures here and see if they came back in order.
    let result = join_all(futures)
        // Check they all came out in the right order.
        .map(|values|
            values.iter()
                .enumerate()
                .all(|(index, &value)| index == value))
        .wait()
        .unwrap();
    println!("Job is done. Values returned in order: {:?}", result)
}
```

Это выводит что-то похожее на следующее: 

```
0 --> START
1 --> START
2 --> START
3 --> START
4 --> START
5 --> START
6 --> START
7 --> START
0 <-- WAITED 37
8 --> START
2 <-- WAITED 86
9 --> START
3 <-- WAITED 110
6 <-- WAITED 326
9 <-- WAITED 458
4 <-- WAITED 568
5 <-- WAITED 748
7 <-- WAITED 757
1 <-- WAITED 794
8 <-- WAITED 838
Job is done. Values returned in order: true
```

На этот раз нам не нужно было управлять потоками или какой поток что делает. Бассейн только что справился с этим за нас. Это очень удобно. Мы можем быть довольно произвольными в отношении того, что мы делаем с пулом. Например, разные порожденные задачи могут возвращать разные типы: 

```rust
use futures::future::Future;
use futures_cpupool::Builder;

use fun_with_futures::sleep_a_little_bit;

fn main() {
    // Creates a CpuPool with workers equal to the cores on the machine.
    let pool = Builder::new()
        .create();

    // Note the two spawns return different types.
    let returns_string = pool.spawn_fn(move || {
        sleep_a_little_bit();
        // We need to return a result!
        let result: Result<_,()> = Ok("First");

        result
    });

    let returns_integer = pool.spawn_fn(move || {
        sleep_a_little_bit();
        // We need to return a result!
        let result: Result<_,()> = Ok(2);

        result
    });

    // Wait for the jobs to finish.
    let resulting_string = returns_string.wait()
        .unwrap();

    let resulting_integer = returns_integer.wait()
        .unwrap();
    
    println!("{}, {}", resulting_string, resulting_integer);
    // Returns `First, 2`
}
```

Это означает, что мы можем создать пул ЦП и делегировать ему произвольные задачи на протяжении всего жизненного цикла нашей программы. Возможно, вы захотите создать несколько пулов для предотвращения истощения, например пул с 2 рабочими для сетевых подключений, а другой с 2 рабочими для отложенных заданий.

## Посещение Tokio's Core

Проект tokio разрабатывался одновременно с футурами, и у проектов много авторов. В проекте есть ряд крэйтов, предназначенных для сборки асинхронных приложений. В крэйте tokio-core есть такие вещи, как основной цикл событий, обработчики TCP и таймауты. Поверх этого строятся крэйти, такие как tokio-proto и tokio-service, которые строятся на этих конструкциях.

Мы начнем с токио-ядра и выполним простой HTTP-запрос GET. Обратите внимание, поскольку на самом деле мы не используем HTTP-клиент, нам нужно обрабатывать все детали самостоятельно. Вот как это выглядит: 

```rust
use std::net::ToSocketAddrs;
use futures::future::Future;
use tokio_core::io::{read_to_end, write_all};
use tokio_core::reactor::Core;
use tokio_core::net::TcpStream;

const DOMAIN: &'static str = "google.com";
const PORT: u16 = 80;

fn main() {
    // Create the event loop that will drive this server.
    let mut core = Core::new().unwrap();
    let handle = core.handle();

    // Get a socket address from a domain name.
    let socket_addr = (DOMAIN, PORT).to_socket_addrs()
        .map(|iter| iter.collect::<Vec<_>>())
        .unwrap()[0];
    
    // Connect with a handle to the core event loop.
    let response = TcpStream::connect(&socket_addr, &handle)
        .and_then(|socket| {
            // Write a raw GET request onto the socket.
            write_all(socket, format!("\
                GET / HTTP/1.0\r\n\
                Host: {}\r\n\
                \r\n\
            ", DOMAIN))
        }).and_then(|(socket, _request)| {
            // Read the response into a buffer.
            read_to_end(socket, Vec::new())
        });

    // Fire the task off onto the event loop.
    let (_socket, data) = core.run(response).unwrap();

    // Parse the data and output it.
    println!("{}", String::from_utf8(data).unwrap());
}
```

Это выглядит немного иначе, чем то, что мы делали ранее с нашим футурой. Core::new() - это то, как мы создаем цикл событий или «Reactor», который мы можем получить. Дескрипторы, которые мы используем при выполнении таких задач, как TcpStream::Connect. Вы можете узнать больше об основах Reactor здесь.

Работа с типами, предоставляемыми Tokio, немного отличается от работы с типами, предоставляемыми std, однако многие идеи и концепции остаются теми же. Многие изменения связаны с различиями между синхронным и асинхронным вводом-выводом.

Ближе к концу примера у нас есть core.run(), который представляет собой способ запускать одноразовые задачи и получать от них возвращаемое значение, аналогично тому, как мы использовали футуры. Tokio также предоставляет функции spawn() и spawn_fn(), которые выполняются в фоновом режиме и должны самостоятельно обрабатывать ошибки, что делает его идеальным для таких задач, как реагирование на новые соединения.

Давайте поиграем с spawn_fn() в нашем следующем примере, создав простой сервер. 

```rust
use std::net::SocketAddr;
use futures::future::Future;
use futures::Stream;
use tokio_core::io::{read_to_end, write_all};
use tokio_core::reactor::Core;
use tokio_core::net::TcpListener;

const LISTEN_TO: &'static str = "0.0.0.0:8080";

fn main() {
    // Create the event loop that will drive this server.
    let mut core = Core::new().unwrap();
    let handle = core.handle();

    // Get a SocketAddr
    let socket: SocketAddr = LISTEN_TO.parse()
        .unwrap();
    
    // Start listening.
    let listener = TcpListener::bind(&socket, &handle)
        .unwrap();
    
    // For each incoming connection...
    let server = listener.incoming().for_each(|(stream, _client_addr)| {
        // Spawn the task on the reactor.
        handle.spawn_fn(|| {
            // Read until EOF
            read_to_end(stream, Vec::new())
                .and_then(|(socket, data)| {
                    // Write the recieved data.
                    write_all(socket, data)
                })
                // Errors have to be handled internally.
                .map(|_|())
                .map_err(|_|())
        });

        Ok(()) // keep accepting connections
    });

    // Run the reactor.
    core.run(server).unwrap();
}
// Throw some data at it with `echo "Test" | nc 127.0.0.1 8080`
```

Запуск echo "Test" | nc 127.0.0.1 8080 в другом терминале возвращается отправленные данные.

Однако наш пример здесь не очень полезен или идиоматичен! Например, мы не можем использовать что-то вроде telnet или curl для взаимодействия с ними. Но это только начало. Давай сделаем лучше.

Мы улучшим наш предыдущий пример, используя кодек. Кодеки позволяют нам описывать, как кодировать и декодировать кадры с помощью TcpStream (или чего-либо еще, что можно кадрировать()). Он просит нас определить тип In и Out, а также функции encode() и decode().

Вот как может выглядеть наш пример с EchoCodec: 

```rust
use std::net::SocketAddr;
use futures::future::Future;
use futures::Stream;
use std::io::Result;
use tokio_core::io::{Codec, EasyBuf, Io};
use tokio_core::reactor::Core;
use tokio_core::net::TcpListener;

const LISTEN_TO: &'static str = "0.0.0.0:8080";

// Codecs can have state, but this one doesn't.
struct EchoCodec;
impl Codec for EchoCodec {
    // Associated types which define the data taken/produced by the codec.
    type In = Vec<u8>;
    type Out = Vec<u8>;

    // Returns `Ok(Some(In))` if there is a frame, `Ok(None)` if it needs more data.
    fn decode(&mut self, buf: &mut EasyBuf) -> Result<Option<Self::In>> {
        // It's important to drain the buffer!
        let amount = buf.len();
        let data = buf.drain_to(amount);
        Ok(Some(data.as_slice().into()))
    }

    // Produces a frame.
    fn encode(&mut self, msg: Self::Out, buf: &mut Vec<u8>) -> Result<()> {
        buf.extend(msg);
        Ok(())
    }
}

fn main() {
    // Create the event loop that will drive this server.
    let mut core = Core::new().unwrap();
    let handle = core.handle();

    // Get a SocketAddr
    let socket: SocketAddr = LISTEN_TO.parse()
        .unwrap();
    
    // Start listening.
    let listener = TcpListener::bind(&socket, &handle)
        .unwrap();
    
    // For each incoming connection...
    let server = listener.incoming().for_each(|(stream, _client_addr)| {
        // Spawn the task on the reactor.
        handle.spawn_fn(|| {
            // Using our codec, split the in/out into frames.
            let (writer, reader) = stream.framed(EchoCodec).split();
            // Here we just sink any data recieved directly back into the socket.
            reader.forward(writer)
                // We need to handle errors internally.
                .map(|_|())
                .map_err(|_|())
        });

        Ok(()) // keep accepting connections
    });

    // Run the reactor.
    core.run(server).unwrap();
}
// Throw some data at it with `echo "Test" | nc 127.0.0.1 8080`
```

Если вы проверите это, вы обнаружите то же поведение, что и в предыдущем примере. С небольшими изменениями мы можем даже заставить его обрабатывать строки вместо целых сообщений, для этого мы позаимствуем код из примеров Tokio: 

```rust
// Codecs can have state, but this one doesn't.
struct EchoCodec;
impl Codec for EchoCodec {
    // Associated types which define the data taken/produced by the codec.
    type In = String;
    type Out = String;

    // Returns `Ok(Some(In))` if there is a frame, `Ok(None)` if it needs more data.
    fn decode(&mut self, buf: &mut EasyBuf) -> io::Result<Option<Self::In>> {
        match buf.as_slice().iter().position(|&b| b == b'\n') {
            Some(i) => {
                // Drain from the buffer (this is important!)
                let line = buf.drain_to(i);

                // Also remove the '\n'.
                buf.drain_to(1);

                // Turn this data into a UTF string and return it in a Frame.
                match str::from_utf8(line.as_slice()) {
                    Ok(s) => Ok(Some(s.to_string())),
                    Err(_) => Err(io::Error::new(io::ErrorKind::Other,
                                                "invalid UTF-8")),
                }
            },
            None => Ok(None),
        }
    }

    // Produces a frame.
    fn encode(&mut self, msg: Self::Out, buf: &mut Vec<u8>) -> io::Result<()> {
        buf.extend(msg.as_bytes());
        // Add the necessary newline.
        buf.push(b'\n');
        Ok(())
    }
}
```

Запустив этот пример, вы можете подключиться к nc 127.0.0.1 8080 и отправить несколько строк текста, а затем вернуть их. Обратите внимание, как нам вообще не пришлось менять остальную часть кода, чтобы внести это изменение? Все, что нам нужно было сделать, это изменить поведение кодека.

Теперь давайте попробуем создать кодек, который кодирует и декодирует запросы HTTP 1.1 GET и POST, сделанные из curl, которые мы можем построить позже: 

```rust
enum ExampleRequest {
    Get { url: String },
    Post { url: String, content: String },
}

enum ExampleResponse {
    NotFound,
    Ok { content: String },
}

// Codecs can have state, but this one doesn't.
struct ExampleCodec;
impl Codec for ExampleCodec {
    // Associated types which define the data taken/produced by the codec.
    type In = ExampleRequest;
    type Out = ExampleResponse;

    // Returns `Ok(Some(In))` if there is a frame, `Ok(None)` if it needs more data.
    fn decode(&mut self, buf: &mut EasyBuf) -> io::Result<Option<Self::In>> {
        let content_so_far = String::from_utf8_lossy(buf.as_slice())
            .to_mut()
            .clone();

        // Request headers/content in HTTP 1.1 are split by double newline.
        match content_so_far.find("\r\n\r\n") {
            Some(i) => {
                // Drain from the buffer (this is important!)
                let mut headers = {
                    let tmp = buf.drain_to(i);
                    String::from_utf8_lossy(tmp.as_slice())
                        .to_mut()
                        .clone()
                };
                buf.drain_to(4); // Also remove the '\r\n\r\n'.

                // Get the method and drain.
                let method = headers.find(" ")
                    .map(|len| headers.drain(..len).collect::<String>());

                headers.drain(..1); // Get rid of the space.

                // Since the method was drained we can do it again to get the url.
                let url = headers.find(" ")
                    .map(|len| headers.drain(..len).collect::<String>())
                    .unwrap_or_default();

                // The content of a POST.
                let content = {
                    let remaining = buf.len();
                    let tmp = buf.drain_to(remaining);
                    String::from_utf8_lossy(tmp.as_slice())
                        .to_mut()
                        .clone()
                };

                match method {
                    Some(ref method) if method == "GET" => {
                        Ok(Some(ExampleRequest::Get { url: url }))
                    },
                    Some(ref method) if method == "POST" => {
                        Ok(Some(ExampleRequest::Post { url: url, content: content }))
                    },
                    _ => Err(io::Error::new(io::ErrorKind::Other, "invalid"))
                }
            },
            None => Ok(None),
        }
    }

    // Produces a frame.
    fn encode(&mut self, msg: Self::Out, buf: &mut Vec<u8>) -> io::Result<()> {
        match msg {
            ExampleResponse::NotFound => {
                buf.extend(b"HTTP/1.1 404 Not Found\r\n");
                buf.extend(b"Content-Length: 0\r\n");
                buf.extend(b"Connection: close\r\n");
            },
            ExampleResponse::Ok { content: v } =>  {
                buf.extend(b"HTTP/1.1 200 Ok\r\n");
                buf.extend(format!("Content-Length: {}\r\n", v.len()).as_bytes());
                buf.extend(b"Connection: close\r\n");
                buf.extend(b"\r\n");
                buf.extend(v.as_bytes());
            }
        }
        buf.extend(b"\r\n");
        Ok(())
    }
}
```

Давайте будем честными с собой, это очень наивно и явно неполно! Однако он работает для curl -vvvv localhost: 8080 и curl -vvvv localhost: 8080 --data hello, и нам нужно немного узнать о том, как использовать Tokio. Все идет нормально! Давайте на этом опираться.

## Сервисы и протоколы

tokio-core по определению минимален. крэйти tokio-proto и tokio-service строятся поверх него для создания общих абстракций, которые мы можем использовать для различных приложений.

В этом следующем примере мы построим наш предыдущий пример нашего простого HTTP-сервера. Поскольку фрагменты кода начинают становиться значительными, давайте работать с изолированными битами, и мы покажем полный пример в самом конце.

Нашей целью будет создание сверхпростого веб-сервера, который будет отвечать на запросы GET / POST. POST для / cats с мяуканьем данных должен возвращать 200 OK со старым значением (если есть) в качестве данных, а любой будущий GET-запрос к / cats также должен возвращать мяу, пока он не будет заменен. Наша цель - простота и обучение, а не надежность или совершенство.

Во-первых, мы продолжим и определим наш протокол. Мы будем использовать простой конвейерный протокол без потоковой передачи. Этот код является довольно общим и, как правило, будет выглядеть примерно одинаково для разных реализаций. tokio-proto позволяют нам определять общий стиль нашей сети. Документация tokio-proto дает хорошее объяснение различий. 

```rust
use tokio_proto::pipeline::ServerProto;

// Like codecs, protocols can carry state too!
struct ExampleProto;
impl<T: Io + 'static> ServerProto<T> for ExampleProto {
    // These types must match the corresponding codec types:
    type Request = <ExampleCodec as Codec>::In;
    type Response = <ExampleCodec as Codec>::Out;

    /// A bit of boilerplate to hook in the codec:
    type Transport = Framed<T, ExampleCodec>;
    type BindTransport = Result<Self::Transport, io::Error>;
    fn bind_transport(&self, io: T) -> Self::BindTransport {
        Ok(io.framed(ExampleCodec))
    }
}
```

Сервис менее общий и обрабатывает нашу маленькую «базу данных» внутри себя. Поскольку Proto и Codec уже обрабатывают большинство сложных битов, это довольно просто. Сервисы - это многократно используемые абстракции, работающие по протоколам.

Вот как выглядит наш небольшой пример HTTP: 

```rust
// Surprise! Services can also carry state.
#[derive(Default)]
struct ExampleService {
    db: Arc<Mutex<HashMap<String, String>>>,
}

impl Service for ExampleService {
    // These types must match the corresponding protocol types:
    type Request = <ExampleCodec as Codec>::In;
    type Response = <ExampleCodec as Codec>::Out;

    // For non-streaming protocols, service errors are always io::Error
    type Error = io::Error;

    // The future for computing the response; box it for simplicity.
    type Future = BoxFuture<Self::Response, Self::Error>;

    // Produce a future for computing a response from a request.
    fn call(&self, req: Self::Request) -> Self::Future {
        println!("Request: {:?}", req);
        
        // Deref the database.
        let mut db = self.db.lock()
            .unwrap(); // This should only panic in extreme cirumstances.
        
        // Return the appropriate value.
        let res = match req {
            ExampleRequest::Get { url: url } => {
                match db.get(&url) {
                    Some(v) => ExampleResponse::Ok { content: v.clone() },
                    None => ExampleResponse::NotFound,
                }
            },
            ExampleRequest::Post { url: url, content: content } => {
                match db.insert(url, content) {
                    Some(v) => ExampleResponse::Ok { content: v },
                    None => ExampleResponse::Ok { content: "".into() },
                }
            }
        };
        println!("Database: {:?}", *db);

        // Return the result.
        future::finished(res).boxed()
    }
}
```

На данный момент у нас есть все части, и нам просто нужно собрать их все вместе. Это требует некоторых изменений в нашей функции main(), например: 

```rust
fn main() {
    // Get a SocketAddr
    let socket: SocketAddr = LISTEN_TO.parse()
        .unwrap();
    
    // Create a server with the protocol.
    let server = TcpServer::new(ExampleProto, socket);
    
    // Create a database instance to provide to spawned services.
    let db = Arc::new(Mutex::new(HashMap::new()));

    // Serve requests with our created service and a handle to the database.    
    server.serve(move || Ok(ExampleService { db: db.clone() }));
}
// Throw some data at it with `curl 127.0.0.1 8080/foo` and `curl 127.0.0.1 8080 --data bar`
```
Тестирование: 

```bash
$ curl localhost:8080/bear           
$ curl localhost:8080/bear --data foo
$ curl localhost:8080/bear           
foo%                                                                                                                                                            
$ curl localhost:8080/bear --data bar
foo%
$ curl localhost:8080/bear
bar%
```

Это здорово, мы создали небольшую сетевую базу данных с REST-ish API. Надеюсь, это научило вас немного о Futures и Tokio и вдохновило вас на дальнейшие эксперименты!

     Этот пост был частично поддержан за счет выделения времени от Asquera, и его можно найти здесь. Спасибо!

Завершить пример Tokio 

```rust
use std::net::SocketAddr;
use futures::future::{self, BoxFuture, Future};
use std::sync::{Mutex, Arc};
use std::{io, str};
use std::collections::HashMap;
use tokio_core::io::{Codec, EasyBuf, Io, Framed};
use tokio_proto::TcpServer;
use tokio_proto::pipeline::ServerProto;
use tokio_service::Service;

const LISTEN_TO: &'static str = "0.0.0.0:8080";

#[derive(Debug)]
enum ExampleRequest {
    Get { url: String },
    Post { url: String, content: String },
}

enum ExampleResponse {
    NotFound,
    Ok { content: String },
}

// Codecs can have state, but this one doesn't.
struct ExampleCodec;
impl Codec for ExampleCodec {
    // Associated types which define the data taken/produced by the codec.
    type In = ExampleRequest;
    type Out = ExampleResponse;

    // Returns `Ok(Some(In))` if there is a frame, `Ok(None)` if it needs more data.
    fn decode(&mut self, buf: &mut EasyBuf) -> io::Result<Option<Self::In>> {
        let content_so_far = String::from_utf8_lossy(buf.as_slice())
            .to_mut()
            .clone();

        // Request headers/content in HTTP 1.1 are split by double newline.
        match content_so_far.find("\r\n\r\n") {
            Some(i) => {
                // Drain from the buffer (this is important!)
                let mut headers = {
                    let tmp = buf.drain_to(i);
                    String::from_utf8_lossy(tmp.as_slice())
                        .to_mut()
                        .clone()
                };
                buf.drain_to(4); // Also remove the '\r\n\r\n'.

                // Get the method and drain.
                let method = headers.find(" ")
                    .map(|len| headers.drain(..len).collect::<String>());

                headers.drain(..1); // Get rid of the space.

                // Since the method was drained we can do it again to get the url.
                let url = headers.find(" ")
                    .map(|len| headers.drain(..len).collect::<String>())
                    .unwrap_or_default();

                // The content of a POST.
                let content = {
                    let remaining = buf.len();
                    let tmp = buf.drain_to(remaining);
                    String::from_utf8_lossy(tmp.as_slice())
                        .to_mut()
                        .clone()
                };

                match method {
                    Some(ref method) if method == "GET" => {
                        Ok(Some(ExampleRequest::Get { url: url }))
                    },
                    Some(ref method) if method == "POST" => {
                        Ok(Some(ExampleRequest::Post { url: url, content: content }))
                    },
                    _ => Err(io::Error::new(io::ErrorKind::Other, "invalid"))
                }
            },
            None => Ok(None),
        }
    }

    // Produces a frame.
    fn encode(&mut self, msg: Self::Out, buf: &mut Vec<u8>) -> io::Result<()> {
        match msg {
            ExampleResponse::NotFound => {
                buf.extend(b"HTTP/1.1 404 Not Found\r\n");
                buf.extend(b"Content-Length: 0\r\n");
                buf.extend(b"Connection: close\r\n");
            },
            ExampleResponse::Ok { content: v } =>  {
                buf.extend(b"HTTP/1.1 200 Ok\r\n");
                buf.extend(format!("Content-Length: {}\r\n", v.len()).as_bytes());
                buf.extend(b"Connection: close\r\n");
                buf.extend(b"\r\n");
                buf.extend(v.as_bytes());
            }
        }
        buf.extend(b"\r\n");
        Ok(())
    }
}

// Like codecs, protocols can carry state too!
struct ExampleProto;
impl<T: Io + 'static> ServerProto<T> for ExampleProto {
    // These types must match the corresponding codec types:
    type Request = <ExampleCodec as Codec>::In;
    type Response = <ExampleCodec as Codec>::Out;

    /// A bit of boilerplate to hook in the codec:
    type Transport = Framed<T, ExampleCodec>;
    type BindTransport = Result<Self::Transport, io::Error>;
    fn bind_transport(&self, io: T) -> Self::BindTransport {
        Ok(io.framed(ExampleCodec))
    }
}

// Surprise! Services can also carry state.
#[derive(Default)]
struct ExampleService {
    db: Arc<Mutex<HashMap<String, String>>>,
}

impl Service for ExampleService {
    // These types must match the corresponding protocol types:
    type Request = <ExampleCodec as Codec>::In;
    type Response = <ExampleCodec as Codec>::Out;

    // For non-streaming protocols, service errors are always io::Error
    type Error = io::Error;

    // The future for computing the response; box it for simplicity.
    type Future = BoxFuture<Self::Response, Self::Error>;

    // Produce a future for computing a response from a request.
    fn call(&self, req: Self::Request) -> Self::Future {
        println!("Request: {:?}", req);
        
        // Deref the database.
        let mut db = self.db.lock()
            .unwrap(); // This should only panic in extreme cirumstances.
        
        // Return the appropriate value.
        let res = match req {
            ExampleRequest::Get { url: url } => {
                match db.get(&url) {
                    Some(v) => ExampleResponse::Ok { content: v.clone() },
                    None => ExampleResponse::NotFound,
                }
            },
            ExampleRequest::Post { url: url, content: content } => {
                match db.insert(url, content) {
                    Some(v) => ExampleResponse::Ok { content: v },
                    None => ExampleResponse::Ok { content: "".into() },
                }
            }
        };
        println!("Database: {:?}", *db);

        // Return the result.
        future::finished(res).boxed()
    }
}

fn main() {
    // Get a SocketAddr
    let socket: SocketAddr = LISTEN_TO.parse()
        .unwrap();
    
    // Create a server with the protocol.
    let server = TcpServer::new(ExampleProto, socket);

    // Create a database instance to provide to spawned services.
    let db = Arc::new(Mutex::new(HashMap::new()));

    // Serve requests with our created service and a handle to the database.    
    server.serve(move || Ok(ExampleService { db: db.clone() }));
}
// Throw some data at it with `curl 127.0.0.1 8080/foo` and `curl 127.0.0.1 8080 --data bar`
```
