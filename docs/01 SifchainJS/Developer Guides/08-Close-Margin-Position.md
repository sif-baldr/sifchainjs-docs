---
stoplight-id: m8fstcxteps1r
tags: [Developer Guides]
---

# Close Margin Trading Position

For this guide we will explore how to close a Margin Trading Position with **SifchainJS**.

> **Example Compatible with:**  **@sifchain/stargate v1.0.0** (https://www.npmjs.com/package/@sifchain/stargate/v/1.0.0

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
  "compilerOptions": {
    "module": "CommonJS"
  },
  "author": "",
  "license": "MIT"
}
```

## Install necessary packages
We will use **@sifchain/stargate**, **@sifchain/proto-types** and **@cosmjs/proto-signing** for the script:

`npm i @sifchain/stargate@1.0.0 @sifchain/proto-types@1.0.0 @cosmjs/proto-signing`

## Create Script
Create an "**.env**" file which will be used to store your mnemonic and allows for not saving this sensitive information to your source control. In the "**.env**" file add the following:

**MNEMONIC="enter mnemonic here"**


You can now add your **.env** file to your **.gitignore** file so it’s not added to source control.

In order to read the "**.env**" we need to install the "**dotenv**" package. Execute the following command to install the package:

`npm install dotenv`

Create a new file **close.ts** that will store the contents of the script:

```js
import { createQueryClient, SifSigningStargateClient } from "@sifchain/stargate";
import { DirectSecp256k1HdWallet } from "@cosmjs/proto-signing";
import Long from "long";
import dotenv from "dotenv";
dotenv.config();

async function main() {
    const mnemonic = `${process.env.MNEMONIC}`;
    const wallet = await DirectSecp256k1HdWallet.fromMnemonic(mnemonic, { prefix: "sif" });
    const [account] = await wallet.getAccounts();

    // Create the Sifchain signing client
    const sifchainSigningClient = await SifSigningStargateClient.connectWithSigner(
        // Specify the correct rpc endpoint here
        "https://proxies.sifchain.finance/api/sifchain-margin-tempnet/rpc",
        wallet,
    );

    const queryClients = await createQueryClient(
        // Specify the correct rpc endpoint here
      "https://proxies.sifchain.finance/api/sifchain-margin-tempnet/rpc",
    );

    const positionsForAddress = await queryClients.margin
      .getPositionsForAddress({ address: account.address })
      .then((x: any) => {
        return x.mtps
      });
    
    const latestId = positionsForAddress[0].id.low;

    const fee = {
        amount: [
            {
                denom: "rowan",
                amount: "100000000000000000", // 0.1 ROWAN
            },
        ],
        gas: "180000", // 180k
    };

    
    // Close margin trading
    const res = await sifchainSigningClient.signAndBroadcast(
        account.address,
        [
          {
            typeUrl: "/sifnode.margin.v1.MsgClose",
            value: {
              signer: account.address,
              id: new Long(latestId) // Specify your position id here
            }
          }
        ],
        fee,
        ""
    );
    console.log(res);
}

main();
```

### Script Breakdown

#### Get User Account
First we will retrieve the mnemonic of the user to get the account of the user. This will allow us to get the account address, which will be used to open a **Margin Long Trading Position** in later steps:

```js
const mnemonic = `${process.env.MNEMONIC}`;
const wallet = await DirectSecp256k1HdWallet.fromMnemonic(mnemonic, { prefix: "sif" });
const [account] = await wallet.getAccounts();
```

#### Create Signing Client to perform the transaction
We set up the Sifchain Signing Client to close a Margin Trading Position:

```js
// Create the Sifchain signing client
const sifchainSigningClient = await SifSigningStargateClient.connectWithSigner(
    // Specify the correct rpc endpoint here
    "https://proxies.sifchain.finance/api/sifchain-margin-tempnet/rpc",
    wallet,
);
```

#### Get the latest Long Trading Position ID to close
We will use this script to get the latest trading position the user has opened to close, however user can specify any position id in the later step:

```js
const queryClients = await createQueryClient(
    // Specify the correct rpc endpoint here
    "https://proxies.sifchain.finance/api/sifchain-margin-tempnet/rpc",
);

const positionsForAddress = await queryClients.margin
    .getPositionsForAddress({ address: account.address })
    .then((x: any) => {
      return x.mtps
    });
    
const latestId = positionsForAddress[0].id.low;
```

#### Close the Margin Trading Position
We then set up the fee, specify the position id and close the **Margin Trading Position**:

```js
const fee = {
    amount: [
        {
            denom: "rowan",
            amount: "100000000000000000", // 0.1 ROWAN
        },
    ],
    gas: "180000", // 180k
};

    
// Close margin trading
const res = await sifchainSigningClient.signAndBroadcast(
    account.address,
    [
      {
        typeUrl: "/sifnode.margin.v1.MsgClose",
        value: {
          signer: account.address,
          id: new Long(latestId) // Specify your position id here
        }
      }
    ],
    fee,
    ""
);
console.log(res);
```

## Execute script:
Then we execute the script with the following command from the terminal:

`ts-node close.ts`

