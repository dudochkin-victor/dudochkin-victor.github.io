+++
title = "Силиконовые кабачки"
description = "Силиконовые кабачки"
weight = 1
+++

[Перевод](https://pascalhertleif.de/artikel/silicon-zucchini/) | Автор оригинала: Pascal Hertleif

Описание генератора статического сайта с использованием схемы JSON для проверки входных данных, создания примеров шаблонов и визуализации интерфейсов редактора.

Сегодня я хотел бы представить некоторые идеи о том, как создавать (статические) веб-сайты с помощью схем данных [1] [1]

Я уже намекал на некоторые из них ранее в контексте некоторых экспериментов.
. В этом посте сначала будет немного рассказано об истории создания систем управления контентом и генераторов статических сайтов, а затем описана моя мотивация к разработке нового генератора статических сайтов и в заключение я расскажу о том, чего я хочу достичь с помощью этого проекта.

## Системы управления контентом и генераторы статических сайтов

Во-первых, давайте немного поговорим о системах управления контентом (CMS) и генераторах статических сайтов (SSG). В то время как первые могут быть настроены, чтобы позволить нетехническим людям легко обновлять содержимое веб-страницы, вторые в основном написаны для использования другими разработчиками [2] [2]

Конечно, это не всегда верно. Каждая CMS теоретически может также использовать SSG. И ничто не мешает написать красивый интерфейс редактирования для SSG.
.

Но давайте сначала уберемся с дороги. Есть одна функция, которую дает вам CMS, которая по сути противоположна тому, что могут предложить SSG: динамический контент (конечно). Вам действительно стоит использовать CMS, если вы хотите, чтобы посетители могли

1. добавлять комментарии (без использования внешних сервисов),
2. создать учетные записи для доступа к ограниченному контенту,
3. или быстро выполните поиск и фильтрацию в большой базе данных вашего сайта (т. Е. На сервере и без предварительного рендеринга всех возможных представлений).

Если вы хотите чего-либо из этого, SSG вам не подойдет. Однако в большинстве случаев люди не нуждаются в этих функциях и по-прежнему используют CMS для удобства.

### Динамический, но фиксированный: CMS

Давайте подумаем, как структурированы данные веб-сайта. Большинство CMS используют реляционные базы данных, в которых они сохраняют список содержимого страницы, сопоставленного страницам.

Простым примером является функция «страницы» в WordPress ›Blog Tool, Publishing Platform и CMS - wordpress.org. Добавляемые вами страницы в основном представляют собой просто записи с заголовком (в виде обычного текста), полем содержимого (в HTML) и URL-адресом (относительно базы, например, / about /). Также могут быть дополнительные поля для категорий и авторов (и, возможно, некоторые данные о встроенных медиа-файлах), но они не являются обязательными. Эта схема может быть записана так: 

```js
{title: "string", content: "string", url: "string"}
```

TYPO3 TYPO3 - Enterprise Open Source CMS - typo3.org (более крупная CMS) основан на конструкции, согласно которой страница содержит различные элементы контента (например, форматированный текст с заголовком, текстом и изображениями, настраиваемые формы). Вы можете представить это так: 

```js
{
  title: "string",
  url: "string",
  content: [{
    headline: "string"
    text: "string",
    pictures: [{url: "string"}]
  }]
}
```

Настройка этих схем по вкусу пользователя - одна из самых трудоемких задач при настройке CMS. Даже в этом случае бывает трудно предвидеть все возможные варианты использования. Иногда проще иметь соглашение («всегда начинать страницы с большого изображения») вместо того, чтобы применять это в CMS.

### Делай, что хочешь: SSG

В отличие от этих строгих схем баз данных, популярные генераторы статических сайтов, такие как Jekyll Jekyll • Простые статические сайты с поддержкой блогов - jekyllrb.com или Metalsmith Metalsmith: чрезвычайно простой подключаемый генератор статических сайтов. - www.metalsmith.io не требует от вас определения такой структуры. Jekyll, например, обрабатывает каждый файл с помощью «предварительной записи YAML» [3] [3]

Текстовые файлы, которые начинаются с блока кода YAML (между двумя строками по 3 дефиса ---), имеют данные, определенные в YAML, связанные с ними. Взгляните на документацию Jekyll.
как нечто оно должно так или иначе трансформироваться.

Файл с титульным листом может выглядеть так: 

```
---
title: Lorem ipsum
author: Pascal
---
Here is the actual content of this article.
```

Этот фасадный материал может содержать произвольные структуры. В то время как ваши «сообщения» всегда могут иметь поле заголовка (содержимое после вступительного сообщения представлено как поле содержимого), некоторые из них также могут включать поля banner_image, author или даже layout. К этим полям можно получить доступ в шаблонах, так же как заголовок и контент, и их можно использовать для индивидуальной настройки внешнего вида страницы.

Однако бессхемный характер SSG может привести к некоторым проблемам. Я всегда ошибаюсь в названиях полей. Поскольку формы для ввода новых данных нет, я редактирую текстовые файлы и думаю, что знаю, как называются поля. Но определение bannerImage (вместо banner_image) не приведет к тому, что я задумал. Он также не покажет никаких ошибок.

## Использование схем для статического анализа

Записывая поля, используемые в шаблонах, вы получаете основную структуру ваших данных. Просто добавьте несколько аннотаций, и у вас будет схема. (Просто задайте несколько вопросов: «Это текст или число? Это поле обязательно для заполнения? Как долго это должно быть максимум?»)

Я был бы скептически настроен, если бы SSG использовал схемы только для проверки правописания имен полей! Поскольку определение и написание схем требует времени, оно того стоит. И есть много других полезных функций, которые можно получить из схем.

Одна из возможностей - использовать схемы для подтверждения того, что структура входных данных соответствует ожиданиям шаблона. Допустим, у меня есть схема статьи, которая требует, чтобы я установил заголовок полей, сводку, содержание и Published_at. Учитывая некоторые входные данные, для которых эта схема действительна, я могу без проблем визуализировать эти данные, используя шаблон, который также соответствует этой схеме [4] [4]

Другой возможный шаг статического анализа: шаблон, который требует данных в схеме статьи, описанной выше, не может безусловно отображать поле автора, поскольку это поле не является обязательным и может быть пустым.
.

Между прочим: когда я говорю «схема» в этом контексте, я не обязательно имею в виду «схему базы данных», а скорее такую конструкцию, как JSON Schema JSON Schema и Hyper-Schema - json-schema.org, которая также может описывать вложенный массив. типы и использовать ссылки на другие схемы. В идеале это может быть способ повторно использовать (части) схемы во многих шаблонах и различных типах данных.

## Использование схем для создания данных

До сих пор мы только видели, как можно использовать схемы для проверки входных данных. Но что, если бы мы могли также использовать те же схемы для простого создания новых данных?

### Получите руководство по стилю бесплатно

С помощью такого инструмента, как Faker marak / faker.js: генерируйте огромное количество поддельных данных в Node.js и браузере - github.com вы можете легко генерировать поддельные значения различных типов, от классического Lorem ispum (случайной длины) до полных адресов. во Франции. Следующий шаг - создать не просто одно значение, а по одному для каждого поля в схеме. К счастью, это именно то, что делает JSON Schema Faker json-schema-faker: JSON-Schema + Faker - github.com.

Помните: входные данные шаблона также определяются как схемы - и теперь у нас есть простой способ сгенерировать для них данные. Итак, давайте возьмем наши шаблоны, партиалы, компоненты и отрендерим их со случайными данными!

Для меня это звучит как идеальное (и интерактивное) руководство по стилю. 

### Редактировать реальные данные

Тем не менее, становится лучше. Какая польза от ваших шаблонов и руководств по стилю, если у вас нет реальных данных, для которых их можно использовать?

Те же схемы, которые определяют требования для входных данных, могут (до определенной степени) также использоваться для создания форм для ввода именно таких данных. Итак, хотя частью прелести использования SSG является возможность записывать все в виде простых файлов, вам пока не нужно отказываться от некоторых тонкостей подхода на основе форм.

Небольшого сервера, который отображает страницу с помощью JSON Editor jdorn / json-editor: JSON Schema Based Editor - github.com или Alpaca.js Alpaca Forms - Easy Forms for jQuery - alpacajs.org и который позволяет отправлять изменения, может быть достаточно, чтобы начать. (Будет сложнее добавить загрузку изображений, переадресацию и перекрестные ссылки между страницами.)

## Силиконовые кабачки

Все, что я описал до сих пор, я пытался реализовать в проекте под кодовым названием [5] [5]

Создано с помощью моего надежного генератора кодовых имен.
Кремниевые кабачки. Он написан на JavaScript и использует потоковый подход для компиляции файлов [6] [6]

Я мог бы поговорить о технических деталях в более позднем посте. Я также, вероятно, перепишу все, прежде чем оно станет готовым к производству.

На данный момент это только прототип. Вы можете найти код в репозитории Github Silicon Zucchini на Github - github.com.

Работа по проверке данных и схемы шаблонов. Также доступны некоторые приятные функции, такие как сопоставление файлов данных с URI и поддержка различных типов входных данных (Markdown, JSON, CSON, YAML).

## Дополнительные функции

Помимо функций, основанных на схемах, описанных выше, Silicon Zucchini должен предлагать следующее.

### Простой контроль версий

Поместите все свои файлы (входные данные Silicon Zucchini) в репозиторий Git, и вы сможете воспроизвести любую версию своей страницы, которая когда-либо существовала. Моя цель - создать набор шагов сборки по умолчанию, который может использовать каждый проект Silicon Zucchini.

Хотя в будущих версиях может быть добавлено больше оптимизаций, построение одних и тех же данных и шаблонов с фиксированной версией Silicon Zucchini всегда должно приводить к одному и тому же результату - сборка должна быть детерминированной.

### Беспроблемная организация данных, схем и шаблонов

Каждый сайт Silicon Zucchini состоит из трех ингредиентов: вашего контента, схем, описывающих структуру этого контента, и шаблонов для создания из него веб-сайта. Silicon Zucchini порекомендует, куда поместить эти файлы.

«Файлы шаблонов» по своей сути представляют собой простые HTML-файлы, содержащие несколько заполнителей, которые при визуализации возвращают полные HTML-страницы. Они могут использовать настраиваемые компоненты («частичные» шаблоны со своими собственными схемами ввода) и требовать внешних файлов. Silicon Zucchini будет отслеживать, какие файлы требуются для используемых вами шаблонов, и автоматически включать их в процесс сборки.

Компоненты должны быть повторно использованы между сайтами, поскольку они очень точно определяют, что им нужно. Однако будет сложно объединить эту цель с чем-то вроде глобальных настроек для значимых цветов и стилей шрифтов. Я ожидаю, что со временем это будет развиваться.

В будущей версии вы также можете определять (или автоматически определять) несколько точек входа, чтобы сборка могла привести к созданию нескольких пакетов JavaScript / CSS, которые включаются только там, где это необходимо. (Нет необходимости загружать код для рендеринга 3D-карты WebGL на стартовой странице!) 

### Полностраничная оптимизация

Например, не стесняйтесь включать полный CSS Twitter Bootstrap, а затем запускать uncss giakki / uncss: Удалите неиспользуемые стили из CSS - github.com как часть процесса сборки, чтобы собрать только те части, которые вы действительно используете. Сделав еще один шаг, вы можете использовать penthouse pocketjoso / penthouse: Critical Path CSS Generator - github.com для автоматического извлечения CSS-кода «критический путь» addyosmani: Critical-path (Abo-the-fold) Инструменты CSS - github.com для каждого страница у вас есть, а не только стартовая страница.

Говоря о статических файлах, в процессе сборки вы можете определить каждый файл, который вам нужен, от CSS, JavaScript и файлов шрифтов, используемых в ваших шаблонах, до изображений, на которые есть ссылки в вашем контенте. Затем вы можете легко оптимизировать их, добавить хеши к именам файлов и позволить браузерам кэшировать их на неопределенный срок.

Кроме того, вы также можете встроить некоторые файлы в другие ресурсы. Например, поместите небольшие изображения в качестве URI данных в таблицы стилей (используя base64) или включите изображения SVG непосредственно в файлы HTML.

### Вы можете легко вносить изменения в пакет

Хотя это возможно с CMS, с SSG это тривиально. Вы можете просто отредактировать свой контент, запустить процесс сборки и предварительно просмотреть весь сайт локально. Когда все изменения внесены и вам понравится результат, вы можете обновить свой действующий сайт.

Тот же процесс сборки также может выполняться на сервере. Помимо действующего сайта («производственная среда»), вы можете добавить экземпляры «предварительного просмотра» (виртуальные хосты). Можно даже иметь общедоступный сервер, на котором запущен «бэкэнд», описанный выше (формы, созданные на основе схем, и возможность сохранять файлы), и повторно создавать сайт предварительного просмотра при изменении файла.

### Большое сообщение об ошибках

Что ж, я думаю, это идет рука об руку с использованием схем данных. Есть много вещей, которые могут пойти не так при компиляции ваших входных данных в полноценный веб-сайт, и будет важно сообщить пользователям, откуда именно возникла ошибка и как ее исправить.

Вот некоторые общие случаи:

- Файл данных недействителен для данной схемы. Сообщите пользователю, какое поле отсутствует / неверно, в каком входном файле и какой файл схемы использовался. (Проблема: схемы могут содержать ссылки на другие схемы.)
- Невозможно отобразить шаблон, потому что для него были указаны недопустимые данные. Эти ошибки должны быть такими же четкими, как и ошибки для недопустимых файлов данных.
- Невозможно отобразить шаблон, потому что его код недействителен (ошибка синтаксического анализа или ошибка JavaScript при выполнении шаблона). Это должно вывести что-то вроде трассировки стека с именами файлов и номерами строк.
- Была сделана ссылка на статический файл, но он не существует. Поскольку мы знаем каждый выходной файл, мы можем проверить все относительные ссылки в наших файлах (как один из последних шагов в процессе сборки). Это должно показать, где был запрошен файл и по какому абсолютному пути мы пытались его найти. 

## Приложение A: Существующие схемы

Основная задача, которую Silicon Zucchini добавляет к существующему рабочему процессу разработки, - это создание схем. Я мог бы утверждать, что каждый веб-сайт уже имеет неявную схему данных и что в идеале возможные значения входных данных уже были проблемой при создании дизайна и информационной структуры сайта. Еще предстоит проделать большую работу, чтобы явно записать эту схему (в формате, который вам может потребоваться сначала изучить [7] [7]

Silicon Zucchini также может проверить, что схемы, которые вы написали, на самом деле являются действительными схемами: спецификация схемы JSON содержит схему JSON для проверки схем JSON.
).

Простой способ сократить это время - иметь набор уже четко определенных схем для нескольких типов данных, например простые статьи в блогах, изображения (с типом файла и размерами) и т.д. - аналогично тому, что schema.org Schema.org представляет собой совместную деятельность сообщества с миссией по созданию, поддержке и продвижению схем для структурированных данных в Интернете, на веб-страницы, сообщения электронной почты и т.д. - schema.org поддерживает схемы RDF [8] [8]

Кстати о schema.org: может быть полезно добавить некоторые аннотации, используемые JSON-LD, в схему JSON для генерации альтернативных представлений данных. Это также может помочь в разработке шаблонов, чтобы они включали правильную разметку микроданных.
. Единственная коллекция схем JSON, которую я смог найти до сих пор, - это «ans-schema» Washington Post / ans-schema: определение схемы JSON и вспомогательный пример / код проверки для спецификации ANS The Washington Post - github.com от The Washington Post. (Существуют также схемы JSON schemastore.org для распространенных форматов файлов JSON - schemastore.org, но, похоже, они ориентированы на файлы конфигурации в JSON.)

Я позабочусь о том, чтобы любой «стартовый набор» Silicon Zucchini, который я публикую, включал несколько полезных схем по умолчанию, которые вы можете расширить и использовать в качестве основы для своих собственных схем. Например, схема для простых страниц с заголовком, контентом и слагом (URL) и другая схема для записей новостей / блогов, которая также включает публикацию_дате и is_draft.

## Приложение B: Уровень техники

То и дело искал похожие проекты. (В основном с надеждой, что кто-то, у кого больше времени, чем у меня, уже реализовал что-то, что я мог бы использовать!) Вот что я нашел на данный момент:

- «Идеальная CMS» Предложение для идеальной CMS »Hay Kranen - www.haykranen.nl, который описывает Hay Kranen, также использует схему JSON для ввода и проверки данных. (Не совсем «предшествующий» уровень техники - я думал об этом как минимум год и реализовал первый прототип Silicon Zucchini в March killercup / Silicone-zucchini @ ecd99b9 - github.com.)
- prismic.io prismic.io | Оживите свой веб-сайт или контент приложения - prismic.io («CMS как услуга») позволяет создавать маски документов (см. Их документацию prismic.io: руководство администратора репозитория - developers.prismic.io) для определения пользовательских типов данных. Формат похож на схему JSON и используется для создания форм, которые видит редактор контента. 
