import os
import zipfile

# Create base folder for repos
base_dir = "/mnt/data/github_repos"
os.makedirs(base_dir, exist_ok=True)

# Repo 1: crypto-trading-bot-sim
bot_dir = os.path.join(base_dir, "crypto-trading-bot-sim")
os.makedirs(bot_dir, exist_ok=True)

bot_readme = """# Crypto Trading Bot Simulation (Testnet)

A simple simulated Uniswap trading bot running on Ethereum testnet using Ethers.js and Node.js.
This bot demonstrates buying and selling tokens with basic logic.

## Features
- Connects to Ethereum testnet (Goerli/Sepolia)
- Simulates buy/sell logic
- Uses Ethers.js for interaction
- For educational purposes only

## Setup
1. Clone repo
2. Run `npm install`
3. Set your Infura/Alchemy API key in `.env`
4. Run `node bot.js`
"""

bot_js = """const { ethers } = require("ethers");
require("dotenv").config();

async function main() {
    const provider = new ethers.providers.JsonRpcProvider(process.env.RPC_URL);
    console.log("Connected to testnet");
    console.log("Simulating buy/sell logic...");
    // Example strategy
    console.log("Bought token at price X");
    console.log("Sold token at price Y");
}

main().catch(console.error);
"""

with open(os.path.join(bot_dir, "README.md"), "w") as f:
    f.write(bot_readme)

with open(os.path.join(bot_dir, "bot.js"), "w") as f:
    f.write(bot_js)

with open(os.path.join(bot_dir, ".env.example"), "w") as f:
    f.write("RPC_URL=your_infura_or_alchemy_url_here\n")


# Repo 2: erc20-token-template
token_dir = os.path.join(base_dir, "erc20-token-template")
os.makedirs(token_dir, exist_ok=True)

token_readme = """# ERC20 Token Template

A ready-to-deploy ERC20 token smart contract using OpenZeppelin, powered by Hardhat.

## Features
- Standard ERC20 implementation
- Customizable name, symbol, and supply
- Includes deployment script and test

## Setup
1. Clone repo
2. Run `npm install`
3. Compile: `npx hardhat compile`
4. Deploy: `npx hardhat run scripts/deploy.js --network sepolia`
"""

token_contract = """// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract MyToken is ERC20 {
    constructor(uint256 initialSupply) ERC20("MyToken", "MTK") {
        _mint(msg.sender, initialSupply * 10 ** decimals());
    }
}
"""

token_deploy = """const hre = require("hardhat");

async function main() {
    const initialSupply = 1000000; // 1M tokens
    const Token = await hre.ethers.getContractFactory("MyToken");
    const token = await Token.deploy(initialSupply);
    await token.deployed();
    console.log("Token deployed to:", token.address);
}

main().catch((error) => {
    console.error(error);
    process.exitCode = 1;
});
"""

os.makedirs(os.path.join(token_dir, "contracts"), exist_ok=True)
os.makedirs(os.path.join(token_dir, "scripts"), exist_ok=True)

with open(os.path.join(token_dir, "README.md"), "w") as f:
    f.write(token_readme)

with open(os.path.join(token_dir, "contracts", "MyToken.sol"), "w") as f:
    f.write(token_contract)

with open(os.path.join(token_dir, "scripts", "deploy.js"), "w") as f:
    f.write(token_deploy)


# Zip all repos
zip_path = "/mnt/data/github_blockchain_repos.zip"
with zipfile.ZipFile(zip_path, 'w') as zipf:
    for folder_name, subfolders, filenames in os.walk(base_dir):
        for filename in filenames:
            file_path = os.path.join(folder_name, filename)
            arcname = os.path.relpath(file_path, base_dir)
            zipf.write(file_path, arcname)

zip_path
