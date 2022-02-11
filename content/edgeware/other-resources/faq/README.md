+++
title = "Часто задаваемые вопросы"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

**Как мне получить свой ETH после завершения Lockdrop?**

[Используйте инструмент разблокировки на сайте Commonwealth.im](https://commonwealth.im/#!/unlock)

[Читайте полные инструкции на blog.edgewa.re](https://blog.edgewa.re/luc-101-retrieving-your-eth-from-the-lockdrop-contract/)

Чтобы вернуть свой ETH из пользовательского контракта Lockdrop, после окончания срока действия блокировки отправьте транзакцию с нулевой или большей суммой с любой учетной записи на адрес контракта LUC.

Затем LUC вернет ETH на исходный адрес, с которого этот ETH был отправлен на главный контракт Lockdrop в начале события Lockdrop. [Читайте более подробное объяснение на Blog.edgewa.re](https://blog.edgewa.re/luc-101-retrieving-your-eth-from-the-lockdrop-contract/)

**Как я могу получить доступ к EDG, который мне выделили с помощью блокировки \(включая сигнализацию\)?**

На момент участия в локдропе у вас должна быть сгенерирована мнемоническая сид-фраза. Пожалуйста, импортируйте его в пользовательский интерфейс Polkadot \(необязательно через расширение Enzyme или расширение polkadot\(.js\)\) или в неофициально поддерживаемую версию кошелька Math для Android, и вы получите доступ к своему EDG! Подробнее о взаимодействии с учетными записями читайте здесь: [https://docs.edgewa.re/understanding-edgeware/accounts](https://docs.edgewa.re/understanding-edgeware/accounts)

**Сколько EDG будет отчеканено в событии Genesis Lockdrop?**

Генезис сети Edgeware будет чеканить 5 миллиардов токенов EDG.

Из этих 5 миллиардов 90% или 4,5 миллиарда будут распределены через процесс блокировки. Оставшиеся 10% распределяются между:

4,5% для Commonwealth Labs, разработчиков Edgeware, и не будут использоваться для финансирования разработки. 3% компании Parity Tech за услуги поддержки их продукта Substrate. 2,5% зарезервировано для поощрения сообщества, разработки OSS или других целей успеха сети.

За 90% распределенных в Lockdrop Event участники lockdrop получают «акции» или возможность получать EDG в определенной пропорции к их заблокированным или сигнализированным ETH. На следующей диаграмме показаны веса этих акций:

| Duration of Lock | Method | Weighting | EDG Received                      |
|:---------------- |:------ |:--------- |:--------------------------------- |
| 0 months         | Signal | 0.20x     | 25% at launch, 75% 265 days later |
| 3 months         | Lock   | 1.00x     | Upon Network Launch               |
| 6 months         | Lock   | 1.30x     | Upon Network Launch               |
| 12 months        | Lock   | 2.20x     | Upon Network Launch               |

Окончательные доли — это доля 90%, а не определенное значение, пока не закроется блокировка и не станет известно общее количество заблокированных ETH. Можно заработать больший вес доли за счет блокировки на более длительный срок или блокировки раньше.

**Бонус за раннее участие Lockdrop: расписание**

| Date Range \(2019\)   | Bonus | Eth Cap                 |
|:--------------------- |:----- |:----------------------- |
| June 1 - June 15      | S50%  | No Cap                  |
| June 16 - June 30     | 35%   | No Cap                  |
| July 1 - July 15      | 23%   | No Cap                  |
| July 16 - July 30     | 14%   | No Cap                  |
| July 31 - August 14   | 8%    | No Cap                  |
| August 15 - August 29 | 5%    | No Cap                  |
| August 30 - August 31 | 0%    | No Cap, end of lockdrop |

Для [https://stats.edgewa.re](https://stats.edgewa.re) мы планируем включить калькулятор с инструментом оценки.

**Как просмотреть статистику участия и результаты блокировки?**

На Commonwealth.im: [https://commonwealth.im/\#!/stats/edgeware](https://commonwealth.im/#!/stats/edgeware)

**Я сигнализирую участнику. Когда мой EDG можно будет передавать?**

25% EDG участников сигнализации уже разблокированы \(можно передавать\), а остальные 75% EDG принадлежат до 17 февраля 2021 г. \(1 год с момента запуска основной сети\).

**Почему нужен бесконечный запас/нет максимального ограничения на предложение/инфляция?**

Инфляция необходима для вознаграждения тех, кто защищает сеть \(валидаторы и номинаторы\), а также используется для роста экосистемы за счет казны. В настоящее время инфляция установлена ​​на уровне 95 EDG за блок. Никто не может иметь прямой доступ к этим EDG. Приблизительно 20% распределяются в качестве вознаграждения за стекинг, а остальное идет в казну.

**Почему ончейн-казначейство?**

Это ончейн-фонд, к которому никто не имеет прямого доступа! Edgeware занимает позицию инкубатора и стремится поддерживать инновационные проекты/идеи, используя сеть Edgeware в качестве платформы для развертывания. Ончейн-казначейство обеспечивает финансирование таких проектов, ончейн-подрядчиков и многих инновационных концепций по получению одобрения от держателей EDG прямо или косвенно (посредством ончейн-совета).

**Почему я не вижу свой баланс после обновления сети?**

В связи с обновлением сети некоторые учетные записи нуждаются в переносе. Любой может перенести чью-либо учетную запись. Более подробная информация здесь: [https://commonwealth.im/edgeware/proposal/discussion/625-edgeware-postupgrade-account-migration](https://commonwealth.im/edgeware/proposal/discussion/625-edgeware-postupgrade -аккаунт-миграция)

## Технические вопросы и ответы

**В чем разница между узлом и обновлением во время выполнения? Как они взаимодействуют?**

Обновление узла — это обновление клиентского программного обеспечения. Эти изменения в основном связаны с сетевым программным обеспечением и/или оптимизацией системы, обрабатывающей исполняемый двоичный файл.

В то время как обновление во время выполнения — это изменение в базовом бинарном файле WebAssembly, который запускается программным обеспечением узла/клиента. Это изменения в функции перехода состояния блокчейна.

