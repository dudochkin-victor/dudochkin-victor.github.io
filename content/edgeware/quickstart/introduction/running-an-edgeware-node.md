+++
title = "Запуск узла Edgeware"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Мы хотим предоставить вам быструю настройку. Если у вас есть Docker, вы можете запустить узел разработки Edgeware за несколько секунд:

> **Примечание** Если у вас не установлен [Docker, вы можете быстро установить его отсюда](https://docs.docker.com/get-docker/)

```bash
git clone https://github.com/yangwao/substrate_playground; cd substrate_playground;
docker-compose up
```

> **Примечание.** Если вы запускали эту команду в прошлом, вы, вероятно, захотите очистить свое сетевое хранилище, чтобы выполнить это руководство с чистого листа. Вы можете легко сделать это, используя `docker-compose rm`, чтобы удалить существующий том докера.

![An image of the terminal starting a Edgeware node](../../.gitbook/assets/start-edgeware-node.png)

Вы должны начать видеть блоки, создаваемые вашим узлом в вашем терминале.

Вы можете взаимодействовать с вашим узлом с помощью пользовательского интерфейса Polkadot:

[https://polkadot.js.org/apps/](https://polkadot.js.org/apps/)

> **Примечание.** Вам потребуется браузер на основе Chromium \(Google Chrome\), чтобы этот сайт взаимодействовал с вашим локальным узлом. Пользовательский интерфейс Polkadot размещен на защищенном сервере, а ваш локальный узел — нет, что может вызвать проблемы совместимости в Firefox. Другой вариант — [клонировать и запустить пользовательский интерфейс Polkadot локально] (https://github.com/polkadot-js/apps).

Чтобы направить пользовательский интерфейс на ваш локальный узел, вам необходимо настроить **Settings**. Просто выберите «Локальный узел \(127.0.0.1:9944\)» в раскрывающемся списке конечных точек:

```
Click on the chain name > Development node/endpoint to connect to > Local Node (127.0.0.1:9944) > Switch
```

![An image of the settings in Polkadot-JS Apps UI](../../.gitbook/assets/switch-to-localhost.png)

Если вы перейдете на вкладку **Explorer** пользовательского интерфейса, вы также должны увидеть создаваемые блоки!

![An image of the Polkadot Apps UI](../../.gitbook/assets/block-explorer-edgeware.png)
