+++
title = "Rust API"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Ящики Solana Rust [опубликованы на crates.io](crates.io) и их можно найти [на docs.rs с префиксом «solana-»](docs.rs).

[crates.io]: https://crates.io/search?q=solana-
[docs.rs]: https://docs.rs/releases/search?query=solana-

Некоторые важные ящики:

- [`solana-program`] &mdash; Импортируется программами, работающими на Solana, компилируется в BPF. Этот крейт содержит множество основных типов данных и повторно экспортируется из [`solana-sdk`], который нельзя импортировать из программы Solana.

- [`solana-sdk`] &mdash; Базовый SDK вне сети, он реэкспортирует [`solana-program`] и добавляет дополнительные API поверх этого. Большинство программ Solana, которые не работают в сети, будут импортировать это.

- [`solana-client`] &mdash; Для взаимодействия с узлом Solana через [JSON RPC API](jsonrpc-api).

- [`solana-cli-config`] &mdash; Загрузка и сохранение файла конфигурации Solana CLI.

- [`solana-clap-utils`] &mdash; Подпрограммы для настройки CLI с использованием [`clap`], как это используется в основном CLI Solana. Включает функции для загрузки всех типов подписантов, поддерживаемых интерфейсом командной строки.

[`solana-program`]: https://docs.rs/solana-program
[`solana-sdk`]: https://docs.rs/solana-sdk
[`солана-клиент`]: https://docs.rs/solana-client
[`solana-cli-config`]: https://docs.rs/solana-cli-config
[`solana-clap-utils`]: https://docs.rs/solana-clap-utils
[`хлопок`]: https://docs.rs/clap
