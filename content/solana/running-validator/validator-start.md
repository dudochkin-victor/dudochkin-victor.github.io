+++
title = "Запуск валидатора"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

## Настройка интерфейса командной строки Solana

solana cli включает команды настройки get и set для автоматической установки аргумента --url для команд cli. Например:

```bash
solana config set --url http://api.devnet.solana.com
```

Хотя в этом разделе показано, как подключиться к кластеру Devnet, шаги аналогичны для других [кластеров Solana](../clusters/).

## Подтверждение доступности кластера

Перед подключением узла проверки работоспособности проверьте, доступен ли кластер для вашего компьютера, получив количество транзакций:

```bash
solana transaction-count
```

Просмотрите [панель метрик](https://metrics.solana.com:3000/d/monitor/cluster-telemetry), чтобы получить дополнительные сведения об активности кластера.

## Включение CUDA

Если на вашем компьютере установлен GPU с CUDA \(в настоящее время только для Linux\), включите аргумент `--cuda` в `solana-validator`.

Когда ваш валидатор запущен, найдите следующее сообщение в журнале, указывающее, что CUDA включена: `"[<timestamp> solana::validator] CUDA включена"`

## Настройка системы

### Линукс

#### Автоматический

Репозиторий solana включает в себя демона для настройки параметров системы для оптимизации производительности (а именно, путем увеличения буфера UDP ОС и пределов сопоставления файлов).

Демон (`solana-sys-tuner`) включен в бинарную версию solana. Перезапустите его, _перед_ перезапуском вашего валидатора, после каждого обновления программного обеспечения, чтобы убедиться, что применяются последние рекомендуемые настройки.

Чтобы запустить его:

```bash
sudo $(command -v solana-sys-tuner) --user $(whoami) > sys-tuner.log 2>&1 &
```

#### Руководство

Если вы предпочитаете управлять настройками системы самостоятельно, вы можете сделать это с помощью следующих команд.

##### **Увеличить буфер UDP**

```bash
sudo bash -c "cat >/etc/sysctl.d/20-solana-udp-buffers.conf <<EOF
# Increase UDP buffer size
net.core.rmem_default = 134217728
net.core.rmem_max = 134217728
net.core.wmem_default = 134217728
net.core.wmem_max = 134217728
EOF"
```

```bash
sudo sysctl -p /etc/sysctl.d/20-solana-udp-buffers.conf
```

##### **Увеличен лимит отображаемых файлов памяти**

```bash
sudo bash -c "cat >/etc/sysctl.d/20-solana-mmaps.conf <<EOF
# Increase memory mapped files limit
vm.max_map_count = 1000000
EOF"
```

```bash
sudo sysctl -p /etc/sysctl.d/20-solana-mmaps.conf
```

и

```
LimitNOFILE=1000000
```

в раздел `[Service]` вашего служебного файла systemd, если вы его используете, в противном случае добавьте

```
DefaultLimitNOFILE=1000000
```

в `[Manager]` раздел `/etc/systemd/system.conf`.

```bash
sudo systemctl daemon-reload
```

```bash
sudo bash -c "cat >/etc/security/limits.d/90-solana-nofiles.conf <<EOF
# Increase process file descriptor count limit
* - nofile 1000000
EOF"
```

```bash
### Close all open sessions (log out then, in again) ###
```

## Создать личность

Создайте пару идентификационных ключей для вашего валидатора, запустив:

```bash
solana-keygen new -o ~/validator-keypair.json
```

Открытый ключ идентификации теперь можно просмотреть, запустив:

```bash
solana-keygen pubkey ~/validator-keypair.json
```

> Примечание. Файл «validator-keypair.json» также является вашим закрытым ключом \(ed25519\).

### Идентификация бумажного кошелька

Вы можете создать бумажный кошелек для своего файла идентификации вместо записи файла пары ключей на диск с помощью:

```bash
solana-keygen new --no-outfile
```

Соответствующий открытый ключ удостоверения теперь можно просмотреть, запустив:

```bash
solana-keygen pubkey ASK
```

а затем введите исходную фразу.

См. [Использование бумажного кошелька](../wallet-guide/paper-wallet/) для получения дополнительной информации.

+++

### Ключевая пара тщеславия

Вы можете сгенерировать собственную пару ключей с помощью solana-keygen. Например:

```bash
solana-keygen grind --starts-with e1v1s:1
```

Вы можете запросить, чтобы сгенерированная пара ключей была выражена в виде исходной фразы, которая позволяет восстановить пару ключей из исходной фразы и необязательно предоставленной фразы-пароля (обратите внимание, что это значительно медленнее, чем шлифовка без мнемоники):

```bash
solana-keygen grind --use-mnemonic --starts-with e1v1s:1
```

В зависимости от запрошенной строки поиск соответствия может занять несколько дней...

+++

Ваша пара ключей идентификации валидатора однозначно идентифицирует вашего валидатора в сети. ** Крайне важно создать резервную копию этой информации. **

Если вы не сделаете резервную копию этой информации, вы НЕ СМОЖЕТЕ ВОССТАНОВИТЬ СВОЙ ВАЛИДАТОР, если потеряете к нему доступ. Если это произойдет, ВЫ ТАКЖЕ ПОТЕРЯЕТЕ СВОЕ РАСПРЕДЕЛЕНИЕ SOL.

Чтобы создать резервную копию пары ключей, идентифицирующих валидатор, **создайте резервную копию файла «validator-keypair.json» или исходной фразы в безопасном месте.**

## Дополнительная конфигурация Solana CLI

Теперь, когда у вас есть пара ключей, установите конфигурацию solana для использования вашей пары ключей валидатора для всех следующих команд:

```bash
solana config set --keypair ~/validator-keypair.json
```

Вы должны увидеть следующий вывод:

```
Config File: /home/solana/.config/solana/cli/config.yml
RPC URL: http://api.devnet.solana.com
WebSocket URL: ws://api.devnet.solana.com/ (computed)
Keypair Path: /home/solana/validator-keypair.json
Commitment: confirmed
```

## Аирдроп и проверка баланса валидатора

Раздайте себе немного SOL, чтобы начать:

```bash
solana airdrop 1
```

Обратите внимание, что аирдропы доступны только в Devnet и Testnet. Оба ограничены 1 SOL за запрос.

Чтобы просмотреть текущий баланс:

```
solana balance
```

Или посмотреть подробнее:

```
solana balance --lamports
```

Узнайте больше о [различии между SOL и lamports здесь](../introduction.md#what-are-sols).

## Создать авторизованную учетную запись для снятия средств

Если вы еще этого не сделали, создайте пару ключей авторизованного отзывателя, которая будет использоваться в качестве высшей инстанции для вашего валидатора. Эта пара ключей будет иметь полномочия для выхода из вашей учетной записи для голосования, а также дополнительные полномочия для изменения всех других аспектов вашей учетной записи для голосования. Излишне говорить, что это очень важная пара ключей, поскольку любой, кто ею владеет, может вносить любые изменения в вашу учетную запись для голосования, в том числе стать ее владельцем на постоянной основе.
Поэтому очень важно хранить пару ключей авторизованного получателя в безопасном месте. Его не нужно хранить в вашем валидаторе, и его нельзя хранить где-либо, откуда к нему могут получить доступ посторонние лица. Чтобы создать пару ключей авторизованного снимающего:

```bash
solana-keygen new -o ~/authorized-withdrawer-keypair.json
```

## Создать учетную запись для голосования

Если вы еще этого не сделали, создайте пару ключей учетной записи для голосования и создайте учетную запись для голосования в сети. Если вы выполнили этот шаг, вы должны увидеть «vote-account-keypair.json» в вашем каталоге среды выполнения Solana:

```bash
solana-keygen new -o ~/vote-account-keypair.json
```

Следующая команда может быть использована для создания вашей учетной записи для голосования в блокчейне со всеми параметрами по умолчанию:

```bash
solana create-vote-account ~/vote-account-keypair.json ~/validator-keypair.json ~/authorized-withdrawer-keypair.json
```

Не забудьте переместить авторизованную пару ключей для снятия средств в очень безопасное место после выполнения вышеуказанной команды.

Узнайте больше о [создании и управлении учетной записью для голосования](vote-accounts/).

## Известные валидаторы

Если вы знаете и уважаете другие операторы валидатора, вы можете указать это в командной строке с аргументом `--known-validator <PUBKEY>` для `solana-validator`. Вы можете указать несколько, повторив аргумент `--known-validator <PUBKEY1> --known-validator <PUBKEY2>`.
Это имеет два эффекта: один из них, когда валидатор загружается с --only-known-rpc, он будет запрашивать только этот набор известных узлов для загрузки данных генезиса и моментальных снимков. Другой заключается в том, что в сочетании с опцией `--halt-on-known-validators-accounts-hash-mismatch`, он будет отслеживать корневой хеш меркла всего состояния учетных записей других известных узлов по сплетням, и если хэши производят какие-либо несоответствие, валидатор остановит узел, чтобы валидатор не мог голосовать или обрабатывать потенциально неверные значения состояния. На данный момент слот, на котором валидатор публикует хэш, привязан к интервалу снапшотов. Чтобы эта функция работала, все валидаторы в известном наборе должны быть установлены на одно и то же значение интервала моментальных снимков или кратное ему.

Настоятельно рекомендуется использовать эти параметры, чтобы предотвратить загрузку злонамеренного состояния моментального снимка или отклонение состояния учетной записи.

## Подключите свой валидатор

Подключитесь к кластеру, выполнив:

```bash
solana-validator \
  --identity ~/validator-keypair.json \
  --vote-account ~/vote-account-keypair.json \
  --rpc-port 8899 \
  --entrypoint entrypoint.devnet.solana.com:8001 \
  --limit-ledger-size \
  --log ~/solana-validator.log
```

Чтобы заставить валидатор вести журнал в консоли, добавьте аргумент `--log -`, иначе валидатор автоматически запишется в файл.

Бухгалтерская книга будет помещена в каталог `ledger/` по умолчанию, используйте аргумент `--ledger`, чтобы указать другое место.

> Примечание. Вы можете использовать
> [исходная фраза бумажного кошелька](../wallet-guide/paper-wallet/)
> для вашего `--identity` и/или
> Пары ключей `--authorized-voter`. Чтобы использовать их, передайте соответствующий аргумент как
> `solana-validator --identity ASK... --authorized-voter ASK...`
> и вам будет предложено ввести исходные фразы и необязательную парольную фразу.

Подтвердите, что ваш валидатор подключен к сети, открыв новый терминал и выполнив:

```bash
solana gossip
```

Если ваш валидатор подключен, его публичный ключ и IP-адрес появятся в списке.

### Управление распределением портов локальной сети

По умолчанию валидатор будет динамически выбирать доступные сетевые порты в диапазоне 8000-10000, и его можно переопределить с помощью `--dynamic-port-range`. Например, solana-validator --dynamic-port-range 11000-11020 ..." ограничит валидатор портами 11000-11020.

### Ограничение размера реестра для экономии места на диске

Параметр `--limit-ledger-size` позволяет вам указать, сколько леджеров [клочков](../terminology.md#shred) ваш узел сохраняет на диске. Если вы не укажете этот параметр, валидатор будет хранить всю книгу до тех пор, пока не закончится место на диске.

Значение по умолчанию пытается сохранить использование диска леджера ниже 500 ГБ. Можно запросить большее или меньшее использование диска, добавив аргумент к `--limit-ledger-size`, если это необходимо. Проверьте `solana-validator --help` для предельного значения по умолчанию, используемого `--limit-ledger-size`. Дополнительная информация о выборе пользовательского предельного значения [доступна здесь](https://github.com/solana-labs/solana/blob/583cec922b6107e0f85c7e14cb5e642bc7dfb340/core/src/ledger_cleanup_service.rs#L15-L26).

### Системный блок

Запуск валидатора как единицы systemd — это один из простых способов управлять работой в фоновом режиме.

Предполагая, что на вашем компьютере есть пользователь с именем sol, создайте файл `/etc/systemd/system/sol.service` со следующим:

```
[Unit]
Description=Solana Validator
After=network.target
Wants=solana-sys-tuner.service
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=1
User=sol
LimitNOFILE=1000000
LogRateLimitIntervalSec=0
Environment="PATH=/bin:/usr/bin:/home/sol/.local/share/solana/install/active_release/bin"
ExecStart=/home/sol/bin/validator.sh

[Install]
WantedBy=multi-user.target
```

Теперь создайте `/home/sol/bin/validator.sh`, чтобы включить желаемую командную строку `solana-validator`. Убедитесь, что для запуска процесса проверки используется команда «exec» (например, «exec solana-validator...»). Это важно, потому что без этого logrotate в конечном итоге будет убивать валидатор каждый раз, когда журналы ротируются.

Убедитесь, что запуск `/home/sol/bin/validator.sh` вручную запускает валидатор, как и ожидалось. Не забудьте пометить его как исполняемый с помощью `chmod +x /home/sol/bin/validator.sh`

Запустите службу с помощью:

```bash
$ sudo systemctl enable --now sol
```

### Логирование

#### Настройка вывода журнала

Сообщения, которые валидатор отправляет в журнал, можно контролировать с помощью переменной среды RUST_LOG. Подробности можно найти в [документации](https://docs.rs/env_logger/latest/env_logger/#enabling-logging) для ящика `env_logger` Rust.

Учтите, что если вывод журнала будет уменьшен, это может затруднить отладку проблем, возникших позже. Если будет запрошена поддержка со стороны команды, любые изменения необходимо будет отменить и воспроизвести проблему, прежде чем можно будет оказать помощь.

#### Ротация журнала

Файл журнала валидатора, указанный в `--log ~/solana-validator.log`, может со временем стать очень большим, поэтому рекомендуется настроить ротацию журнала.

Валидатор повторно откроет его, когда получит сигнал «USR1», который является базовым примитивом, обеспечивающим ротацию журнала.

Если валидатор запускается скриптом-оболочкой, важно запускать процесс с помощью `exec` (`exec solana-validator...`) при использовании logrotate.
Это предотвратит отправку сигнала `USR1` в процесс скрипта вместо валидатора, что уничтожит их обоих.

#### Использование logrotate

Пример настройки для `logrotate`, который предполагает, что валидатор работает как служба systemd с именем `sol.service` и записывает файл журнала в /home/sol/solana-validator.log:

```bash
# Setup log rotation

cat > logrotate.sol <<EOF
/home/sol/solana-validator.log {
  rotate 7
  daily
  missingok
  postrotate
    systemctl kill -s USR1 sol.service
  endscript
}
EOF
sudo cp logrotate.sol /etc/logrotate.d/sol
systemctl restart logrotate.service
```

Как упоминалось ранее, убедитесь, что если вы используете logrotate, любой созданный вами сценарий, который запускает процесс проверки solana, использует для этого «exec» (пример: «exec solana-validator ..."); в противном случае, когда logrotate отправит свой сигнал валидатору, закрывающий скрипт умрет и унесет с собой процесс валидатора.

### Отключить проверку портов для ускорения перезапуска

Как только ваш валидатор заработает нормально, вы можете сократить время, необходимое для перезапуска вашего валидатора, добавив флаг `--no-port-check` в командную строку `solana-validator`.

### Использование виртуального диска с переливом в своп для базы учетных записей для снижения износа SSD

Если на вашем компьютере достаточно оперативной памяти, для хранения базы данных учетных записей можно использовать виртуальный диск tmpfs ([tmpfs](https://man7.org/linux/man-pages/man5/tmpfs.5.html)).

При использовании tmpfs важно также настроить своп на вашем компьютере, чтобы избежать периодической нехватки пространства tmpfs.

Рекомендуется использовать раздел tmpfs объемом 300 ГБ с соответствующим разделом подкачки объемом 250 ГБ.

Пример конфигурации:

1. `sudo mkdir /mnt/solana-accounts`
2. Добавьте раздел tmpfs размером 300 ГБ, добавив новую строку, содержащую `tmpfs /mnt/solana-accounts tmpfs rw,size=300G,user=sol 0 0` в `/etc/fstab` (при условии, что ваш валидатор работает под пользователем «соль»). **ВНИМАНИЕ: если вы неправильно отредактируете /etc/fstab, ваша машина может перестать загружаться**
3. Создайте не менее 250 ГБ пространства подкачки
- Выберите устройство для использования вместо `SWAPDEV` для оставшейся части этих инструкций. В идеале выберите свободный раздел диска размером 250 ГБ или больше на быстром диске. Если он недоступен, создайте файл подкачки с помощью `sudo dd if=/dev/zero of=/swapfile bs=1MiB count=250KiB`, установите его разрешения с помощью `sudo chmod 0600 /swapfile` и используйте `/swapfile` как `SWAPDEV` для остальных инструкций
- Отформатируйте устройство для использования в качестве подкачки с помощью sudo mkswap SWAPDEV.
4. Добавьте файл подкачки в `/etc/fstab` с новой строкой, содержащей `SWAPDEV swap swap defaults 0 0`
5. Включите своп с помощью `sudo swapon -a` и смонтируйте tmpfs с помощью `sudo mount /mnt/solana-accounts/`
6. Подтвердите, что своп активен с помощью `free -g`, а tmpfs смонтирован с помощью `mount`

Теперь добавьте аргумент --accounts /mnt/solana-accounts к аргументам командной строки solana-validator и перезапустите валидатор.

### Индексация аккаунта

По мере роста числа заполненных учетных записей в кластере запросы RPC с данными учетных записей сканируют весь набор учетных записей, например [`getProgramAccounts`](developing/clients/jsonrpc-api.md#getprogramaccounts) и [SPL-token-specific запросы](developing/clients/jsonrpc-api.md#gettokenaccountsbydelegate) — может работать плохо. Если вашему валидатору необходимо поддерживать какой-либо из этих запросов, вы можете использовать параметр --account-index, чтобы активировать один или несколько индексов учетных записей в памяти, которые значительно улучшают производительность RPC за счет индексации учетных записей по ключевому полю. В настоящее время поддерживаются следующие значения параметров:

- `program-id`: каждая учетная запись индексируется своей программой-владельцем; используется [`getProgramAccounts`](developing/clients/jsonrpc-api.md#getprogramaccounts)
- `spl-token-mint`: каждая учетная запись токена SPL индексируется своим токеном Mint; используется [getTokenAccountsByDelegate](developing/clients/jsonrpc-api.md#gettokenaccountsbydelegate) и [getTokenLargestAccounts](developing/clients/jsonrpc-api.md#gettokenlargestaccounts)
- `spl-token-owner`: каждая учетная запись токена SPL индексируется по адресу владельца токена; используется запросами [getTokenAccountsByOwner](developing/clients/jsonrpc-api.md#gettokenaccountsbyowner) и [`getProgramAccounts`](developing/clients/jsonrpc-api.md#getprogramaccounts), которые включают фильтр владельца токена spl.
