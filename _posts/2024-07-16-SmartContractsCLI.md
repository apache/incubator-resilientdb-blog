---
layout: article
title: Getting Started with ResContract CLI 🚀
author: gopal
tags: ResilientDB, ResContract CLI, Smart Contracts, CLI
aside:
  toc: true
article_header:
  type: overlay
  theme: dark
  background_color: '#000000'
  background_image:
    gradient: 'linear-gradient(135deg, rgba(0, 204, 154 , .2), rgba(51, 154, 154, .2))'
    src: /assets/images/resdb-gettingstarted/code_close_up.jpeg
---

**This guide provides a comprehensive walkthrough of the ResContract CLI for managing smart contracts on ResilientDB. It covers installation, configuration, and usage of various commands to create accounts, compile contracts, deploy contracts, and execute contract functions.**

[ResContract CLI GitHub Repository](https://github.com/ResilientEcosystem/ResContract)

---

## 🌐 Smart Contracts on ResilientDB Today

ResilientDB now supports smart contracts, allowing developers to deploy and interact with decentralized applications seamlessly. Here's an overview of the current state:

- **Simplified Workflow**: The ResContract CLI provides an intuitive interface for managing smart contracts.
- **Comprehensive Commands**: It includes commands for account creation, contract compilation, deployment, and execution.
- **Enhanced Logging**: Improved logging for better debugging and auditing.

---

## 📈 Getting Started with ResContract CLI

### **Prerequisites**

Before you begin, ensure you have the following installed:

- **Node.js (version >= 14)**: [Download and install Node.js](https://nodejs.org/en/download/)
- **npm**: Comes with Node.js. Ensure it's up-to-date.
- **Solidity Compiler (`solc`)**: Required to compile smart contracts. Install it using:

  ```bash
  npm install -g solc
  ```

- **ResilientDB**: A running instance with the smart contracts service enabled.

## Installing ResContract CLI
### Install the ResContract CLI globally using npm:

```bash
npm install -g rescontract-cli
```

## Configuration

Before using the ResContract CLI, you must set the ResDB_Home environment variable or provide the path to your ResilientDB installation in a config.yaml file.

**Option 1: Set ResDB_Home Environment Variable**

Set the ResDB_Home environment variable to point to the directory where ResilientDB is installed.

Linux/macOS:

```bash
export ResDB_Home=/path/to/incubator-resilientdb
```

Add the above line to your .bashrc or .zshrc file to make it persistent.

**Option 2: Use a config.yaml File**

Create a `config.yaml`file in the same directory where you run the `rescontract` command or in your home directory.

Example `config.yaml`:

```yaml
ResDB_Home: /path/to/incubator-resilientdb
```
Ensure the `ResDB_Home` path is correct.

Note: The CLI checks for config.yaml in the current directory first, then in your home directory.

## 👨🏻‍💻 Using the ResContract CLI

### Command Overview
- `create`: Create a new account.
- `compile`: Compile a Solidity contract.
- `deploy`: Deploy a smart contract.
- `execute`: Execute a function within a deployed smart contract.

#### Creating a New Account

Command:
```bash
rescontract create --config <path_to_config>
```

Example:
```bash
rescontract create --config ../incubator-resilientdb/service/tools/config/interface/service.config
```

Sample Output:
```bash
0x3b706424119e09dcaad4acf21b10af3b33cde350
```

#### Compiling a Smart Contract

Command:
```bash
rescontract compile --sol <inputFile.sol> --output <outputFile.json>
```

Example:
```bash
rescontract compile --sol /users/gopuman/ResContract/token.sol --output output.json
```

Sample Output:
```json
Compiled successfully to output.json
```

#### Deploying a Smart Contract

Command:
```bash
rescontract deploy --config <configPath> --contract <contract.json> \
--name <contractName> --arguments "<parameters>" --owner <ownerAddress>
```

Example:
```bash
rescontract deploy \
  --config ../incubator-resilientdb/service/tools/config/interface/service.config \
  --contract output.json \
  --name "/users/gopuman/ResContract/token.sol:Token" \
  --arguments "1000" \
  --owner "0x3b706424119e09dcaad4acf21b10af3b33cde350"
```

Sample Output:
```vbnet
owner_address: "0x3b706424119e09dcaad4acf21b10af3b33cde350"
contract_address: "0xc975ab41e0c2042a0229925a2f4f544747fd66cd"
contract_name: "/users/gopuman/ResContract/token.sol:Token"
```

#### Executing a Smart Contract Function

Command:
```bash
rescontract execute --config <configPath> --sender <senderAddress> \
--contract <contractAddress> --function-name "<functionSignature>" --arguments "<parameters>"
```

Example:
```bash
rescontract execute \
  --config ../incubator-resilientdb/service/tools/config/interface/service.config \
  --sender "0x3b706424119e09dcaad4acf21b10af3b33cde350" \
  --contract "0xc975ab41e0c2042a0229925a2f4f544747fd66cd" \
  --function-name "transfer(address,uint256)" \
  --arguments "0x4847155cbb6f2219ba9b7df50be11a1c7f23f829,100"
```

Sample Output:
```json
Function executed successfully.
```

## 📝 Advanced Usage and Tips

- **Logging**: The ResContract CLI logs important events and errors to ~/.rescontract-logs/cli.log.
- **Error Handling**: If you encounter errors, check the logs for detailed information.
- **Permissions**: Ensure you have the necessary permissions to execute commands and access files.
- **Updating ResContract CLI**: Keep your CLI up-to-date by running:

```bash
npm update -g rescontract-cli
```

## 🤝 Contributing

We welcome contributions! Please read our Contributing Guidelines to get started.

## 📄 License

This project is licensed under the Apache License

## 📚 Additional Resources

- ResilientDB Documentation: [ResilientDB GitHub Repository](https://github.com/apache/incubator-resilientdb)
- Solidity Documentation: [Solidity Official Site](https://docs.soliditylang.org/en/v0.8.27/)