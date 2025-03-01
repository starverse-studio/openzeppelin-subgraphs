# OpenZeppelin Subgraphs

## Introduction

This repo contains subgraph schema and templates to index the activity of OpenZeppelin Contracts. For each of the supported OpenZeppelin modules `x`, this repo provides:

- **Primitives to generate a graphql schema:** `src/datasource/x.gql.json`

  In order to allow composability, the schema are not defined in the graphql format but rather in a dedicated json format which is can be assembled and compiled to graphql using the `graph-compiler` tool from `@amxx/graphprotocol-utils`. Graphql version for each modules are also available in `generated/x.schema.graphql`

- **Template to generate a subgraph manifest:** `src/datasource/x.yaml`

  This file lists all the events that the datasources should listen to, and links that to the corresponding indexing logic. Similarly to the schema, the manifest can be generated procedurally from a JSON app description.

- **Indexing logic:** `src/datasources/x.ts` and (optionally) `src/fetch/x.ts`

  This is the core logic that processes the events and to index the onchain activity.

## Suported modules

- AccessControl
- ERC20
- ERC721
- ERC1155
- ERC1967Upgrade
- Governor
- Ownable
- Pausable
- Timelock

<!--
PaymentSplitter
ERC20Snapshot
ERC20Votes
ERC777
Escrow
-->

## Usage

To use OpenZeppelin Subgraphs, install them in your local project directory and follow the steps outlined in __How to build my app's subgraph__:

```sh
yarn install @openzeppelin/subgraphs
```

## How to build my app's subgraph

In order to build your subgraph, the first step is to create a JSON file listing the modules you want to index. Examples of such config can be found in the `config` folder.

For example, `configs/sample.json` describes an app with 4 contracts, the first one is an `ERC20` with `AccessControl`, while the 3 others are `ERC721` registries.

```
{
  "output": "generated/sample.",
  "chain": "mainnet",
  "datasources": [
    { "address": "0xA3B26327482312f70E077aAba62336f7643e41E1", "startBlock": 11633151, "module": [ "erc20", "accesscontrol" ] },
    { "address": "0xB1C52075b276f87b1834919167312221d50c9D16", "startBlock":  9917641, "module": "erc721" },
    { "address": "0x799DAa22654128d0C64d5b79eac9283008158730", "startBlock":  9917642, "module": "erc721" },
    { "address": "0xC76A18c78B7e530A165c5683CB1aB134E21938B4", "startBlock":  9917639, "module": "erc721" }
  ]
}
```

It can be compiled by doing

```
yarn graph-compiler \
  --config configs/star.json \
  --include node_modules/@openzeppelin/subgraphs/src/datasources \
  --export-schema \
  --export-subgraph
```

This will create two files, `generated/sample.schema.graphql` and `generated/sample.subgraph.yaml` that can be used to build and deploy the corresponding subgraph.

Note: `startBlock` is optional but will improve your subgraph initial indexing speed.

# Deploy on graphnode:

```
npx graph deploy --node http://localhost:8020/ --ipfs http://localhost:5001 project/starTicket generated/star.subgraph.yaml
```

For custom code, need to replace include src/datasources from local instead of openzeppelin
