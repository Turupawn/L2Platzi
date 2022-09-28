1. Contrato en arbitrum testnet

```solidity
// SPDX-License-Identifier: MIT
pragma solidity 0.8.17;

contract HelloWorld {
    string public hello = "Hola mundo!";

    function setHello(string memory hello_) public
    {
        hello = hello_;
    }
}
```

2. Contrato en Rinkeby Testnet

```solidity
// SPDX-License-Identifier: MIT
pragma solidity  0.8.17;

interface IInbox {
    function createRetryableTicket(
        address destAddr,
        uint256 l2CallValue,
        uint256 maxSubmissionCost,
        address excessFeeRefundAddress,
        address callValueRefundAddress,
        uint256 maxGas,
        uint256 gasPriceBid,
        bytes calldata data
    ) external payable returns (uint256);
}

interface IHelloWorld {
    function setHello(string memory hello_) external;
}

contract L2Operator {
    IInbox inbox = IInbox(0x578BAde599406A8fE3d24Fd7f7211c0911F5B29e);

    function setHelloInL2(
        address l2ContractAddress,
        string memory _hello,
        uint256 maxSubmissionCost,
        uint256 maxGas,
        uint256 gasPriceBid
    ) public payable returns (uint256) {
        bytes memory data =
            abi.encodeWithSelector(IHelloWorld.setHello.selector, _hello);
        uint256 ticketID = inbox.createRetryableTicket{value: msg.value}(
            l2ContractAddress,
            0,
            maxSubmissionCost,
            msg.sender,
            msg.sender,
            maxGas,
            gasPriceBid,
            data
        );
        return ticketID;
    }
}
```

3. Parametros

l2ContractAddress: ADDRESS DE CONTRATO EN L2
_hello: ¡Hemos ejecutado esto desde L1!
maxSubmissionCost: 80000000000
maxGas: 90000000
gasPriceBid: 90000000

recuerda enviar 0.01 ether para el gas
