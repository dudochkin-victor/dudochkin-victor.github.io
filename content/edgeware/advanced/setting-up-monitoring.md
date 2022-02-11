+++
title = "Настройка мониторинга"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

Если вы используете валидатор или публичный узел, вам следует настроить системный мониторинг, чтобы знать, отключается ли ваш узел, и, если возможно, перезапустить его автоматически.

В этом руководстве объясняется, как настроить **monit** и **mmonit** для достижения этой цели:

- Monit — это инструмент мониторинга процессов, который может перезапустить ваш узел, если он зависает.

- Mmonit - это панель управления, показывающая производительность \(процессор, память, оповещения и т.д.\)
  
   узлов мониторинга.

\(Если вам не нужна панель мониторинга, вы можете сразу перейти к разделу, посвященному настройке `monit` на отдельном узле.\)

В руководстве предполагается, что вы используете Ubuntu 18.04.

## Настройка сервера мониторинга `mmonit`

Для сервера мониторинга должно быть достаточно небольшого сервера \(например, 1 ГБ памяти\). По умолчанию в качестве базы данных будет использоваться SQLite. Это означает, что если вы удалите каталог monit, все зарегистрированные события будут потеряны!

```
apt install -y monit
wget https://mmonit.com/dist/mmonit-3.7.3-linux-x64.tar.gz
tar -vxzf mmonit-3.7.3-linux-x64.tar.gz
mv mmonit-3.7.3 mmonit
```

Используйте monit, чтобы поддерживать mmonit в рабочем состоянии:

```
{
    echo 'check process mmonit with pidfile '`pwd`'/mmonit/logs/mmonit.pid'
    echo '  start program = "'`pwd`'/mmonit/bin/mmonit"'
    echo '  stop program = "'`pwd`'/mmonit/bin/mmonit stop"'
    echo 'set httpd port 2812 and use address localhost'
    echo '  allow localhost'
} > /etc/monit/monitrc

monit reload
monit validate
```

Чтобы запустить панель управления mmonit вручную, вы можете использовать команду `mmonit/bin/mmonit`. Чтобы остановить, используйте `mmonit/bin/mmonit stop`.

Теперь перейдите к узлу в браузере, например, `http://monitor.edgewa.re:8080/`. Войдите в систему с именем пользователя «admin» и паролем «swordfish» и измените имя пользователя и пароль по умолчанию.

У вас должно получиться два пользователя; admin — это то, что вы будете использовать для входа в систему, а другого пользователя — это то, что вы предоставите узлам, которые передают данные на сервер мониторинга.

## Настройка `monit` на отдельном узле

SSH в свой узел.

Прежде чем продолжить, установите соответствующее имя хоста для узла, например.

```
hostnamectl set-hostname validator1
```

Установите monit.

```
apt install -y monit
```

Настройте веб-перехватчик Slack \(необязательно\):

```
export WEBHOOK=[slack webhook url]
{
    echo 'URL="'$WEBHOOK'"'
    echo "PAYLOAD='{\"text\": \"`hostname` restarted edgeware.service\"}'"
    echo 'curl -s -X POST --data-urlencode "payload=$PAYLOAD" $URL'
} > /root/slack_notify.sh

chmod 755 /root/slack_notify.sh
```

Настройте конфигурацию Monit для проверки службы Edgeware с 10-секундными интервалами, перезапустите, если ЦП &gt; 90% за пять проверок \(~50сек\) и публиковать события в mmonit.

Если вы используете mmonit, укажите имя пользователя и пароль, которые вы установили для mmonit выше. В противном случае вам следует удалить разделы, в которых используются имя пользователя и пароль, указанные ниже:

```
export USER=edgeware
export PASSWORD=[password]
export TARGET=[domain]
{
    echo 'set daemon 10'
    echo 'set log /var/log/monit.log'
    echo 'set idfile /var/lib/monit/id'
    echo 'set statefile /var/lib/monit/state'
    echo 'set eventqueue'
    echo '  basedir /var/lib/monit/events'
    echo '  slots 100                    '
    echo ''
    echo 'check process edgeware matching target/release/edgeware'
    echo '  start program = "/bin/systemctl restart edgeware"'
    echo '  stop program = "/bin/systemctl kill edgeware"'
    echo '  if cpu > 90% for 20 cycles then exec "/bin/systemctl stop edgeware" and repeat every 10 cycles'
    echo '  if cpu > 90% for 64 cycles then exec "/bin/systemctl kill edgeware" and repeat every 10 cycles'
    echo '  if cpu > 90% for 64 cycles then alert'
    echo '  if does not exist for 1 cycles then start'
    echo '  if does not exist for 1 cycles then exec "/bin/bash -c /root/slack_notify.sh"'
    echo ''
    echo 'set mmonit http://'$USER:$PASSWORD@$TARGET':8080/collector'
    echo 'set httpd port 2812 and use address localhost'
    echo '  allow localhost'
    echo '  allow '$USER:$PASSWORD
} > /etc/monit/monitrc

chmod 600 /etc/monit/monitrc

monit reload
monit validate
```

Обратитесь к [документации monit](https://mmonit.com/monit/documentation/monit.html), если вы хотите настроить оповещения по электронной почте или другие дополнительные проверки на своем узле.

## Мониторинг работоспособности API

Пока что monit будет проверять только то, что узел не остается загруженным на 100% ЦП в течение продолжительных периодов времени. Теперь мы можем добавить еще одну проверку, которая использует API, чтобы убедиться, что узел правильно принимает соединения и синхронизируется.

Это позволит выявить некоторые сценарии сбоя, когда узел продолжает работать, но перестает распознавать новые блоки. Мы предоставляем `nodeup` для этой цели.

Клонируйте [nodeup](https://github.com/hicommonwealth/nodeup) в каталог `/root`. Следуйте его инструкциям по установке:

```
git clone https://github.com/hicommonwealth/nodeup.git
cd nodeup
apt install -y nodejs npm
npm install -g yarn
yarn
```

Убедитесь, что ваш узел работает с флагом `--rpc-cors="*"`, чтобы соединения WebSocket принимались. Обратите внимание, что по умолчанию это не заставит узел принимать подключения из-за пределов локальной машины (для этого требуется `--ws-external`\).

Тест nodeup:

```
node index.js
```

Если это работает, теперь вы можете добавить эти строки в свою конфигурацию monit в `/etc/monit/monitrc` \ (они предполагают, что nodeup установлен в каталоге `/root/nodeup`, если нет, отрегулируйте соответствующим образом):

```
check program nodeup with path "/root/nodeup/index.js -u ws://localhost:9944"
  if status > 0 for 10 cycles then exec "/bin/systemctl stop edgeware" and repeat every 10 cycles
```

Перезапустите monit и убедитесь, что новый скрипт работает:

```
monit reload
monit status
```
