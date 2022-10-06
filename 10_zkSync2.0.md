1. Obtener fondos en zkSync 2.0

https://portal.zksync.io/bridge

2. Preparar el repositorio

```bash
npm install --global yarn
mkdir ejemploZkSync2
cd ejemploZkSync2
yarn init -y
yarn add -D typescript ts-node ethers zksync-web3 hardhat @matterlabs/hardhat-zksync-solc @matterlabs/hardhat-zksync-deploy dotenv @openzeppelin/contracts
```

3. Archivo de configuración de Hardhat

`hardhat.config.ts`
```ts
require("@matterlabs/hardhat-zksync-deploy");
require("@matterlabs/hardhat-zksync-solc");
import dotenv from "dotenv"

dotenv.config()

module.exports = {
  zksolc: {
    version: "1.1.5",
    compilerSource: "docker",
    settings: {
      optimizer: {
        enabled: true,
      },
      experimental: {
        dockerImage: "matterlabs/zksolc",
        tag: "v1.1.5"
      }
    },
  },
  zkSyncDeploy: {
    zkSyncNetwork: "https://zksync2-testnet.zksync.dev",
    ethNetwork: process.env.RPC_URL,
  },
  networks: {
    hardhat: {
      zksync: true,
    },
  },
  solidity: {
    version: "0.8.16",
  },
};
```

4. Establecer variables de entorno

`.env`
```bash
PRIVATE_KEY=TULLAVEPRIVADAAQUI
RPC_URL=https://goerli.infura.io/v3/TULLAVEPRIVADAAQUI
```

5. Agregar el Smart Contract

`contracts/SimpleToken.sol`
```solidity
// SPDX-License-Identifier: MIT
pragma solidity 0.8.16;

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

6. Archivo de deploy

`deploy/deploy.ts`
```ts
import { utils, Wallet } from "zksync-web3";
import * as ethers from "ethers";
import { HardhatRuntimeEnvironment } from "hardhat/types";
import { Deployer } from "@matterlabs/hardhat-zksync-deploy";
import dotenv from "dotenv"
import * as zksync from "zksync-web3";

dotenv.config()

export default async function (hre: HardhatRuntimeEnvironment) {
  console.log("Imprimiendo en la pantalla tu balance en L2...")
  const ethProvider = ethers.getDefaultProvider("goerli");
  const syncProvider = new zksync.Provider("https://zksync2-testnet.zksync.dev");
  const syncWallet = new zksync.Wallet(process.env.PRIVATE_KEY, syncProvider, ethProvider);
  console.log("Tu balance en Layer 2: " + ethers.utils.formatEther(await syncWallet.getBalance(zksync.utils.ETH_ADDRESS)))

  console.log("Lanzando contrato en L2");
  const wallet = new Wallet(process.env.PRIVATE_KEY);
  const deployer = new Deployer(hre, wallet);
  const artifact = await deployer.loadArtifact("SimpleToken");
  const simpleTokenContract = await deployer.deploy(artifact, ["Mi Token", "MTKN", "1000000"]);

  console.log(`${artifact.contractName} ha sido lanzado en ${simpleTokenContract.address}`);
}
```

7. Lanzamiento

```bash
yarn hardhat deploy-zksync
```

8. zkSync Scan

https://zksync2-testnet.zkscan.io/
