+++
title = "Своевременный поток данных: перезагрузка"
description = "Своевременный поток данных: перезагрузка"
weight = 1
+++

[Перевод](http://www.frankmcsherry.org/dataflow/naiad/2014/12/27/Timely-Dataflow.html)

Поток данных - популярная основа для многих масштабируемых вычислений, потому что структура вычислений фиксируется до выполнения, и единственная ответственность рабочих - реагировать на входящие данные. Основная ответственность хост-системы - доставить данные соответствующим работникам, и это вполне выполнимая задача.

Многие практические системы потоков данных нуждаются в дополнительных функциях, помимо простой доставки данных. Самая неотложная потребность - это возможность сообщить работнику, что он получил все ожидаемые данные. Это позволяет исполнителю завершить вычисления, выдать исходящие сообщения и очистить постоянное состояние. При более высокой степени детализации системы потоковых данных нуждаются в возможности сообщать работнику, когда они получили все данные для логического подмножества их ввода, например, о конце логического пакета, для которого требуется вывод.

## Naiad и своевременный поток данных

Своевременный поток данных - это название, которое мы использовали для описания модели потока данных Naiad. Модель включает в себя ориентированный (возможно, циклический) граф, по краям которого проходят сообщения (данные), и частично упорядоченный набор временных меток, украшающих каждое сообщение. Каждое сообщение условно существует в определенном месте на графике в логический момент времени, пару которых мы назвали отметкой точки. Как указано выше, эти отметки могут не иметь ничего общего с физическим временем; они часто отражали прогресс в потоке (указывающий эпоху входных данных) или прохождение цикла (указывающий итерацию).

Наложение нескольких структурных ограничений (исключенных) на граф потока данных обеспечивает частичный порядок в парах (местоположение, временная метка). Это означает, что для любого набора точечных меток сообщения в одном могут привести к созданию сообщений в другом, но между ними не может быть цикла. Хотя отсутствие полного порядка означает, что мы не можем назвать «самую раннюю» точечную метку в нашем наборе, мы, тем не менее, можем установить набор точечных меток, которые больше никогда не будут видны после того, как мы доставим соответствующие сообщения.

### Отслеживание прогресса в Naiad

Задача Naiad - поддерживать понимание того, какие отметки точек все еще используются, в любое время, чтобы каждый из ее сотрудников знал, когда они будут уверены, что никогда больше не увидят данную отметку. Здесь есть несколько подходов, я рекомендую документ "Обработка вне очереди" в качестве хорошей отправной точки. Однако подход Наяды можно довольно легко резюмировать.

Отслеживание прогресса в Naiad - это, по сути, распределенный подсчет ссылок. Каждый рабочий ведет счет для каждой точечной метки количества сообщений, которые, по его мнению, все еще активны (счетчики ссылок). Когда рабочий обрабатывает сообщение с отметкой точки, он может создавать выходные сообщения с другими отметками точки; он передает каждому рабочему приращение для каждой выходной точки и декремент для входной точки.

Naiad содержит несколько оптимизаций этого подхода, в основном определяющих моменты, когда работник может безопасно накапливать обновления счетчика ссылок, не рискуя при этом останавливать систему. В основном это включает в себя замечание, что ему еще нужно поработать для декремента, который он может отправить, и ему нужно просто подождать, пока он не завершит работу, поскольку частичная информация не позволит другим добиться прогресса.

### Ограничения подхода Наяды

Граф своевременного потока данных, которым управляет Naiad, имеет некоторую структуру, и Naiad представляет его в своей логике отслеживания прогресса просто в виде ориентированного графа. Хотя вершины могут иметь разные типы отметок времени, они ограничены кортежами целых чисел разной степени арности. Это связано с тем, что Naiad необходимо объявить общий тип для точечных штампов, чтобы их можно было сравнивать среди прочего. Использование полной универсальности своевременного потока данных, выбор различных частичных порядков для разных подграфов не представлялось возможным в Naiad с точки зрения типобезопасности.

## Переосмысление своевременного потока данных

Мне дали время подумать о том, как структурировать отслеживание прогресса в своевременном потоке данных, и я придумал что-то новое и интересное. Подход моделирует своевременные графы потоков данных иерархически, где подграф представляет собой вершину в виде вершины графического уровня над ним, скрывая детали реализации и представляя минимальный (подробный в ближайшее время) интерфейс координации.

Он еще не полностью построен, поэтому трудно сказать, будет ли он лучше, но у него есть несколько привлекательных преимуществ по сравнению с подходом Naiad: 

- Подграфы могут дополнять свои временные метки любым частично упорядоченным набором.

    Хотя целые числа по-прежнему популярны, это позволяет использовать такие типы, как DateTime в корневой области видимости, приоритеты (uint, uint) (обманутые в Naiad) и Vec <Stack> для рекурсивных вычислений. Это также приводит к тому, что не требуется динамически выделяемая память для основных типов временных меток.

- Подграфы могут легко координироваться между подмножествами работников.

    Это обеспечивает более тесную координацию, когда это необходимо, например, когда работники машины хотят агрегировать значения перед их передачей. Это также позволяет значительно упростить реализацию «олицетворения», часто комментируемой функции Naiad, которая ускоряет координацию, когда известно, что некоторые ребра не будут обмениваться данными.

- Подграфы могут быть реализованы на других языках или в других средах выполнения.

    Наш выбор C # и .NET не был особенно популярен, но в то же время использование Java во многом противоречит созданию эффективных систем. Естественным компромиссом является создание логики координации и других необходимых сервисов на языке, который пользователь не ожидает знать, и позволяющий им писать свои приложения в выбранной среде.

- Подграфы могут координироваться без участия плоскости данных.

    Дизайн Naiad в значительной степени достиг этого, но он был слишком удобен в реализации, чтобы связывать передачу данных с обновлением хода выполнения. Эта конструкция требует, чтобы они изначально были отдельными, хотя, очевидно, можно построить удобные слои. Эта функция предназначена для поддержки передачи данных через другие носители, включая распределенные файловые системы и общие очереди.

## Предстоящие публикации

Моя цель в этом проекте - увидеть, насколько можно дразнить идею системы больших данных как операционной системы; каков минимальный набор сервисов и функций, которые должна обеспечивать платформа для масштабируемых вычислений, не ограничивая иным образом выполняемые программы.

Я планирую размещать сообщения по нескольким связанным темам в течение следующих нескольких недель, по мере того, как будет достигнут прогресс. В настоящее время существует прототип, работающий с новым подходом, выполняющий разные действия, начиная с обыденного параллельного использования данных(), и заканчивая новыми и интересными подграфами, поддерживаемыми внешними процессами, подключенными к координатору только с помощью каналов unix (хорошо, я признаю it; внешние процессы тоже находятся в Rust). Он обменивается данными по потокам, но еще не по сетевым соединениям, и есть некоторые инструменты, которые делают его более приятным в использовании. 