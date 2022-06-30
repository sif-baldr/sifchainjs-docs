---
stoplight-id: xqqtiise8vszx
tags: [Developer Guides]
---

# SWAP one token for another

> **Example Compatible with:** v0.0.0-snapshot.0833b21 (https://www.npmjs.com/package/@sifchain/stargate/v/0.0.0-snapshot.0833b21)

## Create a New NPM Project
Create a folder to store your project and within that folder execute the following command to initialize a NPM project:

`npm init`

Answer the questions shown in the terminal and at the end a **package.json** file will be created with a similar structure to the following:

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

Install Stargate
The Sifnode/IBC stargate version is required to execute a SWAP transaction. To install this version of SifchainJS (https://www.npmjs.com/package/@sifchain/stargate) execute the following:

`npm i @sifchain/stargate@snapshot`

Let’s also install some typescript dependencies to help with development using the following:

`npm install typescript ts-node @types/node --save-dev`


## Create Script
Create an "**.env**" file which will be used to store your mnemonic and allows for not saving this sensitive information to your source control. In the "**.env**" file add the following:

**MNEMONIC="enter mnemonic here"**

You can now add your **.env** file to your **.gitignore** file so it’s not added to source control.

In order to read the "**.env**" we need to install the "**dotenv**" package. Execute the following command to install the package:

`npm install dotenv`

Create a new file "**swapTokens.ts**" that will store the contents of the script:


```js
const { createQueryClient, SifSigningStargateClient} = require("@sifchain/stargate");
const { Registry, DirectSecp256k1HdWallet } = require("@cosmjs/proto-signing");
const { Decimal } = require("@cosmjs/math");
const dotenv = require('dotenv');
 
dotenv.config();
 
 
async function main() {
   const queryClients = await createQueryClient(
       "https://rpc.sifchain.finance",
   );
 
   const tokenEntries = await queryClients.tokenRegistry
       .entries({})
       .then((x) => x.registry?.entries);
 
   const rowan = tokenEntries?.find((x) => x.baseDenom === "rowan");
   const usdc = tokenEntries?.find((x) => x.baseDenom === "cusdc");
 
   const mnemonic = `${process.env.MNEMONIC}`;
   const wallet = await DirectSecp256k1HdWallet.fromMnemonic(mnemonic, { prefix: "sif" });
   const [firstAccount] = await wallet.getAccounts();
 
   const signingClient = await SifSigningStargateClient.connectWithSigner(
       "https://rpc.sifchain.finance",
       wallet,
   );
 
   //const rowanSwapAmount = "1000000000000000000";
   const rowanSwapAmount = "1";
 
   const swap = await signingClient.simulateSwap(
       {
           denom: rowan.denom,
           amount: Decimal.fromUserInput(rowanSwapAmount, rowan.decimals.toNumber()).atomics,
       },
       { denom: usdc.denom },
       0.05,
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
 
   await signingClient.signAndBroadcast(
       firstAccount.address,
       [
           {
               typeUrl: "/sifnode.clp.v1.MsgSwap",
               value: {
                   signer: firstAccount.address,
                   sentAsset: { symbol: rowan.denom.toString() },
                   receivedAsset: { symbol: usdc.denom.toString() },
                   //sentAmount: "1000000000000000000",
                   //minReceivingAmount: "4191",
                   sentAmount: Decimal.fromUserInput(rowanSwapAmount, rowan.decimals.toNumber()).atomics,
                   minReceivingAmount: Decimal.fromUserInput(swap.minimumReceiving.toString(), usdc.decimals.toNumber()).atomics,
               },
           },
       ],
       fee,
       "",
   );
}
 
main();
```


### Code Breakdown
Firstly lets connect to the query client that will be used to retrieve the token listings from the token registry.

```js
const queryClients = await createQueryClient(
    "https://rpc.sifchain.finance",
);
```


With the query client object we can pull in all the tokens available for trading on the Sifchain DEX. This might not contain all the possible tokens available within the Cosmos ecosystem but once you understand the structure of the token list you can manually configure any token:

```js
const tokenEntries = await queryClients.tokenRegistry
       .entries({})
       .then((x) => x.registry?.entries);
```


In this example we will be swapping ROWAN for USDC so let’s pull in the ROWAN and USDC toekns which will allow us to get the correct denom and decimals for each token which will help in the conversion from the user’s input:

```js
const rowan = tokenEntries?.find((x) => x.baseDenom === "rowan");
const usdc = tokenEntries?.find((x) => x.baseDenom === "cusdc");
```


We now need to load the wallet that will execute the transaction. The mnemonic is being pulled from the **.env** file and the “**sif**” prefix is required to get the correct address from the account:

```js
const mnemonic = `${process.env.MNEMONIC}`;
const wallet = await DirectSecp256k1HdWallet.fromMnemonic(mnemonic, { prefix: "sif" });
const [firstAccount] = await wallet.getAccounts();
```


Generate a signing client that will be used to execute the actual transaction:

```js
const signingClient = await SifSigningStargateClient.connectWithSigner(
       "https://rpc.sifchain.finance",
       wallet,
   );
```

A simulation function is used to calculate the minimum received amount along with the price impact and liquidity provider fee. This value will be used below.

```js
const swap = await signingClient.simulateSwap(
       {
           denom: rowan.denom,
           amount: Decimal.fromUserInput(rowanSwapAmount, rowan.decimals.toNumber()).atomics,
       },
       { denom: usdc.denom },
       0.05,
   );
```


Configure the fee that will be paid to execute the transaction:

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
```


In order to execute the transaction we need to get the typeURL of the RPC endpoint we are trying to execute. You can find this by going through the protobuf file for the respective transaction and looking at the RPC service that is required and joining the parameter to the message object sent to the RPC service to the package. The Swap RPC service can be found here https://github.com/Sifchain/sifnode/blob/develop/proto/sifnode/clp/v1/tx.proto with the message accepted being “**MsgSwap**” and the package being “**sifnode.clp.v1**” and the resulting typeULR being “**/sifnode.clp.v1.MsgSwap**”. The value object will be based on the makeup of the “**MsgSwap**” message type.

```js
await signingClient.signAndBroadcast(
       firstAccount.address,
       [
           {
               typeUrl: "/sifnode.clp.v1.MsgSwap",
               value: {
                   signer: firstAccount.address,
                   sentAsset: { symbol: rowan.denom.toString() },
                   receivedAsset: { symbol: usdc.denom.toString() },
                   sentAmount: Decimal.fromUserInput(rowanSwapAmount, rowan.decimals.toNumber()).atomics,
                   minReceivingAmount: Decimal.fromUserInput(swap.minimumReceiving.toString(), usdc.decimals.toNumber()).atomics,
               },
           },
       ],
       fee,
       "",
   );
```

## Execute Script
Execute the following command in your terminal:

`node swapTokens.ts`

