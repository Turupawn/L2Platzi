https://dev.to/turupawn/este-es-el-futuro-de-ethereum-7m7

1. Contrato en Cairo

`contract.cairo`
```cairo
// Declare this file as a StarkNet contract.
%lang starknet

from starkware.cairo.common.cairo_builtins import HashBuiltin

// Define a storage variable.
@storage_var
func balance() -> (res: felt) {
}

// Increases the balance by the given amount.
@external
func increase_balance{
    syscall_ptr: felt*,
    pedersen_ptr: HashBuiltin*,
    range_check_ptr,
}(amount: felt) {
    let (res) = balance.read();
    balance.write(res + amount);
    return ();
}

// Returns the current balance.
@view
func get_balance{
    syscall_ptr: felt*,
    pedersen_ptr: HashBuiltin*,
    range_check_ptr,
}() -> (res: felt) {
    let (res) = balance.read();
    return (res=res);
}
```

2. Crear una cuenta

```bash
starknet deploy_account
export STARKNET_WALLET="starkware.starknet.wallets.open_zeppelin.OpenZeppelinAccount"
```

3. Lanzar un contrato

```bash
starknet-compile contract.cairo --output contract_compiled.json --abi contract_abi.json
export STARKNET_NETWORK=alpha-goerli
starknet deploy --contract contract_compiled.json --no_wallet
```

4. Interacción con el contrato

```bash
starknet invoke --address ADDRESS_CONTRATO --abi contract_abi.json --function increase_balance --inputs 1234 --max_fee 25607578957226
starknet tx_status --hash HASH_DE_TRANSACCIÓN
starknet call --address ADDRESS_CONTRATO --abi contract_abi.json --function get_balance
```

Nota: El smart contract que lanzamos usando cairo tiene el mismo funcionamiento que el siguiente contrato en solidity

```solidity
// SPDX-License-Identifier: MIT
pragma solidity 0.8.17;

contract MyContract {
  uint balance;

  function increase_balance(uint amount) public
  {
    balance += amount;
  }

  function get_balance() returns(uint) public view
  {
    return balance;
  }
}
```
