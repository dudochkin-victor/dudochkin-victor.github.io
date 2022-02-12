+++
title = "5. Тестируем нашу инструкцию"
description = "5. Тестируем нашу инструкцию"
weight = 1
hash = "5407a787"
+++

[Перевод](https://lorisleiva.com/create-a-solana-dapp-from-scratch/testing-our-instruction) | Автор оригинала: Loris

ЭПИЗОД 5

2 МЕСЯЦА НАЗАД

ЧТЕНИЕ 19 МИН.

Наша программа может быть готова, но наша работа не закончена. В этой статье мы напишем несколько тестов, взаимодействующих с нашей программой и, в частности, с нашей инструкцией SendTweet.

Хотя написание тестов может показаться не самой увлекательной задачей, это прекрасная возможность для нас понять, как мы можем взаимодействовать с нашей программой и от имени какого кошелька.

## Добро пожаловать на другую сторону

До сих пор в этой серии мы сосредоточились на разработке программ, то есть на той части кода, которая находится в блокчейне.

Теперь пришло время перейти к другой стороне — клиенту.

Как и в случае с традиционным веб-сервером, нам нужен клиент для взаимодействия с нашей программой Solana. Позже в этой серии мы реализуем клиент JavaScript с помощью платформы VueJS, но сейчас мы будем использовать клиент JavaScript для тестирования нашей программы.

Преимущество этого в том, что после написания наших тестов мы будем знать точный синтаксис, который будем использовать в нашем интерфейсе для взаимодействия с нашей программой.

Итак, как же взаимодействовать с блокчейном Solana?

**Solana предлагает для этой цели [API JSON RPC](https://docs.solana.com/developing/clients/jsonrpc-api)**. Не пугайтесь спецификаций RPC, в конце концов, это всего лишь API.

При этом Solana предоставляет [библиотеку JavaScript под названием `@solana/web3.js`](https://github.com/solana-labs/solana-web3.js), которая инкапсулирует для нас этот API, предоставляя набор [полезные асинхронные методы](https://docs.solana.com/developing/clients/javascript-reference).

Все эти методы находятся внутри объекта «Соединение», которому требуется «Кластер», чтобы знать, куда отправлять свои запросы. Как обычно, этим кластером может быть localhost, devnet и т. д. Поскольку сейчас мы работаем локально, мы используем кластер «localhost».

Вот небольшое визуальное представление, которое будет расти в этой статье.

![A simple diagram made of two nodes. One “Cluster” node points to another “Connection” node and the arrow says “encapsulated by”.](https://lorisleiva.com/assets/articles/2021/1124-solana-5-test-instruction/solana-models-1.png)

Теперь мы знаем, как взаимодействовать с блокчейном Solana, но как мы можем подписывать транзакции, чтобы подтвердить свою личность? Для этого нам нужен объект Wallet, который имеет доступ к паре ключей пользователя, совершающего транзакцию.

К счастью для нас, Anchor также предоставляет [библиотеку JavaScript под названием `@project-serum/anchor`](https://www.npmjs.com/package/@project-serum/anchor), которая делает все это очень простым.

Библиотека Anchor предоставляет нам объект Wallet, для которого требуется пара ключей и который позволяет подписывать транзакции. Но это еще не все, он также предоставляет нам объект «Поставщик», который обертывает как «Соединение», так и «Кошелек» и автоматически добавляет подпись кошелька к исходящим транзакциям. Объект «Поставщик» упрощает взаимодействие с блокчейном Solana от имени кошелька.

Вот обновленная версия нашей предыдущей диаграммы.

![Same diagram as before but with “Wallet” and “Provider” nodes. Both the “Connection” and the “Wallet” nodes point towards the “Provider” node and the arrow says “used by”.](https://lorisleiva.com/assets/articles/2021/1124-solana-5-test-instruction/solana-models-2.png)

Но подождите, это еще не все! Если вы помните, [в эпизоде 2](https://lorisleiva.com/create-a-solana-dapp-from-scratch/getting-started-with-solana-and-anchor#anchor-build) мы упомянули, что каждый когда мы запускаем `anchor build`, Anchor генерирует файл JSON, называемый `IDL`, что означает ["Язык описания интерфейса"](https://en.wikipedia.org/wiki/Interface_description_language). Этот файл IDL содержит структурированное описание нашей программы, включая ее открытый ключ, инструкции и учетные записи.

Представьте, что мы могли бы получить, если бы объединили этот файл «IDL», который знает все о нашей программе, и этот объект `Provider`, который может взаимодействовать с блокчейном Solana от имени кошелька. Это будет последняя часть головоломки.

Что ж, не представляйте больше, потому что Anchor предоставляет еще один объект под названием «Программа», который использует как `IDL`, так и `Provider` для создания пользовательского API JavaScript, который полностью соответствует нашей программе Solana. Благодаря этому объекту `Program` мы можем взаимодействовать с нашей программой Solana от имени кошелька, даже не зная ничего о базовом API.

И вот оно, окончательное изображение, иллюстрирующее, как Anchor инкапсулирует JavaScript-библиотеку Solana для улучшения нашего опыта разработчиков.

![Same diagram as before but with 2 more nodes: one “IDL” node and one “Program” node. The “IDL” node and the “Provider” node both point towards the “Program” node and the arrow says “used by”. There’s also another new node entitled “anchor build” that points towards the “IDL” node and the arrow says “generates”.](https://lorisleiva.com/assets/articles/2021/1124-solana-5-test-instruction/solana-models-3.png)

## Клиент только для тестов

Давайте применим то, что мы узнали, на практике, настроив объект `Program`, который мы можем использовать в наших тестах.

Прежде всего, нет необходимости импортировать какие-либо новые библиотеки JavaScript, обе упомянутые выше библиотеки включены по умолчанию в каждый проект Anchor.

Затем, если вы посмотрите на диаграмму выше, есть два вопроса, на которые нам нужно ответить, чтобы получить объект `Program`: какой кластер и какой кошелек?

Anchor позаботится об ответах на оба этих вопроса, создав объект Provider, который использует конфигурации из нашего файла Anchor.toml.

Точнее, он изучит настройки вашего провайдера, которые должны выглядеть примерно так.

```rust
[provider]
cluster = "localnet"
wallet = "/Users/loris/.config/solana/id.json"
```

С этими конфигурациями провайдера он знает, как использовать кластер localhost — с помощью локальной книги — и знает, где найти пару ключей на вашем локальном компьютере.

Теперь откройте тестовый файл, который должен находиться по адресу `tests/solana-twitter.ts`. Если вы посмотрите на первые 2 строки, расположенные внутри метода `describe`, вы должны увидеть следующее.

```js
// Configure the client to use the local cluster.
anchor.setProvider(anchor.Provider.env());
const program = anchor.workspace.SolanaTwitter as Program<SolanaTwitter>;
```

- Первая строка вызывает метод `anchor.Provider.env()` для создания нового `Provider` для нас, используя наш конфигурационный файл `Anchor.toml`. Помните: Кластер + Кошелек = Провайдер. Затем он регистрирует этого нового провайдера с помощью метода `anchor.setProvider`.
- Вторая строка использует этого зарегистрированного провайдера для создания нового объекта «Программа», который мы можем использовать в наших тестах. Обратите внимание, что, поскольку тесты написаны на TypeScript, мы также используем пользовательский тип SolanaTwitter, который Anchor сгенерировал для нас при запуске `anchor build`. Таким образом, мы можем получить хорошее автодополнение от нашего редактора кода.

Вот так наш тестовый клиент настроен и готов к использованию! Вот небольшое обновление на нашей диаграмме, чтобы отразить то, что мы здесь узнали.

![Same diagram as before with 2 more nodes. One node is called &quot;Anchor.toml&quot; that points to both the &quot;Cluster&quot; node and the &quot;Wallet&quot; node. Both of these arrows say &quot;configures&quot; on them. The other new node is called &quot;anchor.Provider.env()&quot; and points to the &quot;Provider&quot; node. The arrow here says &quot;generates&quot;.](https://lorisleiva.com/assets/articles/2021/1124-solana-5-test-instruction/solana-models-4.png)

## Отправка твита

Итак, хватит теории, давайте напишем наш первый тест! Начнем с удаления фиктивного теста, который был автоматически сгенерирован в нашем файле `tests/solana-twitter.ts`.

```diff
  // Configure the client to use the local cluster.
  anchor.setProvider(anchor.Provider.env());
  const program = anchor.workspace.SolanaTwitter as Program<SolanaTwitter>;

- it('Is initialized!', async () => {
-     // Add your test here.
-     const tx = await program.rpc.initialize({});
-     console.log("Your transaction signature", tx);
- });
```

Вместо этого добавьте следующий код.

```js
it('can send a new tweet', async () => {
    // Before sending the transaction to the blockchain.

    await program.rpc.sendTweet('TOPIC HERE', 'CONTENT HERE', {
        accounts: {
            // Accounts here...
        },
        signers: [
              // Key pairs of signers here...
        ],
    });

    // After sending the transaction to the blockchain.
});
```

Не беспокойтесь, если ваша IDE везде показывает красный цвет. Это просто TypeScript жалуется, что в нашей инструкции недостаточно данных. Будем приходить постепенно.

Хорошо, давайте переварим этот кусок кода:

- Мы создали новый тест под названием `it can send a new tweet`, используя метод `it` из тестовой среды `mocha`.
- Мы использовали «асинхронную» функцию, потому что внутри нее мы будем вызывать асинхронные функции. Точнее, нам нужно «дождаться» (`await`) завершения транзакции, прежде чем мы сможем убедиться, что правильная учетная запись была создана.
- Мы использовали наш объект `program` для взаимодействия с нашей программой. **Объект программы содержит объект `rpc`, который предоставляет API, соответствующий инструкциям нашей программы**. Следовательно, чтобы вызвать нашу инструкцию SendTweet, нам нужно вызвать метод program.rpc.sendTweet.
- При использовании любого метода из объекта `program.rpc` ** нам нужно сначала предоставить любой аргумент, требуемый инструкцией **. В нашем случае это аргументы `topic` и `content` в указанном порядке.
- **Последним аргументом любого метода `program.rpc` всегда является контекст**. Если вы помните из предыдущего эпизода, контекст инструкции содержит все учетные записи, необходимые для успешного выполнения инструкции. Помимо предоставления «учетных записей» в виде объекта, нам также необходимо предоставить пары ключей всех «подписавшихся» в виде массива. Обратите внимание, что нам не нужно предоставлять пару ключей нашего кошелька, так как Anchor делает это автоматически.

Хорошо, давайте заполним этот метод sendTweet реальными данными. Давайте предоставим тему и контент для нашего твита и сделаем его веганским, а почему бы и нет?

```js
it('can send a new tweet', async () => {
    await program.rpc.sendTweet('veganism', 'Hummus, am I right?', {
        accounts: {
            // Accounts here...
        },
        signers: [
              // Key pairs of signers here...
        ],
    });
});
```

Далее заполняем счета. В контексте нашей инструкции `SendTweet` нам нужно указать следующие учетные записи: `tweet`, `author` и `system_program`. Начнем с учетной записи `tweet`.

Поскольку это учетная запись, которую создаст наша инструкция, **нам просто нужно сгенерировать для нее новую пару ключей**. Таким образом, мы также можем доказать, что нам разрешено инициализировать учетную запись по этому адресу, потому что мы можем добавить учетную запись «tweet» в качестве подписывающей стороны.

Мы можем сгенерировать новую пару ключей в JavaScript, используя метод `anchor.web3.Keypair.generate()`. Затем мы можем добавить эту сгенерированную пару ключей в массив «signers» и добавить ее открытый ключ в объект `accounts`.

```js
it('can send a new tweet', async () => {
    const tweet = anchor.web3.Keypair.generate();
    await program.rpc.sendTweet('veganism', 'Hummus, am I right?', {
        accounts: {
            tweet: tweet.publicKey,
        },
        signers: [tweet],
    });
});
```

Далее, учетная запись `author`. Для этого нам нужно получить доступ к открытому ключу кошелька, используемого внутри нашей программы. Помните, как наша программа содержит провайдера, который содержит кошелек? Это означает, что мы можем получить доступ к открытому ключу нашего кошелька через `program.provider.wallet.publicKey`.

Поскольку Anchor автоматически добавляет кошелек в качестве подписавшего для каждой транзакции, нам не нужно изменять массив `signers`.

```js
it('can send a new tweet', async () => {
    const tweet = anchor.web3.Keypair.generate();
    await program.rpc.sendTweet('veganism', 'Hummus, am I right?', {
        accounts: {
            tweet: tweet.publicKey,
            author: program.provider.wallet.publicKey,
        },
        signers: [tweet],
    });
});
```

Наконец, нам нужно предоставить учетную запись `system_program`. Обратите внимание, что в JavaScript Anchor автоматически преобразует переменные case змеи в переменные camel case внутри нашего контекста. Это означает, что нам нужно предоставить системную программу, используя `systemProgram` вместо `system_program`.

Теперь, как мы можем получить доступ к открытому ключу официальной системной программы Соланы в JavaScript? Проще говоря, мы можем получить доступ к системной программе, используя `anchor.web3.SystemProgram`, и поэтому мы можем получить доступ к ее открытому ключу через `anchor.web3.SystemProgram.programId`.

```js
it('can send a new tweet', async () => {
    const tweet = anchor.web3.Keypair.generate();
    await program.rpc.sendTweet('veganism', 'Hummus, am I right?', {
        accounts: {
            tweet: tweet.publicKey,
            author: program.provider.wallet.publicKey,
            systemProgram: anchor.web3.SystemProgram.programId,
        },
        signers: [tweet],
    });
});
```

Бум! Вот так мы готовы отправлять твиты в нашу собственную программу Solana. 🤯

Хотя запуск этого теста позволит успешно создать учетную запись `Tweet` в блокчейне, на самом деле мы еще ничего не тестируем. Итак, следующий шаг — сделать еще один вызов блокчейна, чтобы получить вновь созданную учетную запись и убедиться, что данные совпадают с тем, что мы отправили.

Чтобы получить учетную запись в блокчейне, нам нужно получить доступ к другому API, предоставляемому объектом `program`. Вызывая `program.account.tweet`, мы получаем доступ к нескольким методам, которые помогают нам получать учетные записи `Tweet` из блокчейна. Обратите внимание, что **эти методы доступны для каждой учетной записи, указанной в нашей программе Solana**. Таким образом, если бы у нас была учетная запись UserProfile, мы могли бы получить ее с помощью API Program.account.userProfile.

В этих методах API мы можем использовать `fetch` для получения только одной учетной записи, предоставляя ее открытый ключ. Поскольку Anchor знает, какой тип учетной записи мы пытаемся получить, он автоматически анализирует все данные для нас.

Итак, давайте получим нашу недавно созданную учетную запись `Tweet`. Мы будем использовать открытый ключ нашей пары ключей `tweet`, чтобы получить наш `tweetAccount`. Давайте также зарегистрируем содержимое этой учетной записи, чтобы мы могли видеть, что там.

```js
it('can send a new tweet', async () => {
    // Call the "SendTweet" instruction.
    const tweet = anchor.web3.Keypair.generate();
    await program.rpc.sendTweet('veganism', 'Hummus, am I right?', {
        accounts: {
            tweet: tweet.publicKey,
            author: program.provider.wallet.publicKey,
            systemProgram: anchor.web3.SystemProgram.programId,
        },
        signers: [tweet],
    });

    // Fetch the account details of the created tweet.
    const tweetAccount = await program.account.tweet.fetch(tweet.publicKey);
      console.log(tweetAccount);
});
```

Теперь давайте запустим наши тесты с помощью `anchor test` — помните, что это создаст, развернет и протестирует нашу программу, запустив ее собственный локальный реестр.

Напоминание: пользователям Apple M1 потребуется запустить `solana-test-validator --no-bpf-jit --reset` и `anchor test --skip-local-validator` в отдельном сеансе терминала.

Вы должны увидеть прохождение теста — нормально, поскольку мы еще не определили никаких утверждений — но вы также должны увидеть объект, который выглядит так в журналах.

```js
{
  author: PublicKey {
    _bn: <BN: 7d9c91c77d1f5b693cf0b3960a0c037211298a1e495ac14ef0d8fb904b38388f>
  },
  timestamp: <BN: 619e2495>,
  topic: 'veganism',
  content: 'Hummus, am I right?'
}
```

Поздравляю! Это учетная запись, которую мы извлекли из блокчейна, и похоже, что она содержит правильные данные. Хотя бы по теме и содержанию.

Итак, последнее, что нам нужно сделать для завершения нашего теста, — это написать утверждения. Для этого нам нужно импортировать библиотеку `assert` в начало нашего тестового файла. Его не нужно устанавливать, это уже одна из наших зависимостей.

```js
import * as anchor from '@project-serum/anchor';
import { Program } from '@project-serum/anchor';
import { SolanaTwitter } from '../target/types/solana_twitter';
import * as assert from "assert";
```

Теперь мы можем использовать `assert` для:

- Убедитесь, что две вещи равны, используя [`assert.equal(actualThing, ожидаемая вещь)`](https://nodejs.org/api/assert.html#assertequalactual-expected-message).
- Убедитесь, что что-то соответствует действительности, используя [`assert.ok(что-то)`](https://nodejs.org/api/assert.html#assertokvalue-message).

Итак, давайте удалим наш предыдущий `console.log` и добавим несколько утверждений в наш тест.

```js
it('can send a new tweet', async () => {
    // Execute the "SendTweet" instruction.
    const tweet = anchor.web3.Keypair.generate();
    await program.rpc.sendTweet('veganism', 'Hummus, am I right?', {
        accounts: {
            tweet: tweet.publicKey,
            author: program.provider.wallet.publicKey,
            systemProgram: anchor.web3.SystemProgram.programId,
        },
        signers: [tweet],
    });

    // Fetch the account details of the created tweet.
    const tweetAccount = await program.account.tweet.fetch(tweet.publicKey);

    // Ensure it has the right data.
    assert.equal(tweetAccount.author.toBase58(), program.provider.wallet.publicKey.toBase58());
    assert.equal(tweetAccount.topic, 'veganism');
    assert.equal(tweetAccount.content, 'Hummus, am I right?');
    assert.ok(tweetAccount.timestamp);
});
```

Несколько замечаний:

- Первое утверждение гарантирует, что автор вновь созданной учетной записи соответствует открытому ключу нашего кошелька. Поскольку и `tweetAccount.author`, и `program.provider.wallet.publicKey` являются объектами открытого ключа, они будут иметь разные ссылки, и поэтому мы не можем просто сравнивать их как объекты. Вместо этого мы конвертируем их в формат Base 58 с помощью метода toBase58, чтобы они были равны тогда и только тогда, когда совпадают эти две строки. Обратите внимание, что адрес кошелька, который вы даете людям в Солане, представляет собой кодировку Base 58 вашего открытого ключа. Мой: `B1AfN7AgpMyctfFbjmvRAvE1yziZFDb9XCwydBjJwtRN`.
- Следующие два утверждения гарантируют, что и «тема», и «содержание» нашего твита были сохранены правильно.
- Наконец, последнее утверждение гарантирует, что у нашего твита есть непустая временная метка. Мы также могли бы проверить, соответствует ли отметка времени текущему времени, но это немного сложно сделать, не допуская частых сбоев теста из-за того, что время не совпадает с секундой. Итак, давайте сделаем тест простым и убедимся, что у нас есть метка времени.

Все сделано! Теперь мы можем запустить `anchor test` и увидеть, что наш тест и все его утверждения пройдены!

Должны ли мы написать еще несколько?

## Отправка твита без темы

Теперь, когда мы поняли, как писать тесты для нашей программы, мы можем просто скопировать/вставить наш первый тест и настроить несколько вещей для тестирования различных сценариев.

В этом случае я бы хотел, чтобы мы добавили сценарий для твитов, у которых нет тем, так как наш интерфейс позволит пользователям отправлять твиты без них.

Чтобы протестировать этот сценарий, скопируйте/вставьте наш первый тест, переименуйте его в «можно отправить новый твит без темы» и замените тему «веганство» пустой строкой «». Обратите внимание, что я также заменил содержимое на «gm».

```js
it('can send a new tweet without a topic', async () => {
    // Call the "SendTweet" instruction.
    const tweet = anchor.web3.Keypair.generate();
    await program.rpc.sendTweet('', 'gm', {
        accounts: {
            tweet: tweet.publicKey,
            author: program.provider.wallet.publicKey,
            systemProgram: anchor.web3.SystemProgram.programId,
        },
        signers: [tweet],
    });

    // Fetch the account details of the created tweet.
    const tweetAccount = await program.account.tweet.fetch(tweet.publicKey);

    // Ensure it has the right data.
    assert.equal(tweetAccount.author.toBase58(), program.provider.wallet.publicKey.toBase58());
    assert.equal(tweetAccount.topic, '');
    assert.equal(tweetAccount.content, 'gm');
    assert.ok(tweetAccount.timestamp);
});
```

И вуаля! Теперь у нас есть два теста для тестирования разных сценариев. На следующем.

## Отправка твита от другого автора

Теперь давайте проверим немного более сложный сценарий. До сих пор мы использовали наш кошелек в качестве автора твита, который мы отправляем, но технически мы должны иметь возможность публиковать твиты от имени любого автора, если мы можем доказать, что мы владеем его публичным адресом, подписав транзакцию.

Итак, давайте сделаем это. Опять же, начиная с копирования/вставки нашего первого теста, мы сделаем следующее:

- Создайте новую пару ключей и назначьте ее в переменной `otherUser`.
- Укажите открытый ключ `otherUser` в качестве учетной записи `author`.
- Добавьте пару ключей `otherUser` в массив `signers`. Обратите внимание, что Anchor будет автоматически подписывать транзакции только с использованием нашего кошелька, поэтому нам нужно явно подписать здесь.
- Убедитесь, что `author` извлеченного `tweetAccount` совпадает с открытым ключом нашего «otherUser».

```js
it('can send a new tweet from a different author', async () => {
    // Generate another user and airdrop them some SOL.
    const otherUser = anchor.web3.Keypair.generate();

    // Call the "SendTweet" instruction on behalf of this other user.
    const tweet = anchor.web3.Keypair.generate();
    await program.rpc.sendTweet('veganism', 'Yay Tofu!', {
        accounts: {
            tweet: tweet.publicKey,
            author: otherUser.publicKey,
            systemProgram: anchor.web3.SystemProgram.programId,
        },
        signers: [otherUser, tweet],
    });

    // Fetch the account details of the created tweet.
    const tweetAccount = await program.account.tweet.fetch(tweet.publicKey);

    // Ensure it has the right data.
    assert.equal(tweetAccount.author.toBase58(), otherUser.publicKey.toBase58());
    assert.equal(tweetAccount.topic, 'veganism');
    assert.equal(tweetAccount.content, 'Yay Tofu!');
    assert.ok(tweetAccount.timestamp);
});
```

К сожалению, этот тест не пройдет.

Если мы попытаемся запустить его, мы получим следующую ошибку — вам нужно внимательно прочитать журналы ошибок, чтобы найти ее.

```
Transfer: insufficient lamports 0, need 10467840
```

Хорошо, что происходит и что такое лампорт?

**Lamport — это наименьший десятичный знак собственного токена Solana** `SOL`. Токен Solana имеет ровно 9 знаков после запятой, что означает, что **1 SOL равен 1 000 000 000 лампортов**.

Лампорты часто используются в разработке Solana, поскольку они позволяют нам совершать микроплатежи в виде дробных SOL, в то же время обрабатывая суммы с помощью целых чисел. Они названы в честь самого большого технического влияния Соланы, [Лесли Лэмпорт](https://en.wikipedia.org/wiki/Leslie_Lamport).

Итак, ошибка говорит нам, что у нас недостаточно средств. Точнее, нам нужно `10467840 лампортов` или `0,01046784 SOL`.

Оказывается, именно столько денег нам нужно, чтобы наш аккаунт в Твиттере был освобожден от арендной платы. Когда мы определяли размер нашей учетной записи «Tweet» в эпизоде 3, мы пришли к требуемому объему хранилища 1376 байт. Давайте узнаем, сколько денег нам нужно, чтобы счет в 1376 байт был освобожден от арендной платы.

```bash
solana rent 1376
# Outputs:
# Rent per byte-year: 0.00000348 SOL
# Rent per epoch: 0.000028659 SOL
# Rent-exempt minimum: 0.01046784 SOL       <- Aha!
```

Хорошо, теперь мы понимаем, что происходит. Транзакция не удалась, потому что мы используем `otherUser` в качестве `автора` твита, который должен платить освобожденные от арендной платы деньги на счет `Tweet`, но у этого `otherUser` вообще нет денег!

Чтобы исправить это, нам нужно отправить немного денег другому пользователю, прежде чем мы сможем вызвать нашу инструкцию `SendTweet`.

Мы можем сделать это, используя объект подключения, который доступен в разделе `program.provider.connection`. Этот объект содержит асинхронный метод requestAirdrop, который принимает открытый ключ и количество лампорта. Давайте дадим этому пользователю 1 SOL — или 1 миллиард лампортов.

```js
await program.provider.connection.requestAirdrop(otherUser.publicKey, 1000000000);
```

Теперь этот метод API немного особенный, потому что у пользователя все равно не будет денег после вызова `await`. Это потому, что он только «запрашивает» аирдроп. Чтобы убедиться, что мы достаточно долго ждем, пока деньги окажутся на счете `otherUser`, нам нужно дождаться подтверждения транзакции.

К счастью для нас, в объекте подключения есть метод `confirmTransaction`, который делает именно это. Он принимает подпись транзакции, которая возвращается предыдущим вызовом `requestAirdrop`.

```js
  const signature = await program.provider.connection.requestAirdrop(otherUser.publicKey, 1000000000);
  await program.provider.connection.confirmTransaction(signature);
```

Давайте добавим этот код в наш тест и запустим `anchor test`, чтобы увидеть, проходит ли он.

```js
it('can send a new tweet from a different author', async () => {
    // Generate another user and airdrop them some SOL.
    const otherUser = anchor.web3.Keypair.generate();
    const signature = await program.provider.connection.requestAirdrop(otherUser.publicKey, 1000000000);
    await program.provider.connection.confirmTransaction(signature);

    // Call the "SendTweet" instruction on behalf of this other user.
    const tweet = anchor.web3.Keypair.generate();
    await program.rpc.sendTweet('veganism', 'Yay Tofu!', {
        accounts: {
            tweet: tweet.publicKey,
            author: otherUser.publicKey,
            systemProgram: anchor.web3.SystemProgram.programId,
        },
        signers: [otherUser, tweet],
    });

    // Fetch the account details of the created tweet.
    const tweetAccount = await program.account.tweet.fetch(tweet.publicKey);

    // Ensure it has the right data.
    assert.equal(tweetAccount.author.toBase58(), otherUser.publicKey.toBase58());
    assert.equal(tweetAccount.topic, 'veganism');
    assert.equal(tweetAccount.content, 'Yay Tofu!');
    assert.ok(tweetAccount.timestamp);
});
```

Да! Все тесты проходят!

```bash
solana-twitter
  ✔ can send a new tweet (277ms)
  ✔ can send a new tweet without a topic (517ms)
  ✔ can send a new tweet from a different author (1055ms)


3 passing (2s)
```

Прежде чем мы перейдем к нашему следующему тесту, вы можете задаться вопросом: почему нам не нужно было делать сброс денег на наш кошелек в предыдущих тестах?

Это потому, что **каждый раз, когда создается новый локальный реестр, он автоматически переводит 500 миллионов SOL на ваш локальный кошелек**, который по умолчанию находится по адресу `~/.config/solana/id.json`.

Если вы помните, запуск `anchor test` запускает для нас новую локальную бухгалтерскую книгу и, следовательно, каждый раз автоматически переводит деньги в наш кошелек. Вот почему нам никогда не нужно переводить деньги в наш локальный кошелек перед каждым тестом.

Хорошо, давайте перейдем к нашим двум последним тестам для этого эпизода.

## Тестирование наших индивидуальных охранников

До сих пор мы тестировали только «счастливые пути» — то есть разрешенные сценарии.

В предыдущем эпизоде мы создали два настраиваемых сторожа в нашей инструкции `SendTweet`, чтобы темы и содержимое не могли содержать более 50 и 280 символов соответственно. Поэтому было бы неплохо добавить тест для каждого из этих охранников, чтобы убедиться, что они работают правильно.

Эти тесты будут немного отличаться от предыдущих, потому что мы будем утверждать, что возникает ошибка.

Давайте начнем с темы и создадим новый тест под названием «невозможно предоставить тему с более чем 50 символами». На этот раз мы скопируем только первую часть первого теста, который мы создали.

```js
it('cannot provide a topic with more than 50 characters', async () => {
    const tweet = anchor.web3.Keypair.generate();
    await program.rpc.sendTweet('veganism', 'Hummus, am I right?', {
        accounts: {
            tweet: tweet.publicKey,
            author: program.provider.wallet.publicKey,
            systemProgram: anchor.web3.SystemProgram.programId,
        },
        signers: [tweet],
    });
});
```

Теперь нам нужно заменить тему «веганство» на все, что содержит более 50 символов. Чтобы сделать это очевидным, мы создадим тему, состоящую только из одного символа, повторяющегося 51 раз, с помощью [функции `repeat` JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference /Global_Objects/String/repeat).

```js
it('cannot provide a topic with more than 50 characters', async () => {
    const tweet = anchor.web3.Keypair.generate();
    const topicWith51Chars = 'x'.repeat(51);
    await program.rpc.sendTweet(topicWith51Chars, 'Hummus, am I right?', {
        // ...
    });
});
```

Хорошо, теперь — если наша защита работает правильно — этот вызов должен выдать ошибку. Но как мы утверждаем для этого? Есть много способов сделать это, в том числе [метод `assert.throws` ](https://nodejs.org/api/assert.html#assertthrowsfn-error-message), который принимает обратный вызов и ошибку, которая должна соответствовать выброшена ошибка. Тем не менее, я предпочитаю использовать блок try/catch, чтобы мы могли делать дальнейшие утверждения об объекте ошибки.

Идея такова:

- Мы оборачиваем наш код в блок try.
- Мы отлавливаем (`catch`) любую ошибку и делаем дополнительные утверждения об ошибке перед возвратом, поэтому тест здесь останавливается.
- После блока `try/catch` мы вызываем [`assert.fail`](https://nodejs.org/api/assert.html#assertfailmessage), так как мы должны были вернуться в блоке `catch`.

В итоге получаем следующий тест.

```js
it('cannot provide a topic with more than 50 characters', async () => {
    try {
        const tweet = anchor.web3.Keypair.generate();
        const topicWith51Chars = 'x'.repeat(51);
        await program.rpc.sendTweet(topicWith51Chars, 'Hummus, am I right?', {
            accounts: {
                tweet: tweet.publicKey,
                author: program.provider.wallet.publicKey,
                systemProgram: anchor.web3.SystemProgram.programId,
            },
            signers: [tweet],
        });
    } catch (error) {
        assert.equal(error.msg, 'The provided topic should be 50 characters long maximum.');
        return;
    }

    assert.fail('The instruction should have failed with a 51-character topic.');
});
```

И точно так же у нас есть пройденный тест, который гарантирует, что наша специальная защита темы работает правильно!

Мы можем сделать то же самое для нашей пользовательской защиты «контента», скопировав/вставив этот тест, используя содержимое из 281 символа и настроив некоторые другие переменные и тексты. В итоге получаем следующий тест.

```js
it('cannot provide a content with more than 280 characters', async () => {
    try {
        const tweet = anchor.web3.Keypair.generate();
        const contentWith281Chars = 'x'.repeat(281);
        await program.rpc.sendTweet('veganism', contentWith281Chars, {
            accounts: {
                tweet: tweet.publicKey,
                author: program.provider.wallet.publicKey,
                systemProgram: anchor.web3.SystemProgram.programId,
            },
            signers: [tweet],
        });
    } catch (error) {
        assert.equal(error.msg, 'The provided content should be 280 characters long maximum.');
        return;
    }

    assert.fail('The instruction should have failed with a 281-character content.');
});
```

Круто, теперь оба наших кастомных щитка протестированы!

Единственная неприятность с вызовами инструкций тестирования, которые выдают ошибки, заключается в том, что эти ошибки неизбежно становятся видимыми в наших журналах тестирования и добавляют много шума в терминал. Я не видел никакого способа обойти это без переопределения некоторых `консольных` методов, поэтому, если у кого-то есть изящное решение этой проблемы, не стесняйтесь поделиться им, и я соответствующим образом обновлю эту статью.

## Вывод

Поздравляю с завершением 5 серии! Мы не только реализовали пять тестов для нашей программы Solana, но и научились создавать клиенты JavaScript, взаимодействующие с нашей программой.

Знание этих концепций — `Program`, `Provider`, `Wallet` и т. д. — очень поможет при реализации нашего интерфейса JavaScript.

В следующем эпизоде мы добавим три финальных теста, которые будут одновременно получать несколько учетных записей `Tweet`. Это позволит нам понять, как мы можем получить и отобразить все твиты и отфильтровать их по теме или по автору. Увидимся там! 👋

[Просмотреть эпизод 5 на GitHub](https://github.com/lorisleiva/solana-twitter/tree/episode-5)

[Сравните с Эпизодом 4](https://github.com/lorisleiva/solana-twitter/compare/episode-4...episode-5)
