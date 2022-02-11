+++
title = "Часто используемые команды"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

## Сгенерируйте ключи с помощью недавно добавленного сетевого идентификатора Edgeware

```
subkey -n edgeware generate
```

## Запуск узла

Чтобы запустить узел Edgeware и подключиться к тестовой сети 3.0.5 \(Это может быть устаревшим!, запустите:

>Показанная здесь тестовая сеть может быть устаревшей. Проверьте версию и имя JSON.

```
./target/release/edgeware --chain=chains/testnet-3.0.5.json --name <INSERT_NAME>
```

Чтобы запустить цепочку локально в целях разработки:

```
./target/release/edgeware --chain=local --alice --validator
```

Чтобы разрешить подключение приложений в вашем браузере, а также всех остальных в вашей локальной сети, добавьте флаг `--rpc-cors=all`.

Чтобы разрешить подключение приложений в вашем браузере, а также всех остальных в вашей локальной сети, добавьте флаг `--rpc-cors=all`.

Чтобы заставить ваш локальный сервер создавать новые блоки, даже если он не в сети, добавьте флаг `--force-authoring`.

## Генерация пары ключей

Чтобы создать пару ключей, установите подраздел с помощью `cargo install --force --git https://github.com/paritytech/substrate subkey`.

Затем выполните следующее:

```
subkey generate
```

Чтобы создать пару ключей ED25519, выполните следующее:

```
subkey -e generate
```

Чтобы создать производные пары ключей, используйте мнемонику, сгенерированную вышеописанным методом, и запустите \ (используйте `-e` для ED25519\):

```
subkey inspect "<mnemonic>"//<derive_path>
```

```
subkey inspect "<mnemonic>"//<derive_path>
```

Например:

```
subkey inspect "west paper guide park design weekend radar chaos space giggle execute canoe"//edgewarerocks
```

```
subkey inspect "west paper guide park design weekend radar chaos space giggle execute canoe"//edgewarerocks
```

## Публичные узлы

Commonwealth Labs поддерживает несколько общедоступных узлов в основной и тестовой сетях. Пожалуйста, обратитесь к следующей странице для последних контактов узла.

**Синтаксис:**

* wss://testnet1.edgewa.re
  
  или \(если wss: терпит неудачу\)

* ws://testnet1.edgewa.re:9944
