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

- **ResilientDB**: A running instance with the smart contracts service enabled. [Setup Instructions](#)
- **Smart Contracts CLI**: A running instance of the CLI tool. [Setup Instructions](#)

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

Your server will be up and running on port 4000. Access the GraphQL API at http://localhost:4000/graphql.

## Exploring the GraphQL API
Our API supports several operations to manage and interact with smart contracts. Here’s a look at what you can do:

**Create Account**
Generate a new account using a configuration file.

```graphql
{
  createAccount(config: "path/to/config/file")
}
```

**Compile Contract**
Compile a smart contract from a source file and save the output.

```graphql
{
  compileContract(sourcePath: "path/to/source/file", outputPath: "path/to/output/file")
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
    contract: "contractName",
    function: "functionName",
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

- Compile Contract:
```graphql
{
  compileContract(sourcePath: "contracts/MyContract.sol", outputPath: "build/MyContract.json")
}
```

- Deploy Contract:
```graphql
{
  deployContract(
    config: "incubator-resilientdb/service/tools/config/interface/service.config",
    contract: "build/MyContract.json",
    name: "MyContract",
    arguments: "1000",
    owner: "0x1be8e78d765a2e63339fc99a66320db73158a35a"
  )
}
```

- Execute Contract:
```graphql
{
  executeContract(
    config: "incubator-resilientdb/service/tools/config/interface/service.config",
    sender: "0x1be8e78d765a2e63339fc99a66320db73158a35a",
    contract: "MyContract",
    function: "transfer",
    arguments: "0xRecipientAddress,100"
  )
}
```

## Conclusion

The Smart Contracts GraphQL API simplifies the process of interacting with smart contracts on ResilientDB. By following this guide, you can set up the API and start making API calls to manage and execute your smart contracts efficiently. If you have any questions or run into any issues, don’t hesitate to reach out for support.

Happy coding! 🚀