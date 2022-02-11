+++
title = "Программы тестирования"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Приложения отправляют транзакции в кластер Solana и запрашивают валидаторы, чтобы подтвердить, что транзакции были обработаны, и проверить результат каждой транзакции. Когда кластер ведет себя не так, как ожидалось, это может быть по ряду причин:

- Программа глючит

- Загрузчик BPF отклонил небезопасную программную инструкцию

- Сделка была слишком большой

- Транзакция недействительна

- Среда выполнения пыталась выполнить транзакцию при доступе к ней другой
  
   тот же аккаунт

- Сеть сбросила транзакцию

- Кластер откатил леджер

- Валидатор злонамеренно ответил на запросы

## Черты AsyncClient и SyncClient

Для устранения неполадок приложение должно перенацелить компонент более низкого уровня, где возможно меньше ошибок. Перенацеливание можно выполнить с помощью различных реализаций трейтов AsyncClient и SyncClient.

Компоненты реализуют следующие основные методы:

```
trait AsyncClient {
    fn async_send_transaction(&self, transaction: Transaction) -> io::Result<Signature>;
}

trait SyncClient {
    fn get_signature_status(&self, signature: &Signature) -> Result<Option<transaction::Result<()>>>;
}
```

Пользователи отправляют транзакции и асинхронно и синхронно ожидают результатов.

### Тонкий клиент для кластеров

Реализация самого высокого уровня, ThinClient, нацелена на кластер Solana, который может быть развернутой тестовой сетью или локальным кластером, работающим на машине разработки.

### TpuClient для TPU

Следующий уровень — это реализация TPU, которая еще не реализована. На уровне TPU приложение отправляет транзакции по каналам Rust, где не может быть сюрпризов от сетевых очередей или отброшенных пакетов. TPU реализует все «обычные» ошибки транзакций. Он выполняет проверку подписи, может сообщать об ошибках в использовании учетной записи и в противном случае приводит к бухгалтерской книге с доказательствами хэшей истории.

## Низкоуровневое тестирование

### БанкКлиент для Банка

Ниже уровня ТПУ находится банк. Банк не проверяет подпись и не создает бухгалтерскую книгу. Банк — это удобный уровень для тестирования новых сетевых программ. Это позволяет разработчикам переключаться между реализациями собственных программ и вариантами, скомпилированными BPF. Здесь нет необходимости в трейте Transact. API банка синхронный.

## Модульное тестирование с помощью среды выполнения

Под банком находится среда выполнения. Среда выполнения — идеальная тестовая среда для модульного тестирования. Статически связывая среду выполнения с собственной реализацией программы, разработчик получает кратчайший возможный цикл редактирования-компиляции-запуска. Без какой-либо динамической компоновки трассировка стека включает символы отладки, а программные ошибки легко устраняются.