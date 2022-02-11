+++
title = "Настройка аварийного переключения"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Здесь описан простой метод аварийного переключения двух экземпляров компьютеров, который позволяет:

- обновить программное обеспечение валидатора практически без простоев, и
- переключение на дополнительный экземпляр, когда ваш мониторинг обнаруживает проблему с основным экземпляром
  без каких-либо проблем с безопасностью, которые в противном случае были бы связаны с запуском двух экземпляров вашего валидатора.

Вам понадобятся две машины класса валидатора для вашего основного и вторичного валидатора. Третья машина для запуска кластера [etcd](https://etcd.io/), который используется для хранения записи голосования башни для вашего валидатора.

## Настраивать

### Настройка кластера etcd

На https://etcd.io/ имеется обширная документация по установке и настройке etcd, пожалуйста, ознакомьтесь с etcd, прежде чем продолжить.

Рекомендуется, чтобы etcd был установлен на машине, отдельной от вашей основной и дополнительной машин валидатора. Эта машина должна быть высокодоступной, и в зависимости от ваших потребностей вы можете настроить etcd с более чем одним узлом.

Сначала установите `etcd` для вашей машины. Затем необходимо создать сертификаты TLS для аутентификации между кластером etcd и вашим валидатором. Вот один из способов сделать это:

С установленным [Golang](https://golang.org/) запустите «go get github.com/cloudflare/cfssl/cmd/cfssl». Теперь программа `cfssl` должна быть доступна в `~/go/bin/cfssl`. Убедитесь, что `~/go/bin` находится в вашем PATH, запустив `PATH=$PATH:~/go/bin/`.

Теперь создайте каталог сертификата и файл конфигурации:

```
mkdir -p certs/
echo '{"CN":"etcd","hosts":["localhost", "127.0.0.1"],"key":{"algo":"rsa","size":2048}}' > certs/config.json
```

затем создайте сертификаты для сервера etcd и валидатора:

```
cfssl gencert -initca certs/config.json | cfssljson -bare certs/etcd-ca
cfssl gencert -ca certs/etcd-ca.pem -ca-key certs/etcd-ca-key.pem certs/config.json | cfssljson -bare certs/validator
cfssl gencert -ca certs/etcd-ca.pem -ca-key certs/etcd-ca-key.pem certs/config.json | cfssljson -bare certs/etcd
```

Скопируйте эти файлы на ваш основной и дополнительный компьютеры валидатора:

* `certs/validator-key.pem`
* `certs/validator.pem`
* `certs/etcd-ca.pem`

и эти файлы на машину с сервером etcd:

* `certs/etcd.pem`
* `certs/etcd-key.pem`
* `certs/etcd-ca.pem`

В этой конфигурации и валидатор, и etdc будут использовать один и тот же центр сертификации TLS и будут аутентифицировать друг друга с его помощью.

Запустите `etcd` со следующими аргументами:

```bash
etcd --auto-compaction-retention 2 --auto-compaction-mode revision \
  --cert-file=certs/etcd.pem --key-file=certs/etcd-key.pem \
  --client-cert-auth \
  --trusted-ca-file=certs/etcd-ca.pem \
  --listen-client-urls=https://127.0.0.1:2379 \
  --advertise-client-urls=https://127.0.0.1:2379
```

и используйте `curl`, чтобы убедиться, что сертификаты etcd TLS настроены правильно:

```bash
curl --cacert certs/etcd-ca.pem https://127.0.0.1:2379/ --cert certs/validator.pem --key certs/validator-key.pem
```

В случае успеха curl вернет ответ 404.

Для получения дополнительной информации о настройке etcd TLS, пожалуйста, обратитесь к https://etcd.io/docs/v3.5/op-guide/security/#example-2-client-to-server-authentication-with-https-client- сертификаты

### Основной валидатор

Следующие дополнительные параметры `solana-validator` необходимы для включения хранилища башни в etcd:

```
solana-validator ... \
  --tower-storage etcd \
  --etcd-cacert-file certs/etcd-ca.pem \
  --etcd-cert-file certs/validator.pem \
  --etcd-key-file certs/validator-key.pem \
  --etcd-endpoint 127.0.0.1:2379  # <-- replace 127.0.0.1 with the actual IP address
```

Обратите внимание, что после запуска ваш валидатор *прекратит работу*, если он не сможет записать свою башню в etcd перед отправкой транзакции голосования, поэтому важно, чтобы ваша конечная точка etcd оставалась доступной в любое время.

### Вторичный валидатор

Настройте вторичный валидатор так же, как и первичный, за исключением следующих изменений аргумента командной строки solana-validator:

- Создайте и используйте вторичный идентификатор валидатора: `--identitysecondary-validator-keypair.json`
- Добавить `--no-check-vote-account`
- Добавьте `--authorized-voter validator-keypair.json` (где
   `validator-keypair.json` — это пара ключей идентификации для вашего основного валидатора)

## Запуск аварийного переключения вручную

Когда оба валидатора работают нормально и догоняют кластер, можно запустить отработку отказа с основного на вторичный, выполнив следующую команду на вторичном валидаторе:

```bash
$ solana-validator wait-for-restart-window --identity validator-keypair.json \
  && solana-validator set-identity validator-keypair.json
```

Вторичный валидатор получит блокировку башни в etcd, чтобы обеспечить безопасное переключение голосования и производства блоков с основного валидатора.

Затем первичный валидатор прекратит работу, как только обнаружит вторичного валидатора, используя его идентификатор.

Примечание. Когда основной валидатор перезапустится (что может произойти немедленно, если вы настроили основной валидатор на это), он вернет свою идентификацию от вторичного валидатора. Это, в свою очередь, приведет к выходу вторичного валидатора. Однако, если/когда вторичный валидатор перезапустится, он сделает это, используя идентификатор вторичного валидатора, и, таким образом, цикл перезапуска будет нарушен.

## Запуск аварийного переключения через мониторинг

Мониторинг по вашему выбору может вызвать команду solana-validator set-identity validator-keypair.json, упомянутую в предыдущем разделе.

Нет необходимости гарантировать, что первичный валидатор остановился перед переключением на вторичный, так как процесс аварийного переключения не позволит первичному валидатору голосовать и создавать блоки, даже если он находится в неизвестном состоянии.

## Обновления программного обеспечения валидатора

Чтобы выполнить обновление программного обеспечения с помощью этого метода аварийного переключения:

1. Установите новую версию программного обеспечения в свою основную систему валидатора, но пока не перезапускайте ее.
2. Инициируйте ручное переключение на резервный валидатор. Это должно привести к прекращению работы вашего основного валидатора.
3. Когда ваш основной валидатор перезапустится, он теперь будет использовать новую версию программного обеспечения.
4. Как только основной валидатор наверстает упущенное, обновите вторичный валидатор по своему усмотрению.