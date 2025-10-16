---
layout: article
title: Dive into Smart Contracts with GraphQL API 🌐
author: gopal
tags: ResilientDB SmartContracts GraphQL API
aside:
   toc: true
article_header:
  type: overlay
  theme: dark
  background_color: '#000000'
  background_image:
    gradient: 'linear-gradient(135deg, rgba(0, 204, 154, .2), rgba(51, 154, 154, .2))'
    src: /assets/images/resdb-orm/database_code.jpeg
---

Unlock the power of smart contracts with our GraphQL API. This guide will walk you through setting up and using the API to interact with smart contracts seamlessly.

[Smart Contracts GraphQL API](https://github.com/ResilientEcosystem/smart-contracts-graphql)

## Introduction

Welcome to the world of smart contracts on ResilientDB! Our GraphQL API provides a streamlined way to interact with smart contracts, making it easier than ever to create, deploy, and execute contracts. Whether you’re a seasoned developer or just getting started, this guide will help you navigate the setup and usage of the Smart Contracts GraphQL API.

## Prerequisites

Before diving in, ensure you have the following:

1. **ResilientDB**: A running instance of ResilientDB with the smart contracts service running. More information and setup instructions can be found here: [ResilientDB](https://github.com/apache/incubator-resilientdb)

2. **ResContract CLI**: Install the `rescontract-cli` tool globally. Follow the instructions in the [ResContract CLI Repository](https://github.com/apache/incubator-resilientdb-ResContract) to install and configure it.

   ```bash
   npm install -g rescontract-cli
   ```

3. **Node.js (version >= 15.6.0)**: Download and install Node.js version 15.6.0 or higher, as the application uses crypto.randomUUID() which was introduced in Node.js v15.6.0.

   ```bash
   # You can check your Node.js version with:
   node -v
   ```

**The prerequisites listed above can be installed using the `INSTALL.sh` script:**

   ```bash
   chmod +x INSTALL.sh
   ./INSTALL.sh
   ```

Check out this blog post to find out more about Smart Contracts on ResilientDB - [Getting Started with Smart Contract on Nexres](https://blog.resilientdb.com/2023/01/15/GettingStartedSmartContract.html)

## Setting Up the GraphQL API

### Step 1: Clone the Repository

Start by cloning the repository to your local machine:

```bash
git clone https://github.com/yourusername/smart-contracts-graphql.git
cd smart-contracts-graphql
```

### Step 2: Install Dependencies

Install the necessary dependencies using npm:

```bash
npm install
```

Step 3: Start the Server

Launch the GraphQL API server:

```bash
node server.js
```

Your server will be up and running on port 8400. Access the GraphQL API at http://localhost:8400/graphql.

## Exploring the GraphQL API
Our API supports several operations to manage and interact with smart contracts. Here’s a look at what you can do:

**Create Account**
Generate a new account using a configuration file.

```graphql
{
  createAccount(config: "path/to/config/file")
}
```

**Add Address**
Add an existing address to the configuration.

```graphql
{
  addAddress(
    config: "path/to/config/file",
    address: "0xAddressToAdd"
  )
}
```

**Compile Contract**
Compile a smart contract from a source file and save the output.

```graphql
{
  compileContract(source: "path/to/source/file")
}
```

**Deploy Contract**
Deploy a compiled smart contract with specified parameters.

```graphql
{
  deployContract(
    config: "path/to/config/file",
    contract: "path/to/contract/file",
    name: "contractName",
    arguments: "constructorArguments",
    owner: "ownerAddress"
  )
}
```

**Execute Contract**
Execute a function of a deployed smart contract.

```graphql
{
  executeContract(
    config: "path/to/config/file",
    sender: "senderAddress",
    contract: "contractAddress",
    functionName: "functionName",
    arguments: "functionArguments"
  )
}
```

## Sample Queries
Here are some practical examples to get you started:

- Create Account:
```graphql
{
  createAccount(config: "incubator-resilientdb/service/tools/config/interface/service.config")
}
```

- Add Address:
```graphql
{
  addAddress(
    config: "incubator-resilientdb/service/tools/config/interface/service.config",
    address: "0x67c6697351ff4aec29cdbaabf2fbe3467cc254f8"
  )
}
```

- Compile Contract:
```graphql
{
  compileContract(source: "token.sol")
}
```

- Deploy Contract:
```graphql
{
  deployContract(
    config: "incubator-resilientdb/service/tools/config/interface/service.config",
    contract: "compiled_contracts/MyContract.json",
    name: "token.sol:Token",
    arguments: "1000",
    owner: "0x67c6697351ff4aec29cdbaabf2fbe3467cc254f8"
  )
}
```

- Execute Contract:
```graphql
{
  executeContract(
    config: "incubator-resilientdb/service/tools/config/interface/service.config",
    sender: "0x67c6697351ff4aec29cdbaabf2fbe3467cc254f8",
    contract: "0xfc08e5bfebdcf7bb4cf5aafc29be03c1d53898f1",
    functionName: "transfer(address,uint256)",
    arguments: "0x1be8e78d765a2e63339fc99a66320db73158a35a,100"
  )
}
```

## Advanced Usage with Type Parameter

The API supports both "path" and "data" types for all mutations. The `type` parameter defaults to "path" and doesn't have to be explicitly set.

### Using type "path" (Default)
When using file paths, you don't need to specify the type parameter:

```graphql
mutation {
  createAccount(config: "../incubator-resilientdb/service/tools/config/interface/service.config")
}
```

### Using type "data"
When passing configuration data directly, specify `type: "data"`:

```graphql
mutation {
  createAccount(
    config: "5 127.0.0.1 10005",
    type: "data"
  )
}
```

**Note**: The response formats differ between "path" and "data" types:
- **"path" type**: Returns structured JSON objects
- **"data" type**: Returns string responses with newlines

## Sample Responses

Here are the expected responses for each mutation:

**Create Account Response:**
```json
{
  "data": {
    "createAccount": "0x67c6697351ff4aec29cdbaabf2fbe3467cc254f8"
  }
}
```

**Add Address Response:**
```json
{
  "data": {
    "addAddress": "Address added successfully"
  }
}
```

**Compile Contract Response:**
```json
{
  "data": {
    "compileContract": "Compiled successfully to /users/yourusername/smart-contracts-graphql/compiled_contracts/MyContract.json"
  }
}
```

**Deploy Contract Response:**
```json
{
  "data": {
    "deployContract": {
      "ownerAddress": "0x67c6697351ff4aec29cdbaabf2fbe3467cc254f8",
      "contractAddress": "0xfc08e5bfebdcf7bb4cf5aafc29be03c1d53898f1",
      "contractName": "token.sol:Token"
    }
  }
}
```

**Execute Contract Response:**
```json
{
  "data": {
    "executeContract": "Execution successful"
  }
}
```

## Conclusion

The Smart Contracts GraphQL API simplifies the process of interacting with smart contracts on ResilientDB. By following this guide, you can set up the API and start making API calls to manage and execute your smart contracts efficiently. If you have any questions or run into any issues, don’t hesitate to reach out for support.

Happy coding! 🚀