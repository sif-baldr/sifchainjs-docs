---
stoplight-id: s1ulcv7mk6cbc
---

# Getting Started

The Data API is a group of endpoints that are separate from the Sifnode API/RPC endpoints and utilize data stored in a centralized database generated from on-chain events within the Sifchain Network. This provides an alternative data source to the Sifnode chain data which allows for more performant data access for use on the DEX, data analytics, reporting, and also by our third-party partners.

This API can be accessed from the following data source https://data.sifchain.finance but we have also provided a git repository that provides the same list of API endpoints accessible at https://github.com/Sifchain/sif-apis.

Examples of endpoints being used by our third party partners include the following being utilized by Coinmarketcap on the Sifchain page (https://coinmarketcap.com/currencies/sifchain/):
- https://data.sifchain.finance/beta/asset/cmcTotalSupply
- https://data.sifchain.finance/beta/asset/circulatingsupply

In our bid to make these database records available publicly to anyone, we created the Sifchain ETL (which stands for Extract Transform Load) repository which allows anyone to set up their own database with the same data the API endpoints pull from. The repository is located at: https://github.com/Sifchain/sif-etl.