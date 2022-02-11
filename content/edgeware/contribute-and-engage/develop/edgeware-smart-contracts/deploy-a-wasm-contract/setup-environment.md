+++
title = "Настройка среды"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Чтобы следовать этому руководству, вам нужно будет настроить некоторые вещи на вашем компьютере.

Требования к субстрату

Для начала вам нужно убедиться, что ваш компьютер настроен для сборки Substrate. Если вы используете **OSX** или самые популярные дистрибутивы **Linux**, вы можете сделать это, запустив:

```bash
curl https://sh.rustup.rs -sSf | sh -s -- -y
```

```
rustup target add wasm32-unknown-unknown --toolchain stable
rustup component add rust-src --toolchain nightly
rustup toolchain install nightly-2020-06-01
rustup target add wasm32-unknown-unknown --toolchain nightly-2020-06-01
```

Последний инструмент, который мы будем устанавливать, — это чернила! Утилита командной строки, которая упростит настройку проектов смарт-контрактов Substrate.

Вы можете установить утилиту с помощью Cargo:

```
cargo install --git https://github.com/hicommonwealth/cargo-contract cargo-contract --force
```

Затем вы можете использовать `cargo Contract --help`, чтобы начать изучение доступных вам команд.

>**Примечание:** Чернила! CLI находится в стадии интенсивной разработки, и некоторые из его команд еще не реализованы!
