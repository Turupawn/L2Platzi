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
Cover image for Este es el futuro de Ethereum
EditManageStats
Ahmed Castro
Ahmed Castro
Posted on 3 ene • Updated on 27 feb

Este es el futuro de Ethereum
#
solidity
#
cairo
#
blockchain
#
starknet
Es muy importante estar actualizados con el blockchain, por eso en este video exploramos los ZK-Rollups. Lanzaremos e interactuaremos con un smart contract en Starknet, una solución de ethereum L2 que provee escalabilidad a nivel de protocolo. Starknet cuenta con su propio lenguaje basado en Cairo, conserva muchas similitudes con Solidity que veremos a continuación.


Dependencias
Antes de comenzar debemos instalar las dependencias.
apt install -y python3.8-venv libgmp3-dev
sudo apt install python3.8-venv
python3.8 -m venv ~/cairo_venv
source ~/cairo_venv/bin/activate
pip3 install cairo-lang
Lanzar smart contract
Creamos un archivo y escribimos el contrato usando el lenguaje StarkNet.

contract.cairo
# Declare this file as a StarkNet contract and set the required
# builtins.
%lang starknet
%builtins pedersen range_check

from starkware.cairo.common.cairo_builtins import HashBuiltin

# Define a storage variable.
@storage_var
func balance() -> (res : felt):
end

# Increases the balance by the given amount.
@external
func increase_balance{
        syscall_ptr : felt*, pedersen_ptr : HashBuiltin*,
        range_check_ptr}(amount : felt):
    let (res) = balance.read()
    balance.write(res + amount)
    return ()
end

# Returns the current balance.
@view
func get_balance{
        syscall_ptr : felt*, pedersen_ptr : HashBuiltin*,
        range_check_ptr}() -> (res : felt):
    let (res) = balance.read()
    return (res)
end
Compilamos el contrato.
starknet-compile contract.cairo --output contract_compiled.json --abi contract_abi.json
Establecemos que estaremos deployando nuestro contrato en el testnet.
export STARKNET_NETWORK=alpha-goerli
Lo lanzamos.
starknet deploy --contract contract_compiled.json
Probarlo
Ejecutamos la función increase_balance, asegúrate de cambiar el address por el que devolvió el comando anterior.
starknet invoke \
    --address 0x0000000000000000000000000000000000000000000000000000000000000000 \
    --abi contract_abi.json \
    --function increase_balance \
    --inputs 1234
Observamos el estado de la transacción. Asegúrate de cambar el tx hash por el que devolvió el comando anterior.
starknet tx_status --hash 0x0000000000000000000000000000000000000000000000000000000000000000
Una vez que el estado de la transacción sea ACCEPTED_ON_L2 ejecutamos obtenemos el comando de la siguiente manera.
starknet call \
    --address 0x0000000000000000000000000000000000000000000000000000000000000000 \
    --abi contract_abi.json \
    --function get_balance
Bono: Cairo vs Solidity
¿Te preguntás cómo se miraría este contrato si lo escribimos en Soldity? Aquí te lo dejo.
// SPDX-License-Identifier: MIT
pragma solidity 0.8.10;

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
