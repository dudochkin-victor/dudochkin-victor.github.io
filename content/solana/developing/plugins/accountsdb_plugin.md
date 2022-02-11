+++
title = "Плагины"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

## Обзор

Валидаторы при больших нагрузках RPC, например, при обслуживании вызовов getProgramAccounts, могут отставать от сети. Чтобы решить эту проблему, валидатор был усовершенствован для поддержки механизма плагинов, с помощью которого информация об учетных записях и слотах может передаваться во внешние хранилища данных, такие как реляционные базы данных, базы данных NoSQL или Kafka. Затем можно разработать службы RPC для использования данных из этих внешних хранилищ данных с возможностью более гибкой и целенаправленной оптимизации, такой как кэширование и индексирование. Это позволяет валидатору сосредоточиться на обработке транзакций, не замедляясь из-за загруженных запросов RPC.

В этом документе описываются интерфейсы подключаемого модуля и ссылочная реализация подключаемого модуля для базы данных PostgreSQL.

[crates.io]: https://crates.io/search?q=solana-
[docs.rs]: https://docs.rs/releases/search?query=solana-

### Важные ящики:

- [`solana-accountsdb-plugin-interface`] &mdash; Этот крейт определяет интерфейсы плагинов.

- [`solana-accountsdb-plugin-postgres`] &mdash; Крейт для реализации ссылочного плагина для базы данных PostgreSQL.

[`solana-accountsdb-plugin-interface`]: https://docs.rs/solana-accountsdb-plugin-interface
[`solana-accountsdb-plugin-postgres`]: https://docs.rs/solana-accountsdb-plugin-postgres
[`solana-sdk`]: https://docs.rs/solana-sdk
[`solana-transaction-status`]: https://docs.rs/solana-transaction-status

## Интерфейс плагина

Интерфейс плагина объявлен в [`solana-accountsdb-plugin-interface`]. Он определяется трейтом `AccountsDbPlugin`. Плагин должен реализовать трейт и предоставить функцию "C" `_create_plugin`, чтобы вернуть указатель на этот трейт. Например, в ссылочной реализации следующий код создает экземпляр подключаемого модуля PostgreSQL `AccountsDbPluginPostgres` и возвращает его указатель.

```
#[no_mangle]
#[allow(improper_ctypes_definitions)]
/// # Safety
///
/// This function returns the AccountsDbPluginPostgres pointer as trait AccountsDbPlugin.
pub unsafe extern "C" fn _create_plugin() -> *mut dyn AccountsDbPlugin {
    let plugin = AccountsDbPluginPostgres::new();
    let plugin: Box<dyn AccountsDbPlugin> = Box::new(plugin);
    Box::into_raw(plugin)
}
```

Реализация плагина может реализовать метод on_load для своей инициализации. Эта функция вызывается после динамической загрузки плагина в валидатор при его запуске. Конфигурация плагина управляется конфигурационным файлом в формате JSON5. В файле JSON5 должно быть поле `libpath`, которое указывает на полный путь к общей библиотеке, реализующей подключаемый модуль, и может содержать другую информацию о конфигурации, например параметры подключения к внешней базе данных. Файл конфигурации плагина указывается параметром CLI валидатора `--accountsdb-plugin-config`, и файл должен быть доступен для чтения процессу валидатора.

В качестве примера см. файл конфигурации для ссылочного плагина PostgreSQL ниже.

Плагин может реализовать метод `on_unload` для выполнения любой очистки перед выгрузкой плагина, когда валидатор корректно завершает работу.

Платформа плагина поддерживает потоковую передачу учетных записей, транзакций или и того, и другого. Плагин использует следующую функцию, чтобы указать, заинтересован ли он в получении данных учетной записи:

```
fn account_data_notifications_enabled(&self) -> bool
```

И он использует следующую функцию, чтобы указать, заинтересован ли он в получении данных транзакции:

```
    fn transaction_notifications_enabled(&self) -> bool
```

Для уведомления об обновлении учетной записи используется следующий метод:

```
    fn update_account(
        &mut self,
        account: ReplicaAccountInfoVersions,
        slot: u64,
        is_startup: bool,
    ) -> Result<()>
```

Структура ReplicaAccountInfoVersions содержит метаданные и данные потоковой учетной записи. «Слот» указывает на слот, в котором обновляется учетная запись. Когда `is_startup` имеет значение true, это означает, что учетная запись загружается из моментальных снимков при запуске валидатора. Когда `is_startup` имеет значение false, учетная запись обновляется при обработке транзакции.

Следующий метод вызывается, когда все учетные записи были уведомлены, когда валидатор восстанавливает AccountsDb из моментальных снимков при запуске.

```
fn notify_end_of_startup(&mut self) -> Result<()>
```

Когда `update_account` вызывается во время обработки транзакций, плагин должен обрабатывать уведомление как можно быстрее, потому что любая задержка может привести к тому, что валидатор отстанет от сети. Сохранение внешнего хранилища данных лучше всего выполнять асинхронно.

Для уведомления об изменении статуса слота используется следующий метод:

```
    fn update_slot_status(
        &mut self,
        slot: u64,
        parent: Option<u64>,
        status: SlotStatus,
    ) -> Result<()>
```

Чтобы обеспечить согласованность данных, реализация плагина может отменить валидатор в случае сохранения ошибки во внешних хранилищах. При перезапуске валидатора данные учетной записи будут переданы повторно.

Для уведомления о транзакциях используется следующий метод:

```
    fn notify_transaction(
        &mut self,
        transaction: ReplicaTransactionInfoVersions,
        slot: u64,
    ) -> Result<()>
```

Структура ReplicaTransactionInfoVersionsoVersions содержит информацию о потоковой транзакции. Он обертывает `ReplicaTransactionInfo`

```
pub struct ReplicaTransactionInfo<'a> {
    /// The first signature of the transaction, used for identifying the transaction.
    pub signature: &'a Signature,

    /// Indicates if the transaction is a simple vote transaction.
    pub is_vote: bool,

    /// The sanitized transaction.
    pub transaction: &'a SanitizedTransaction,

    /// Metadata of the transaction status.
    pub transaction_status_meta: &'a TransactionStatusMeta,
}
```

Подробную информацию о SanitizedTransaction и TransactionStatusMeta см. в [`solana-sdk`] и [`solana-transaction-status`].

«Слот» указывает на слот, в котором выполняется транзакция. Для получения более подробной информации обратитесь к документации Rust в [`solana-accountsdb-plugin-interface`].

## Пример плагина PostgreSQL

Крейт [`solana-accountsdb-plugin-postgres`] реализует плагин, сохраняющий данные учетной записи в базе данных PostgreSQL, чтобы проиллюстрировать, как можно разработать плагин.

<a name="config">
### Формат файла конфигурации
</a>

Плагин настраивается с помощью входного файла конфигурации. Пример файла конфигурации выглядит следующим образом:

```
{
    "libpath": "/solana/target/release/libsolana_accountsdb_plugin_postgres.so",
    "host": "postgres-server",
    "user": "solana",
    "port": 5433,
    "threads": 20,
    "batch_size": 20,
    "panic_on_db_errors": true,
    "accounts_selector" : {
        "accounts" : ["*"]
    }
}
```

«Хост», «пользователь» и «порт» управляют информацией о конфигурации PostgreSQL. Для более продвинутых параметров подключения используйте поле `connection_str`. См. [Конфигурация Rust postgres](https://docs.rs/postgres/0.19.2/postgres/config/struct.Config.html).

Чтобы повысить пропускную способность базы данных, подключаемый модуль поддерживает пул соединений с использованием нескольких потоков, каждый из которых поддерживает соединение с базой данных PostgreSQL. Количество потоков контролируется полем threads. Большее количество потоков обычно обеспечивает лучшую производительность.

Для дальнейшего повышения производительности при сохранении большого количества учетных записей при запуске плагин использует массовые вставки. Размер пакета контролируется параметром `batch_size`. Это может помочь сократить циклы обращения к базе данных.

`panic_on_db_errors` можно использовать для паники валидатора в случае ошибок базы данных, чтобы обеспечить согласованность данных.

### Выбор учетной записи

Accounts_selector можно использовать для фильтрации учетных записей, которые должны быть сохранены.

Например, можно использовать следующее, чтобы сохранить только учетные записи с определенными публичными ключами в кодировке Base58:

```
    "accounts_selector" : {
         "accounts" : ["pubkey-1", "pubkey-2", ..., "pubkey-n"],
    }
```

Или используйте следующее, чтобы выбрать учетные записи с определенными владельцами программы:

```
    "accounts_selector" : {
         "owners" : ["pubkey-owner-1", "pubkey-owner-2", ..., "pubkey-owner-m"],
    }
```

Чтобы выбрать все учетные записи, используйте подстановочный знак (*):

```
    "accounts_selector" : {
         "accounts" : ["*"],
    }
```

### Выбор транзакции

«transaction_selector» определяет, какие транзакции следует хранить. Если это поле отсутствует, ни одна из транзакций не сохраняется.

Например, можно использовать следующее, чтобы выбрать только транзакции, ссылающиеся на учетные записи с определенными публичными ключами в кодировке Base58,

```
"transaction_selector" : {
    "mentions" : \["pubkey-1", "pubkey-2", ..., "pubkey-n"\],
}
```

Поле «упоминания» поддерживает подстановочные знаки для выбора всех транзакций или всех транзакций «голосования». Например, чтобы выбрать все транзакции:

```
"transaction_selector" : {
    "mentions" : \["*"\],
}
```

Чтобы выбрать все операции голосования:

```
"transaction_selector" : {
    "mentions" : \["all_votes"\],
}
```

### Настройка базы данных

#### Установить сервер PostgreSQL

Следуйте инструкциям [Установка PostgreSQL Ubuntu](https://www.postgresql.org/download/linux/ubuntu/), чтобы установить сервер базы данных PostgreSQL. Например, чтобы установить postgresql-14,

```
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo apt-get update
sudo apt-get -y install postgresql-14
```

#### Контролируйте доступ к базе данных

При необходимости измените pg_hba.conf, чтобы предоставить подключаемому модулю доступ к базе данных. Например, в /etc/postgresql/14/main/pg_hba.conf следующая запись разрешает узлам с IP-адресами в CIDR 10.138.0.0/24 доступ ко всем базам данных. Валидатор работает в узле с IP-адресом в указанном диапазоне.

```
host    all             all             10.138.0.0/24           trust
```

Рекомендуется запускать сервер базы данных на отдельном узле от валидатора для лучшей производительности.

#### Настройка параметров производительности базы данных

Пожалуйста, обратитесь к [Конфигурация сервера PostgreSQL](https://www.postgresql.org/docs/14/runtime-config.html) для получения подробной информации о конфигурации. Ссылочная реализация использует следующие конфигурации для повышения производительности базы данных в /etc/postgresql/14/main/postgresql.conf, которые отличаются от установки postgresql-14 по умолчанию.

```
max_connections = 200                  # (change requires restart)
shared_buffers = 1GB                   # min 128kB
effective_io_concurrency = 1000        # 1-1000; 0 disables prefetching
wal_level = minimal                    # minimal, replica, or logical
fsync = off                            # flush data to disk for crash safety
synchronous_commit = off               # synchronization level;
full_page_writes = off                 # recover from partial page writes
max_wal_senders = 0                    # max number of walsender processes
```

Образец [postgresql.conf](https://github.com/solana-labs/solana/blob/7ac43b16d2c766df61ae0a06d7aaf14ba61996ac/accountsdb-plugin-postgres/scripts/postgresql.conf) можно использовать для справки.

#### Создайте экземпляр базы данных и роль

Запустите сервер:

```
sudo systemctl start postgresql@14-main
```

Создайте базу данных. Например, следующий код создает базу данных с именем «solana»:

```
sudo -u postgres createdb solana -p 5433
```

Создайте пользователя базы данных. Например, следующий код создает обычного пользователя с именем solana:

```
sudo -u postgres createuser -p 5433 solana
```

Убедитесь, что база данных работает с помощью psql. Например, если предположить, что узел, на котором работает PostgreSQL, имеет IP-адрес 10.138.0.9, следующая команда попадет в оболочку, где можно вводить команды SQL:

```
psql -U solana -p 5433 -h 10.138.0.9 -w -d solana
```

#### Создайте объекты схемы

Используйте [create_schema.sql](https://github.com/solana-labs/solana/blob/a70eb098f4ae9cd359c1e40bbb7752b3dd61de8d/accountsdb-plugin-postgres/scripts/create_schema.sql) для создания объектов для хранения учетных записей и слотов.

Скачиваем скрипт с гитхаба:

```
wget https://raw.githubusercontent.com/solana-labs/solana/a70eb098f4ae9cd359c1e40bbb7752b3dd61de8d/accountsdb-plugin-postgres/scripts/create_schema.sql
```

Затем запустите скрипт:

```
psql -U solana -p 5433 -h 10.138.0.9 -w -d solana -f create_schema.sql
```

После этого запустите валидатор с плагином, используя аргумент `--accountsdb-plugin-config`, упомянутый выше.

#### Уничтожить объекты схемы

Чтобы уничтожить объекты базы данных, созданные `create_schema.sql`, используйте [drop_schema.sql](https://github.com/solana-labs/solana/blob/a70eb098f4ae9cd359c1e40bbb7752b3dd61de8d/accountsdb-plugin-postgres/scripts/drop_schema.sql ).
Например,

```
psql -U solana -p 5433 -h 10.138.0.9 -w -d solana -f drop_schema.sql
```

### Захват исторических данных счета

Чтобы получить исторические данные учетной записи, в файле конфигурации установите для параметра store_account_historical_data значение true.

И убедитесь, что триггер базы данных создан для сохранения данных в `audit_table` при обновлении записей в `account`, как показано в `create_schema.sql`,

```
CREATE FUNCTION audit_account_update() RETURNS trigger AS $audit_account_update$
    BEGIN
        INSERT INTO account_audit (pubkey, owner, lamports, slot, executable, rent_epoch, data, write_version, updated_on)
            VALUES (OLD.pubkey, OLD.owner, OLD.lamports, OLD.slot,
                    OLD.executable, OLD.rent_epoch, OLD.data, OLD.write_version, OLD.updated_on);
        RETURN NEW;
    END;

$audit_account_update$ LANGUAGE plpgsql;

CREATE TRIGGER account_update_trigger AFTER UPDATE OR DELETE ON account
    FOR EACH ROW EXECUTE PROCEDURE audit_account_update();
```

Триггер можно сбросить, чтобы отключить эту функцию, например,

```
DROP TRIGGER account_update_trigger ON account;
```

Со временем account_audit может накопить большой объем данных. Вы можете ограничить это, удалив старые исторические данные.

Например, следующую инструкцию SQL можно использовать для хранения до 1000 самых последних записей для учетной записи:

```
delete from account_audit a2 where (pubkey, write_version) in
    (select pubkey, write_version from
        (select a.pubkey, a.updated_on, a.slot, a.write_version, a.lamports,
            rank() OVER ( partition by pubkey order by write_version desc) as rnk
            from account_audit a) ranked
            where ranked.rnk > 1000)
```

### Основные таблицы

Ниже приведены таблицы в базе данных Postgres.

| Table         | Description             |
|:------------- |:----------------------- |
| account       | Account data            |
| slot          | Slot metadata           |
| transaction   | Transaction data        |
| account_audit | Account historical data |

### Вопросы производительности

Когда валидатору не хватает вычислительной мощности, накладные расходы на сохранение данных учетной записи могут привести к тому, что он отстанет от сети, особенно когда выбраны все учетные записи или большое количество учетных записей. Узел, на котором размещена база данных PostgreSQL, также должен быть достаточно мощным, чтобы справляться с нагрузками базы данных. Было обнаружено, что использование типа машины GCP n2-standard-64 для валидатора и n2-highmem-32 для узла PostgreSQL достаточно для обработки передачи всех учетных записей, не отставая от сети. Кроме того, лучше всего держать валидатор и PostgreSQL в одной локальной сети, чтобы уменьшить задержку. Возможно, вам придется изменить размер узла валидатора и базы данных, если вы обслуживаете другие нагрузки.
