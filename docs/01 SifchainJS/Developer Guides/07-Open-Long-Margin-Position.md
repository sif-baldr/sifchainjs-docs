---
stoplight-id: 7ayow9bvdbpfa
tags: [Developer Guides]
---

# Open Long Margin Trading Position

For this guide we will explore how to open a LONG Margin Trading Position with **SifchainJS**.

> **Example Compatible with:** 
>
> **@sifchain/stargate v1.0.0** (https://www.npmjs.com/package/@sifchain/stargate/v/1.0.0
>
> **@sifchain/proto-types v1.0.0** (https://www.npmjs.com/package/@sifchain/proto-types/v/1.0.0)

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

## Install @sifchain/stargate and @sifchain/proto-types packages

`npm i @sifchain/stargate@1.0.0 @sifchain/proto-types@1.0.0`

## Create Script
Create an "**.env**" file which will be used to store your mnemonic and allows for not saving this sensitive information to your source control. In the "**.env**" file add the following:

**MNEMONIC="enter mnemonic here"**


You can now add your **.env** file to your **.gitignore** file so it’s not added to source control.

In order to read the "**.env**" we need to install the "**dotenv**" package. Execute the following command to install the package:

`npm install dotenv`

Create a new file **open.ts** that will store the contents of the script:

```js
import { createQueryClient, SifSigningStargateClient } from "@sifchain/stargate";
import { DirectSecp256k1HdWallet } from "@cosmjs/proto-signing";
import { Decimal } from "@cosmjs/math" ;
import dotenv from "dotenv";
import{ positionFromJSON } from "@sifchain/proto-types/sifnode/margin/v1/types";
dotenv.config();

async function main() {
    const mnemonic = `${process.env.MNEMONIC}`;
    const wallet = await DirectSecp256k1HdWallet.fromMnemonic(mnemonic, { prefix: "sif" });
    const [account] = await wallet.getAccounts();

    // Create the query client
    const queryClients = await createQueryClient(
        "https://proxies.sifchain.finance/api/sifchain-margin-tempnet/rpc",
    );
  
    const tokenEntries = await queryClients.tokenRegistry
        .entries({})
        .then((x: any) => x.registry?.entries);
  
    const rowan = tokenEntries?.find((x: any) => x.baseDenom === "rowan");

    // Create the Sifchain signing client
    const sifchainSigningClient = await SifSigningStargateClient.connectWithSigner(
        "https://proxies.sifchain.finance/api/sifchain-margin-tempnet/rpc",
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

    const rowanAmount = "1000";

    // Open Margin Trading
    const res = await sifchainSigningClient.signAndBroadcast(
        account.address,
        [
          {
            typeUrl: "/sifnode.margin.v1.MsgOpen",
            value: {
              signer: account.address,
              collateralAsset: "rowan",
              collateralAmount: Decimal.fromUserInput(rowanAmount, rowan.decimals.toNumber()).atomics,
              borrowAsset: "cusdc",
              position: positionFromJSON("LONG"),
              leverage: Decimal.fromUserInput("2", rowan.decimals.toNumber()).atomics
            },
          },
        ],
        fee,
        "",
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

#### Get Token Entry with Query Client
We will open a Long position with rowan as collateral, so we need to get rowan token from the token registry:

```js
// Create the query client
const queryClients = await createQueryClient(
    // Specify the correct rpc endpoint you will use
    "https://proxies.sifchain.finance/api/sifchain-margin-tempnet/rpc",
);
  
const tokenEntries = await queryClients.tokenRegistry
    .entries({})
    .then((x: any) => x.registry?.entries);
  
const rowan = tokenEntries?.find((x: any) => x.baseDenom === "rowan");
```

#### Create Signing Client to perform the transaction
We set up the Sifchain Signing Client to open a Long Trading Position:

```js
// Create the Sifchain signing client
const sifchainSigningClient = await SifSigningStargateClient.connectWithSigner(
    "https://proxies.sifchain.finance/api/sifchain-margin-tempnet/rpc",
    wallet,
);
```

#### Open a LONG Trading Position
We then set up the fee, specify the rowan amount for collateral and open the **Long Trading Position** to borrow **cusdc**:
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

const rowanAmount = "1000";

// Open Margin Trading
const res = await sifchainSigningClient.signAndBroadcast(
    account.address,
    [
      {
        typeUrl: "/sifnode.margin.v1.MsgOpen",
        value: {
          signer: account.address,
          collateralAsset: "rowan",
          collateralAmount: Decimal.fromUserInput(rowanAmount, rowan.decimals.toNumber()).atomics,
          borrowAsset: "cusdc",
          position: positionFromJSON("LONG"),
          leverage: Decimal.fromUserInput("2", rowan.decimals.toNumber()).atomics},
      },
    ],
    fee,
    "",
);
console.log(res);
```

## Execute script:
Then we execute the script with the following command from the terminal:

`ts-node export.ts`



