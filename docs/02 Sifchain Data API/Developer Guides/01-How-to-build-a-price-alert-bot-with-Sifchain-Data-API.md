---
stoplight-id: w8d2o1f6qqt4g
tags: [Developer Guides]
---

# How to build a price alert Telegram bot with Sifchain Data API

How to build a Telegram bot that allows users to choose a token and a target price and alert them when the token price is either below, above or equal to the target price.

### How to use:

1. Set up [Sif-APIs](https://github.com/Sifchain/sif-apis)

2. Clone the repository:
```
git clone https://github.com/hungng157/sif-price-alert-bot
```

3. Set up the bot in [BotFather](https://core.telegram.org/bots#6-botfather) in Telegram and put the token inside the `.env` file as in `.env-example`

4. Update the ```tokenStats``` endpoint according to Sif-APIs in ```.env``` file. For example, set ```TOKEN_STATS_ENDPOINT="https://data.sifchain.finance/beta/asset/tokenStats"```

5. Run the bot
```
cd sif-price-alert-bot
node index.js
```

### Bot commands:

```/help```: Get the information about the features of the bot

```/alert [token] [> | < | =] [target_price]```: Set the price alert for specific token, in USD. 

For example, ```/alert eth < 1000 ```, the bot will notify when the price of eth is lower than $1000.

### Further notes:
Notice that the price of the tokens is in the Sifchain DEX.
