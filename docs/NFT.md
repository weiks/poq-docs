# Non-Fungible Token (NFT) Support

## Overview

The Quarters system is adding support for NFTs! This means that you can now register custom and unique virtual items significant to your own games on the blockchain via Quarters. We've worked hard to make the process simple, with the same ease you've come to expect when integrating the Quarters universal gaming currency into your own game.

For this guide, a general understanding of the Pocketful of Quarters (PoQ) system is already assumed. This can be found following the [links on our homepage](https://www.poq.gg/). We also assume that general knowledge of [Blockchain](https://en.wikipedia.org/wiki/Blockchain) concepts and [Quarter's cross-game fungible cryptocurrency](https://invest.poq.gg/) is already understood.

If you feel anything in this guide needs further explanation, please open an issue on our [docs Github](https://github.com/weiks/poq-docs).

Our offering is built on top of the NFT support on the [Klaytn blockchain](https://www.klaytn.com/). For those interested in a deeper understanding of how your NFTs are actually being recorded on-chain, you can see the Klaytn Improvement Proposal (KIP) that standardizes their support of NFTs [here](https://kips.klaytn.com/KIPs/kip-17). However, no deep understanding is required to start utilizing NFTs in your own games!

## Prerequisites

You will first need to set up authentication with our [Oauth API](./oauth-api.md) to receive a valid Auth token before interacting with this API.

## NFT API
---

### `POST /api/v1/nft/create/collection`

Created NFT Collection with given name and symbol

- Scope:

  - user -> app: `nft`;
  - app -> user: none;

- Parameters:

| Name               | Type   | Description                    |
| ------------------ | ------ | ------------------------------ |
| `collectionName`   | string | Name of your NFT Collection.   |
| `collectionSymbol` | string | Symbol of your NFT Collection. |
| `description`      | string | A label for the transaction.   |
|                    |        |                                |

- Code example:

```sh
# /!\ On Windows, escape the double-quotes around the payload's fields
# /!\ On Windows 10, the powershell command `curl` isn't the "actual" curl

# Create New Collection
curl -X POST \
  -H "Authorization: Bearer <your-token>" \
  -H "Content-Type: application/json" \
  -d '{"collectionName":"MyCollection","collectionSymbol":"MyCol","description":"My First NFT Collection"}' \
  https://www.poq.gg/api/v1/create/collection


#  Response: { done, collectionName, collectionSymbol }
# `collectionName`: collectionName given by user
# `collectionSymbol`: collectionSymbol given by user
```

---

### `GET api/v1/nft/getCollections`

Query collection indexes for particular user participant

- Scope: `nft`

- Code example:

```sh

# Get the collection indexes of participant
curl -H "Authorization: Bearer <your-token>" \
  https://www.poq.gg/api/v1/nft/getCollections

#  Response: { collectionIds, done:true }
#  `collectionIds`: NFT collectionIndexes created for user
```

---

### `POST api/v1/nft/mint`

Mints a new NFT in a given collection

- Scope:

  - user -> app: `nft`;
  - app -> user: none;

- Parameters:

| Name                | Type   | Description                                                              |
| ------------------- | ------ | ------------------------------------------------------------------------ |
| `collectionIndex`   | number | Index of one of your NFT Collections where you wish to register the NFT. |
| `collectionAddress` | string | Address of the NFT collection where you wish to add the NFT.             |
| `uri`               | string | Metadata of new token.                                                   |
| `receiverAddress`   | string | Address to be initial owner of the new NFT.                              |
| `description`       | string | A label for the transaction.                                             |
|                     |        |                                                                          |

- Code example:

```sh
# /!\ On Windows, escape the double-quotes around the payload's fields
# /!\ On Windows 10, the powershell command `curl` isn't the "actual" curl

# Mint New NFT
curl -X POST \
  -H "Authorization: Bearer <your-token>" \
  -H "Content-Type: application/json" \
  -d '{"collectionIndex":0,"uri":"https://example-nft-uri.com","description":"My First Minted NFT","receiverAddress":"0x5cC788f1a171a024BcA758A34d50F55BE18f7cc0"}' \
  https://www.poq.gg/api/v1/nft/mint


#  Response: { done, id, collectionIndex, uri, nftReceiverAddress }
# `id`: Id of submitted transaction
# `collectionIndex`: given by user
# `uri`: given by user
# `nftReceiverAddress`: given by user
```

---

### `POST api/v1/nft/transfer`

Transfers an NFT to another address

- Scope:

  - user -> app: `nft`;
  - app -> user: none;

- Parameters:

| Name                | Type   | Description                     |
| -----------------   | ------ | ------------------------------- |
| `tokenId`           | number | Id of the token to transfer.    |
| `collectionAddress` | string | Address of the collection.           |
| `receiverAddress`   | string | Address to transfer the NFT to. |
| `description`       | string | A label for the transaction.    |
|                     |        |                                 |

- Code example:

```sh
# /!\ On Windows, escape the double-quotes around the payload's fields
# /!\ On Windows 10, the powershell command `curl` isn't the "actual" curl

# Mint New NFT
curl -X POST \
  -H "Authorization: Bearer <your-token>" \
  -H "Content-Type: application/json" \
  -d '{"tokenId":1,"description":"My First NFT Transfer","receiverAddress":"0x5cC788f1a171a024BcA758A34d50F55BE18f7cc0", "tokenAddress":"0xb52547dcc9c1b0557bf44b18988fa8d553d65c5e"}' \
  https://www.poq.gg/api/v1/nft/mint


#  Response: { done, id, tokenAddress, receiverAddress }
# `id`: Id of submitted transaction
# `tokenAddress`: given by user
# `receiverAddress`: given by user
```

---

### `GET api/v1/nft/getCollectionAddress`

Query collection address at given Index

- Scope: `nft`

- Code example:

```sh

# Get the collection address for given collectionIndex
curl -H "Authorization: Bearer <your-token>" \
  https://www.poq.gg/api/v1/nft/getCollectionAddress?collectionIndex=10

#  Response: { collectionAddress, done:true }
# `collectionAddress`: Collection Address for given Index
```
**_NOTE:_**  if collection doesn't exist fo given index it will return `0x0000000000000000000000000000000000000000`

---

### `POST api/v1/nft/getTokenIdsForCollection`

returns tokenids at given collection address for given public address wrt startIndex and lastIndex

- Scope:

  - user -> app: `nft`;
  - app -> user: none;

- Parameters:

| Name              | Type   | Description                     |
| -------------------- | ------ | ------------------------------- |
| `collectionAddress`  | string | Address of collection.          |
| `publicAddress`      | string | Public Address of user.         |
| `startIndex`         | number | starting index to start query   |
| `lastIndex`          | number | last index to stop query        |
|                      |        |                                 |

- Code example:

```sh
# /!\ On Windows, escape the double-quotes around the payload's fields
# /!\ On Windows 10, the powershell command `curl` isn't the "actual" curl

# Get Token Ids
curl -X POST \
  -H "Authorization: Bearer <your-token>" \
  -H "Content-Type: application/json" \
  -d '{"collectionAddress":"0xb52547dcc9c1b0557bf44b18988fa8d553d65c5e","publicAddress":"0xc1912fee45d61c87cc5ea59dae31190fffff23aa","startIndex":9, "lastIndex":10}' \
  https://www.poq.gg/api/v1/nft/getTokenIdsForCollection


#  Response: { done, tokenIds }
# `tokenIds`: array of token id
```
**_NOTE:_**  lastIndex must be less than nftBalance

---

### `GET api/v1/nft/getCollectionNameAndSymbol`

Returns TokenName can TokenSymbol of given collection address

- Scope: `nft`

- Code example:

```sh

# Get CollectionName and CollectionSymbol at given collectionAddress
curl -H "Authorization: Bearer <your-token>" \
  https://www.poq.gg/api/v1/nft/balanceOf?collectionAddress=0xb52547dcc9c1b0557bf44b18988fa8d553d65c5e

#  Response: { collectionName,collectionSymbol,done }
# `collectionName`: Name of collection
# 'collectionSymbol': Symbol of collection
```

---

### `GET api/v1/nft/balanceOf`

Returns balance at given collection address for given public address

- Scope: `nft`

- Code example:

```sh

# Get balance at given collection address for given public address
curl -H "Authorization: Bearer <your-token>" \
  https://www.poq.gg/api/v1/nft/balanceOf?collectionAddress=0xb52547dcc9c1b0557bf44b18988fa8d553d65c5e&publicAddress=0xc1912fee45d61c87cc5ea59dae31190fffff23aa

#  Response: { balance,done }
# `balance`: Balance of nft 
```