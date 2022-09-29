## Notas: Lanzar un token en Capa 2

1. Dirígete al sitio web de [Remix](https://remix.ethereum.org/)

2. Coloca el código de un token ERC20
```solidity
// SPDX-License-Identifier: MIT
pragma solidity 0.8.17;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract SimpleToken is ERC20 {
    constructor(
        string memory name,
        string memory symbol,
        uint256 initialSupply
    ) ERC20(name, symbol) {
        _mint(msg.sender, initialSupply * 1 ether);
    }
}
```

3. Verifica el token en [Arbiscan Testnet](https://testnet.arbiscan.io/) o [Goerli Optimism Scan](https://goerli-optimism.etherscan.io/)
