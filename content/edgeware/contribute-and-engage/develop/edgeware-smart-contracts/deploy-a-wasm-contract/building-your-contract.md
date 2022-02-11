+++
title = "Создание контракта"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Запустите следующую команду, чтобы скомпилировать смарт-контракт:

```
cargo +nightly contract build
```

Эта специальная команда превратит ваш ink! проект в двоичный файл Wasm, который вы можете развернуть в своей цепочке. Если все пойдет хорошо, вы должны увидеть целевую папку, содержащую этот файл .wasm.

```
  target
  └── flipper.wasm
```

## Метаданные контракта

Выполнив следующую команду, мы сгенерируем метаданные контракта \(он же ABI контракта\):

```
cargo +nightly contract generate-metadata
```

У вас должен быть новый файл JSON \(`metadata.json`\) в том же целевом каталоге:

```
  target
  ├── flipper.wasm
  └── metadata.json
```

Давайте посмотрим на структуру внутри:

```
  {
  "metadataVersion": "0.1.0",
  "source": {
    "hash": "0x11ba777b3457bf64cb99421786c90d421afde1c56579aa3dae336e58ccc8f783",
    "language": "ink! 3.0.0-rc1",
    "compiler": "rustc 1.47.0-nightly"
  },
  "contract": {
    "name": "flipper",
    "version": "0.1.0",
    "authors": [
      "[your_name] "
    ]
  },
  "spec": {
    "constructors": [
      {
        "args": [
          {
            "name": "init_value",
            "type": {
              "displayName": [
                "bool"
              ],
              "type": 1
            }
          }
        ],
        "docs": [
          " Constructor that initializes the `bool` value to the given `init_value`."
        ],
        "name": [
          "new"
        ],
        "selector": "0xd183512b"
      },
      {
        "args": [],
        "docs": [
          " Constructor that initializes the `bool` value to `false`.",
          "",
          " Constructors can delegate to other constructors."
        ],
        "name": [
          "default"
        ],
        "selector": "0x6a3712e2"
      }
    ],
    "docs": [],
    "events": [],
    "messages": [
      {
        "args": [],
        "docs": [
          " A message that can be called on instantiated contracts.",
          " This one flips the value of the stored `bool` from `true`",
          " to `false` and vice versa."
        ],
        "mutates": true,
        "name": [
          "flip"
        ],
        "payable": false,
        "returnType": null,
        "selector": "0xc096a5f3"
      },
      {
        "args": [],
        "docs": [
          " Simply returns the current value of our `bool`."
        ],
        "mutates": false,
        "name": [
          "get"
        ],
        "payable": false,
        "returnType": {
          "displayName": [
            "bool"
          ],
          "type": 1
        },
        "selector": "0x1e5ca456"
      }
    ]
  },
  "storage": {
    "struct": {
      "fields": [
        {
          "layout": {
            "cell": {
              "key": "0x0000000000000000000000000000000000000000000000000000000000000000",
              "ty": 1
            }
          },
          "name": "value"
        }
      ]
    }
  },
  "types": [
    {
      "def": {
        "primitive": "bool"
      }
    }
  ]
}
```

Как видите, этот файл описывает все интерфейсы, которые можно использовать для взаимодействия с вашим контрактом.

- Реестр предоставляет **строки** и пользовательские **типы**, используемые в остальной части JSON.
- Storage определяет все элементы **хранилища**, управляемые вашим контрактом, и то, как в конечном итоге получить к ним доступ.
- Контракт хранит информацию о вызываемых функциях, таких как **конструкторы** и **сообщения**, которые пользователь может вызывать для взаимодействия с вашим контрактом. Он также содержит полезную информацию, такую как **события**, создаваемые контрактом, или любые **документы.**

Если вы внимательно посмотрите на конструкторы и сообщения, вы также заметите `селектор`, который представляет собой 4-байтовый хэш имени функции и используется для маршрутизации ваших контрактных вызовов к правильным функциям.

**Выучить больше**

ink! обеспечивает встроенную защиту от переполнения, включенную в нашем файле `Cargo.toml`. Рекомендуется оставить его включенным, чтобы предотвратить возможные ошибки переполнения в вашем контракте.

```
[profile.release]
panic = "abort"           <-- Panics shall be treated as aborts: reduces binary size
lto = true                <-- enable link-time-optimization: more efficient codegen
opt-level = "z"           <-- Optimize for small binary output
overflow-checks = true    <-- Arithmetic overflow protection
```
