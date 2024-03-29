+++
title = "Голосование за Совет"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

---
description: 'https://wiki.polkadot.network/docs/en/maintain-guides-how-to-vote-councillor'
---

## Вступление <a id="__docusaurus"></a>

Совет — это выборный орган сетевых учетных записей, который предназначен для представления пассивных заинтересованных сторон Edgeware. У совета есть две основные задачи в управлении: предлагать референдумы и накладывать вето на опасные или злонамеренные референдумы. Это руководство проведет вас через процесс голосования за советников на выборах.

### Голосование за советников

Голосование за советников требует, чтобы вы заблокировали свой EDG на время вашего голосования. Как и при выборах валидатора, вы можете утвердить до 16 разных советников, и ваш голос будет уравнен среди выбранной группы. В отличие от выборов валидатора, для ваших зарезервированных токенов не существует периода развязки. Как только вы удалите свой голос, ваши токены снова станут ликвидными.

> Предупреждение: вы несете ответственность за то, чтобы не вкладывать весь свой баланс в зарезервированное значение, когда вы голосуете за советников. Лучше иметь _хотя бы_ несколько KSM для оплаты транзакционных комиссий.

Перейдите на [Панель инструментов Polkadot Apps](https://polkadot.js.org/apps), **подключитесь к конечной точке Edgeware** и щелкните вкладку «Совет». В правой части окна есть две синие кнопки, нажмите на ту, что с надписью «Проголосовать».

![](https://wiki.polkadot.network/docs/assets/council/vote.png)

Поскольку совет использует голосование по одобрению, когда вы голосуете, вы сигнализируете, кого из валидаторов вы одобряете, и ваши проголосовавшие токены будут уравнены среди выбранных кандидатов. Выберите до 16 кандидатов в совет, переместив ползунок в положение «Да» для каждого кандидата, которого вы хотите избрать. Когда вы сделали правильную настройку, отправьте транзакцию.

#### Голосование за членов совета [¶](https://guide.kusama.network/en/latest/try/governance/#voting-for-council-members) <a id="voting-for-council-members"></a>

- Используя [пользовательский интерфейс Polkadot](https://polkadot.js.org/apps/), убедитесь, что у вас есть учетная запись, и выберите сеть Kusama на [вкладке настроек](https://polkadot.js.org/). приложения/#/настройки).
- Перейдите на вкладку [Совет](https://polkadot.js.org/apps/#/council), чтобы увидеть текущих кандидатов в совет.
- На [форуме Кусамы](https://forum.kusama.network/) вы найдете ветку, посвященную членам совета, предлагающим свои кандидатуры, и получите дополнительную информацию.
- Перейдите на [вкладку Extrinsics](https://polkadot.js.org/apps/#/extrinsics), выберите учетную запись, с помощью которой вы хотите проголосовать, и выберите «совет» в разделе «отправить следующие внешние данные». Выберите «setApprovals(votes, index)» во втором столбце, введите индекс совета кандидата, за которого вы хотите проголосовать, и выберите «да», чтобы поддержать кандидата, и «нет», чтобы проголосовать против него.
- Нажмите «Отправить транзакцию» и подпишите транзакцию.

![](https://wiki.polkadot.network/docs/assets/council/vote_for_yourself.png)

Вы должны увидеть свой голос в интерфейсе сразу после того, как ваша транзакция будет включена.

### Удаление вашего голоса

Чтобы вернуть зарезервированные токены, вам нужно будет удалить свой голос. Удаляйте свой голос только тогда, когда вы закончите участвовать в выборах и больше не хотите, чтобы ваши зарезервированные жетоны учитывались для одобренных вами советников.

Перейдите на вкладку «Extrinsics» на [панели инструментов Substrate Apps](https://polkadot.js.org/apps).

Выберите учетную запись, голосование по которой вы хотите удалить, и выберите опции «electionsPhragmen -> removeVoter\(\)» и отправьте транзакцию.

![](https://wiki.polkadot.network/docs/assets/council/remove_vote.png)

Когда транзакция будет включена в блок, ваши зарезервированные токены должны снова стать ликвидными, и ваш голос больше не будет учитываться ни для каких советников на выборах, начинающихся в следующем сроке.
