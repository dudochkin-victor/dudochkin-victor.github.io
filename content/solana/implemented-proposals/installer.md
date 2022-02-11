+++
title = "Cluster Software Installation and Updates"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

В настоящее время пользователям необходимо самостоятельно собирать программное обеспечение кластера solana из репозитория git и обновлять его вручную, что чревато ошибками и неудобно.

В этом документе предлагается простая в использовании программа установки и обновления программного обеспечения, которую можно использовать для развертывания готовых двоичных файлов для поддерживаемых платформ. Пользователи могут выбрать использование двоичных файлов, предоставленных Solana или любым другим сторонним поставщиком. Развертывание обновлений управляется с помощью встроенной программы манифеста обновлений.

## Мотивирующие примеры

### Получите и запустите готовый установщик с помощью загрузочного скрипта curl/shell

Самый простой способ установки для поддерживаемых платформ:

```bash
$ curl -sSf https://raw.githubusercontent.com/solana-labs/solana/v1.0.0/install/solana-install-init.sh | sh
```

Этот скрипт проверит github на наличие последней версии с тегами, загрузит и запустит оттуда двоичный файл solana-install-init.

Если во время установки необходимо указать дополнительные аргументы, используется следующий синтаксис оболочки:

```bash
$ init_args=.... # arguments for `solana-install-init ...`
$ curl -sSf https://raw.githubusercontent.com/solana-labs/solana/v1.0.0/install/solana-install-init.sh | sh -s - ${init_args}
```

### Получите и запустите предварительно созданный установщик из выпуска Github.

Имея общеизвестный URL-адрес выпуска, можно получить готовый двоичный файл для поддерживаемых платформ:

```bash
$ curl -o solana-install-init https://github.com/solana-labs/solana/releases/download/v1.0.0/solana-install-init-x86_64-apple-darwin
$ chmod +x ./solana-install-init
$ ./solana-install-init --help
```

### Соберите и запустите установщик из исходников

Если предварительно собранный двоичный файл недоступен для данной платформы, всегда можно собрать установщик из исходного кода:

```bash
$ git clone https://github.com/solana-labs/solana.git
$ cd solana/install
$ cargo run -- --help
```

### Разверните новое обновление в кластере

Имея tar-архив релиза solana \ (созданный с помощью ci/publish-tarball.sh`\), который уже был загружен по общедоступному URL-адресу, следующие команды развернут обновление:

```bash
$ solana-keygen new -o update-manifest.json  # <-- only generated once, the public key is shared with users
$ solana-install deploy http://example.com/path/to/solana-release.tar.bz2 update-manifest.json
```

### Запустите узел проверки, который автоматически обновляется

```bash
$ solana-install init --pubkey 92DMonmBYXwEMHJ99c9ceRSpAmk9v6i3RdvDdXaVcrfj  # <-- pubkey is obtained from whoever is deploying the updates
$ export PATH=~/.local/share/solana-install/bin:$PATH
$ solana-keygen ...  # <-- runs the latest solana-keygen
$ solana-install run solana-validator ...  # <-- runs a validator, restarting it as necesary when an update is applied
```

## Манифест обновления в сети

Манифест обновления используется для объявления о развертывании архивов новых выпусков в кластере solana. Манифест обновления сохраняется с помощью программы `config`, и каждая учетная запись манифеста обновления описывает логический канал обновления для данной целевой тройки \(например, `x86_64-apple-darwin`\). Открытый ключ учетной записи известен между организацией, развертывающей новые обновления, и пользователями, использующими эти обновления.

Сам архив обновлений размещен в другом месте, вне сети, и его можно получить по указанному `download_url`.

```
use solana_sdk::signature::Signature;

/// Information required to download and apply a given update
pub struct UpdateManifest {
    pub timestamp_secs: u64, // When the release was deployed in seconds since UNIX EPOCH
    pub download_url: String, // Download URL to the release tar.bz2
    pub download_sha256: String, // SHA256 digest of the release tar.bz2 file
}

/// Data of an Update Manifest program Account.
#[derive(Serialize, Deserialize, Default, Debug, PartialEq)]
pub struct SignedUpdateManifest {
    pub manifest: UpdateManifest,
    pub manifest_signature: Signature,
}
```

Обратите внимание, что само поле `manifest` содержит соответствующую подпись \(`manifest_signature`\) для защиты от атак "человек посередине" между инструментом `solana-install` и RPC API кластера solana.

Чтобы защититься от атак с откатом, solana-install откажется устанавливать обновление с более старым значением timestamp_secs, чем то, что установлено в данный момент.

## Выпуск содержимого архива

Ожидается, что архив выпуска будет представлять собой файл tar, сжатый с помощью bzip2, со следующей внутренней структурой:

- `/version.yml` - простой файл YAML, содержащий поле `"target"` - целевой кортеж. Любые дополнительные поля игнорируются.

- `/bin/` -- каталог, содержащий доступные программы в релизе.
  
  solana-install создаст символическую ссылку на этот каталог на `~/.local/share/solana-install/bin` для использования переменной окружения PATH.

- `...` -- разрешены любые дополнительные файлы и каталоги

## Инструмент установки solana

Инструмент solana-install используется пользователем для установки и обновления программного обеспечения своего кластера.

Он управляет следующими файлами и каталогами в домашнем каталоге пользователя:

- `~/.config/solana/install/config.yml` - конфигурация пользователя и информация о текущей установленной версии программного обеспечения
- `~/.local/share/solana/install/bin` - символическая ссылка на текущий релиз. например, `~/.local/share/solana-update/<update-pubkey>-<manifest_signature>/bin`
- `~/.local/share/solana/install/releases/<download_sha256>/` - содержимое релиза

### Интерфейс командной строки

```
solana-install 0.16.0
The solana cluster software installer

USAGE:
    solana-install [OPTIONS] <SUBCOMMAND>

FLAGS:
    -h, --help       Prints help information
    -V, --version    Prints version information

OPTIONS:
    -c, --config <PATH>    Configuration file to use [default: .../Library/Preferences/solana/install.yml]

SUBCOMMANDS:
    deploy    deploys a new update
    help      Prints this message or the help of the given subcommand(s)
    info      displays information about the current installation
    init      initializes a new installation
    run       Runs a program while periodically checking and applying software updates
    update    checks for an update, and if available downloads and applies it
```

```
solana-install-init
initializes a new installation

USAGE:
    solana-install init [OPTIONS]

FLAGS:
    -h, --help    Prints help information

OPTIONS:
    -d, --data_dir <PATH>    Directory to store install data [default: .../Library/Application Support/solana]
    -u, --url <URL>          JSON RPC URL for the solana cluster [default: http://api.devnet.solana.com]
    -p, --pubkey <PUBKEY>    Public key of the update manifest [default: 9XX329sPuskWhH4DQh6k16c87dHKhXLBZTL3Gxmve8Gp]
```

```
solana-install info
displays information about the current installation

USAGE:
    solana-install info [FLAGS]

FLAGS:
    -h, --help     Prints help information
    -l, --local    only display local information, don't check the cluster for new updates
```

```
solana-install deploy
deploys a new update

USAGE:
    solana-install deploy <download_url> <update_manifest_keypair>

FLAGS:
    -h, --help    Prints help information

ARGS:
    <download_url>               URL to the solana release archive
    <update_manifest_keypair>    Keypair file for the update manifest (/path/to/keypair.json)
```

```
solana-install update
checks for an update, and if available downloads and applies it

USAGE:
    solana-install update

FLAGS:
    -h, --help    Prints help information
```

```
solana-install run
Runs a program while periodically checking and applying software updates

USAGE:
    solana-install run <program_name> [program_arguments]...

FLAGS:
    -h, --help    Prints help information

ARGS:
    <program_name>            program to run
    <program_arguments>...    arguments to supply to the program

The program will be restarted upon a successful software update
```
