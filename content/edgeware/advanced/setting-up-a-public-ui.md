+++
title = "Настройка общедоступного пользовательского интерфейса"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

На этой странице объясняется, как настроить [пользовательский интерфейс polkadot-js/apps](https://polkadot.js.org/apps/) для Edgeware, используя ветку Edgeware приложения. Основное отличие заключается в том, что ветвь Edgeware поддерживает типы, определенные в модулях голосования и управления Edgeware.

## 0. Инициализация сервера

Подготовьте сервер подходящего размера у одного из рекомендуемых поставщиков VPS.

Мы предполагаем, что вы используете Ubuntu 18.04 x64; другие версии или операционные системы потребуют внесения изменений в эти инструкции.

Настройте DNS, указывающий на сервер, например `apps.edgewa.re`. Настоятельно рекомендуется сделать это сейчас.

SSH на сервер.

## 1. Установка `apps` и настройка их как системной службы

Клонируйте репозиторий `apps`:

```
git clone https://github.com/hicommonwealth/apps.git
cd apps
```

Установите nodejs 11.x и yarn. Вам нужно будет добавить новые репозитории пакетов:

```
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
curl -sL https://deb.nodesource.com/setup_11.x | sudo -E bash -
apt update
apt install -y nodejs yarn
```

Установите зависимости:

```
yarn
```

Создайте юнит службы для systemd:

```
{
    echo '[Unit]'
    echo 'Description=EdgewareApps'
    echo '[Service]'
    echo 'Type=exec'
    echo 'WorkingDirectory='`pwd`
    echo 'Environment=ENV=production'
    echo 'ExecStart=yarn run start'
    echo '[Install]'
    echo 'WantedBy=multi-user.target'
} > /etc/systemd/system/apps.service
```

Запустите и проверьте, работает ли служба:

```
systemctl start apps
systemctl status apps
curl localhost:3000
```

## 2. Настройка SSL-сертификата

Чтобы сделать `apps` безопасным и доступным через SSL, мы будем использовать Let’s Encrypt и certbot.

Установите Certbot:

```
apt -y install software-properties-common
add-apt-repository universe
add-apt-repository ppa:certbot/certbot
apt update
apt -y install certbot python-certbot-nginx
```

Запустите certbot, чтобы получить сертификат от Let's Encrypt:

```
certbot --nginx
```

Certbot задаст вам несколько вопросов, запустит собственный веб-сервер и поговорит с Let's Encrypt, чтобы выдать сертификат.

Когда он спросит вас, следует ли перенаправлять трафик с порта 80 на SSL, вы должны ответить **да**.

## 3. Обновление конфигурации nginx

Установите предполагаемый публичный адрес сервера, например. apps.edgewa.re в качестве переменной среды:

```
export name=apps.edgewa.re
```

Настройте конфигурацию nginx. Это внедрит публичный адрес, который вы только что определили.

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
    echo '      ssl_protocols  SSLv2 SSLv3 TLSv1;'
    echo '      ssl_ciphers  HIGH:!aNULL:!MD5;'
    echo '      ssl_prefer_server_ciphers   on;'
    echo ''
    echo '      location / {'
    echo '          proxy_pass http://127.0.0.1:3000 ;'
    echo '      }'
    echo '    }'
    echo '}'
} > /etc/nginx/nginx.conf
```

Запустите nginx и убедитесь, что он запускается при запуске системы:

```
systemctl daemon-reload
systemctl start nginx
systemctl enable nginx
```

Убедитесь, что ваш сервер работает в локальном браузере.
