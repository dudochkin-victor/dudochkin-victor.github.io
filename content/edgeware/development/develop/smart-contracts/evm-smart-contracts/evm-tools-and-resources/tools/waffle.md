+++
title = "Waffle"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

![](../../../../../../../.gitbook/assets/wafflelogo.png)

[Waffle](https://www.getwaffle.io) — популярная среда разработки для тестирования смарт-контрактов Solidity. Поскольку Edgeware совместим с Ethereum, с несколькими дополнительными строками конфигурации вы можете использовать Edgeware, как обычно, с Ethereum для разработки на Edgeware.

### Настройте Waffle для подключения к Edgware <a href="configure-waffle-to-connect-to-moonbeam" id="configure-waffle-to-connect-to-moonbeam"></a>

Предполагая, что у вас уже есть проект JavaScript или TypeScript, установите Waffle:

```
npm install ethereum-waffle
```

Чтобы настроить Waffle для запуска тестов на узле разработки Edgeware или в тестовой сети Edgeware Beresheet, в рамках ваших тестов создайте собственного поставщика и добавьте сетевые конфигурации:

{% tabs %}
{% tab title="Javascript" %}
describe ('Test Contract', () => { // Use custom provider to connect to Beresheet or Edgeware development node const BeresheetProvider = new ethers.providers.JsonRpcProvider(('[https://beresheet2.edgewa.re/evm](https://beresheet2.edgewa.re/evm)')(Alternatively, one can use [https://beresheetX.edgewa.re/evm](https://beresheetx.edgewa.re/evm) where X can be any number from 1 to 8.); 

const devProvider = new ethers.providers.JsonRpcProvider('[http://localhost:9933/](http://localhost:9933)'); })
{% endtab %}

{% tab title="Typescript" %}
describe ('Test Contract', () => { // Use custom provider to connect to Beresheet or Edgeware development node const BeresheetProvider: Provider = new ethers.providers.JsonRpcProvider(('[https://beresheet2.edgewa.re/evm](https://beresheet2.edgewa.re/evm)')(Alternatively, one can use [https://beresheetX.edgewa.re/evm](https://beresheetx.edgewa.re/evm) where X can be any number from 1 to 8.); 

const devProvider: Provider = new ethers.providers.JsonRpcProvider('[http://localhost:9933/](http://localhost:9933)'); })
{% endtab %}
{% endtabs %}
