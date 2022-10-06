1. Instala Docker

Puedes instalar Docker Desktop en Windows y MacOs desde [este enlace](https://www.docker.com/products/docker-desktop/).
O desde linux con los siguientes comandos:

```bash
sudo apt-get update
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
wget https://desktop.docker.com/linux/main/amd64/docker-desktop-4.8.2-amd64.deb
sudo apt install ./docker-desktop-4.8.2-amd64.deb
systemctl --user start docker-desktop
sudo usermod -aG docker $USER
```

Luego asegurate que tienes instalado docker correctamente ejecutando el siguiente comando:
```bash
systemctl start docker
```

1. Obten fondos en zkSync 2.0

https://portal.zksync.io/bridge

2. Prepara el repositorio

```bash
npm install --global yarn
mkdir ejemploZkSync2
cd ejemploZkSync2
yarn init -y
yarn add -D typescript ts-node ethers zksync-web3 hardhat @matterlabs/hardhat-zksync-solc @matterlabs/hardhat-zksync-deploy dotenv @openzeppelin/contracts
```

3. Crea el archivo de configuración de Hardhat

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

4. Establece las variables de entorno

`.env`
```bash
PRIVATE_KEY=TULLAVEPRIVADAAQUI
RPC_URL=https://goerli.infura.io/v3/TULLAVEPRIVADAAQUI
```

5. Agrega el Smart Contract

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

6. Crea el archivo de deploy

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

7. Compila y lanza el token

```bash
yarn hardhat compile
yarn hardhat deploy-zksync
```

8. Verifica tu lanzamiento desde zkScan

https://zksync2-testnet.zkscan.io/
