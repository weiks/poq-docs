# POQ - Game API

This API allows game developpers to perform server-side actions, such as
rewarding players with Quarters. Contrarily to [our Oauth2 API](./oauth-api.md),
in here we don't perform actions on behalf of a user, but on behalf of your game
itself.

The authentication of your requests is made by adding a `x-api-key` header with
your app's secret as value.

Typical failed call of an endpoint:

```json
{
  "error": "error_type",
  "error_description": "..."
}
```

## Endpoints available - v2

### `POST /api/v2/games/reward`

Transfers Quarters from your game's wallet into a user's wallet.

#### Parameters:

| Name          | Type    | Description                             |
| ------------- | ------- | --------------------------------------- |
| `user`        | string  | The PoQ user ID.                        |
| `amount`      | integer | The amount of Quarters to transfer      |
| `description` | string  | (optional) A label for the transaction. |
|               |         |                                         |

> **Note**: to find a given user's ID, see (see
> [/api/v2/oauth2/users/me](oauth-api.md#endpoints-available---v2))

```sh
# /!\ On Windows, escape the double-quotes around the payload's fields
# /!\ On Windows 10, the powershell command `curl` isn't the "actual" curl

# Reward 20 Quarters to a user
curl -X POST \
  -H "x-api-key: <your-client-secret>" \
  -H "Content-Type: application/json" \
  -d '{ "amount": 20, "user": "2c6657eb-d784-4077-9e4a-abfbebb9974d", "description": "Defeated boss ..." }' \
  https://www.poq.gg/api/v2/games/reward

#  Response: { id }
#  `id`: Quarters transaction id (currently useless)
```
