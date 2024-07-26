# Basic Staking dApp

This is a basic staking decentralized application (dApp) that allows users to stake, unstake, and claim rewards. The dApp interacts with smart contracts on the Core Testnet.

## Features

- **Stake:** Users can stake tokens into the contract.
- **Unstake:** Users can unstake their tokens.
- **Claim Rewards:** Users can claim their staking rewards.

## Prerequisites

- Node.js
- npm or yarn
- MetaMask (configured for Core Testnet)

## Installation

1. **Clone the Repository:**

   ```sh
   git clone https://github.com/yourusername/staking-dapp.git
   cd staking-dapp
   ```

2. **Install Dependencies:**

   ```sh
   npm install
   # or
   yarn install
   ```

3. **Set Up Environment Variables:**

   Create a `.env` file in the root directory and add your Core Testnet private key and Infura project ID (or other provider endpoint).

   ```sh
   CORE_PRIVATE_KEY=your-core-testnet-private-key
   CORE_INFURA_PROJECT_ID=your-infura-project-id
   ```

4. **Deploy Smart Contract:**

   Compile and deploy the smart contract to the Core Testnet.

   ```sh
   npx hardhat compile
   npx hardhat run scripts/deploy.js --network coreTestnet
   ```

5. **Configure Frontend:**

   Update the frontend configuration with your deployed contract address in `src/config.js`.

   ```js
   export const STAKING_CONTRACT_ADDRESS = "your-deployed-contract-address";
   export const CORE_TESTNET_RPC_URL = "https://core-testnet.infura.io/v3/your-infura-project-id";
   ```

## Usage

1. **Run the dApp:**

   Start the frontend application.

   ```sh
   npm start
   # or
   yarn start
   ```

2. **Interact with the dApp:**

   - Open the dApp in your browser.
   - Connect your MetaMask wallet (configured for Core Testnet).
   - Use the interface to stake, unstake, and claim rewards.

## Smart Contract

The smart contract for this dApp is a simple staking contract written in Solidity. It allows users to stake Ether, track balances, and claim rewards. Here is the contract code:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Staking {
    mapping(address => uint256) public balances;
    mapping(address => uint256) public rewards;
    uint256 public rewardRate = 100; // Example reward rate

    function stake() external payable {
        require(msg.value > 0, "Cannot stake 0");
        balances[msg.sender] += msg.value;
        rewards[msg.sender] += msg.value * rewardRate / 100;
    }

    function unstake(uint256 _amount) external {
        require(_amount > 0 && _amount <= balances[msg.sender], "Invalid amount");
        balances[msg.sender] -= _amount;
        payable(msg.sender).transfer(_amount);
    }

    function claimRewards() external {
        uint256 reward = rewards[msg.sender];
        rewards[msg.sender] = 0;
        payable(msg.sender).transfer(reward);
    }
}
```

## Frontend Integration

The frontend interacts with the smart contract using the ethers.js library. Here are the basic functions for staking, unstaking, and claiming rewards:

```js
import { ethers } from "ethers";
import { STAKING_CONTRACT_ADDRESS, CORE_TESTNET_RPC_URL } from "./config";
import StakingABI from "./abis/Staking.json";

const provider = new ethers.providers.JsonRpcProvider(CORE_TESTNET_RPC_URL);
const signer = provider.getSigner();
const stakingContract = new ethers.Contract(STAKING_CONTRACT_ADDRESS, StakingABI, signer);

export const stake = async (amount) => {
    const tx = await stakingContract.stake({ value: ethers.utils.parseEther(amount) });
    await tx.wait();
};

export const unstake = async (amount) => {
    const tx = await stakingContract.unstake(ethers.utils.parseEther(amount));
    await tx.wait();
};

export const claimRewards = async () => {
    const tx = await stakingContract.claimRewards();
    await tx.wait();
};
```

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## Contributing

Contributions are welcome! Please open an issue or submit a pull request for any improvements or bug fixes.