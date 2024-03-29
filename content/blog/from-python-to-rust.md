+++
title = "От Python до Rust"
description = "От Python до Rust"
weight = 1
hash = "c58ac531"
+++

[Перевод](https://levelup.gitconnected.com/from-python-to-rust-fc43d6985670) | Автор оригинала: Maxwell Flitton

Моим первым языком был Python. Я начал заниматься программированием серверных веб-систем Python в области финансовых технологий, а затем - машинным обучением в области машиностроения. Я помогал преподавать питон в университетах и использовал его в сторонних проектах. Мне нравится Python. С ним легко справиться, это непринужденно, и вы можете сосредоточиться на проблеме, которую пытаетесь решить. Его гибкость позволяет создавать всевозможные странные и чудесные шаблоны дизайна, а его волшебные методы позволяют окунуться в воду с помощью механизмов очистки и т.д.

![](/imgs/posts/c58ac531_01.png)

Я очень редко чувствовал себя сдержанным при использовании Python. Однако я понял, что просто глубоко полагаюсь на один язык. После изучения ряда учебников Python по чистому объектно-ориентированному программированию, расширенным шаблонам проектирования, параллелизму, рефакторингу устаревшего кода и разработке через тестирование я освоился с созданием гибких, многоразовых, тестируемых модулей Python, импортированных другими разработчиками и используется на работе. Может быть… я мог бы найти более быстрый и более сложный инструмент, который можно было бы добавить к своему поясу. Мой план состоял в том, чтобы попытаться Rust, и если это будет слишком сложно, я спущусь на Голанг. В конце концов, я прекрасно понимал, что Python - не самый сложный язык для освоения. Хотя Rust был быстрее, имел отличные возможности параллелизма, меньшую память и заставлял вас думать по-другому, я также отметил множество предупреждений о крутой кривой обучения.

Однако на самом деле предупреждения не сбылись. Самым сложным было придумать какой-нибудь полезный проект, в который можно загнать Rust. Сначала я поигрался с гипер-веб-фреймворком. Не имея реальной цели, я создал базовые API, которые на самом деле мало что сделали. Некоторая документация была немного скудной, и я в конечном итоге опубликовал разочаровывающий вопрос о StackOverflow по сериализации JSON, но в конечном итоге пнул себя, когда получил ответ. Переводчик слегка ударил меня по голове, но ничего особенного. В основном он жаловался, что тип непоследователен, но это больше похоже на «о, спасибо, что указали на это», а не на борьбу с компилятором. Я бы предпочел обнаруживать ошибки во время компиляции, а не молчаливо давать сбой в продакшене. Другая ошибка заключалась в том, что в заявлении о совпадении не учитывались все возможные результаты. Опять же, это было «спасибо», потому что вы все равно должны это делать. Проверка кредита была легкой головной болью, но я должен подчеркнуть, она была легкой. Кратко это можно описать следующим образом:

![](/imgs/posts/c58ac531_02.png)

Иногда вас могут остановить, но компилятор очень полезен для определения этого. Если следовать приведенным выше правилам, это действительно несложно.

«Хорошо, это не так уж плохо, - подумал я. Однако эффект Даннинга-Крюгера мог сыграть со мной злую шутку. Я напомнил себе, что я новичок в Rust и играх. Игра отличается от решения конкретной проблемы. Поэтому я решил написать программу командной строки, которая взаимодействовала бы с API-интерфейсом docker и автоматизировала ряд команд git и docker для работы. Может быть, попытка решить проблему из конца в конец приведет к разочарованию, которое выдергивает волосы.

Это не так. С конструкцией крэйтов приятно работать. Если вы с самого начала сведете количество предупреждений к нулю, это будет легко поддерживать. Хотя я определенно не мастер Rust, я нашел его безопасным, быстрым и забавным в работе. Если у вас есть сильные основы программной инженерии, я рекомендую вам сразу погрузиться в нее. Если вы не чувствуете себя готовым, я рекомендую следующие книги: 

- Python 3: объектно-ориентированное программирование, второе издание (Дасти Филлипс)
- Чистый код в Python: реорганизуйте устаревшую кодовую базу (Мариано Анайя)
- Расширенное программирование на Python: создавайте высокопроизводительные, параллельные и многопоточные приложения с помощью python (д-р Габриэле Ланаро)

Итак, помимо своего приятного удивления, что еще я узнал:

Да, он быстрый, эффективный и описывается как системный язык. Люди отбрасывают этот ярлык, а затем откидываются назад, как будто это лишает его права на веб-разработку. Что на самом деле означает системное программирование? Что ж, лучший консенсус, который я смог найти по этому определению, заключался в следующем: программирование с распределением ресурсов является большой частью внимания. Это определенно играет роль в некоторых веб-разработках.

Другие сравнивают его с C++. Есть некоторые сравнения, но они определенно не идентичны. Я бы не сказал, что если бы вы не использовали C++ для решения проблемы, вы бы не использовали Rust. Rust безопасен для памяти, а компилятор действительно хорош и понимает, что вы делаете, и помечает ошибки. Безопасность памяти - важная вещь. 70% проблем безопасности Microsoft в ходе одного известного аудита были связаны с проблемами управления памятью. Таким образом, потенциальные проблемы безопасности, возникающие при кодировании веб-сервера на C++, отсутствуют при кодировании на Rust.

Абстракции высокого уровня легко кодировать из-за безопасности памяти. Если вы посмотрите на фреймворк Rocket, вы увидите, что он в значительной степени имитирует фреймворк Flask в python для создания веб-сервера. Функции с декоратором определяют маршруты. Как только вы поймете язык, с ним будет довольно легко работать. Когда ему удается скомпилировать, вы очень уверены, что это безопасно. 