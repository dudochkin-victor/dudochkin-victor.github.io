+++
title = "Проверка статуса продолжительности блокировки и даты разблокировки"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Есть два способа проверить продолжительность блокировки. Самый простой способ — через инструмент Commonwealth Unlock или Lockdrop Stats, другой способ требует, чтобы вы просмотрели транзакцию, инициирующую блокировку, в обозревателе блоков.

{% tabs %}

Посетите либо

- [Инструмент статистики Lockdrop Содружества](https://commonwealth.im/edgeware/stats)
- [Инструмент разблокировки Содружества](https://commonwealth.im/edgeware/unlock) \ (также может автоматизировать разблокировку ETH \)

Введите свой участвующий адрес ETH, чтобы просмотреть все пользовательские контракты блокировки, связанные с этим адресом.

## Предпосылки

- ETH-адрес, с которого вы заблокировали
- Транзакция, которую вы отправили, чтобы активировать запись блокировки.

## Шаги

Найдите транзакцию блокировки в [обозревателе блоков, таком как Etherscan.io](http://etherscan.io), отметьте дату отправки.

В деталях транзакции найдите Входные данные \(см. снимок экрана ниже\) и найдите сегмент, который показывает `[0]:0000000000 ...`, и обратите внимание на число в конце сегмента.![](../../.gitbook/assets/screen-shot-2020-02-13-at-12.02.31-pm%20%281%29%20%281%29.png)

Если это число..

`0` = продолжительность блокировки три месяца

`1` = шесть месяцев блокировки

`2` = Двенадцать месяцев блокировки

Добавьте значение месяца к дате транзакции, чтобы найти дату разблокировки и посмотреть, не прошла ли эта дата. Если это так, ваш LUC готов выпустить ваш ETH.

{% page-ref page="../../quickstart/retrive-your-eth/" %}

### Пример

Например, на снимке экрана выше дата — «15 июня 2019 года», а продолжительность блокировки — «2», что означает 12 месяцев. 15 июня 2019 г. плюс 12 месяцев — это год спустя или ~15 июня **2020 г.**