+++
title = "Оптимистическое подтверждение и слэшинг"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Прогресс по оптимистичному подтверждению можно отслеживать здесь

https://github.com/solana-labs/solana/projects/52

В конце мая бета-версия основной сети переходит на версию 1.1, а тестовая сеть — на версию 1.2. С 1.2 тестовая сеть будет вести себя так, как если бы у нее была оптимистичная завершенность, пока не более 4,66% валидаторов действуют злонамеренно. Приложения могут предположить, что 2/3+ голосов, наблюдаемых в сплетнях, подтверждают блокировку или что не менее 4,66% сети нарушают протокол.

## Как это работает?

Общая идея заключается в том, что валидаторы должны продолжать голосование после своего последнего форка, если только валидатор не сможет создать доказательство того, что их текущий форк может не достичь окончательности. Способ, которым валидаторы строят это доказательство, заключается в сборе голосов за все форки, кроме их собственных. Если набор действительных голосов составляет более 1/3+X от веса ставки эпохи, у текущего форка валидаторов может не быть возможности достичь окончательности 2/3+.
Валидатор хэширует доказательство (создает свидетеля) и отправляет его своим голосом за альтернативный форк. Но если 2/3+ проголосуют за один и тот же блок, ни один из валидаторов не сможет построить это доказательство, и, следовательно, ни один валидатор не сможет переключать форки, и этот блок в конечном итоге будет завершен.

## Компромиссы

Запас прочности составляет 1/3+X, где X представляет собой минимальную сумму ставки, которая будет сокращена в случае нарушения протокола. Компромисс заключается в том, что живучесть теперь снижается в 2 раза в худшем случае. Если недоступно более 1/3–2X сети, сеть может остановиться и возобновит финализацию блоков только после того, как сеть восстановится ниже 1/3–2X отказавших узлов. До сих пор мы не наблюдали серьезной недоступности в нашей основной сети, космосе или tezos. Для нашей сети, которая в основном состоит из систем высокой доступности, это кажется маловероятным. В настоящее время мы установили пороговый процент на 4,66%, что означает, что если 23,68% не пройдены, сеть может прекратить финализацию блоков. Для нашей сети, которая в основном состоит из систем высокой доступности, снижение доступности на 23,68% кажется маловероятным. Коэффициент 1:10^12, при условии пяти узлов с 4,7% ставок и временем безотказной работы 0,995.

## Безопасность

Долгосрочное среднее количество голосов на слот составило 670 000 000 голосов / 12 000 000 слотов, или 55 из 64 валидаторов для голосования. Сюда входят пропущенные блоки из-за сбоев производителя блоков. Когда клиент видит 55/64, или ~86%, подтверждающих блок, он может ожидать, что ~24% или `(86 - 66,666.. + 4,666..)%` сети должны быть перерезаны, чтобы этот блок не завершился. доработка.

## Почему Солана?

Этот подход можно реализовать и в других сетях, но сложность реализации в Solana значительно снижается, поскольку наши голоса имеют доказуемые тайм-ауты на основе VDF. Неясно, можно ли легко построить доказательства переключения в сетях со слабыми предположениями о времени.

## Сокращение дорожной карты

Слэшинг — сложная проблема, и она становится еще сложнее, когда цель сети — иметь минимально возможную задержку. Компромиссы особенно очевидны при оптимизации задержки. Например, в идеале валидаторы должны подавать и распространять свои голоса до того, как память будет синхронизирована с диском, а это означает, что риск повреждения локального состояния намного выше.

По сути, наша цель слэшинга — 100% слеш в тех случаях, когда нода злонамеренно пытается нарушить правила безопасности и 0% во время обычной работы. Как мы стремимся достичь этого, так это сначала внедрить доказательства косой черты без какой-либо автоматической косой черты.

Прямо сейчас, для регулярного консенсуса, после нарушения безопасности сеть остановится. Мы можем проанализировать данные и выяснить, кто виноват, и предложить урезать ставку после перезапуска. Аналогичный подход будет использоваться с оптимистичной конф.
Нарушение безопасности оптимистичной конфигурации легко заметить, но при нормальных обстоятельствах нарушение безопасности оптимистического подтверждения может не остановить работу сети. Как только нарушение будет обнаружено, валидаторы заморозят затронутую долю в следующей эпохе и примут решение о следующем обновлении, если нарушение требует сокращения.

В долгосрочной перспективе транзакции должны иметь возможность вернуть часть залога, если будет доказано нарушение оптимистичной безопасности. В этом сценарии каждый блок эффективно застрахован сетью.