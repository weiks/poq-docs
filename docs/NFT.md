# Non-Fungible Token (NFT) Support

## Overview

The Quarters system is adding support for NFTs! This means that you can now register custom and unique virtual items significant to your own games on the blockchain via Quarters. We've worked hard to make the process simple, with the same ease you've come to expect when integrating the Quarters universal gaming currency into your own game.

For this guide, a general understanding of the Pocketful of Quarters (PoQ) system is already assumed. This can be found following the [links on our homepage](https://www.poq.gg/). We also assume that general knowledge of [Blockchain](https://en.wikipedia.org/wiki/Blockchain) concepts and [Quarter's cross-game fungible cryptocurrency](https://invest.poq.gg/) is already understood.

If you feel anything in this guide needs further explanation, please open an issue on our [docs Github](https://github.com/weiks/poq-docs).

Our offering is built on top of the NFT support on the [Klaytn blockchain](https://www.klaytn.com/). For those interested in a deeper understanding of how your NFTs are actually being recorded on-chain, you can see the Klaytn Improvement Proposal (KIP) that standardizes their support of NFTs [here](https://kips.klaytn.com/KIPs/kip-17). However, no deep understanding is required to start utilizing NFTs in your own games!

## NFT API

---

### `POST /v1/nft/changeDeveloperStatus`

Created NFT Collection with given name and symbol

- Scope:

  - user -> app: `nft`;
  - app -> user: none;

- Parameters:

| Name     | Type    | Description                                  |
| -------- | ------- | -------------------------------------------- |
| `status` | boolean | New Status For Developer.                    |
| `userId` | string  | UserId of user whose status need to changed. |
|          |         |                                              |

- Code example:

```sh
# /!\ On Windows, escape the double-quotes around the payload's fields
# /!\ On Windows 10, the powershell command `curl` isn't the "actual" curl

# Create New Collection
curl -X POST \
  -H "Authorization: Bearer <your-token>" \
  -H "Content-Type: application/json" \
  -d '{"status":true ,"userId":'58uty'}' \
  https://www.poq.gg/api/v1/nft/changeDeveloperStatus


#  Response: { done, status}
# `status`: new status for user
```

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
  -d '{"collectionName":'MyCollection',"collectionSymbol":'MyCol'","description":'My First NFT Collection'}' \
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