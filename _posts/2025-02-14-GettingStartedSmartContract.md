---
layout: article
title: Getting Started with Smart Contract on ResilientDB
author: Junchao Chen
tags: NexRes
aside:
    toc: true
article_header:
  type: overlay
  theme: dark
  background_color: '#000000'
  background_image:
    gradient: 'linear-gradient(135deg, rgba(0, 204, 154 , .2), rgba(51, 154, 154, .2))'
    src: /assets/images/resdb-gettingstarted/codecover.jpg

---

Here we illustrate how to run a smart contract on Nexres locally. We provide step-by-step tutorials to set up locally with 4 nodes and use the built-in smart contract service.

# Install
Following the instruction [install tutorial]([https://blog.resilientdb.com/2022/09/28/GettingStartedNexRes.html](https://github.com/apache/incubator-resilientdb)) to initial the environment that runs a Key-Value Service locally.
If you are using our could, you can ignore the system install section.




# Smart Contract Install
It contains three steps if you want to deploy your contract and execute its functions in ResilientDB: 
Create the contract account, deploy the contract, and execute the functions.

The 'contract_service_tools' provides access to the system by providing a JSON file and the service config.


## contract_service_tools
> bazel-bin/service/tools/kv/api_tools/contract_service_tools -c ServiceConfig --config_file=JSON Path


### Parameters:
  > -c the client configuration path  
  > --config_file the JSON file describing the actions 


## Create a Owner Account
For creating an account, the [JSON file](https://github.com/apache/incubator-resilientdb/blob/master/service/tools/kv/api_tools/create.js) is simply to provide the action:
```
{
  "command":"create_account",
}
```
### Command:
> bazel-bin/service/tools/kv/api_tools/contract_service_tools -c service/tools/config/interface/service.config --config_file=service/tools/kv/api_tools/create.js

### Response:
  Then, you will see the result
  ```
   create account: address: "0x67c6697351ff4aec29cdbaabf2fbe3467cc254f8"
  ```

## Deploy Your Contract

### Contract
Nexres only handles the JSON description of the contract source code. We use solc, a tool from Solidity, to obtain the JSON file.
Currently, we only support solidity version is larger than 0.5.0 (solidity >= 0.5.0)  

We provide [token.sol]([service/tools/kv/api_tools/example_contract/token.sol](https://github.com/apache/incubator-resilientdb/blob/master/service/tools/kv/api_tools/example_contract/token.sol)) as an example below:
  > solc --evm-version homestead --combined-json bin,hashes --pretty-json --optimize token.sol > token.json

Once get your [json contract]([service/tools/kv/api_tools/example_contract/token.json](https://github.com/apache/incubator-resilientdb/blob/master/service/tools/kv/api_tools/example_contract/token.json)), you can find the contract name("token.sol:Token") and its function hashes under the contract name section in the file.

token.sol：
```
pragma solidity >= 0.5.0;

// Transfer tokens from the contract owner
contract Token {
  mapping (address => uint256) balances;

  event Transfer(address indexed _from, address indexed _to, uint256 _value);

  constructor(uint256 s) public {
    balances[msg.sender] = s;
  }

  // Get the account balance of another account with address _owner
  function balanceOf(address _owner) public view returns (uint256) {
    return balances[_owner];
  }

  // Send _value amount of tokens to address _to
  function transfer(address _to, uint256 _value) public returns (bool) {
    if (balances[msg.sender] >= _value) {
      balances[msg.sender] -= _value;
      balances[_to] += _value;
      emit Transfer(msg.sender, _to, _value);
      return true;
    }
    else
    {
      return false;
    }
  }
}
```

token.json
```
{
  "contracts":
  {
    "token.sol:Token":
    {
      "bin": "608060405234801561001057600080fd5b506040516101fe3803806101fe8339818101604052602081101561003357600080fd5b5051336000908152602081905260409020556101aa806100546000396000f3fe608060405234801561001057600080fd5b5060043610610052577c0100000000000000000000000000000000000000000000000000000000600035046370a082318114610057578063a9059cbb1461008f575b600080fd5b61007d6004803603602081101561006d57600080fd5b5035600160a060020a03166100cf565b60408051918252519081900360200190f35b6100bb600480360360408110156100a557600080fd5b50600160a060020a0381351690602001356100ea565b604080519115158252519081900360200190f35b600160a060020a031660009081526020819052604090205490565b33600090815260208190526040812054821161016b573360008181526020818152604080832080548790039055600160a060020a03871680845292819020805487019055805186815290519293927fddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef929181900390910190a350600161016f565b5060005b9291505056fea265627a7a72315820600579dee5d66ea3489085c4cb41116045ba977cb4804caac8d5f24e248e337064736f6c63430005100032",
      "hashes":
      {
        "balanceOf(address)": "70a08231",
        "transfer(address,uint256)": "a9059cbb"
      }
    }
  },
  "version": "0.5.16+commit.9c3226ce.Linux.g++"
}
```

### Deploy JSON file
Now we generate the [JSON](https://github.com/apache/incubator-resilientdb/blob/master/service/tools/kv/api_tools/deploy.js) for the deployment. The JSON file contains the command ("deploy"), the contract location, the contract section in the JSON file, the owner_address address who owns the contract (we have created in the previous step), and the initial parameters, which can be empty.

```
{
  "command":"deploy",
  "contract_path": "service/tools/kv/api_tools/example_contract/token.json",
  "contract_name": "token.sol:Token",
  "init_params": "1000",
  "owner_address": "0x67c6697351ff4aec29cdbaabf2fbe3467cc254f8"
}
```

### Command:
> bazel-bin/service/tools/kv/api_tools/contract_service_tools -c service/tools/config/interface/service.config --config_file=service/tools/kv/api_tools/deploy.js

### Response:
Then you will see the response:
```
deploy contract:
owner_address: "0x67c6697351ff4aec29cdbaabf2fbe3467cc254f8"
contract_address: "0xfc08e5bfebdcf7bb4cf5aafc29be03c1d53898f1"
contract_name: "token.sol:Token"
```


## Execute Contract
Now we generate the JSON for the execution. The JSON file contains the command ("execute"), the contract address, the caller address who owns the contract, the function name, and the parameters to run the function.

```
{
  "command":"execute",
  "contract_address": "0xfc08e5bfebdcf7bb4cf5aafc29be03c1d53898f1",
  "caller_address": "0x67c6697351ff4aec29cdbaabf2fbe3467cc254f8",
  "func_name":"transfer(address,uint256)",
  "params":"0x1be8e78d765a2e63339fc99a66320db73158a35a,100"
}
```

### Command:
> bazel-bin/service/tools/kv/api_tools/contract_service_tools -c service/tools/config/interface/service.config --config_file=service/tools/kv/api_tools/execute.js

### Response:
Once it is done, you will see the result:
> 0x0000000000000000000000000000000000000000000000000000000000000001


# Using Key-Value Interfaces
The Key-Value interfaces provide the API to access the accounts: get_balance and set_balance.

The JSON files and corresponding commands are shown as below:
```
{
  "command":"get_balance",
  "address":"0x1be8e78d765a2e63339fc99a66320db73158a35a"
}
```
> bazel-bin/service/tools/kv/api_tools/contract_service_tools -c service/tools/config/interface/service.config --config_file=service/tools/kv/api_tools/get_balance.js

```
{
  "command":"set_balance",
  "address":"0x1be8e78d765a2e63339fc99a66320db73158a35a",
  "balance":"2000"
}
```

> bazel-bin/service/tools/kv/api_tools/contract_service_tools -c service/tools/config/interface/service.config --config_file=service/tools/kv/api_tools/set_balance.js


### Notice
The accounts do not need to be created using the [API](https://github.com/apache/incubator-resilientdb-blog/edit/main/_posts/2023-01-15-GettingStartedSmartContract.md#create-a-owner-account) below before being used.
However, if the account is used to deploy the contract, it must first be created by the API to register in the contract database.


# Future Work
Apply the authorization for the execution that verifies the owner to execute its contracts, like using the signatures.


