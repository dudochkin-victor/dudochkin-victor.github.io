+++
title = "Демократия"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

---
description: Модуль публичных референдумов
---

## `..Из технического описания Edgeware`

Модуль демократии создает и перемещает различные обязательные референдумы в процессе голосования.

Шаги

- [Предложение о референдуме](https://wiki.polkadot.network/docs/en/learn-governance#proposing-referenda) \(Участвующая информация: [Референдум](https://wiki.polkadot.network/docs/en /учиться-управление#референдумы)\)
- [Голосование за предложение](https://wiki.polkadot.network/docs/en/learn-governance#voting-for-a-proposal) \(Участвующая информация: [Добровольная блокировка](https://wiki. polkadot.network/docs/en/learn-governance#voluntary-locking)\)
- [Подсчет](https://wiki.polkadot.network/docs/en/learn-governance#tallying) \(Участвующая информация: [Адаптивное смещение кворума](https://wiki.polkadot.network/docs/en/ Learn-Governance#adaptive-quorum-biasing)\)

Referendums позволяет выполнять произвольный внешний вызов в цепочке от имени пользователя root.

Это позволяет выполнять довольно важные действия (например, настройку баланса, изменение временных меток и т. д.) без выполнения обновления во время выполнения.

В настоящее время модуль поддерживает только бинарное квалифицированное большинство голосов, если оно не представлено членом совета (который также поддерживает квалифицированное большинство и простое большинство). Люди голосуют в бюллетенях с бинарным выбором, которые подсчитываются по весу монеты с корректировкой времени блокировки. Вначале все голоса будут ограничены двухнедельным фиксированным периодом. В будущем модуль демократии будет расширен на множество «VotingTypes».

{% page-ref page="democracy-features.md" %}
