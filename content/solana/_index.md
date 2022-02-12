+++
title = "Документация Solana"
description = "Документация Solana"
weight = 1
+++

## Общая информация

- [Введение](introduction/)
- [Терминология](terminology/)
- [История](history/)

## Кошельки

- [Руководство по кошельку Solana](wallet-guide/)
- [Кошельки для мобильных приложений](wallet-guide/apps/)
- Веб-кошельки
    - [Веб-кошельки](wallet-guide/web-wallets/)
    - [Веб-кошелек SolFlare](wallet-guide/solflare/)
- Hardware Wallets
    - [Ledger Nano S и Nano X](wallet-guide/ledger-live/)
- Command-line Wallets
    - [Кошельки командной строки](wallet-guide/cli/)
    - [Бумажный кошелек](wallet-guide/paper-wallet/)
    - Hardware Wallets
        - [Использование аппаратных кошельков в Solana CLI](wallet-guide/hardware-wallets/)
        - [Ledger Nano](wallet-guide/hardware-wallets/ledger/)
    - [Кошелек файловой системы](wallet-guide/file-system-wallet/)
- [Поддержка / устранение неполадок](wallet-guide/support/)

## Стэйкинг

- [Стэйкинг на Solana](staking/)
- [Структура стейкинг-счета](staking/stake-accounts/)

## Интерфейс командной строки

- [Руководство по командной строке](cli/)
- [Установка набора инструментов Solana](cli/install-solana-cli-tools/)
- [Использование Solana CLI](cli/conventions/)
- [Подключение к кластеру](cli/choose-a-cluster/)
- [Отправка и получение токенов](cli/transfer-tokens/)
- [Стэйкинг](cli/delegate-stake/)
- [Деплой программ](cli/deploy-a-program/)
- [Подписание офлайн-транзакций](offline-signing/)
- [Одноразовые номера устойчивых транзакций](offline-signing/durable-nonce/)
- [CLI Usage Reference](cli/usage/)

## Разработка

- Programming Model
    - [Обзор](developing/programming-model/overview/)
    - [Транзакции](developing/programming-model/transactions/)
    - [Учетные записи](developing/programming-model/accounts/)
    - [Runtime](developing/programming-model/runtime/)
    - [Вызовы между программами](developing/programming-model/calling-between-programs/)
- Clients
    - [JSON RPC API](developing/clients/jsonrpc-api/)
    - [Web3 JavaScript API](developing/clients/javascript-api/)
    - [Справочник по Web3 API](developing/clients/javascript-reference/)
    - [Rust API](developing/clients/rust-api/)
- Runtime Facilities
    - [Нативные программы](developing/runtime-facilities/programs/)
    - [Sysvar Cluster Data](developing/runtime-facilities/sysvars/)
- On-Chain Programs
    - [Обзор](developing/on-chain-programs/overview/)
    - [Разработка на Rust](developing/on-chain-programs/developing-rust/)
    - [Разработка на C](developing/on-chain-programs/developing-c/)
    - [Развертывание](developing/on-chain-programs/deploying/)
    - [Отладка](developing/on-chain-programs/debugging/)
    - [Примеры](developing/on-chain-programs/examples/)
    - [FAQ](developing/on-chain-programs/faq/)
- [Тестовый валидатор Solana](developing/test-validator/)
- [Политика обратной совместимости](developing/backwards-compatibility/)
- [Плагины](developing/plugins/accountsdb-plugin/)
- Учебники
    - [Созданеи Solana dApp c нуля](from-scratch/)

## Интеграция

- [Добавьте Солану на свою биржу](integrations/exchange/)

## Валидация

- [Работа валидатора](running-validator/)
- [Требования к валидатору](running-validator/validator-reqs/)
- [Запуск валидатора](running-validator/validator-start/)
- [Управление учетными записями для голосования](running-validator/vote-accounts/)
- [Стэйкинг](running-validator/validator-stake/)
- [Мониторинг валидатора](running-validator/validator-monitor/)
- [Публикация информации о валидаторе](running-validator/validator-info/)
- [Настройка аварийного переключения](running-validator/validator-failover/)
- [Исправление проблем](running-validator/validator-troubleshoot/)

## Кластеры

- [Кластеры Solana](clusters/)
- [Конечные точки RPC кластера Solana](cluster/rpc-endpoints/)
- [Бенчмарк кластера](cluster/bench-tps/)
- [Показатели эффективности](cluster/performance-metrics/)

## Архитектура

- Cluster
    - [Кластер Solana](cluster/overview/)
    - [Синхронизация](cluster/synchronization/)
    - [Ротация лидеров](cluster/leader-rotation/)
    - [Генерация форков](cluster/fork-generation/)
    - [Управление форками](cluster/managing-forks/)
    - [Распространение блока турбины](cluster/turbine-block-propagation/)
    - [Безопасное подписание голосов](cluster/vote-signing/)
    - [Делегирование ставок и вознаграждения](cluster/stake-delegation-and-rewards/)
- Validator
    - [Анатомия валидатора](validator/anatomy/)
    - [TPU](validator/tpu/)
    - [TVU](validator/tvu/)
    - [Blockstore](validator/blockstore/)
    - [Сервис Gossip](validator/gossip/)
    - [Runtime](validator/runtime/)

## Экономика

- [Обзор экономики Соланы](economics-overview/)
- Inflation Design
    - [Терминология](inflation/terminology/)
    - [Предлагаемый график инфляции Solana](inflation/inflation-schedule/)
    - [Скорректированная доходность по стейкингу](inflation/adjusted-staking-yield/)
- [Transaction Fees](transaction-fees/)
- [Экономика аренды хранилища](storage-rent-economics/)

## Design proposals

- Implemented
    - [Реализованные проектные предложения](implemented-proposals/implemented-proposals/)
    - [Процесс управления Solana ABI](implemented-proposals/abi-management/)
    - [Коррекция метки времени банка](implemented-proposals/bank-timestamp-correction/)
    - [Обязательство](implemented-proposals/commitment/)
    - [Одноразовые номера устойчивых транзакций](implemented-proposals/durable-tx-nonces/)
    - [Cluster Software Installation and Updates](implemented-proposals/installer/)
    - [Интроспекция инструкций](implemented-proposals/instruction-introspection/)
    - [Переход от лидера к лидеру](implemented-proposals/leader-leader-transition/)
    - [Переход от лидера к валидатору](implemented-proposals/leader-validator-transition/)
    - [Постоянное хранилище учетных записей](implemented-proposals/persistent-account-storage/)
    - [Аккаунты только для чтения](implemented-proposals/readonly-accounts/)
    - [Надежная передача голоса](implemented-proposals/reliable-vote-transmission/)
    - [Аренда](implemented-proposals/rent/)
    - [Служба восстановления](implemented-proposals/repair-service/)
    - [Долгосрочная история транзакций RPC](implemented-proposals/rpc-transaction-history/)
    - [Проверка снапшотов](implemented-proposals/snapshot-verification/)
    - [Награды за стекинг](implemented-proposals/staking-rewards/)
    - [Программы тестирования](implemented-proposals/testing-programs/)
    - [Tower BFT](implemented-proposals/tower-bft/)
    - [Детерминированные комиссии за транзакции](implemented-proposals/transaction-fees/)
    - [Валидатор оракла Отметки времени](implemented-proposals/validator-timestamp-oracle/)
- Accepted
    - [Принятые предложения по дизайну](proposals/accepted-design-proposals/)
    - [Безбанковский лидер](proposals/bankless-leader/)
    - [Подтверждение блокировки](proposals/block-confirmation/)
    - [Платформа кластерного тестирования](proposals/cluster-test-framework/)
    - [Встраивание языка перемещения](proposals/embedding-move/)
    - [Межсетевая проверка транзакций](proposals/interchain-transaction-verification/)
    - [Репликация леджера](proposals/ledger-replication-to-implement/)
    - [Оптимистическое подтверждение и слэшинг](proposals/optimistic-confirmation-and-slashing/)
    - [Optimistic Confirmation](proposals/optimistic-confirmation/)
    - [RiP Curl: RPC с малой задержкой, ориентированный на транзакции](proposals/rip-curl/)
    - [Rust Клиенты](proposals/rust-clients/)
    - [Простая оплата и проверка состояния](proposals/simple-payment-and-state-verification/)
    - [Правила Слэшинга](proposals/slashing/)
    - [Проверка снапшотов](proposals/snapshot-verification/)
    - [Проверка отметок](proposals/tick-verification/)
    - [Transactions v2 — таблицы поиска адресов в цепочке](proposals/transactions-v2/)
    - [Валидатор](proposals/validator-proposal/)
    - [Безопасное подписание голосов](proposals/vote-signing-to-implement/)
