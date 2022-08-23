---
stoplight-id: 3tnro0mg4izej
tags: [Developer Guides]
---

# Export token from Sifchain to Ethereum

> **Example Compatible with:** v0.0.0-snapshot.0833b21 (https://www.npmjs.com/package/@sifchain/stargate/v/0.0.0-snapshot.0833b21)

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

## Install Stargate and Cosmjs

The ***@sifchain/stargate*** and ***@cosmjs/proto-signing*** package is required to export tokens from Sifchain to Ethereum. To install ***sifchain@stargate*** version 0.0.0-snapshot.0833b21 (https://www.npmjs.com/package/@sifchain/stargate/v/0.0.0-snapshot.0833b21) and ***@cosmjs/proto-signing*** package (https://www.npmjs.com/package/@cosmjs/proto-signing) execute the following:

`npm i @sifchain/stargate@0.0.0-snapshot.0833b21 @cosmjs/proto-signing`


## Create Script
Create an "**.env**" file which will be used to store your Ethereum wallet private key and your Sifchain wallet address and allows for not saving this sensitive information to your source control. In the "**.env**" file add the following:

**MNEMONIC="enter mnemonic here"**

**SIF_ADDRESS="enter your Sifchain wallet address"**

**ETH_ADDRESS="enter your Ethereum wallet address"**

You can now add your **.env** file to your **.gitignore** file so it’s not added to source control.

In order to read the "**.env**" we need to install the "**dotenv**" package. Execute the following command to install the package:

`npm install dotenv`

Create a new file "**export.ts**" that will store the contents of the script:

```js
import { SifSigningStargateClient } from "@sifchain/stargate";
import { DirectSecp256k1HdWallet } from "@cosmjs/proto-signing";
import { Decimal } from "@cosmjs/math";
import dotenv from "dotenv";
dotenv.config();

async function main() {
    const mnemonic = `${process.env.MNEMONIC}`;
    const wallet = await DirectSecp256k1HdWallet.fromMnemonic(mnemonic, { prefix: "sif" });

    // Create the Sifchain signing client
    const sifchainSigningClient = await SifSigningStargateClient.connectWithSigner(
        "http://sifchain.testnet.sifchain.finance:26657",
        wallet,
    );

    const fee = {
        amount: [
            {
                denom: "rowan",
                amount: "100000000000000000", // 0.1 ROWAN
            },
        ],
        gas: "180000", // 180k
    };

    // Export token from Sifchain to Ethereum
    const res = await sifchainSigningClient.sendTokensToEth(
        `${process.env.SIF_ADDRESS}`,
        `${process.env.ETH_ADDRESS}`,
        {
          denom: "rowan",
          amount: Decimal.fromUserInput("1.0", 18).toString(),
        },
        // Ropsten network chain id
        0x3,
        undefined,
        fee,
    );
    console.log(res);
}

main();
```

### Code Breakdown

Firstly we need to get our Sifchain wallet mnemonic and get the Sifchain wallet from that mnemonic to perform the transaction:

```js
const mnemonic = `${process.env.MNEMONIC}`;
const wallet = await DirectSecp256k1HdWallet.fromMnemonic(mnemonic, { prefix: "sif" });
```

Then create the Sifchain signing client on the **Sifchain Testnet**:

```js
// Create the Sifchain signing client
const sifchainSigningClient = await SifSigningStargateClient.connectWithSigner(
    "http://sifchain.testnet.sifchain.finance:26657",
    wallet,
);
```

We configure the fee that will be used to pay the transaction:

```js
// Create approval transaction
const fee = {
  amount: [
      {
          denom: "rowan",
          amount: "100000000000000000", // 0.1 ROWAN
      },
  ],
  gas: "180000", // 180k
};
```


Finally, we execute the transaction to send the token from **Sifchain Testnet** to **Ethereum Ropsten Testnet**. We then output the log of the transaction:

```js
// Export token from Sifchain to Ethereum
const res = await sifchainSigningClient.sendTokensToEth(
    `${process.env.SIF_ADDRESS}`,
    `${process.env.ETH_ADDRESS}`,
    {
      denom: "rowan",
      amount: Decimal.fromUserInput("1.0", 18).toString(),
    },
    // Ropsten network chain id
    0x3,
    undefined,
    fee,
);
console.log(res);
};
```


### Execute Script
Execute the following command in your terminal to execute the script:

`ts-node export.ts`