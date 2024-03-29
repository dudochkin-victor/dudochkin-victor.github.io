+++
title = "Библиотеки параллелизма Rust"
description = "Библиотеки параллелизма Rust"
weight = 1
+++

[Перевод](https://deterministic.space/rust-concurrency-libraries.html) | Автор оригинала: Pascal Hertleif

Краткий обзор.

## Ввод / вывод

- Модель потока данных с футурами
- Позвольте циклу событий tokio обрабатывать неблокирующий ввод-вывод
- Используйте библиотеки, построенные на tokio, которые реализуют нужные вам протоколы

## Параллельная обработка

- Используйте rayon, чтобы ваши итераторы использовали все ядра ЦП
- Используйте потоки с областью видимости Crossbeam для параллельного выполнения задач вручную.

## Структуры данных

- Используйте структуры данных без блокировки данных crossbeam, если вам нужно получить доступ к данным из нескольких потоков.

Спасибо за чтение.