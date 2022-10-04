---
stoplight-id: 4eqzoficbbgvj
tags: [Developer Guides]
---

# Export tokens via IBC

For this guide we will walk through the process of exporting the **Juno** token from the Sifchain DEX to the Juno blockchain via IBC using **SifchainJS**.

> **Example Compatible with:** v1.0.0 (https://www.npmjs.com/package/@sifchain/stargate/v/1.0.0)

## Create a New NPM Project
Create a folder to store your project and within that folder execute the following command to initialize a NPM project:

`npm init`

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

## Install Stargate and @cosmjs/proto-signing

The **@sifchain/stargate** and **@cosmjs/proto-signing** packages are required to send tokens from Sifchain to another blockchain via IBC. To install **@sifchain/stargate 1.0.0** and **@cosmjs/proto-sigining** execute the following:

`npm i @sifchain/stargate@1.0.0 @cosmjs/proto-signing`

## Create Script
Create an "**.env**" file which will be used to store your mnemonic and allows for not saving this sensitive information to your source control. In the "**.env**" file add the following:

**MNEMONIC="enter mnemonic here"**

**SIF_ADDRESS="enter your Sifchain wallet address here"**

**JUNO_ADDRESS="enter your Juno wallet address here"**

You can now add your **.env** file to your **.gitignore** file so it’s not added to source control.

In order to read the "**.env**" we need to install the "**dotenv**" package. Execute the following command to install the package:

`npm install dotenv`

Create a new file **export.ts** that will store the contents of the script:

```js
import { SifSigningStargateClient, createQueryClient } from "@sifchain/stargate";
import { DirectSecp256k1HdWallet } from "@cosmjs/proto-signing";
import { Decimal } from "@cosmjs/math";
import dotenv from "dotenv";
dotenv.config();

async function main() {
    const mnemonic = `${process.env.MNEMONIC}`;
    const wallet = await DirectSecp256k1HdWallet.fromMnemonic(mnemonic, { prefix: "sif" });

    const sifchainSigningClient = await SifSigningStargateClient.connectWithSigner(
        "https://rpc.sifchain.finance/",
        wallet
    )

    const queryClients = await createQueryClient(
        "https://rpc.sifchain.finance",
    );

    const tokenEntries = await queryClients.tokenRegistry
        .entries({})
        .then((x) => x.registry?.entries);

    const juno = tokenEntries?.find((x) => x.baseDenom === "ujuno");

    const fee = {
        amount: [
            {
                denom: "rowan",
                amount: "100000000000000000", // 0.1 ROWAN
            },
        ],
        gas: "180000", // 180k
    };

    const res = await sifchainSigningClient.exportIbcTokens(
        `${process.env.SIF_ADDRESS}`,
        `${process.env.JUNO_ADDRESS}`,
        {
            denom: `${juno?.denom}`,
            amount: Decimal.fromUserInput("0.01", 6).atomics,
        },
        "transfer",
        undefined,
        Math.floor(Date.now() / 1000) + 60,
        fee
    )

    console.log(res);
}

main();
```

### Script Breakdown

#### Wallets and Signing Client
Firstly we need to get our Sifchain wallet mnemonic and get the Sifchain wallet from that mnemonic to perform the transaction:

```js
const mnemonic = `${process.env.MNEMONIC}`;
const wallet = await DirectSecp256k1HdWallet.fromMnemonic(mnemonic, { prefix: "sif" })
const sifchainSigningClient = await SifSigningStargateClient.connectWithSigner(
    "https://rpc.sifchain.finance/",
    wallet
);
```

#### Query Client and Get Token Entry
Then we will use the query client to get the token we want to export via IBC, in our example is the **JUNO** token
```js
const queryClients = await createQueryClient("https://rpc.sifchain.finance");

const tokenEntries = await queryClients.tokenRegistry
  .entries({})
  .then((x) => x.registry?.entries);

const juno = tokenEntries?.find((x) => x.baseDenom === "ujuno");
```

#### Export token via IBC
Then we set up the fee and export token via IBC:
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

// Export token via IBC
const res = await sifchainSigningClient.exportIbcTokens(
    `${process.env.SIF_ADDRESS}`,
    `${process.env.JUNO_ADDRESS}`,
    {
        denom: `${juno?.denom}`,
        amount: Decimal.fromUserInput("0.01", 6).atomics,
    },
    "transfer",
    undefined,
    Math.floor(Date.now() / 1000) + 60,
    fee
)

console.log(res);
```

## Execute script
Then we execute the script with the following command from the terminal:

`ts-node export.ts`





