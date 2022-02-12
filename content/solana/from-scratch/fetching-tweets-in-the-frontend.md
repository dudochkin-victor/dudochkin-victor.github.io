+++
title = "9. Получение твитов в интерфейсе"
description = "9. Получение твитов в интерфейсе"
weight = 1
hash = "5407a787"
+++

[Перевод](https://lorisleiva.com/create-a-solana-dapp-from-scratch/fetching-tweets-in-the-frontend) | Автор оригинала: Loris

ЭПИЗОД 9

2 МЕСЯЦА НАЗАД

ЧТЕНИЕ 17 МИН.

В предыдущем эпизоде мы усердно работали над тем, чтобы пользователи могли подключать свои кошельки, и в итоге получили объект «программа» от Anchor, позволяющий нам взаимодействовать с нашей программой Solana. Теперь пришло время использовать эту «программу», чтобы удалить все фиктивные данные и получить настоящие твиты из блокчейна.

Часть работы, которую мы проделаем в этой статье, будет вам знакома, потому что мы уже видели, как получать твиты из блокчейна при тестировании нашей программы Solana.

Ладно, приступим!

## Получение всех твитов

Мы начнем с простого, собрав все существующие твиты и отобразив их на главной странице.

Откройте файл `api/fetch-tweets.js` и вставьте следующий код.

```js
export const fetchTweets = async ({ program }) => {
    const tweets = await program.value.account.tweet.all();
    return tweets
}
```

Несколько вещей, на которые следует обратить внимание:

- Во-первых, мы ожидаем, что вся «рабочая область» (workspace), определенная в предыдущем эпизоде, будет указана в качестве первого параметра. Таким образом, у нас есть все необходимое для взаимодействия с нашей программой. Обратите внимание, что мы можем получить доступ к рабочей области непосредственно в файле API через `useWorkspace`, но это может вызвать проблемы в зависимости от того, где мы вызываем этот метод API, потому что inject/provide в VueJS работает только при вызове внутри метода `setup` компонента.
- Поскольку нам нужен доступ только к объекту `program` из рабочей области, мы деструктурируем его из первого параметра.
- Мы обращаемся к программе, используя `program.value`, потому что `program` является реактивной переменной и обернута в объект `Ref`.
- Наконец, мы получаем доступ ко всем учетным записям `Tweet`, используя `account.tweet.all()`, точно так же, как мы делали это при тестировании нашей программы.

Хорошо, давайте попробуем это в нашем компоненте `PageHome.vue`. Внутри скриптовой части компонента мы импортируем компонуемый `useWorkspace` и передаем его в наш метод fetchTweets в качестве параметра.

```js
import { ref } from 'vue'
import { fetchTweets } from '@/api'
import TweetForm from '@/components/TweetForm'
import TweetList from '@/components/TweetList'
import { useWorkspace } from '@/composables'

const tweets = ref([])
const loading = ref(true)
fetchTweets(useWorkspace())
    .then(fetchedTweets => tweets.value = fetchedTweets)
    .finally(() => loading.value = false)

// ...
```

На этом этапе все должно быть правильно подключено к нашей домашней странице, поэтому давайте посмотрим, все ли работает.

Во-первых, вам нужно запустить новый локальный валидатор. Вы можете сделать это, запустив solana-test-validator в своем терминале или, альтернативно, запустив `anchor localnet`, который также пересоберет и повторно развернет вашу программу.

Чтобы мы могли видеть некоторые твиты в нашем приложении, нам нужно иметь несколько учетных записей `Tweet` в нашем локальном реестре. К счастью для нас, мы знаем, что при выполнении тестов будет создано 3 из них, поэтому давайте запустим `anchor run test`, чтобы добавить их в нашу локальную книгу.

Хорошо, теперь у нас есть работающая локальная книга, содержащая всего 3 твита. Поэтому мы должны увидеть эти твиты на главной странице.

Однако, если вы перейдете на домашнюю страницу и откроете инструменты разработчика `Network` в своем браузере, вы должны увидеть следующее.

![Screenshot of the app with the dev tools open showing that we are getting 3 tweets but they are not displayed properly.](https://lorisleiva.com/assets/articles/2021/1203-solana-9-frontend-fetch-tweets/home-page-1.png)

Как мы видим на вкладке сети, мы действительно получаем 3 твит-аккаунта, но они не отображаются должным образом на главной странице.

Это потому, что наш интерфейс ожидает получить объект с определенной структурой, которая не соответствует тому, что мы получаем от вызова API.

Поэтому вместо того, чтобы менять весь наш интерфейс, чтобы приспособиться к этой структуре, давайте создадим новую модель `Tweet`, которая работает для нашего интерфейса и абстрагирует данные, полученные от API.

## Модель твита

Внутри папки `src` нашего внешнего приложения создадим новую папку с именем `models`. В эту новую папку мы добавим два файла:

- `Tweet.js`. Это позволит структурировать наши учетные записи твитов с помощью класса «Tweet».
- `index.js`. Это зарегистрирует модель «Tweet», чтобы мы могли импортировать ее так же, как мы импортируем составные объекты и конечные точки API.

Как только эта папка и эти два файла будут созданы, вставьте следующий код в файл index.js.

```js
export * from './Tweet'
```

И вставьте следующее в файл `Tweet.js`.

```js
export class Tweet
{
    constructor (publicKey, accountData) {
        this.publicKey = publicKey
        this.author = accountData.author
        this.timestamp = accountData.timestamp.toString()
        this.topic = accountData.topic
        this.content = accountData.content
    }
}
```

Как видите, для создания нового объекта `Tweet` нам нужно предоставить:

- `publicKey`, который будет экземпляром класса `PublicKey` Соланы.
- И объект `accountData`, предоставленный конечной точкой API.

При создании нового объекта `Tweet` мы сохраняем его открытый ключ и все свойства внутри объекта `accountData` по отдельности. Таким образом, мы можем получить доступ, скажем, к теме через `tweet.topic`. Мы также анализируем отметку времени в строку, потому что конечная точка API предоставляет нам отметку времени в виде массива байтов.

Вдобавок к этим свойствам, наш интерфейс полагается на объекты `Tweet`, которые имеют следующие дополнительные свойства: `key`, `author_display`, `created_at` и `created_ago`.

### Ключ

Свойство `key` должно быть уникальным идентификатором, представляющим наш твит. Он используется в некоторых шаблонах VueJS при переборе массивов твитов. Поскольку открытый ключ уникален для каждого твита, мы будем использовать его формат base-58, чтобы предоставить уникальную строку.

Мы будем использовать функцию-получатель, чтобы предоставить это свойство `key`. Вы можете добиться этого, добавив следующий геттер в конце класса `Tweet`.

```js
export class Tweet
{
    // ...

    get key () {
        return this.publicKey.toBase58()
    }
}
```

### Отображение автора

Хотя у нас уже есть доступ к открытому ключу автора через свойство author, интерфейс использует сжатую версию этого адреса в компоненте `TweetCard.vue`, чтобы визуально не перегружать пользователя.

Эта сокращенная версия представляет собой просто первые 4 символа и последние 4 символа открытого ключа с парой точек посередине.

Таким образом, давайте добавим еще одну функцию-получатель с именем `author_display` и используем метод `slice` для сжатия открытого ключа автора.

```js
export class Tweet
{
    // ...

    get author_display () {
        const author = this.author.toBase58()
        return author.slice(0,4) + '..' + author.slice(-4)
    }
}
```

### Свойства `created_at` и `created_ago`

Последние два свойства, которые нам нужны, являются удобочитаемыми версиями временной метки, предоставляемой нашей программой. `created_at` должна быть локализованной удобочитаемой датой, включая время, тогда как `created_ago` должна кратко описывать, как давно был опубликован твит.

К счастью, существует множество библиотек JavaScript для управления датами. [Moment.js](https://momentjs.com/), вероятно, является самым популярным, но я бы сказал, что это слишком много для наших целей. Вместо этого я часто предпочитаю использовать [Day.js](https://day.js.org/), который по умолчанию очень легкий и расширяемый в соответствии с нашими потребностями.

Итак, давайте начнем с установки Day.js с помощью npm.

```bash
npm install dayjs
```

Затем нам нужно импортировать его и немного расширить, чтобы он поддерживал локализованные форматы и относительное время — используемые для `created_at` и `created_ago` соответственно.

В файле main.js добавьте следующий код после раздела «CSS».

```js
// CSS.
import '@solana/wallet-adapter-vue-ui/styles.css'
import './main.css'

// Day.js
import dayjs from 'dayjs'
import localizedFormat from 'dayjs/plugin/localizedFormat'
import relativeTime from 'dayjs/plugin/relativeTime'
dayjs.extend(localizedFormat)
dayjs.extend(relativeTime)

// ...
```

Теперь вернемся к нашей модели `Tweet.js`, мы можем импортировать Day.js и предоставить две функции получения для `created_at` и `created_ago`. Оба они могут использовать `dayjs.unix(this.timestamp)`, чтобы преобразовать наше свойство `timestamp` в объект Day.js. Затем мы можем использовать методы `format('lll')` и `fromNow()`, чтобы получить локализованную дату и относительное время соответственно.

В итоге мы получаем следующую модель Tweet.js!

```js
import dayjs from "dayjs"

export class Tweet
{
    constructor (publicKey, accountData) {
        this.publicKey = publicKey
        this.author = accountData.author
        this.timestamp = accountData.timestamp.toString()
        this.topic = accountData.topic
        this.content = accountData.content
    }

    get key () {
        return this.publicKey.toBase58()
    }

    get author_display () {
        const author = this.author.toBase58()
        return author.slice(0,4) + '..' + author.slice(-4)
    }

    get created_at () {
        return dayjs.unix(this.timestamp).format('lll')
    }

    get created_ago () {
        return dayjs.unix(this.timestamp).fromNow()
    }
}
```

### Возврат моделей `Tweet`

Теперь, когда наша модель «Tweet» готова, давайте используем ее в нашей конечной точке API `fetch-tweets.js`, чтобы она возвращала объекты `Tweet` вместо того, что возвращает API.

Для этого мы можем использовать `map` в массиве `tweets` для преобразования каждого элемента внутри него. Как мы видели в предыдущем эпизоде, API возвращает объект, содержащий `publicKey` и объект `account`, а это именно то, что нам нужно для создания нового объекта `Tweet`.

```js
import { Tweet } from '@/models'

export const fetchTweets = async ({ program }) => {
    const tweets = await program.value.account.tweet.all();
    return tweets.map(tweet => new Tweet(tweet.publicKey, tweet.account))
}
```

Хорошо, на данный момент мы должны увидеть, что все твиты правильно отображаются на главной странице. На изображении ниже я также записал возвращаемое значение метода `fetchTweets`, чтобы мы могли убедиться, что все наши пользовательские геттеры работают правильно.

![Screenshot of the home page with our 3 test tweets properly displayed and the console showing an array of Tweet objects with all the relevant data.](https://lorisleiva.com/assets/articles/2021/1203-solana-9-frontend-fetch-tweets/home-page-2.png)

Все хорошо! Переходим к следующему заданию.

## Добавлени ссылки в карточку твита

Просматривая твиты на главной странице, вы могли заметить, что каждый твит содержит 3 ссылки:

- Один на адрес автора, который должен привести вас на страницу этого автора.
- Один во время твита, который должен привести вас на страницу, которая показывает только этот твит, чтобы пользователи могли поделиться им.
- Один на тему твита, который должен привести вас на страницу этой темы.

Однако, если вы попытаетесь нажать на них, они всегда отправят вас на домашнюю страницу. Это просто потому, что эти ссылки еще не реализованы, и это то, что мы собираемся сделать сейчас.

Если вы заглянете внутрь компонента `TweetCard.vue`, то увидите несколько комментариев к шаблону, которые выглядят примерно так: `<!-- TODO: Link to... -->`. Итак, давайте рассмотрим каждый из этих комментариев один за другим, начиная со ссылки автора.

### Ссылка автора

Эта ссылка немного сложнее, чем другие, потому что маршрут, на который она ведет, зависит от того, является ли это одним из наших твитов или нет. Под этим я подразумеваю, что если мы нажмем на свой собственный адрес, он должен направить нас на страницу профиля, тогда как, если мы нажмем на адрес кого-то другого, он должен привести нас на страницу пользователя с уже предварительно заполненным адресом.

Поэтому мы собираемся создать вычисляемое свойство с именем authorRoute, которое будет использовать подключенный кошелек, чтобы определить, на какой маршрут нас следует направить.

Обновите часть скрипта компонента `TweetCard.vue` следующими строками.

```js
import { toRefs, computed } from 'vue'
import { useWorkspace } from '@/composables'

const props = defineProps({
    tweet: Object,
})

const { tweet } = toRefs(props)
const { wallet } = useWorkspace()
const authorRoute = computed(() => {
    if (wallet.value && wallet.value.publicKey.toBase58() === tweet.value.author.toBase58()) {
        return { name: 'Profile' }
    } else {
        return { name: 'Users', params: { author: tweet.value.author.toBase58() } }
    }
})
```

Давайте пройдемся по этому фрагменту кода:

- Мы начинаем с импорта метода `computed` из VueJS, который мы будем использовать для создания нашего вычисляемого свойства `authorRoute`.
- Мы также импортируем наше рабочее пространство через компонуемый useWorkspace и получаем доступ к подключенному кошельку из него.
- Внутри вычисляемого метода мы сначала проверяем, есть ли у автора твита тот же открытый ключ, что и у подключенного кошелька.
- Если это так, мы просто перенаправляем на страницу профиля, возвращая `{ name: 'Profile' }`. В Vue Router именно так вы можете определить маршрут с именем «Профиль».
- В противном случае нам нужно перенаправить пользователя на «страницу пользователей», предварительно заполнив ее адрес автором твита. В Vue Router мы можем предоставить параметры маршрута через объект params. Таким образом, мы можем получить доступ к странице «Пользователи» автора твита, вернув: `{ name: 'Users', params: { author: tweet.value.author.toBase58() } }`

Теперь, когда наше вычисляемое свойство authorRoute доступно, мы можем передать его соответствующему компоненту `<router-link>` и удалить комментарий выше.

```diff
- <!-- TODO: Link to author page or the profile page if it's our own tweet. -->
- <router-link :to="{ name: 'Home' }" class="hover:underline">
+ <router-link :to="authorRoute" class="hover:underline">
      {{ tweet.author_display }}
  </router-link>
```

### Ссылка на твит

Далее давайте реализуем ссылку на страницу твита. Для этого мы можем использовать формат base-58 открытого ключа твита в качестве параметра маршрута `Tweet`. В итоге мы получаем следующий объект маршрута.

```js
{ name: 'Tweet', params: { tweet: tweet.publicKey.toBase58() } }
```

На этот раз мы можем использовать этот объект непосредственно внутри соответствующей `<router-link>` без необходимости в новой переменной.

```diff
- <!-- TODO: Link to the tweet page. -->
- <router-link :to="{ name: 'Home' }" class="hover:underline">
+ <router-link :to="{ name: 'Tweet', params: { tweet: tweet.publicKey.toBase58() } }" class="hover:underline">
      {{ tweet.created_ago }}
  </router-link>
```

### Сылка на тему

Наконец, нам нужно реализовать ссылку на страницу темы. Как и в предыдущих ссылках, мы можем передать тему как параметр маршрута «Темы» и получить следующий объект маршрута…

```js
{ name: 'Topics', params: { topic: tweet.topic } }
```

… который мы можем использовать непосредственно в финальном `<router-link>`, который нуждается в обновлении.

```diff
- <!-- TODO: Link to the topic page. -->
- <router-link v-if="tweet.topic" :to="{ name: 'Home' }" class="inline-block mt-2 text-pink-500 hover:underline">
+ <router-link v-if="tweet.topic" :to="{ name: 'Topics', params: { topic: tweet.topic } }" class="inline-block mt-2 text-pink-500 hover:underline">
      {{ tweet.created_ago }}
  </router-link>
```

И просто свяжите это, наш компонент `TweetCard.vue` завершен, и все его ссылки указывают на нужные места.

К сожалению, если вы сейчас попытаетесь перейти по этим ссылкам, они не сработают. Это просто потому, что мы изменили конечную точку API  `fetchTweets` для нашего компонента `PageHome.vue`, но еще не обновили другие страницы.

Итак, давайте исправим это. Мы начнем со страниц `Topics` и `Users`. Обеим этим страницам потребуется доступ ко всем твитам из нашей программы, которые соответствуют определенному критерию. Однако наша конечная точка API fetchTweets еще не поддерживает фильтры. Поэтому мы должны сначала разобраться с этим.

## Вспомогательные фильтры

Поскольку [мы уже видели, как фильтровать аккаунты в Солане](https://lorisleiva.com/create-a-solana-dapp-from-scratch/fetching-tweets-from-the-program#filtering-tweets-by -author), поддержка фильтров в нашей конечной точке API должна быть приятной и простой.

Первое, что нам нужно сделать, это добавить новый параметр `filters` в метод `fetchTweets` нашего файла `fetch-tweets.js`, что позволит нам дополнительно предоставлять фильтры при получении твитов.

```js
import { Tweet } from '@/models'

export const fetchTweets = async ({ program }, filters = []) => {
    const tweets = await program.value.account.tweet.all(filters);
    return tweets.map(tweet => new Tweet(tweet.publicKey, tweet.account))
}
```

Теперь написание фильтров Соланы может быть немного утомительным, и такая логика, разбросанная повсюду в наших компонентах, может быть не идеальной для поддержки. Было бы неплохо, если бы мы могли предложить несколько вспомогательных методов, которые будут генерировать фильтры, чтобы наши компоненты могли использовать эти методы вместо того, чтобы сами генерировать фильтры. Итак, давайте сделаем это!

Мы начнем с экспорта функции authorFilter, которая принимает открытый ключ в формате base 58 и возвращает соответствующий фильтр memcmp, как мы видели [в эпизоде 5 этой серии]([Fetching tweets from the program | Create a Solana dApp from scratch | Loris](https://lorisleiva.com/create-a-solana-dapp-from-scratch/fetching-tweets-from-the-program#use-the-memcmp-filter-on-the-authors-public-key).

Вот упомянутая функция, которую теперь вы можете добавить в конец вашего файла fetch-tweets.js.

```js
export const authorFilter = authorBase58PublicKey => ({
    memcmp: {
        offset: 8, // Discriminator.
        bytes: authorBase58PublicKey,
    }
})
```

Затем мы сделаем то же самое для тем, экспортировав функцию `topicFilter`, которая принимает тему в виде строки и возвращает фильтр `memcmp`, который правильно кодирует тему и предоставляет для нее правильное смещение.

Добавьте следующую функцию `topicFilter` в конец вашего файла `fetch-tweets.js` и не забудьте импортировать библиотеку `bs58`, чтобы она могла кодировать заданную строку темы в массив байтов, отформатированный по основанию 58.

```js
import { Tweet } from '@/models'
import bs58 from 'bs58'

// ...

export const topicFilter = topic => ({
    memcmp: {
        offset: 8 + // Discriminator.
            32 + // Author public key.
            8 + // Timestamp.
            4, // Topic string prefix.
        bytes: bs58.encode(Buffer.from(topic)),
    }
})
```

Если вам интересно, почему мы используем именно это смещение, это по тем же причинам, которые мы описали [в эпизоде 5 при фильтрации твитов по темам в наших тестах]([Fetching tweets from the program | Create a Solana dApp from scratch | Loris](https://lorisleiva.com/create-a-solana-dapp-from-scratch/fetching-tweets-from-the-program#filtering-tweets-by-topic).

И это все! Теперь у нас есть конечная точка fetchTweets, которая не только поддерживает фильтры, но и упрощает их использование нашими компонентами. Ваш окончательный файл fetch-tweets.js должен выглядеть так.

```js
import { Tweet } from '@/models'
import bs58 from 'bs58'

export const fetchTweets = async ({ program }, filters = []) => {
    const tweets = await program.value.account.tweet.all(filters);
    return tweets.map(tweet => new Tweet(tweet.publicKey, tweet.account))
}

export const authorFilter = authorBase58PublicKey => ({
    memcmp: {
        offset: 8, // Discriminator.
        bytes: authorBase58PublicKey,
    }
})

export const topicFilter = topic => ({
    memcmp: {
        offset: 8 + // Discriminator.
            32 + // Author public key.
            8 + // Timestamp.
            4, // Topic string prefix.
        bytes: bs58.encode(Buffer.from(topic)),
    }
})
```

Теперь наши компоненты могут использовать эту конечную точку API для получения и фильтрации подобных твитов.

```js
import { fetchTweets, authorFilter, topicFilter } from '@/api'
import { useWorkspace } from '@/composables'
const workspace = useWorkspace()

// Fetch all tweets.
const allTweets = await fetchTweets()

// Filter tweets by author.
const myTweets = await fetchTweets([
    authorFilter('B1AfN7AgpMyctfFbjmvRAvE1yziZFDb9XCwydBjJwtRN'),
])

// Filter tweets by topic.
const solanaTweets = await fetchTweets([
    topicFilter('solana'),
])

// Filter tweets by author and topic.
const mySolanaTweets = await fetchTweets([
    authorFilter('B1AfN7AgpMyctfFbjmvRAvE1yziZFDb9XCwydBjJwtRN'),
    topicFilter('solana'),
])
```

Нойс! Давайте используем эту новую блестящую конечную точку API на наших страницах `Topics` и `Users`.

## Получение твитов по теме

Внутри нашего компонента PageTopics.vue давайте импортируем функцию `topicFilter` и компонуемый `useWorkspace`, которые мы можем немедленно использовать для доступа к нашему рабочему пространству.

```js
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import { fetchTweets, topicFilter } from '@/api'
import { useSlug, useFromRoute, useWorkspace } from '@/composables'
import TweetForm from '@/components/TweetForm'
import TweetList from '@/components/TweetList'
import TweetSearch from '@/components/TweetSearch'

// Data.
const router = useRouter()
const tweets = ref([])
const loading = ref(true)
const topic = ref('')
const slugTopic = useSlug(topic)
const viewedTopic = ref('')
const workspace = useWorkspace()
```

Затем давайте прокрутим немного вниз и предоставим соответствующие параметры методу `fetchTweet`. Здесь мы будем использовать значение вычисляемого свойства `slugTopic` в качестве темы для фильтрации твитов.

```js
const fetchTopicTweets = async () => {
    if (slugTopic.value === viewedTopic.value) return
    try {
        loading.value = true
        const fetchedTweets = await fetchTweets(workspace, [topicFilter(slugTopic.value)])
        tweets.value = fetchedTweets
        viewedTopic.value = slugTopic.value
    } finally {
        loading.value = false
    }
}
```

Страница темы… Готово! 

Теперь вы сможете щелкнуть ссылку на тему и просмотреть все твиты из этой темы.

![Screenshot of the topics page showing two of our tweets from the “veganism” topic.](https://lorisleiva.com/assets/articles/2021/1203-solana-9-frontend-fetch-tweets/topics-page.png)

## Получение твитов по автору

Давайте сделаем то же самое для нашего компонента `PageUsers.vue`.

Точно так же мы импортируем функцию `authorFilter` и компонуемый `useWorkspace`, прежде чем использовать последний для доступа к нашему рабочему пространству.

```js
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import { fetchTweets, authorFilter } from '@/api'
import { useFromRoute, useWorkspace } from '@/composables'
import TweetList from '@/components/TweetList'
import TweetSearch from '@/components/TweetSearch'

// Data.
const router = useRouter()
const tweets = ref([])
const loading = ref(true)
const author = ref('')
const viewedAuthor = ref('')
const workspace = useWorkspace()
```

Затем мы передаем `workspace` нашей функции `fetchTweets` в качестве параметра и предоставляем `authorFilter`, используя свойство `author`.

```js
const fetchAuthorTweets = async () => {
    if (author.value === viewedAuthor.value) return
    try {
        loading.value = true
        const fetchedTweets = await fetchTweets(workspace, [authorFilter(author.value)])
        tweets.value = fetchedTweets
        viewedAuthor.value = author.value
    } finally {
        loading.value = false
    }
}
```

Бум, страница пользователя… Готово!

![Screenshot of the users page showing two tweets from a given author.](https://lorisleiva.com/assets/articles/2021/1203-solana-9-frontend-fetch-tweets/users-page.png)

Прежде чем мы двинемся дальше, есть еще одна страница, на которой необходимо использовать функцию `authorFilter`, и это страница профиля.

Давайте сделаем то же самое с нашим компонентом `PageProfile.vue`. На этот раз компонуемый `useWorkspace` уже используется для доступа к свойству `wallet`. Таким образом, мы обязательно получим доступ ко всему рабочему пространству, прежде чем деструктурировать его для доступа к подключенному кошельку.

```js
import { ref, watchEffect } from 'vue'
import { fetchTweets, authorFilter } from '@/api'
import TweetForm from '@/components/TweetForm'
import TweetList from '@/components/TweetList'
import { useWorkspace } from '@/composables'

const tweets = ref([])
const loading = ref(true)
const workspace = useWorkspace()
const { wallet } = workspace
```

Затем мы можем предоставить `workspace` и `authorFilter` в качестве параметров функции `fetchTweets`». Поскольку это страница профиля, мы будем использовать открытый ключ подключенного кошелька в фильтре автора.

```js
watchEffect(() => {
    if (! wallet.value) return
    fetchTweets(workspace, [authorFilter(wallet.value.publicKey.toBase58())])
        .then(fetchedTweets => tweets.value = fetchedTweets)
        .finally(() => loading.value = false)
})
```

Обратите внимание, что в дополнение к изменениям, внесенным в вызов метода `fetchTweets`, мы добавили строку, которая гарантирует, что у нас есть подключенный кошелек, прежде чем продолжить, то есть `if (! wallet.value) return`. Несмотря на то, что страница профиля скрыта, когда кошелек не подключен, нам все равно нужно добавить эту дополнительную проверку, потому что после обновления будет небольшая задержка перед автоматическим повторным подключением кошелька.

## Получение только одного твита

Есть последняя страница, на которой пользователи могут получить доступ к твитам, и это страница `Tweet`. Эта страница немного особенная, потому что вместо отображения нескольких твитов она просто извлекает содержимое учетной записи `Tweet` по заданному адресу.

Поэтому мы не можем использовать здесь конечную точку API `fetchTweets`. Вместо этого есть конечная точка API getTweet, расположенная в файле `get-tweet.js`, которую нам нужно обновить.

Замените все внутри этого файла следующим кодом.

```js
import { Tweet } from '@/models'

export const getTweet = async ({ program }, publicKey) => {
    const account = await program.value.account.tweet.fetch(publicKey);
    return new Tweet(publicKey, account)
}
```

Как и наш метод fetchTweets, getTweet ожидает получить наш объект workspace в качестве первого параметра. Кроме того, он принимает параметр `publicKey`, который должен быть экземпляром класса `PublicKey` Соланы.

Затем он использует метод `fetch` из API `account.tweet`, предоставляемого программой Anchor, для получения содержимого учетной записи. Затем мы можем объединить эти данные учетной записи с предоставленным открытым ключом, чтобы вернуть новый объект `Tweet`.

Теперь, когда наша конечная точка API `getTweet` готова, давайте используем ее внутри нашего компонента `PageTweet.vue`.

Если вы посмотрите на его импорт, вы заметите, что он уже импортирован, потому что именно так мы отображали фиктивные данные раньше. Однако нам нужно будет передать объект рабочей области в качестве первого параметра, поэтому давайте импортируем составной объект `useWorkspace`.

```js
import { ref, watchEffect } from 'vue'
import { PublicKey } from '@solana/web3.js'
import { getTweet } from '@/api'
import { useFromRoute, useWorkspace } from '@/composables'
import TweetCard from '@/components/TweetCard'
```

Далее, давайте прокрутим немного вниз и получим доступ к свойству `workspace`, прежде чем передать его вызову метода `getTweet`. Что касается открытого ключа, мы можем использовать реактивное свойство tweetAddress, которое динамически извлекается из текущего URL. Однако нам нужно будет обернуть его `value` внутри объекта `PublicKey`, так как это то, что ожидает получить наша конечная точка API.

```js
const loading = ref(false)
const tweet = ref(null)
const workspace = useWorkspace()
watchEffect(async () => {
    try {
        loading.value = true
        tweet.value = await getTweet(workspace, new PublicKey(tweetAddress.value))
    } catch (e) {
        tweet.value = null
    } finally {
        loading.value = false
    }
})
```

Все готово! 

Если вы нажмете на отметку времени твита, у вас появится доступ к странице, которую можно использовать, чтобы поделиться им.

![Screenshot of the tweet page showing one of the tweets generated by our tests.](https://lorisleiva.com/assets/articles/2021/1203-solana-9-frontend-fetch-tweets/tweet-page.png)

## Вывод

Наше приложение действительно начинает обретать форму! На этом этапе вы должны быть в состоянии осмотреться и прочитать все твиты, присутствующие в блокчейне.

Единственное, чего не хватает, прежде чем мы сможем поделиться нашим приложением со всем миром, — это позволить пользователям отправлять твиты напрямую через наше внешнее приложение, что мы и сделаем в следующем эпизоде. 🙌

[Просмотреть эпизод 9 на GitHub](https://github.com/lorisleiva/solana-twitter/tree/episode-9)

[Сравните с Эпизодом 8](https://github.com/lorisleiva/solana-twitter/compare/episode-8...episode-9)
