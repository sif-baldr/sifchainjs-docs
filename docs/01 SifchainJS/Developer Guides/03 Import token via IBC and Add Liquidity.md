---
stoplight-id: 25b0ca1mje2rp
---

# Import tokens via IBC and Add Liquidity

For this guide we will walk through the process of Importing tokens from the JUNO network to your Sifchain account and then use those tokens along with ROWAN to add liquidity in the JUNO liquidity pool.

> **Example Compatible with:** @sifchain/stargate 0.0.0-snapshot.0833b21 (https://www.npmjs.com/package/@sifchain/stargate/v/0.0.0-snapshot.0833b21)

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
  "author": "",
  "license": "MIT"
}
```

Install Stargate
The Sifnode/IBC stargate version is required to send tokens from one sifchain wallet to another. To install this version of SifchainJS (https://www.npmjs.com/package/@sifchain/stargate) execute the following:

`npm i @sifchain/stargate@snapshot`


## Create Script
Create an "**.env**" file which will be used to store your mnemonic and allows for not saving this sensitive information to your source control. In the "**.env**" file add the following:

**MNEMONIC="enter mnemonic here"**

You can now add your **.env** file to your **.gitignore** file so it’s not added to source control.

In order to read the "**.env**" we need to install the "**dotenv**" package. Execute the following command to install the package:

`npm install dotenv`

Create a new file **importAddLiquidity.ts** that will store the contents of the script:

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
    const juno = tokenEntries?.find((x) => x.baseDenom === "ujuno");

    const mnemonic = `${process.env.MNEMONIC}`;
    const wallet = await DirectSecp256k1HdWallet.fromMnemonic(mnemonic, { prefix: "sif" });
    const wallet2 = await DirectSecp256k1HdWallet.fromMnemonic(mnemonic, { prefix: "juno" });
    const [firstAccount] = await wallet.getAccounts();

    // Create the Sifchain signing client
    const sifchainSigningClient = await SifSigningStargateClient.connectWithSigner(
        "https://rpc.sifchain.finance",
        wallet,
    );

    // Create the Juno signing client
    const junoSigningClient = await SifSigningStargateClient.connectWithSigner(
        "https://rpc-juno-ia.notional.ventures",
        wallet2,
    );

    const junoFee = {
        amount: [
            {
                //denom: juno.denom.toString(),
                denom: 'ujuno',
                amount: "1250", // 0.00125 JUNO
            },
        ],
        gas: "180000", // 180k
    };

    const fee = {
        amount: [
            {
                denom: "rowan",
                amount: "100000000000000000", // 0.1 ROWAN
            },
        ],
        gas: "180000", // 180k
    };

    // Import the JUNO tokens
    await sifchainSigningClient.importIBCTokens(
        junoSigningClient,
        "juno1lfuz8dn5nww43q933q3ft8pknlutf2y37u25ja",
        "sif1lfuz8dn5nww43q933q3ft8pknlutf2y3dnxe62",
        {
            denom: "ujuno",
            amount: Decimal.fromUserInput("0.01", 6).atomics,
        },
        "transfer",
        undefined,
        Math.floor(Date.now() / 1000) + 60,
        junoFee,
    );

    const nativeAssetAmount = "0.41203";
    const externalAssetAmount = "0.001";

    // Add Liquidity
    await sifchainSigningClient.signAndBroadcast(
        firstAccount.address,
        [
            {
                typeUrl: "/sifnode.clp.v1.MsgAddLiquidity",
                value: {
                    signer: firstAccount.address,
                    externalAsset: { symbol: juno.denom.toString() },
                    nativeAssetAmount: Decimal.fromUserInput(nativeAssetAmount, rowan.decimals.toNumber()).atomics,
                    externalAssetAmount: Decimal.fromUserInput(externalAssetAmount, juno.decimals.toNumber()).atomics,
                },
            },
        ],
        fee,
        "",
    );
}

main();
```

### Script Breakdown

#### Get token entries
Firstly we need to get the token entries for ROWAN and JUNO. This will allow us to get the right decimals for each token and the correct denom which in JUNOs case will be the IBC denom when executing the add liquidity transaction.

```js
const rowan = tokenEntries?.find((x) => x.baseDenom === "rowan");
const juno = tokenEntries?.find((x) => x.baseDenom === "ujuno");
```


#### Wallets and Signing Clients
We need to initialize two separate wallet objects for each as the import transaction will require us to connect to the JUNO chain.


   ```js
const wallet = await DirectSecp256k1HdWallet.fromMnemonic(mnemonic, { prefix: "sif" });
   const wallet2 = await DirectSecp256k1HdWallet.fromMnemonic(mnemonic, { prefix: "juno" });
   const [firstAccount] = await wallet.getAccounts();
 
   // Create the Sifchain signing client
   const sifchainSigningClient = await SifSigningStargateClient.connectWithSigner(
       "https://rpc.sifchain.finance",
       wallet,
   );
 
   // Create the Juno signing client
   const junoSigningClient = await SifSigningStargateClient.connectWithSigner(
       "https://rpc-juno-ia.notional.ventures",
       wallet2,
   );
```

#### Import Token
We can now import JUNO from the JUNO chain to Sifchain. The timeoutHeight or timeoutTimestamp value is required but the example sets the timeoutTimestamp by executing the following Math.floor(Date.now() / 1000) + 60 and leaving the other value undefined.

```js
await sifchainSigningClient.importIBCTokens(
       junoSigningClient,
       "juno1lfuz8dn5nww43q933q3ft8pknlutf2y37u25ja",
       "sif1lfuz8dn5nww43q933q3ft8pknlutf2y3dnxe62",
       {
           denom: "ujuno",
           amount: Decimal.fromUserInput("0.01", 6).atomics,
       },
       "transfer",
       undefined,
       Math.floor(Date.now() / 1000) + 60,
       junoFee,
   );
```

#### Add Liquidity
After the tokens are imported we can then add liquidity using the following. To make sure the values were imported successfully a check can be done to see if the values were imported and a timeout done to do additional checks to make sure before ending closing the script. In this example the token amounts to be added to the liquidity pool are manually set but you could do the necessary calculations to make sure the ratio is correct.

```js
const nativeAssetAmount = "0.41203";
   const externalAssetAmount = "0.001";
 
   // Add Liquidity
   await sifchainSigningClient.signAndBroadcast(
       firstAccount.address,
       [
           {
               typeUrl: "/sifnode.clp.v1.MsgAddLiquidity",
               value: {
                   signer: firstAccount.address,
                   externalAsset: { symbol: juno.denom.toString() },
                   nativeAssetAmount: Decimal.fromUserInput(nativeAssetAmount, rowan.decimals.toNumber()).atomics,
                   externalAssetAmount: Decimal.fromUserInput(externalAssetAmount, juno.decimals.toNumber()).atomics,
               },
           },
       ],
       fee,
       "",
   );
```


## Execute Script
Execute the following command in your terminal:

`importAddLiquidity.ts`
