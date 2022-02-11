+++
title = "Библиотеки Ethereum"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

>#### Действовать с осторожностью! Эта страница находится в разработке. 🏗


Чтобы веб-приложение могло взаимодействовать с блокчейном Ethereum \ (т. е. читать данные блокчейна и/или отправлять транзакции в сеть \), оно должно подключаться к узлу Ethereum.

С этой целью каждый клиент Ethereum реализует спецификацию [JSON-RPC](https://ethereum.org/en/developers/docs/apis/json-rpc/), поэтому существует единый набор [конечных точек](https ://ethereum.org/en/developers/docs/apis/json-rpc/endpoints/), на которые могут положиться приложения.

Если вы хотите использовать JavaScript для подключения к узлу Ethereum, можно использовать обычный JavaScript, но в экосистеме существует несколько удобных библиотек, которые значительно упрощают это. С помощью этих библиотек разработчики могут писать интуитивно понятные однострочные методы для инициализации JSON RPC-запросов \(под капотом\), которые взаимодействуют с Ethereum.

**Зачем использовать библиотеку?**

Эти библиотеки абстрагируются от большей части сложности прямого взаимодействия с узлом Ethereum. Они также предоставляют служебные функции \(например, преобразование ETH в Gwei\), поэтому как разработчик вы можете тратить меньше времени на тонкости работы с клиентами Ethereum и больше времени на уникальную функциональность своего приложения.

Для получения дополнительной информации о [библиотеках Ethereum нажмите здесь](https://ethereum.org/en/developers/docs/apis/javascript/)

### Использование библиотек с Edgeware

{% page-ref page="../tutorials/deploy-an-evm-contract/using-web3.md" %}

{% page-ref page="../tutorials/deploy-an-evm-contract/using-web3.py.md" %}

{% page-ref page="../tutorials/deploy-an-evm-contract/using-ethers.js.md" %}

### Руководства по документации официальной библиотеки

{% embed url="https://web3js.readthedocs.io/en/v1.5.2/" caption="Official documentation - web3.js" %}

{% embed url="https://web3py.readthedocs.io/en/stable/" caption="Official documentation - web3.py" %}

{% embed url="https://docs.ethers.io/v5/" caption="Official documentation - ethers.js" %}
