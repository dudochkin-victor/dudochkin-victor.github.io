+++
title = "Платформа кластерного тестирования"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

В этом документе предлагается среда кластерного тестирования \(CTF\). CTF — это тестовая система, позволяющая выполнять тесты на локальном внутрипроцессном кластере или на развернутом кластере.

## Мотивация

Цель CTF — предоставить основу для написания тестов независимо от того, где и как развернут кластер. В этих тестах можно фиксировать регрессии, а тесты можно запускать на развернутых кластерах для проверки развертывания. В центре внимания этих тестов должны быть стабильность кластера, консенсус, отказоустойчивость, стабильность API.

Тесты должны проверять единственную ошибку или сценарий и должны быть написаны с наименьшим количеством внутренней сантехники, подвергаемой тестированию.

## Обзор дизайна

Для тестов предоставляется точка входа, которая представляет собой структуру `contact_info::ContactInfo`, и уже профинансированная пара ключей.

Каждый узел в кластере настраивается с помощью `validator::ValidatorConfig` во время загрузки. Во время загрузки эта конфигурация указывает любую дополнительную конфигурацию кластера, необходимую для теста. Кластер должен загружаться с конфигурацией, когда он работает внутри процесса или в центре обработки данных.

После загрузки тест обнаружит кластер через точку входа сплетен и настроит любое поведение во время выполнения через валидатор RPC.

## Тестовый интерфейс

Каждый тест CTF начинается с непрозрачной точки входа и финансируемой пары ключей. Тест не должен зависеть от того, как развернут кластер, и должен иметь возможность использовать все функции кластера через общедоступные интерфейсы.

```
use crate::contact_info::ContactInfo;
use solana_sdk::signature::{Keypair, Signer};
pub fn test_this_behavior(
    entry_point_info: &ContactInfo,
    funding_keypair: &Keypair,
    num_nodes: usize,
)
```

## Обнаружение кластера

При запуске теста кластер уже установлен и полностью подключен. Тест может обнаружить большинство доступных узлов за несколько секунд.

```
use crate::gossip_service::discover_nodes;

// Discover the cluster over a few seconds.
let cluster_nodes = discover_nodes(&entry_point_info, num_nodes);
```

## Конфигурация кластера

Чтобы включить определенные сценарии, кластер необходимо загрузить со специальными конфигурациями. Эти конфигурации можно зафиксировать в `validator::ValidatorConfig`.

Например:

```
let mut validator_config = ValidatorConfig::default();
let local = LocalCluster::new_with_config(
                num_nodes,
                10_000,
                100,
                &validator_config
                );
```

## Как разработать новый тест

Например, есть ошибка, которая показывает, что кластер выходит из строя, когда он заполнен недействительными анонсированными узлами сплетен. Наша библиотека сплетен и протокол могут измениться, но кластер по-прежнему должен оставаться устойчивым к потокам недействительных объявленных узлов сплетен.

Настройте службу RPC:

```
let mut validator_config = ValidatorConfig::default();
validator_config.rpc_config.enable_rpc_gossip_push = true;
validator_config.rpc_config.enable_rpc_gossip_refresh_active_set = true;
```

Подключите RPC и напишите новый тест:

```
pub fn test_large_invalid_gossip_nodes(
    entry_point_info: &ContactInfo,
    funding_keypair: &Keypair,
    num_nodes: usize,
) {
    let cluster = discover_nodes(&entry_point_info, num_nodes);

    // Poison the cluster.
    let client = create_client(entry_point_info.client_facing_addr(), VALIDATOR_PORT_RANGE);
    for _ in 0..(num_nodes * 100) {
        client.gossip_push(
            cluster_info::invalid_contact_info()
        );
    }
    sleep(Durration::from_millis(1000));

    // Force refresh of the active set.
    for node in &cluster {
        let client = create_client(node.client_facing_addr(), VALIDATOR_PORT_RANGE);
        client.gossip_refresh_active_set();
    }

    // Verify that spends still work.
    verify_spends(&cluster);
}
```
