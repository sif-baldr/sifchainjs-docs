---
stoplight-id: gp864x3f6je85
tags: [Developer Guides]
---

# Transfer tokens to another Sifchain account


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

Install the SDK
The Sifnode/IBC version of the SDK is required to send tokens from one wallet to another. To install the general IBC version of the SDK (https://www.npmjs.com/package/@sifchain/stargate) execute the following:

`npm i @sifchain/stargate@snapshot`


## Create Script
Create an "**.env**" file which will be used to store your mnemonic and allows for not saving this sensitive information to your source control. In the "**.env**" file add the following:

**MNEMONIC="enter mnemonic here"**

You can now add your **.env** file to your **.gitignore** file so it’s not added to source control.

In order to read the "**.env**" we need to install the "**dotenv**" package. Execute the following command to install the package:

`npm install dotenv`

Create a new file "**sendTokens.ts**" that will store the contents of the script:

```js
const { createQueryClient, SifSigningStargateClient} = require("@sifchain/stargate");
const { DirectSecp256k1HdWallet } = require("@cosmjs/proto-signing");
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

    const mnemonic = `${process.env.MNEMONIC}`;
    const wallet = await DirectSecp256k1HdWallet.fromMnemonic(mnemonic, { prefix: "sif" });
    const [sendingAccount] = await wallet.getAccounts();
    const receivingAccount = "sif19vprdtfha0xsls0qlwqj2sas32nqqtf4f0ks3m";

    const signingClient = await SifSigningStargateClient.connectWithSigner(
        "https://rpc.sifchain.finance",
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

    await signingClient.signAndBroadcast(
        sendingAccount.address,
        [
            {
                typeUrl: "/cosmos.bank.v1beta1.MsgSend",
                value: {
                    fromAddress: sendingAccount.address,
                    toAddress: receivingAccount,
                    amount: [
                        {
                            denom: rowan.denom,
                            amount: Decimal.fromUserInput(
                                "1000000000000000000",
                                rowan.decimals.toNumber(),
                            ).toString(),
                        },
                    ],
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


In this example we will be sending ROWAN so let’s pull in the ROWAN token which will allow us to get the correct denom and convert the token amount to the correct precision:

```js
const rowan = tokenEntries?.find((x) => x.baseDenom === "rowan");
```


We now need to load the wallet that will execute the transaction. The mnemonic is pulled in from the .env file and the wallet generated from the value. The “**sif**” prefix is required to get the correct address from the account:

```js
const mnemonic = `${process.env.MNEMONIC}`;
const wallet = await DirectSecp256k1HdWallet.fromMnemonic(mnemonic, { prefix: "sif" });
const [sendingAccount] = await wallet.getAccounts();
```


Generate a signing client that will be used to execute the actual transaction:

```js
const signingClient = await SifSigningStargateClient.connectWithSigner(
  "https://rpc.sifchain.finance",
  wallet,
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


Execute the transaction:

```js
await signingClient.signAndBroadcast(
  sendingAccount.address,
  [
    {
      typeUrl: "/cosmos.bank.v1beta1.MsgSend",
      value: {
        fromAddress: sendingAccount.address,
        toAddress: receivingAccount,
        amount: [
          {
            denom: rowan.denom,
            amount: Decimal.fromUserInput(
              "1000000000000000000",
              rowan.decimals.toNumber(),
            ).toString(),
          },
        ],
      },
    },
  ],
  fee,
  "",
);
```

### Execute Script
Execute the following command in your terminal to execute the script:

`node sendTokens.ts`

