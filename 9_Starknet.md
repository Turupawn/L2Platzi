https://dev.to/turupawn/este-es-el-futuro-de-ethereum-7m7



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

```bash
export STARKNET_WALLET="starkware.starknet.wallets.open_zeppelin.OpenZeppelinAccount"
starknet deploy_account
```

```bash
starknet-compile contract.cairo --output contract_compiled.json --abi contract_abi.json
export STARKNET_NETWORK=alpha-goerli
starknet deploy --contract contract_compiled.json --no_wallet
```


```bash
starknet invoke --address 0x06f9e8be19b746f6a25c0b329f554fc604d1e7cd17c146bfb223c854845a47df --abi contract_abi.json --function increase_balance --inputs 1234
starknet call --address 0x06f9e8be19b746f6a25c0b329f554fc604d1e7cd17c146bfb223c854845a47df --abi contract_abi.json --function get_balance
```
