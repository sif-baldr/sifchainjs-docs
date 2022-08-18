---
stoplight-id: 6rm8g0eijbhy8
tags: [Developer Guides]
---

# Import token from Ethereum to Sifchain

> **Example Compatible with:** v0.0.0-snapshot.d31f736 (https://www.npmjs.com/package/@sifchain/evm/v/0.0.0-snapshot.d31f736)

## Create a New NPM Project
Create a folder to store your project and within that folder execute the following command to initialize a NPM project:

`npm init -y`

Answer the questions shown in the terminal and at the end a package.json file will be created with a similar structure to the following:

```json
{
  "name": "test-project",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "MIT"
}
```

## Install Peggy Client and ethers.js

The ***@sifchain/evm*** and ***ethers*** package is required to import tokens from Ethereum to Sifchain. To install ***sifchain@evm*** version 0.0.0-snapshot.d31f736 (https://www.npmjs.com/package/@sifchain/evm/v/0.0.0-snapshot.d31f736) and ***ethers*** package (https://www.npmjs.com/package/ethers) execute the following:

`npm i @sifchain/evm@0.0.0-snapshot.d31f736 ethers`




## Create Script
Create an "**.env**" file which will be used to store your Ethereum wallet private key and your Sifchain wallet address and allows for not saving this sensitive information to your source control. In the "**.env**" file add the following:

**ETH_PRIVATE_KEY="enter your Eth private key"**

**SIF_ADDRESS="enter your Sifchain wallet address"**

You can now add your **.env** file to your **.gitignore** file so it’s not added to source control.

In order to read the "**.env**" we need to install the "**dotenv**" package. Execute the following command to install the package:

`npm install dotenv`

Create a new file "**import.ts**" that will store the contents of the script:

```js
import { getTestnetSdk } from "@sifchain/evm";
import { ethers } from "ethers";
import dotenv from 'dotenv';
dotenv.config();

async function main() {
    // Get the private for your Ethereum account
    const privateKey = `${process.env.ETH_PRIVATE_KEY}`;
    const testnetProvider = ethers.getDefaultProvider("ropsten");

    const signer = new ethers.Wallet(privateKey, testnetProvider);   
    const sdk = getTestnetSdk(signer);

    // Create approval transaction
    let abi = ["function approve(address _spender, uint256 _value) public returns (bool success)"];
        
    let contract = new ethers.Contract("0xEC017aC9003D2906Fc855258040A56C671a315d6", abi, signer);

    let test1 = await contract.approve("0x6CfD69783E3fFb44CBaaFF7F509a4fcF0d8e2835", ethers.utils.parseEther("100.0"));
    await test1.wait();


    // convenience method for sending tokens to Cosmos
    const burnOrLockTransaction = await sdk.peggy.sendTokensToCosmos(
        ethers.utils.toUtf8Bytes(`${process.env.SIF_ADDRESS}`),
        "0xEC017aC9003D2906Fc855258040A56C671a315d6",
        ethers.utils.parseEther("0.01"),
    );
    await burnOrLockTransaction.wait();
}

main();
```

### Code Breakdown

Firstly lets get the Ethereum private key that is stored in the "**.env**" file:

```js
const privateKey = `${process.env.ETH_PRIVATE_KEY}`;
```


Then set the Ethereum provider to ***Ropsten Testnet***

```js
const testnetProvider = ethers.getDefaultProvider("ropsten");
```

With the `privateKey` and the `testnetProvider` we can get the signer wallet and the Peggy Client by the following scripts:

```js
const signer = new ethers.Wallet(privateKey, testnetProvider);   
const sdk = getTestnetSdk(signer);
```


To perform the import transaction, we need to perform the **approve** function of the erowan smart contract, so first we set the **abi** of the **approve** function:

```js
// Create approval transaction
let abi = ["function approve(address _spender, uint256 _value) public returns (bool success)"];
```


Then we get the contract with the Ethereum Wallet of the signer we created before and the abi of the function we want to execute, in which `"0xEC017aC9003D2906Fc855258040A56C671a315d6"` is the address of **erowan** contract in **Ropsten Testnet**. You can see more details of the contract [here](https://ropsten.etherscan.io/address/0xEC017aC9003D2906Fc855258040A56C671a315d6):

```js
let contract = new ethers.Contract("0xEC017aC9003D2906Fc855258040A56C671a315d6", abi, signer);
```

We perform the **approve** function to allow the Peggy Client to perform the transaction, with `"0x6CfD69783E3fFb44CBaaFF7F509a4fcF0d8e2835"` is the address of the Peggy Client. You can see the block explorer of the Peggy Client address [here](https://ropsten.etherscan.io/address/0x6CfD69783E3fFb44CBaaFF7F509a4fcF0d8e2835).

```js
let test1 = await contract.approve("0x6CfD69783E3fFb44CBaaFF7F509a4fcF0d8e2835", ethers.utils.parseEther("100.0"));
await test1.wait();
```


We execute the transaction to import erowan token from Ethereum to Sifchain:

```js
// convenience method for sending tokens to Cosmos
const burnOrLockTransaction = await sdk.peggy.sendTokensToCosmos(
  ethers.utils.toUtf8Bytes(`${process.env.SIF_ADDRESS}`),
  "0xEC017aC9003D2906Fc855258040A56C671a315d6",
  ethers.utils.parseEther("0.01"),
);
await burnOrLockTransaction.wait();
```

### Execute Script
Execute the following command in your terminal to execute the script:

`ts-node import.ts`
