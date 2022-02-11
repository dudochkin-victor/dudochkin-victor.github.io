+++
title = "ERC20 на WASM"
sort_by = "weight"
updated = 2022-10-02T15:00:00Z
+++

## Введение <a id="introduction"></a>

В этой главе мы покажем вам, как вы можете создать контракт токена ERC20 с помощью ink!.

В ходе главы мы рассмотрим:

- Первоначальная чеканка токенов
- Переводы токенов
- Утверждения и передача третьей стороне
- Выдача событий времени выполнения через Substrate

Но сначала мы рассмотрим стандарт ERC20 для тех из вас, кто не знаком с ним.

### Стандарт ERC20 <a id="erc20-standard"></a>

[Стандарт токена ERC20](https://eips.ethereum.org/EIPS/eip-20) определяет интерфейс для самого популярного смарт-контракта Ethereum.

```javascript
// ----------------------------------------------------------------------------
// ERC Token Standard #20 Interface
// https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20.md
// ----------------------------------------------------------------------------

contract ERC20Interface {
    // Storage Getters
    function totalSupply() public view returns (uint);
    function balanceOf(address tokenOwner) public view returns (uint balance);
    function allowance(address tokenOwner, address spender) public view returns (uint remaining);

    // Public Functions
    function transfer(address to, uint tokens) public returns (bool success);
    function approve(address spender, uint tokens) public returns (bool success);
    function transferFrom(address from, address to, uint tokens) public returns (bool success);

    // Contract Events
    event Transfer(address indexed from, address indexed to, uint tokens);
    event Approval(address indexed tokenOwner, address indexed spender, uint tokens);
}Copy to clipboardErrorCopied
```

Таким образом, это позволяет людям развертывать свою собственную криптовалюту поверх существующей платформы смарт-контрактов. В этом контракте не происходит много волшебства. Балансы пользователей хранятся в HashMap, и создан набор API-интерфейсов, позволяющих пользователям передавать токены, которыми они владеют, или позволять третьей стороне передавать некоторое количество токенов от их имени. Самое главное, что вся эта логика реализована таким образом, чтобы средства не создавались и не уничтожались непреднамеренно, а средства пользователя были защищены от злоумышленников.

Обратите внимание, что все общедоступные функции возвращают `bool`, который указывает, был ли вызов успешным или нет. Мы будем придерживаться этой спецификации.
