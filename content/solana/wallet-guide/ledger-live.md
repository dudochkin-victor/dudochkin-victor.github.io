+++
title = "Ledger Nano S и Nano X"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

В этом документе описывается, как настроить [Ledger Nano S](https://shop.ledger.com/products/ledger-nano-s) или [Ledger Nano X](https://shop.ledger.com/pages). /ledger-nano-x) с помощью программного обеспечения [Ledger Live](https://www.ledger.com/ledger-live).

После того, как этапы настройки, показанные ниже, выполнены и приложение Solana установлено на вашем устройстве Nano, у пользователей есть несколько вариантов того, как использовать Nano для взаимодействия с сетью Solana

## Начиная

- Закажите [Nano S](https://shop.ledger.com/products/ledger-nano-s) или [Nano X](https://shop.ledger.com/pages/ledger-nano-x) из Леджера.
- Следуйте инструкциям по настройке устройства, включенным в пакет, или [Начальная страница Ledger](https://www.ledger.com/start/)
- Установите [программное обеспечение Ledger Live для настольных ПК](https://www.ledger.com/ledger-live/)
  - Если у вас уже установлен Ledger Live, обновите Ledger Live до последней версии, которая позволяет устанавливать новейшие прошивки и обновления приложений.
- Подключите Nano к компьютеру и следуйте инструкциям на экране.
- Обновите прошивку вашего нового Nano. Это необходимо, чтобы убедиться, что вы можете установить последнюю версию приложения Solana.
  - [Обновление прошивки Nano S](https://support.ledger.com/hc/en-us/articles/360002731113-Update-Ledger-Nano-S-firmware)
  - [Обновление прошивки Nano X](https://support.ledger.com/hc/en-us/articles/360013349800)

## Установите приложение Solana на свой Nano

- Открыть бухгалтерскую книгу в прямом эфире
- Нажмите «Менеджер» на левой панели приложения и найдите «Solana» в каталоге приложений, затем нажмите «Установить».
  - Убедитесь, что ваше устройство подключено через USB и разблокировано PIN-кодом.
- На Nano может появиться запрос на подтверждение установки приложения Solana.
- «Солана» теперь должна отображаться как «Установлено» в Ledger Live Manager.

## Обновите приложение Solana до последней версии

Чтобы убедиться, что у вас есть последние функции, если вы используете более старую версию
приложения Solana, обновите его до версии `v1.0.1`, выполнив следующие действия.

- Убедитесь, что у вас есть Ledger Live версии 2.10.0 или новее.
  - Чтобы проверить свою версию Ledger Live, нажмите кнопку «Настройки» в правом верхнем углу, затем нажмите «О программе». Если доступна более новая версия Ledger Live, вы должны увидеть баннер с предложением выполнить обновление при первом открытии Ledger Live.
- Обновите прошивку на вашем Nano
  - [Обновление прошивки Nano S](https://support.ledger.com/hc/en-us/articles/360002731113-Update-Ledger-Nano-S-firmware)
  - [Обновление прошивки Nano X](https://support.ledger.com/hc/en-us/articles/360013349800)
- После успешного обновления прошивки приложение Solana должно быть автоматически переустановлено с последней версией приложения.

## Взаимодействуйте с сетью Solana

Пользователи могут использовать любую из следующих опций, чтобы использовать свой Nano для взаимодействия с
Солана:

- [SolFlare.com](https://solflare.com/) — это веб-кошелек, не связанный с хранением, созданный специально для Solana и поддерживающий основные операции по переводу и стейкингу с помощью устройства Ledger. Ознакомьтесь с нашим руководством по [использованию Nano с SolFlare](solflare/).

- Разработчики и опытные пользователи могут [использовать Nano с инструментами командной строки Solana](hardware-wallets/ledger/). Новые функции кошелька почти всегда поддерживаются в собственных инструментах командной строки, прежде чем они будут поддерживаться сторонними кошельками.

## Известные проблемы

- Nano X иногда не может подключиться к веб-кошелькам с помощью операционной системы Windows. Вероятно, это повлияет на любые браузерные кошельки, использующие WebUSB.
   Команда Ledger работает над решением этой проблемы.

## Служба поддержки

Посетите нашу [страницу поддержки кошелька](support/), чтобы узнать, как получить помощь.
