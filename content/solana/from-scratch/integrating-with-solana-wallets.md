+++
title = "8. Интеграция с кошельками Solana"
description = "8. Интеграция с кошельками Solana"
weight = 1
hash = "5407a787"
+++

[Перевод](https://lorisleiva.com/create-a-solana-dapp-from-scratch/integrating-with-solana-wallets) | Автор оригинала: Loris

ЭПИЗОД 8

2 МЕСЯЦА НАЗАД

ЧТЕНИЕ 20 МИН.

На данный момент у нас есть приятный пользовательский интерфейс для отправки и чтения твитов, но ничего не подключено к нашей программе Solana. Кроме того, у нас нет возможности узнать, какой кошелек подключен в браузере пользователя. Итак, давайте это исправим.

В этом эпизоде мы сосредоточимся на интеграции нашего интерфейса с поставщиками кошельков Solana, такими как [Phantom](https://phantom.app/) или [Solfare](https://solflare.com/), чтобы мы могли отправлять транзакции. от имени пользователя. Как только у нас будет доступ к подключенному кошельку, мы сможем создать объект `Program`, как мы это делали в наших тестах.

Хорошо, давайте начнем!

## Установка библиотек кошелька Solana

К счастью для нас, у Solana есть несколько официальных библиотек JavaScript, которые могут помочь нам интегрироваться со многими поставщиками кошельков.

Эти библиотеки [доступны на GitHub](https://github.com/solana-labs/wallet-adapter), и даже есть пакеты для Vue 3! Итак, давайте установим эти библиотеки прямо сейчас. Нам понадобится один для основной логики «адаптера кошелька», один для логики Vue 3, один для компонентов Vue 3 и один для импорта [всех поддерживаемых кошельков](https://github.com/solana-labs/wallet -адаптер#кошельки).

Запустите следующее в вашем каталоге `app`, чтобы установить их.

```bash
npm install @solana/wallet-adapter-base \
    @solana/wallet-adapter-vue \
    @solana/wallet-adapter-vue-ui \
    @solana/wallet-adapter-wallets
```

## Использование провайдера кошелька

После установки этих библиотек первое, что нам нужно сделать, это использовать компонент WalletProvider. Этот компонент немного особенный и не отображает ничего, кроме своего содержимого. Однако он предоставляет данные своим потомкам с помощью [provide/inject](https://v3.vuejs.org/guide/composition-api-provide-inject.html#provide-inject) API VueJS. Если вы знакомы с React, это очень похоже на использование контекста React. Возьмем следующий пример.

```html
<template>
    <wallet-provider>
        <some-other-component></some-other-component>
    </wallet-provider>
</template>
```

Этот код визуально эквивалентен использованию `<some-other-component>` непосредственно в шаблоне. Однако этот компонент теперь будет иметь доступ ко всем данным, необходимым для получения подключенного кошелька, и даже к методам инициирования такого подключения. Позже в этом эпизоде мы объясним, что мы получаем и как это получить, а пока давайте посмотрим, как настроить этот компонент WalletProvider.

Внутри скриптовой части вашего компонента App.vue добавьте следующие строки.

```js
import { useRoute } from 'vue-router'
import TheSidebar from './components/TheSidebar'
import { getPhantomWallet, getSolflareWallet } from '@solana/wallet-adapter-wallets'
import { WalletProvider } from '@solana/wallet-adapter-vue'

const route = useRoute()

const wallets = [
    getPhantomWallet(),
    getSolflareWallet(),
]
```

Этот код делает две вещи.

1. Он импортирует двух поставщиков кошельков: Phantom и Solflare. Затем он регистрирует этих двух провайдеров кошельков в массиве кошельков. Хотя в этой серии мы будем использовать только эти два, обратите внимание, что вы можете использовать любого из [поддерживаемых поставщиков кошельков, перечисленных здесь](https://github.com/solana-labs/wallet-adapter#wallets). Чтобы добавить что-то еще, импортируйте соответствующий метод из `@solana/wallet-adapter-wallets` и добавьте его в массив `wallets`.
2. Он импортирует компонент WalletProvider, чтобы мы могли использовать его в нашем шаблоне.

Затем нам нужно передать этот массив `кошельков` компоненту `WalletProvider`, чтобы он знал, каких провайдеров кошельков мы хотим поддерживать. Кроме того, мы будем использовать режим «автоматического подключения» для этого компонента, чтобы он автоматически пытался повторно подключить кошелек пользователя при обновлении страницы.

Чтобы сделать все это, нам нужно обернуть содержимое нашего шаблона `App.vue` внутри компонента `WalletProvider` и предоставить следующие реквизиты.

```html
<template>
    <wallet-provider :wallets="wallets" auto-connect>
        <div class="w-full max-w-3xl lg:max-w-4xl mx-auto">

            <!-- Sidebar. -->
            <the-sidebar class="py-4 md:py-8 md:pl-4 md:pr-8 fixed w-20 md:w-64"></the-sidebar>

            <!-- Main -->
            <main class="flex-1 border-r border-l ml-20 md:ml-64 min-h-screen">
                <header class="flex space-x-6 items-center justify-between px-8 py-4 border-b">
                    <div class="text-xl font-bold" v-text="route.name"></div>
                </header>
                <router-view></router-view>
            </main>
        </div>
    </wallet-provider>
</template>
```

Поскольку мы упаковываем все содержимое нашего компонента `App.vue` в компонент `WalletProvider`, каждый компонент внутри нашего приложения теперь будет иметь доступ к данным кошелька. Кроме того, у них будет доступ к различным методам, позволяющим пользователю выбрать поставщика кошелька и подключиться к нему.

К счастью, эти библиотеки кошельков также предоставляют компоненты пользовательского интерфейса, которые обрабатывают все это за нас.

## Использование компонентов пользовательского интерфейса кошелька

Из библиотек кошельков, которые мы импортировали ранее, одна из них — `@solana/wallet-adapter-vue-ui` — также предоставляет компоненты VueJS, которые позволяют пользователю выбирать поставщика кошелька и подключаться к нему. Он содержит кнопку для запуска процесса, модальное окно для выбора поставщика кошелька и раскрывающийся список, который можно использовать после подключения для копирования вашего адреса, смены поставщика или отключения.

Все это можно добавить в ваше приложение с помощью следующих компонентов.

```html
<wallet-modal-provider>
    <wallet-multi-button></wallet-multi-button>
</wallet-modal-provider>
```

Компонент `<wallet-modal-provider>` предоставляет небольшой контекст для отображения и скрытия модальных окон, в то время как компонент `<wallet-multi-button>` содержит весь дизайн и логику, позволяющие пользователям подключать свои кошельки.

В настоящее время у нас есть поддельная кнопка «Выбрать кошелек» на боковой панели. Таким образом, давайте заменим его двумя компонентами выше, чтобы связать наши кошельки по-настоящему.

Внутри скриптовой части компонента TheSidebar.vue добавьте следующую строку для импорта компонентов.

```js
import { WalletMultiButton, WalletModalProvider } from '@solana/wallet-adapter-vue-ui'
```

Затем используйте их внутри части шаблона и удалите фальшивую кнопку.

```diff
- <!-- TODO: Connect wallet -->
- <div class="bg-pink-500 text-center w-full text-white rounded-full px-4 py-2">
-     Select a wallet
- </div>
+ <wallet-modal-provider>
+     <wallet-multi-button></wallet-multi-button>
+ </wallet-modal-provider>
```

И последнее, но не менее важное: нам нужно импортировать CSS для правильного оформления этих компонентов. Добавьте следующую строку в файл main.js. Важно добавить его *перед* нашим файлом `main.css`, чтобы мы могли внести некоторые изменения в дизайн в следующем разделе.

```js
// CSS.
import '@solana/wallet-adapter-vue-ui/styles.css'
import './main.css'

// ...
```

Круто! На этом этапе вы должны быть в состоянии скомпилировать свое приложение — используя `npm run serve` — и подключить свой кошелек! 

Обратите внимание: если у вас еще не установлен кошелек или расширение для браузера, такое как Phantom, не беспокойтесь об этом, мы займемся этим через минуту. Но сначала давайте посмотрим, как выглядит кнопка нашего кошелька.

![Screenshot of the application with the &quot;Connect Wallet&quot; modal opened.](https://lorisleiva.com/assets/articles/2021/1130-solana-8-wallets/connect-wallet-1.png)

![Screenshot of the application with a connected wallet and the dropdown menu opened.](https://lorisleiva.com/assets/articles/2021/1130-solana-8-wallets/connect-wallet-2.png)

В целом, не так уж и плохо, но стиль не совсем соответствует остальной части нашего приложения, а раскрывающийся список не выровнен должным образом, так что давайте это исправим.

## Обновление дизайна кнопки кошелька

К счастью для нас, все компоненты пользовательского интерфейса, предоставляемые библиотекой `@solana/wallet-adapter-vue-ui`, используют классы CSS, которые мы можем переопределить, чтобы настроить их стиль.

Итак, давайте сделаем это. Откройте файл main.css и добавьте следующие строки в конец файла.

```css
.wallet-adapter-dropdown {
    @apply w-full;
}

.wallet-adapter-button {
    @apply rounded-full w-full;
}

.wallet-adapter-button-trigger {
    @apply bg-pink-500 justify-center !important;
}

.wallet-adapter-dropdown-list {
    @apply top-auto bottom-full md:top-full md:bottom-auto md:left-0 md:right-auto;
}

.wallet-adapter-dropdown-list-active {
    @apply transform -translate-y-3 md:translate-y-3;
}
```

Директива `@apply` позволяет нам писать CSS, используя классы TailwindCSS для удобства. Кроме того, мы просто обновляем некоторые классы CSS.

Хорошо, давайте теперь посмотрим на нашу кнопку кошелька.

![Screenshot of the application with a connected wallet and the dropdown menu opened with the new design.](https://lorisleiva.com/assets/articles/2021/1130-solana-8-wallets/connect-wallet-3.png)

Намного лучше!

## Подключаем свой кошелек

Стоит отметить, что кошелек, который вы собираетесь использовать в своем браузере, обычно отличается от кошелька, который мы создали ранее в этой серии для запуска наших тестов в консоли. Первый обычно является вашим «настоящим» кошельком, а второй — просто кошельком, который ваш локальный компьютер использует для запуска тестов или использования инструментов CLI. Они могут быть одинаковыми, если вы хотите, чтобы они были, но я предпочитаю держать их отдельно.

Теперь, если у вас уже есть кошелек, зарегистрированный у поставщика кошельков, такого как Phantom или Solflare, вы уже должны быть готовы к работе. Если вы используете другого поставщика кошельков, не стесняйтесь добавлять его в массив `wallets`, который мы определили ранее.

Однако, если у вас нет кошелька или поставщика кошелька, установленного в качестве расширения браузера, вам нужно будет сделать это для взаимодействия с вашим приложением. Для этого я рекомендую установить [Phantom](https://phantom.app/) в ваш браузер. Это очень популярный поставщик кошельков с удобным пользовательским интерфейсом. После установки вы можете выполнить шаги по созданию нового кошелька непосредственно в расширении Phantom. Обязательно сохраните фразу восстановления в надежном месте, так как она может восстановить весь кошелек, включая закрытый ключ.

По умолчанию ваш кошелек покажет вам деньги или активы, которые у вас есть в кластере «mainnet». Кластер «mainnet» — это, по сути, настоящий кластер, в котором хранятся реальные деньги. Однако тот же кошелек можно использовать в других кластерах, таких как «devnet» — живой кластер с фальшивыми деньгами для тестирования — или «localnet» — ваш локальный кластер.

Таким образом, если вы хотите видеть свои деньги или активы в других кластерах, вы можете сделать это, изменив настройку в поставщике вашего кошелька. В Phantom вы можете сделать это, щелкнув значок шестеренки, затем перейдя к настройке «Изменить сеть» и выбрав здесь свой кластер.

![Three screenshots of the Phantom app to show how to change the network.](https://lorisleiva.com/assets/articles/2021/1130-solana-8-wallets/phantom-switch-network.png)

Обратите внимание, что изменение этого параметра необязательно, поскольку оно влияет только на активы, отображаемые поставщиком кошелька. Это не влияет на то, в какой кластер наше приложение отправляет транзакции. Мы настроим это в нашем коде чуть позже в этой статье.

## Доступ к данным кошелька

На этом этапе пользователи могут подключить свой кошелек к нашему приложению, и мы можем получить доступ к этим данным в нашем компоненте VueJS. Но как нам получить доступ к этим данным и что мы на самом деле из них получаем?

### Как и что?

Начнем с `как`.

Вы можете получить доступ к данным, предоставленным компонентом `WalletProvider`, используя `useWallet`, компонуемый из библиотеки `@solana/wallet-adapter-vue`.

```js
import { useWallet } from '@solana/wallet-adapter-vue'
const data = useWallet()
```

Пока вы вызываете `useWallet()` внутри компонента, у вас будет доступ к свойствам и методам, связанным с подключенным кошельком.

Так что же мы на самом деле получаем от `useWallet()`?

- `wallet`. Потенциально наиболее интересной для нас информацией является подключенный кошелек пользователя. Если пользователь подключил кошелек, это будет объект, содержащий его публичный ключ. В противном случае это свойство будет `null`.
- `ready`, `connected`, `connecting` и `disconnecting`. Это полезные логические значения, позволяющие нам понять, в каком состоянии мы находимся. Например, мы можем использовать логическое значение `connected`, чтобы узнать, подключил ли пользователь свой кошелек или нет.
- Методы `select`, `connect` и `disconnect` позволяют нам выбирать, подключаться и отключаться от поставщика кошелька. Нам не нужно использовать эти методы напрямую, поскольку они уже используются компонентами пользовательского интерфейса кошелька, которые мы импортировали ранее.
- Методы `sendTransaction`, `signTransaction`, `signAllTransactions` и `signMessage` позволяют нам подписывать сообщения и/или транзакции от имени подключенного кошелька. Хотя мы не будем использовать их напрямую, Anchor требует, чтобы некоторые из этих методов были внутри его объекта `wallet`.

### Anchor кошелек

Как видите, useWallet() дает нам много детализированной информации, которую можно использовать для взаимодействия с подключенным кошельком. Из-за этого объект `wallet`, который он предоставляет, не совместим с определением кошелька Anchor. Если вы помните следующую диаграмму из эпизода 5, вы можете видеть, что Anchor использует свой собственный объект `Wallet` для взаимодействия с подключенным кошельком и подписания транзакций от его имени.

![Diagram from episode 5 showing the incompatibility with the wallet provided by &quot;useWallet()&quot;.](https://lorisleiva.com/assets/articles/2021/1130-solana-8-wallets/diagram-wallet-not-compatible.png)

Чтобы получить объект, совместимый с определением кошелька Anchor, мы можем использовать еще один компонуемый объект под названием `useAnchorWallet`. Это вернет объект `кошелька`, который может подписывать транзакции.

```js
import { useAnchorWallet } from '@solana/wallet-adapter-vue'
const wallet = useAnchorWallet()
```

Точно так же мы можем соединить нашу предыдущую диаграмму Anchor с нашей совершенно новой интеграцией кошелька.

![Previous diagram using &quot;useAnchorWallet()&quot; to connect &quot;WalletAdapter&quot; with the &quot;Wallet&quot; object from Anchor.](https://lorisleiva.com/assets/articles/2021/1130-solana-8-wallets/diagram-wallet-compatible.png)

### Реактивные переменные в VueJS

Я хотел бы сделать здесь небольшую паузу, чтобы поговорить о реактивных переменных в Vue 3. Если вы не знакомы с ними, часть кода, который вы прочитаете позже, может быть немного запутанной.

**Большинство перечисленных выше свойств являются реактивными и заключены в объекты `Ref`**. Если вы не знакомы с переменными `Ref` Vue, они гарантируют, что содержимое переменной **передается по ссылке**, а не **по значению**.

![Pass by reference vs pass by value animation using a cup of coffee and its content to illustrate the two.](https://lorisleiva.com/assets/articles/2021/1130-solana-8-wallets/pass-by-reference-vs-pass-by-value-animation.gif)

Это означает, что, имея ссылку на переменную `Ref`, мы можем изменить ее содержимое, и любой код, использующий эту переменную, может быть уведомлен о таком изменении. Чтобы получить доступ к содержимому переменной `Ref`, вы должны получить доступ к ее свойству `value` — например. `wallet.value` — если вы не используете эту переменную внутри шаблона VueJS, в этом случае VueJS автоматически сделает это за вас. Подробнее о них можно прочитать в [документации Vue](https://v3.vuejs.org/guide/composition-api-introduction.html#reactive-variables-with-ref) или — если вы привыкли к React — [это может помочь](https://twitter.com/lorismatic/status/1445672788153577472?s=20).

Вот небольшой пример, чтобы обобщить, как мы можем получить доступ к переменным `Ref`. Внутри скриптовой части мы используем `value`. Внутри части шаблона мы этого не делаем.

```html
<script setup>
import { ref } from 'vue'
const name = ref('Loris')
console.log(name.value) // Outputs: Loris
</script>

<template>
  <div>{{ name }}</div> <!-- Displays: Loris -->
</template>
```

### Использование данных кошелька в компонентах

Хорошо, давайте применим то, что мы узнали, на практике.

На данный момент любой желающий может увидеть форму, которая позволяет пользователям отправлять твиты. Однако эта форма должна быть видна только пользователям, которые подключили свои кошельки, так что давайте это исправим.

В части скрипта компонента `TweetForm.vue` импортируйте составной файл `useWallet`.

```js
import { computed, ref, toRefs } from 'vue'
import { useAutoresizeTextarea, useCountCharacterLimit, useSlug } from '@/composables'
import { sendTweet } from '@/api'
import { useWallet } from '@solana/wallet-adapter-vue'
```

Затем в шаблонной части компонента — в разделе `Permissions` — обновите следующую строку.

```diff
  // Permissions.
- const connected = ref(true) // TODO: Check connected wallet.
+ const { connected } = useWallet()
```

Это будет использовать переменную `connected` из данных кошелька, а не всегда `true`, как это было раньше.

Если вы заглянете внутрь шаблона этого компонента, то увидите, что эта переменная `connected` используется для переключения того, какой HTML мы показываем пользователю: либо форму, либо пустое состояние.

```html
<template>
    <div v-if="connected" class="px-8 py-4 border-b">
        <!-- Form here... -->
    </div>

    <div v-else class="px-8 py-4 bg-gray-50 text-gray-500 text-center border-b">
        Connect your wallet to start tweeting...
    </div>
</template>
```

И это все! Теперь форму твита могут видеть только пользователи с подключенными кошельками.

Сделаем еще один. На этот раз мы позаботимся о том, чтобы страница профиля не отображалась на боковой панели, если вы не подключены.

В части сценария компонента `TheSidebar.vue` импортируйте и вызовите `useWallet`, чтобы получить доступ к переменной `connected`.

```js
import { WalletMultiButton, WalletModalProvider } from '@solana/wallet-adapter-vue-ui'
import { useWallet } from '@solana/wallet-adapter-vue'
const { connected } = useWallet()
```

Затем внутри шаблона найдите комментарий «TODO: Проверить подключенный кошелек». Под этим комментарием замените `v-if="true"` на `v-if="connected"` и вуаля! Вы также можете удалить этот комментарий `TODO` прямо сейчас.

```html
<template>
    <aside class="flex flex-col items-center md:items-stretch space-y-2 md:space-y-4">
        <!-- ... -->
        <div class="flex flex-col items-center md:items-stretch space-y-2">
              <!-- ... -->
            <router-link v-if="connected" :to="{ name: 'Profile' }" ...>
                <!-- ... -->
            </router-link>
        </div>
        <div class="fixed bottom-8 right-8 md:static w-48 md:w-full">
            <wallet-modal-provider>
                <wallet-multi-button></wallet-multi-button>
            </wallet-modal-provider>
        </div>
    </aside>
</template>
```

Напомним, вот что вы должны увидеть, если у вас есть подключенный кошелек.

![Screenshot of the home page when a wallet is connected. We can see the profile page link in the sidebar and the tweet form on the home page.](https://lorisleiva.com/assets/articles/2021/1130-solana-8-wallets/wallet-connected.png)

И вот что вы должны увидеть, если вы этого не сделаете. т.е. нет страницы профиля и формы твита.

![Screenshot of the home page when no wallet is connected. We can no longer see the profile page link in the sidebar nor the tweet form on the home page that now says &quot;Connect your wallet to start tweeting...&quot;.](https://lorisleiva.com/assets/articles/2021/1130-solana-8-wallets/wallet-not-connected.png)

## Нам нужно больше данных

Хорошо, давайте сделаем глубокий вдох и посмотрим, чего мы уже достигли в этом эпизоде.

- Мы импортировали `WalletProvider`, который предоставляет все необходимое для подключения кошелька и доступа к его данным.
- Мы импортировали некоторые компоненты пользовательского интерфейса, которые позволяют пользователям подключать свои кошельки.
- Мы получили доступ к данным, предоставленным `WalletProvider`, используя `useWallet()` в различных компонентах.
- Мы обнаружили, что можем использовать `useAnchorWallet()` для получения объекта `кошелька`, совместимого с Anchor.

Итак, если мы обратимся к нашей диаграмме, которая представляет все объекты, необходимые Anchor для создания Программы, мы увидим, что в настоящее время у нас есть только одна часть головоломки: `Wallet`.

![Previous diagram where &quot;WalletAdapter&quot; points to Anchor's &quot;Wallet&quot; object but this time &quot;Wallet&quot; is highlighted in red to show that we've only got this piece of information so far.](https://lorisleiva.com/assets/articles/2021/1130-solana-8-wallets/diagram-wallet-only.png)

Чтобы у нас было все необходимое для взаимодействия с нашей программой Solana, нам нужно заполнить оставшуюся часть головоломки. К счастью для нас, часть `Wallet` было труднее всего найти, поскольку нам нужно было интегрироваться с поставщиками кошельков, что мы сейчас и сделали.

Anchor называет всю эту картину «Workspace», потому что она дает нам все необходимое для работы с нашей программой.

## Доступ к workspace

Хорошо, давайте заполним недостающие части головоломки и создадим наше рабочее пространство. Мы создадим новый файл `useWorkspace.js` в папке `composables` и зарегистрируем его в `composables/index.js`.

```js
export * from './useAutoresizeTextarea'
export * from './useCountCharacterLimit'
export * from './useFromRoute'
export * from './useSlug'
export * from './useWorkspace'
```

Внутри компонуемого `useWorkspace.js` мы будем использовать API предоставления/вставки из VueJS для ввода данных в наши компоненты так же, как это делает `WalletProvider`. Для этого нам нужен метод initWorkspace, который инициализирует наш контекст, и метод useWorkspace, который обращается к нему. Вот как мы можем сделать это с помощью VueJS.

```js
import { inject, provide } from 'vue'

const workspaceSymbol = Symbol()

export const useWorkspace = () => inject(workspaceSymbol)

export const initWorkspace = () => {
    provide(workspaceSymbol, {
        // Provided data here...
    })
}
```

Давайте начнем с простого, импортировав подключенный кошелек Anchor и предоставив его в качестве данных. Таким образом, нам не нужно использовать другие составные компоненты для доступа к подключенному кошельку. У нас будет все в одном месте.

```js
import { inject, provide } from 'vue'
import { useAnchorWallet } from '@solana/wallet-adapter-vue'

const workspaceSymbol = Symbol()

export const useWorkspace = () => inject(workspaceSymbol)

export const initWorkspace = () => {
    const wallet = useAnchorWallet()

    provide(workspaceSymbol, {
        wallet,
    })
}
```

Следующее, что нам нужно, это объект Connection. Для этого нам просто нужно знать, с каким кластером или сетью мы хотим взаимодействовать. А пока мы продолжим развивать наше приложение локально. Поэтому мы жестко закодируем URL-адрес нашего локального хоста: `http://127.0.0.1:8899`. У нас будет более динамичный способ справиться с этим в будущем, когда мы развернемся в devnet.

![Previous diagram with a new &quot;localhost&quot; node pointing to the &quot;Cluster&quot; node and the array says &quot;hardcoded&quot;.](https://lorisleiva.com/assets/articles/2021/1130-solana-8-wallets/diagram-with-localhost.png)

Итак, давайте создадим новый объект `Connection`, используя этот URL-адрес кластера, и также предоставим его как данные.

```js
import { inject, provide } from 'vue'
import { useAnchorWallet } from '@solana/wallet-adapter-vue'
import { Connection } from '@solana/web3.js'

const workspaceSymbol = Symbol()

export const useWorkspace = () => inject(workspaceSymbol)

export const initWorkspace = () => {
    const wallet = useAnchorWallet()
    const connection = new Connection('http://127.0.0.1:8899')

    provide(workspaceSymbol, {
        wallet,
        connection,
    })
}
```

Мы знаем, что `Connection + Wallet = Provider`, поэтому теперь мы можем создать новый объект `Provider`. Однако этот объект-поставщик должен быть «вычисляемым» свойством, чтобы он воссоздавался при изменении свойства `wallet`, например. он отключен или подключен как другой кошелек.

Вот как мы можем добиться этого с помощью VueJS. Обратите внимание, как мы получаем доступ к кошельку, используя `wallet.value` внутри метода `computed`.

```js
import { inject, provide, computed } from 'vue'
import { useAnchorWallet } from '@solana/wallet-adapter-vue'
import { Connection } from '@solana/web3.js'
import { Provider } from '@project-serum/anchor'

const workspaceSymbol = Symbol()

export const useWorkspace = () => inject(workspaceSymbol)

export const initWorkspace = () => {
    const wallet = useAnchorWallet()
    const connection = new Connection('http://127.0.0.1:8899')
    const provider = computed(() => new Provider(connection, wallet.value))

    provide(workspaceSymbol, {
        wallet,
        connection,
        provider,
    })
}
```

Затем нам нужно получить доступ к файлу `IDL`, который представляет собой файл JSON, представляющий структуру нашей программы. Этот файл автоматически создается в папке target в корне нашего проекта, поэтому давайте получим к нему доступ прямо оттуда.

Обратите внимание, что это не будет работать, когда приложение развернуто на сервере само по себе, так как целевая папка будет пустой, но мы позаботимся об этом позже, когда будем развертывать в devnet.

```js
import { inject, provide, computed } from 'vue'
import { useAnchorWallet } from '@solana/wallet-adapter-vue'
import { Connection } from '@solana/web3.js'
import { Provider } from '@project-serum/anchor'
import idl from '../../../target/idl/solana_twitter.json'

const workspaceSymbol = Symbol()

export const useWorkspace = () => inject(workspaceSymbol)

export const initWorkspace = () => {
    const wallet = useAnchorWallet()
    const connection = new Connection('http://127.0.0.1:8899')
    const provider = computed(() => new Provider(connection, wallet.value))

    provide(workspaceSymbol, {
        wallet,
        connection,
        provider,
    })
}
```

Наконец, поскольку `IDL + Provider = Program`, теперь мы можем создать наш программный объект. Здесь мы также будем использовать вычисляемое свойство, потому что `provider` также является реактивным.

Помимо запроса объектов `idl` и `provider`, для создания `Program` также требуется ее адрес как экземпляр `PublicKey`. К счастью для нас, файл IDL уже содержит эту информацию в разделе idl.metadata.address. Нам просто нужно обернуть это в объект PublicKey и передать его программе.

⛔️ **Предупреждение**: переменная `metadata.address`, содержащая идентификатор нашей программы, будет доступна только после запуска `anchor deploy`, потому что именно тогда Anchor узнает, по какому адресу была развернута программа. Поэтому, если вы запустите `anchor build` без запуска `anchor deploy`, вы получите следующую ошибку: `Cannot read properties of undefined (reading 'address')`.

И вот оно! Окончательный код нашего составного файла `useWorkspace.js`, который дает нам доступ ко всему, что нам нужно для взаимодействия с нашей программой Solana.

```js
import { inject, provide, computed } from 'vue'
import { useAnchorWallet } from '@solana/wallet-adapter-vue'
import { Connection, PublicKey } from '@solana/web3.js'
import { Provider, Program } from '@project-serum/anchor'
import idl from '../../../target/idl/solana_twitter.json'

const programID = new PublicKey(idl.metadata.address)
const workspaceSymbol = Symbol()

export const useWorkspace = () => inject(workspaceSymbol)

export const initWorkspace = () => {
    const wallet = useAnchorWallet()
    const connection = new Connection('http://127.0.0.1:8899')
    const provider = computed(() => new Provider(connection, wallet.value))
    const program = computed(() => new Program(idl, programID, provider.value))

    provide(workspaceSymbol, {
        wallet,
        connection,
        provider,
        program,
    })
}
```

Теперь все, что нам нужно сделать, это вызвать этот метод `initWorkspace` внутри компонента, чтобы он предоставил данные рабочей области всем своим потомкам.

В папке `components` создайте новый компонент `WorkspaceProvider.vue` и вставьте следующий код.

```html
<script setup>
import { initWorkspace } from '@/composables'
initWorkspace()
</script>

<template>
    <slot></slot>
</template>
```

Затем внутри нашего компонента App.vue мы импортируем только что созданный WorkspaceProvider и используем его в шаблоне. Он должен обернуть все содержимое нашего шаблона, кроме компонента `WalletProvider`, который должен быть отрисован первым, чтобы компонент `WorkspaceProvider` мог получить доступ к своим данным.

```html
<script setup>
import { useRoute } from 'vue-router'
import TheSidebar from './components/TheSidebar'
import { getPhantomWallet, getSolflareWallet } from '@solana/wallet-adapter-wallets'
import { WalletProvider } from '@solana/wallet-adapter-vue'
import WorkspaceProvider from '@/components/WorkspaceProvider'

// ...
</script>

<template>
    <wallet-provider :wallets="wallets" auto-connect>
        <workspace-provider>
            <!-- ... -->
        </workspace-provider>
    </wallet-provider>
</template>
```

Все готово! Теперь мы можем получить доступ к данным рабочей области из любого компонента нашего приложения.

## Использование workspace

Прежде чем мы закончим эту статью, давайте кратко рассмотрим, как мы можем получить доступ к этим данным рабочей области в наших компонентах.

Мы воспользуемся этой возможностью, чтобы обновить адрес кошелька на странице профиля.

Если вы откроете компонент `PageProfile.vue`, вы должны увидеть открытый ключ, жестко закодированный в шаблоне.

```html
<div v-if="true" class="border-b px-8 py-4 bg-gray-50">
    B1AfN7AgpMyctfFbjmvRAvE1yziZFDb9XCwydBjJwtRN
</div>
```

Теперь, когда у нас есть доступ к реальному подключенному кошельку, давайте заменим его открытым ключом.

```html
<script setup>
import { ref, watchEffect } from 'vue'
import { fetchTweets } from '@/api'
import TweetForm from '@/components/TweetForm'
import TweetList from '@/components/TweetList'
import { useWorkspace } from '@/composables'

const tweets = ref([])
const loading = ref(true)
const { wallet } = useWorkspace()

// ...
</script>

<template>
    <div v-if="wallet" class="border-b px-8 py-4 bg-gray-50">
        {{ wallet.publicKey.toBase58() }}
    </div>
    <tweet-form @added="addTweet"></tweet-form>
    <tweet-list :tweets="tweets" :loading="loading"></tweet-list>
</template>
```

Как видите, мы:

1. Импортирован компонуемый файл useWorkspace.
2. Извлек все необходимые переменные из `useWorkspace()` — здесь объект `кошелька`.
3. Использовал объект `wallet` внутри нашего шаблона для отображения его открытого ключа в формате base 58.

Это был простой пример, но теперь мы можем сделать гораздо больше. Как и в наших тестах, теперь мы можем получить доступ к объекту `program` и использовать его различные API для взаимодействия с нашей программой Solana, что мы и будем делать в следующих нескольких эпизодах.

## Вывод

Во-первых, молодцы, что зашли так далеко! Честно говоря, этот сериал был настоящим путешествием, и я очень рад видеть, что многие из вас с нетерпением ждут следующих эпизодов.

Хотя интеграция с кошельками стала для нас очень простой благодаря репозиторию [wallet-adapters](https://github.com/solana-labs/wallet-adapter) и предоставляемым им библиотекам JavaScript, нам все равно пришлось их настраивать. правильно и понимать различные концепции по пути. Но посмотрите, что мы имеем сейчас:

- Пользователи могут подключать свои кошельки.
- Наше приложение может получить доступ к данным о подключенном кошельке и действовать соответствующим образом.
- Наше приложение имеет доступ к полному рабочему пространству, что позволяет ему взаимодействовать с нашей программой Solana так же, как мы это делали в наших тестах.

Это огромный прогресс по сравнению с нашим фиктивным приложением, которое раньше ничего не делало!

Как обычно, вы можете получить доступ к коду этого эпизода в ветке ниже и сравнить его с предыдущим эпизодом.

[Просмотреть эпизод 8 на GitHub](https://github.com/lorisleiva/solana-twitter/tree/episode-8)

[Сравните с Эпизодом 7](https://github.com/lorisleiva/solana-twitter/compare/episode-7...episode-8)

В следующем эпизоде мы заменим фиктивные данные из наших файлов `api` и используем наше совершенно новое рабочее пространство для извлечения реальных твитов из нашей программы Solana.
