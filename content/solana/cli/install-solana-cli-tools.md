+++
title = "Установка набора инструментов Solana"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Существует несколько способов установки инструментов Solana на ваш компьютер в зависимости от вашего предпочтительного рабочего процесса:

- Использовать инструмент установки Solana (самый простой вариант)
- Загрузить готовые двоичные файлы
- Сборка из исходного кода

## Используйте инструмент установки Solana

### MacOS и Linux

- Откройте ваше любимое приложение терминала

- Установите выпуск Solana [LATEST_SOLANA_RELEASE_VERSION](https://github.com/solana-labs/solana/releases/tag/LATEST_SOLANA_RELEASE_VERSION) на свой компьютер, запустив:

```bash
sh -c "$(curl -sSfL https://release.solana.com/LATEST_SOLANA_RELEASE_VERSION/install)"
```

- Вы можете заменить `LATEST_SOLANA_RELEASE_VERSION` тегом выпуска, соответствующим версии программного обеспечения желаемого выпуска, или использовать одно из трех символических имен канала: `stable`, `beta` или `edge`.

- Следующий вывод указывает на успешное обновление:

```
downloading LATEST_SOLANA_RELEASE_VERSION installer
Configuration: /home/solana/.config/solana/install/config.yml
Active release directory: /home/solana/.local/share/solana/install/active_release
* Release version: LATEST_SOLANA_RELEASE_VERSION
* Release URL: https://github.com/solana-labs/solana/releases/download/LATEST_SOLANA_RELEASE_VERSION/solana-release-x86_64-unknown-linux-gnu.tar.bz2
Update successful
```

- В зависимости от вашей системы сообщение об окончании программы установки может предложить вам

```bash
Please update your PATH environment variable to include the solana programs:
```

- Если вы получили указанное выше сообщение, скопируйте и вставьте под ним рекомендуемую команду, чтобы обновить `PATH`.
- Подтвердите, что у вас установлена нужная версия solana, запустив:

```bash
solana --version
```

- После успешной установки `solana-install update` можно использовать для простого обновления программного обеспечения Solana до более новой версии в любое время.

+++

### Окна

- Откройте командную строку (`cmd.exe`) от имени администратора.
  
  - Найдите командную строку в строке поиска Windows. Когда появится приложение командной строки, щелкните правой кнопкой мыши и выберите «Открыть от имени администратора». Если появится всплывающее окно с вопросом «Вы хотите разрешить этому приложению вносить изменения в ваше устройство?», нажмите «Да».

- Скопируйте и вставьте следующую команду, затем нажмите Enter, чтобы загрузить установщик Solana во временный каталог:

```bash
curl https://release.solana.com/LATEST_SOLANA_RELEASE_VERSION/solana-install-init-x86_64-pc-windows-msvc.exe --output C:\solana-install-tmp\solana-install-init.exe --create-dirs
```

- Скопируйте и вставьте следующую команду, затем нажмите Enter, чтобы установить последнюю версию Solana. Если вы видите всплывающее окно безопасности вашей системы, выберите, чтобы разрешить запуск программы.

```bash
C:\solana-install-tmp\solana-install-init.exe LATEST_SOLANA_RELEASE_VERSION
```

- После завершения установки нажмите Enter.

- Закройте окно командной строки и снова откройте новое окно командной строки как обычный пользователь.
  
  - Найдите «Командная строка» в строке поиска, затем щелкните левой кнопкой мыши значок приложения командной строки, не нужно запускать от имени администратора)

- Подтвердите, что у вас установлена нужная версия solana, введя:

```bash
solana --version
```

- После успешной установки `solana-install update` можно использовать для простого обновления программного обеспечения Solana до более новой версии в любое время.

## Скачать готовые двоичные файлы

Если вы предпочитаете не использовать `solana-install` для управления установкой, вы можете вручную загрузить и установить двоичные файлы.

### Линукс

Загрузите двоичные файлы, перейдя по адресу [https://github.com/solana-labs/solana/releases/latest](https://github.com/solana-labs/solana/releases/latest), загрузите **solana- release-x86_64-unknown-linux-msvc.tar.bz2**, затем распакуйте архив:

```bash
tar jxf solana-release-x86_64-unknown-linux-gnu.tar.bz2
cd solana-release/
export PATH=$PWD/bin:$PATH
```

### MacOS

Загрузите двоичные файлы, перейдя по адресу [https://github.com/solana-labs/solana/releases/latest](https://github.com/solana-labs/solana/releases/latest), загрузите **solana- release-x86_64-apple-darwin.tar.bz2**, затем распакуйте архив:

```bash
tar jxf solana-release-x86_64-apple-darwin.tar.bz2
cd solana-release/
export PATH=$PWD/bin:$PATH
```

### Окна

- Загрузите двоичные файлы, перейдя по адресу [https://github.com/solana-labs/solana/releases/latest](https://github.com/solana-labs/solana/releases/latest), загрузите **solana. -release-x86_64-pc-windows-msvc.tar.bz2**, затем распакуйте архив с помощью WinZip или аналогичного.

- Откройте командную строку и перейдите в каталог, в который вы распаковали двоичные файлы, и запустите:

```bash
cd solana-release/
set PATH=%cd%/bin;%PATH%
```

## Сборка из исходного кода

Если вы не можете использовать готовые двоичные файлы или предпочитаете собирать их самостоятельно из исходного кода, перейдите по адресу [https://github.com/solana-labs/solana/releases/latest](https://github.com/solana- labs/solana/releases/latest) и загрузите архив **Исходный код**. Извлеките код и создайте двоичные файлы с помощью:

```bash
./scripts/cargo-install-all.sh .
export PATH=$PWD/bin:$PATH
```

Затем вы можете запустить следующую команду, чтобы получить тот же результат, что и с готовыми двоичными файлами:

```bash
solana-install init
```
