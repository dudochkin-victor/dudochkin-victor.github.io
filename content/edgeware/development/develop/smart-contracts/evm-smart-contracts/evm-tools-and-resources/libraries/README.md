+++
title = "Библиотеки Ethereum"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

>**Действовать с осторожностью! Эта страница находится в разработке.** :construction\_site:

Чтобы веб-приложение могло взаимодействовать с блокчейном Ethereum (т. е. считывать данные блокчейна и/или отправлять транзакции в сеть), оно должно подключаться к узлу Ethereum.

С этой целью каждый клиент Ethereum реализует спецификацию [JSON-RPC](https://ethereum.org/en/developers/docs/apis/json-rpc/), поэтому существует единый набор [конечных точек](https ://ethereum.org/en/developers/docs/apis/json-rpc/endpoints/), на которые могут положиться приложения.

Если вы хотите использовать JavaScript для подключения к узлу Ethereum, можно использовать ванильный JavaScript, но в экосистеме существует несколько удобных библиотек, которые значительно упрощают эту задачу. С помощью этих библиотек разработчики могут писать интуитивно понятные однострочные методы для инициализации JSON RPC-запросов (внутренне), которые взаимодействуют с Ethereum.

**Зачем использовать библиотеку?**

Эти библиотеки абстрагируются от большей части сложности прямого взаимодействия с узлом Ethereum. Они также предоставляют вспомогательные функции (например, преобразование ETH в Gwei), поэтому как разработчик вы можете тратить меньше времени на тонкости работы с клиентами Ethereum и больше времени на уникальную функциональность своего приложения.

Для получения дополнительной информации о [библиотеках Ethereum нажмите здесь](https://ethereum.org/en/developers/docs/apis/javascript/)

### Ресурсы

{% content-ref url="web3.js.md" %}
[web3.js.md](web3.js/)
{% endcontent-ref %}

{% content-ref url="ethers.js.md" %}
[ethers.js.md](ethers.js/)
{% endcontent-ref %}

{% content-ref url="web3.py.md" %}
[web3.py.md](web3.py/)
{% endcontent-ref %}

### Учебники по использованию библиотек с Edgeware

{% content-ref url="../../tutorials/deploy-an-evm-contract/using-web3.md" %}
[using-web3.md](../../tutorials/deploy-an-evm-contract/using-web3/)
{% endcontent-ref %}

{% content-ref url="../../tutorials/deploy-an-evm-contract/using-web3.py.md" %}
[using-web3.py.md](../../tutorials/deploy-an-evm-contract/using-web3.py/)
{% endcontent-ref %}

{% content-ref url="../../tutorials/deploy-an-evm-contract/using-ethers.js.md" %}
[using-ethers.js.md](../../tutorials/deploy-an-evm-contract/using-ethers.js/)
{% endcontent-ref %}

### Руководства по документации официальной библиотеки

{% embed url="https://web3js.readthedocs.io/en/v1.5.2/" %}
Официальная документация - web3.js
{% endembed %}

{% embed url="https://web3py.readthedocs.io/en/stable/" %}
Официальная документация - web3.py
{% endembed %}

{% embed url="https://docs.ethers.io/v5/" %}
Официальная документация - ethers.js
{% endembed %}
