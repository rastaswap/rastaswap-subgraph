# RastaSwap Subgraph

[RastaSwap](https://rastaswap.finance/) is a decentralized protocol for automated token exchange on Binance Smart Chain.

This subgraph dynamically tracks any pair created by the RastaSwap factory. It tracks of the current state of RastaSwap contracts, and contains derived stats for things like historical data and USD prices.

- aggregated data across pairs and tokens,
- data on individual pairs and tokens,
- data on transactions
- data on liquidity providers
- historical data on RastaSwap, pairs or tokens, aggregated by day
<br/><br/>

# Deployment Guide

## 1. Factory Address and Block Number
After Factory Contract deployment, go to ``` subgraph.yaml ``` and update the Factory address and block number of the factory contract creation:
```
address: '[new address]'
    abi: Factory
    startBlock: [block number of factory contract creation]
```
then go to ``` src/helpers.ts ``` and change ```export const FACTORY_ADDRESS = '0x3cc0fc3b82af5ea222d3545249f81644159eec8a'``` to new Factory Address.
<br/>

## 2. Deploy configurations
Then to deploy the subgraph go to ``` package.json ``` and change the value of ```deploy``` with subgraph username and subgraph name: 
<br/><br/>
**rastaswap/jt-subgraph --> [username]/[subgraph-name]**
<br/><br/>
Like in this example:
```
    "deploy-local": "graph deploy rastaswap/jt-subgraph --debug --ipfs http://localhost:5001 --node http://127.0.0.1:8020",
    "deploy": "graph deploy rastaswap/jt-subgraph --ipfs https://api.thegraph.com/ipfs/ --node https://api.thegraph.com/deploy/ --debug",
    "deploy-staging": "graph deploy rastaswap/jt-subgraph /Uniswap --ipfs https://api.staging.thegraph.com/ipfs/ --node https://api.staging.thegraph.com/deploy/",
```
To this: 
```
    "deploy-local": "graph deploy [username]/[subgraph-name] --debug --ipfs http://localhost:5001 --node http://127.0.0.1:8020",
    "deploy": "graph deploy [username]/[subgraph-name] --ipfs https://api.thegraph.com/ipfs/ --node https://api.thegraph.com/deploy/ --debug",
    "deploy-staging": "graph deploy [username]/[subgraph-name] /Uniswap --ipfs https://api.staging.thegraph.com/ipfs/ --node https://api.staging.thegraph.com/deploy/",
```
<br/>

## 3. Deploy configurations

Go to ``` src/mappings/pricing.ts ``` and search for ``` WINGS [TO BE CHANGED TO MAINNET] ``` comment then change with the new ```WINGS``` token contract address.

## 4. Build and Authenticate using Graph-CLI
- Run ``` yarn install ``` to install dependencies.
- Run ``` yarn codegen ``` to compile subgraph.
Run ``` graph auth https://api.thegraph.com/deploy/ [accesstoken]  ``` to authenticate with your access token that can be found on subgraph page.
- Run ``` yarn deploy ``` to deploy subgraph.

<br/>

## Running Locally

Make sure to update package.json settings to point to your own graph account.

## Queries

Below are a few ways to show how to query the RastaSwap-subgraph for data. The queries show most of the information that is queryable, but there are many other filtering options that can be used, just check out the [querying api](https://thegraph.com/docs/graphql-api). These queries can be used locally or in The Graph Explorer playground.

## Key Entity Overviews

#### RastaSwapFactory

Contains data across all of RastaSwap V2. This entity tracks important things like total liquidity (in ETH and USD, see below), all time volume, transaction count, number of pairs and more.

#### Token

Contains data on a specific token. This token specific data is aggregated across all pairs, and is updated whenever there is a transaction involving that token.

#### Pair

Contains data on a specific pair.

#### Transaction

Every transaction on RastaSwap is stored. Each transaction contains an array of mints, burns, and swaps that occured within it.

#### Mint, Burn, Swap

These contain specifc information about a transaction. Things like which pair triggered the transaction, amounts, sender, recipient, and more. Each is linked to a parent Transaction entity.

## Example Queries

### Querying Aggregated RastaSwap Data

This query fetches aggredated data from all RastaSwap pairs and tokens, to give a view into how much activity is happening within the whole protocol.

```graphql
{
  uniswapFactories(first: 1) {
    pairCount
    totalVolumeUSD
    totalLiquidityUSD
  }
}
```
