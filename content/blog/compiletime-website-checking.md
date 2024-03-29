+++
title = "Проверка веб-сайта во время компиляции"
description = "Проверка веб-сайта во время компиляции"
weight = 1
+++

[Перевод](https://deterministic.space/compile-time-website-checking.html) | Автор оригинала: Pascal Hertleif

Мне нужен генератор статического сайта, который применяет кучу правил во время компиляции, чтобы не могли возникнуть целые классы ошибок. Подобно моим идеям из прошлого года.

## Проверки во время компиляции

1. Все шаблоны определяют свои структуры входных данных (например, как схему JSON).
    - К необязательным полям можно получить доступ только в условии if или с явными значениями по умолчанию, иначе компиляция вызовет панику (например, (неявно?) Вызов Rust's Option::unwrap).
    - Сюда входят минимальные размеры изображения.
2. Таким же образом файлы содержимого проверяются на соответствие схемам содержимого (например, схеме JSON), а также схемам шаблонов, в которых они используются.
3. Вывод HTML будет проверяться с помощью
    - валидатор HTML5
    - простая проверка SEO-работоспособности
        - Вменяемый план
        - Мета-теги
    - валидатор для микроформатов

Оптимизация всей страницы после компиляции:

- Удалите весь неиспользуемый CSS
- Убедитесь, что все ссылки работают

## Другие примечания

- Построить дерево зависимостей
    - с шаблонами страниц в корне и используемыми частями / необходимыми активами в качестве листьев
    - с содержанием страниц и содержания включает
- Предварительная компиляция вариантов шаблона (генерация кода)
- Храните как можно больше информации, чтобы выдавать хорошие ошибки
- Поддержка автоматической перезагрузки шаблонов и ресурсов.
    - Сначала это могло быть достаточно быстро, чтобы всегда перекомпилировать
    - Использование генерации кода и дерева зависимостей: кэш оставляет, делает недействительным дерево
- Добавить простой маршрутизатор для создания карты страниц
- В идеале писать как отдельное приложение
    - node.js в настоящее время имеет лучшую экосистему, но его сложно / невозможно связать как двоичный при использовании собственных зависимостей (что мы, конечно, будем)
    - Оптимизация может зависеть от таких вещей, как PhantomJS.
- Конечно:
    - Автоматически создавать пользовательские интерфейсы редактора из схем контента
    - Автоматическое создание шаблона формы руководств по стилю, заполненного случайными данными

Спасибо за чтение.