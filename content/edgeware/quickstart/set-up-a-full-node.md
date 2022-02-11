+++
title = "Настройка полного узла"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

В этом руководстве рассказывается, как настроить узел Edgeware. Есть два способа продолжить:

- Настройка частного узла, например. если вы хотите запустить валидатор
- Настройка общедоступного узла, например. если вы хотите запускать сервисы подключения или децентрализованные приложения к Edgeware

{% подсказка стиль = "информация" %}
Чтобы быстро запустить узел из образа **Docker**: [https://hub.docker.com/repository/docker/hicommonwealth/edgeware](https://hub.docker.com/repository/docker/hicommonwealth/edgeware)
{% конец%}

Если вы используете частный узел, вам нужно будет выполнить только **шаги 0 и 1** этого руководства. В противном случае мы проведем вас через настройку SSL-сертификата на **шагах 2 и 3**, чтобы любой браузер мог безопасно подключиться к вашему узлу. (Большинству людей, включая валидаторов, достаточно настроить приватный узел.)

## 0. Инициализация сервера

Предоставьте сервер подходящего размера от надежного поставщика VPS, например:

* [Vultr](https://www.vultr.com)
* [DigitalOcean](https://www.digitalocean.com)
* [Linode](https://www.linode.com)
* [OVH](https://www.ovh.com.au)
* [Contabo](https://contabo.com)
* [Scaleway](https://www.scaleway.com/en/)
* [Amazon AWS](https://aws.amazon.com), etc.

Мы рекомендуем узел с оперативной памятью не менее 2 ГБ и Ubuntu 18.04 x64. Другие операционные системы потребуют внесения изменений в эти инструкции.

Если вы используете общедоступный узел, настройте DNS с доменного имени, которым вы владеете, чтобы оно указывало на сервер. Цель DNS должна быть типа A, а не типа Redirect. Мы будем использовать testnet1.edgewa.re. (Вам не нужно делать это, если вы настраиваете частный узел.)

SSH на сервер.

## 1. Установка Edgeware и настройка его как системной службы

Сначала клонируйте репозиторий `edgeware-node`, установите все зависимости и запустите необходимые сценарии сборки.

```
apt update
apt install -y gcc libc6-dev
apt install -y cmake pkg-config libssl-dev git clang libclang-dev

# Prefetch SSH publickeys
ssh-keyscan -H github.com >> ~/.ssh/known_hosts

# Install rustup
curl https://sh.rustup.rs -sSf | sh -s -- -y
source /root/.cargo/env
export PATH=/root/.cargo/bin:$PATH

# Get packages
git clone https://github.com/hicommonwealth/edgeware-node.git
cd edgeware-node

# Build packages
./setup.sh
```

Настройте узел как системную службу. Для этого перейдите в корневой каталог репозитория `edgeware-node` и выполните следующее, чтобы создать файл конфигурации службы:

```
{
    echo '[Unit]'
    echo 'Description=Edgeware'
    echo '[Service]'
    echo 'Type=exec'
    echo 'WorkingDirectory='`pwd`
    echo 'ExecStart='`pwd`'/target/release/edgeware --chain=edgeware --ws-external --rpc-cors "*"'
    echo '[Install]'
    echo 'WantedBy=multi-user.target'
} > /etc/systemd/system/edgeware.service
```

**Примечание. Это создаст сервер Edgeware, который принимает входящие подключения от любого пользователя в Интернете. Если вы используете узел в качестве валидатора, вам следует вместо этого удалить флаг `ws-external`, чтобы Edgeware не принимал внешние подключения.**

Дважды проверьте правильность записи конфигурации в `/etc/systemd/system/edgeware.service`. Если это так, включите службу, чтобы она запускалась при запуске, а затем попробуйте запустить ее сейчас:

```
systemctl enable edgeware
systemctl start edgeware
```

Проверить статус услуги:

```
systemctl status edgeware
```

Вы должны увидеть узел, подключающийся к сети и синхронизирующий последние блоки. Если вам нужно сохранить последний вывод, вы можете использовать:

```
journalctl -u edgeware.service -f
```

## 2. Настройка SSL-сертификата (только для публичных узлов)

Мы будем использовать Certbot для связи с Let’s Encrypt. Установите зависимости Certbot:

```
apt -y install software-properties-common
add-apt-repository universe
snap install --classic certbot
apt update
```

Установите Сертбот:

```
apt -y install certbot python-certbot-nginx
```

Он поможет вам получить сертификат от Let's Encrypt:

```
certbot certonly --standalone
```

Если у вас уже есть работающий веб-сервер (например, nginx, Apache и т. д.), вам нужно будет остановить его, запустив, например, `service nginx stop`, чтобы это работало.

Certbot задаст вам несколько вопросов, запустит собственный веб-сервер и поговорит с Let's Encrypt, чтобы выдать сертификат. В конце вы должны увидеть вывод, который выглядит следующим образом:

```
root:~/edgeware-node# certbot certonly --standalone
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator standalone, Installer None
Please enter in your domain name(s) (comma and/or space separated)  (Enter 'c'
to cancel): testnet1.edgewa.re
Obtaining a new certificate
Performing the following challenges:
http-01 challenge for testnet1.edgewa.re
Waiting for verification...
Cleaning up challenges

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/testnet1.edgewa.re/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/testnet1.edgewa.re/privkey.pem
   Your cert will expire on 2019-10-08. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot
   again. To non-interactively renew *all* of your certificates, run
   "certbot renew"
```

## 3. Настройка прокси-сервера Websockets (только для публичных узлов)

Сначала устанавливаем nginx:

```
apt -y install nginx
```

Установите предполагаемый публичный адрес сервера, например. `testnet1.edgewa.re` в качестве переменной среды:

```
export name=testnet1.edgewa.re
```

Настройте конфигурацию nginx. Это введет публичный адрес, который вы только что определили.

```
{
    echo 'user       www-data;  ## Default: nobody'
    echo 'worker_processes  5;  ## Default: 1'
    echo 'error_log  /var/log/nginx/error.log;'
    echo 'pid        /var/run/nginx.pid;'
    echo 'worker_rlimit_nofile 8192;'
    echo ''
    echo 'events {'
    echo '  worker_connections  4096;  ## Default: 1024'
    echo '}'
    echo ''
    echo 'http {'
    echo '    map $http_upgrade $connection_upgrade {'
    echo '      default upgrade;'
    echo "      \'\' close;"
    echo '    }'
    echo '    server {'
    echo '      listen       443 ssl;'
    echo '      server_name  '$name';'
    echo ''
    echo '      ssl_certificate /etc/letsencrypt/live/'$name'/cert.pem;'
    echo '      ssl_certificate_key /etc/letsencrypt/live/'$name'/privkey.pem;'
    echo '      ssl_session_timeout 5m;'
    echo '      ssl_protocols  SSLv2 SSLv3 TLSv1 TLSv1.1 TLSv1.2 TLSv1.3;'
    echo '      ssl_ciphers  HIGH:!aNULL:!MD5;'
    echo '      ssl_prefer_server_ciphers   on;'
    echo ''
    echo '      location / {'
    echo '          proxy_pass http://127.0.0.1:9944 ;'
    echo '          proxy_http_version 1.1;'
    echo '          proxy_set_header Upgrade $http_upgrade;'
    echo '          proxy_set_header Connection $connection_upgrade;'
    echo '      }'
    echo '    }'
    echo '}'
} > /etc/nginx/nginx.conf
```

Убедитесь, что пути `ssl_certificate` и `ssl_certificate_key` соответствуют тому, что Let's Encrypt создал ранее. Убедитесь, что файл конфигурации создан правильно.

```
cat /etc/nginx/nginx.conf
nginx -t
```

Убедитесь, что версия OpenSSL Nginx > 1.0.2. Вам придется пересобрать Nginx, если версия OpenSSL ниже. В противном случае современные протоколы TLS, созданные в сертификатах letsencrypt, не будут работать, и Nginx выдаст ошибку.

Если есть ошибка, `nginx -t` должен сказать вам, где она находится. **Обратите внимание, что могут быть незначительные различия в настройке различных систем, например. некоторые ящики могут иметь разных пользователей входа в систему или места для файлов журнала. Вам решать, как примирить эти разногласия.**

Запустите сервер:

```
service nginx restart
```

Теперь вы можете попробовать подключиться к своему новому узлу из [polkadot.js/apps](https://polkadot.js.org/apps/#/settings) или сделать запрос curl, который имитирует открытие защищенного соединения WebSockets:

```
curl --include --no-buffer --header "Connection: Upgrade" --header "Upgrade: websocket" --header "Host: $name:80" --header "Origin: http://$name:80" --header "Sec-WebSocket-Key: SGVsbG8sIHdvcmxkIQ==" --header "Sec-WebSocket-Version: 13" http://$name:9944/
```

## 4. Подключение к вашему узлу

Поздравляю с новым узлом! Если вы настроили общедоступный DNS и SSL-сертификат на шагах 2 и 3, вы сможете подключиться к нему сейчас из [polkadot-js/apps](https://polkadot.js.org/apps/#/settings) :

![](https://user-images.githubusercontent.com/1273926/66156368-631b3400-e5d6-11e9-8254-33040f87ee4f.png)

В противном случае вы сможете использовать [edgeware-cli](https://github.com/hicommonwealth/edgeware-cli) для подключения к нему:

```
git clone https://github.com/hicommonwealth/edgeware-cli.git
cd edgeware-cli
yarn
bin/edge -r ws://testnet1.edgewa.re:9944 balances freeBalance 5G8jA2TLTQqnofx2jCE1MAtaZNqnJf1ujv7LdZBv2LGznJE2
```

Как правило, вы должны использовать эти URL-адреса для подключения к вашему узлу:

- `ws://testnet1.edgewa.re:9944`, если вы настроили его как общедоступный узел с помощью `--ws-external` на шаге 1.
- `wss://testnet1.edgewa.re`, если вы настроили его как общедоступный узел, а также выполнили шаги 2 и 3.

## 5. Следующие шаги

Ваш узел автоматически перезапустится при перезагрузке системы, но может не восстановиться после других сбоев. Чтобы справиться с ними, следуйте нашему руководству [Настройка мониторинга](../advanced/setting-up-monitoring/).

Вы также можете перейти к [Проверке на Edgeware](set-up-a-validator/).
