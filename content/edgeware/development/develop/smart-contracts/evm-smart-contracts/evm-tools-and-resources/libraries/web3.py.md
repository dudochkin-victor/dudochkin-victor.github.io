+++
title = "Web3.py"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

### Введение <a href="introduction" id="introduction"></a>

[Web3.py](https://web3py.readthedocs.io) — это набор библиотек, которые позволяют разработчикам взаимодействовать с узлами Ethereum, используя протоколы HTTP, IPC или WebSocket с Python. Moonbeam имеет доступный API, подобный Ethereum, который полностью совместим с вызовами JSON RPC в стиле Ethereum. Поэтому разработчики могут использовать эту совместимость и использовать библиотеку web3.py для взаимодействия с узлом Moonbeam, как если бы они делали это на Ethereum.

### Настройте Web3.py с помощью Edgeware <a href="setup-web3py-with-moonbeam" id="setup-web3py-with-moonbeam"></a>

Чтобы начать работу с библиотекой web3.py, установите ее с помощью следующей команды:

```
pip3 install web3
```

После этого самая простая настройка для начала использования библиотеки и ее методов выглядит следующим образом:

```
from web3 import Web3

web3 = Web3(Web3.HTTPProvider('RPC_URL'))
```

В зависимости от того, к какой сети вы хотите подключиться, вы можете установить для `RPC_URL` следующие значения:

**Узел разработки Edgeware:**

* RPC_URL:  [`http://localhost:9933/`](http://localhost:9933)``

**Beresheet testnet:**

* RPC_URL:  [`https://beresheet2.edgewa.re/evm`](https://beresheet2.edgewa.re/evm) `(В качестве альтернативы можно использовать `[`https://beresheetX.edgewa.re/evm`](https://beresheetx.edgewa.re/evm)` где X может быть любым числом от 1 до 8.)`

**Edgeware mainnet:**

* RPC_URL: [`https://mainnet2.edgewa.re/evm`](https://mainnet2.edgewa.re/evm)`  (В качестве альтернативы можно использовать  `[`https://mainnetX.edgewa.re/evm`](https://mainnetx.edgewa.re/evm)` где X может быть любым числом от 1 до 20.)`

### Учебник по использованию Web3.py в Edgeware

{% content-ref url="../../tutorials/deploy-an-evm-contract/using-web3.py.md" %}
[using-web3.py.md](../../tutorials/deploy-an-evm-contract/using-web3.py/)
{% endcontent-ref %}
