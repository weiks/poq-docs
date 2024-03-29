# POQ - OAuth2 API

This OAuth2 API allows game developers to have users delegate access to their
`Pocketful Of Quarters` (`PoQ`) user account. This page will guide you through
the integration process.

## Create a Quarters application

- If you don't have a PoQ account yet, create one at
  [https://www.poq.gg/login](https://www.poq.gg/login);
- Head towards [your user
  profile](https://www.poq.gg/profile) and click _'Enter Dev Portal'_ at the bottom of the section:

    <img width="416" alt="image" src="https://github.com/weiks/poq-docs/assets/36847481/c4b936d4-552e-4040-bebc-78b705113c65">

- Once you click the button you will be redirected to your [Dev Portal](https://poq.gg/dev), click the _'Create'_ button and fill out
the self-explanatory creation form:

    <img width="573" alt="image" src="https://github.com/weiks/poq-docs/assets/36847481/37e64b97-f140-4398-a4aa-8558c55686e6">

- Finally, you will be taken to your app's page:
_(`https://poq.gg/manage_app/<your_app_id>`)_, where you will find
your public and secret keys:

  <img width="547" alt="Screenshot 2023-09-05 at 15 39 16" src="https://github.com/weiks/poq-docs/assets/36847481/48e574ff-ac64-4ee6-8e14-284820beb62c">


## How to Submit Your App for Review

If you have followed the previous steps, you have just created your **Quarters Application**. Once you have completed the setup of your game and are ready to go live, you will need to submit your app for review and approval before it becomes **LIVE**.

- In your application's admin panel, click on the _'View Submissions'_ button

  <img width="547" alt="Screenshot 2023-09-05 at 15 39 16" src="https://github.com/weiks/poq-docs/assets/36847481/48e574ff-ac64-4ee6-8e14-284820beb62c">

- Once you're in the Reviews panel, create a submission by clicking the _'Submit APP NAME'_ button
- Your game is now in the process of being reviewed, and you will be notified of its progress through this panel. If you wish to leave a message for the administrator, you can do so:

  <img width="757" alt="image" src="https://github.com/weiks/poq-docs/assets/36847481/791b7423-2e39-4d4b-ae7a-4a806c55a053">

> **NOTE**
> You can test your integration by logging into your game using the same Quarters account you used to set up your app. Once your game is approved to go live, other Quarters accounts can send Quarters to your registered wallet as well.
---

## Integration via Authorization Flow

### 1 - Send your users to the authorization page, to request the access

```shell
https://www.poq.gg/oauth2/authorize?response_type=code&client_id=YOUR_CLIENT_ID&redirect_uri=YOUR_REDIRECT_URL&scope=email%20wallet%20transactions
```

| Parameter       | Required | Description                                                   |
| --------------- | -------- | ------------------------------------------------------------- |
| `response_type` | yes      | Must be `code`.                                               |
| `client_id`     | yes      | The value of the `client_id` field, _(see above screenshot)_. |
| `redirect_uri`  | yes      | The URL _(encoded)_ to redirect the users back to your app.   |
| `scope`         | yes      | Space delimited list of scopes                                |

**Available scopes:**

- `identity`: Identity of the PoQ user: `{ id, gamerTag, avatar }`;
- `email`: Same as `identity`, with the additional field `{ email }`.
- `wallet`: Allows for querying info about a user's wallet.
- `transactions`: Allows making transactions in behalf of the user.

### 2 - `PocketfulOfQuarters` redirects the users back to your app

If the user approves your authorization request, they will be redirected back to
your `redirect_uri` with a temporary code parameter.

```shell
https://test-game.games.poq.gg/poq_auth/success?code=eyJhbGciOiJIUzI1NiIsInR5
```

> In this example, `redirect_uri` is the auto-generated subdomain for your game.

### 3 - Request the access token

You can now use the `code` passed to your `redirect_uri` page to request the
`PoQ` access token from your backend.

```SHELL
curl -X POST \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "client_id=<CLIENT_ID>&client_secret=<CLIENT_SECRET>&grant_type=authorization_code&redirect_uri=<REDIRECT_URI>&code=eyJhbGciOiJIUzI1NiIsInR5" \
  https://www.poq.gg/api/oauth2/token
```

With the following parameters (`application/x-www-form-urlencoded`):

| Parameter       | Required | Description                                                   |
| --------------- | -------- | ------------------------------------------------------------- |
| `client_id`     | yes      | Value of the `client_id` field, _(see above screenshot)_.     |
| `client_secret` | yes      | Value of the `client_secret` field, _(see above screenshot)_. |
| `grant_type`    | yes      | Must be `authorization_code`.                                 |
| `code`          | yes      | Value from step (2).                                          |
| `redirect_uri`  | yes      | Same value as step (2).                                       |

The response's json:

```javascript
{
  token_type: "bearer",
  expires_in: 3600,
  access_token: "the_new_access_token",
  refresh_token: "the_new_refresh_token",
  scope:"email,wallet,transactions",
}
```

### 4 - Refreshing the access token

You can get a new `access_token` when the previous one expires, by calling the
same endpoint:

```shell
curl -X POST \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "client_id=<CLIENT_ID>&client_secret=<CLIENT_SECRET>&grant_type=refresh_token&refresh_token=<REFRESH_TOKEN>" \
  https://www.poq.gg/api/oauth2/token
```

With the following parameters (`application/x-www-form-urlencoded`):

| Parameter       | Required | Description                                                   |
| --------------- | -------- | ------------------------------------------------------------- |
| `client_id`     | yes      | Value of the `client_id` field, _(see above screenshot)_.     |
| `client_secret` | yes      | Value of the `client_secret` field, _(see above screenshot)_. |
| `grant_type`    | yes      | Must be `refresh_token`.                                      |
| `refresh_token` | yes      | `refresh_token` received from step (3).                       |
| `scope`         | no       | Same scope as initial, or restricted.                         |

> **Note**: Refresh tokens are valid for a duration of 6 months. Beyond that,
> the user will need to go through the authorization flow again.

(Same response as described in step (3)).

### 5. Make an API call

After you have a valid access token, you can make your first API call:

```shell
curl https://www.poq.gg/api/v2/oauth2/users/me -H 'Authorization: Bearer <your_access_token>'
```

Example response:

```javascript
{
  id: "aZKj1ae58awWEF63",
  gamerTag: "User1",
  avatar: "ccc954.png", // Full URL: https://api.poq.gg/images/${id}/${avatar}
  email: "user1@example.com", // email scope only
}
```

## Integration via Authorization Flow with Proof Key for Code Exchange (PKCE)

If your application has no server component and is a public application (native
apps, whether mobile or desktop) it is unadvisable to use the above flow as it
would require you to store your application's secret inside of the app, all
public applications can be mined for the secrets they contain thus making this
unadvisable, instead we offer the same flow with some slight modifications that
makes it usable by public applications without the need for storing secret
credentials

### 0 - Create a code verifier and derive a code challenge from it

Your application should generate a cryptographically random string between 43
and 128 characters that can only contain standard ASCII latin letters (both
upper case and lower case allowed), digits, underscores, hyphens and tildes. It
is advised to generate such a string with a cryptographically random number
generator with at least 256-bits of entropy. We will call this string **code
verifier** from here on out.

Next to generate the code challenge, the string must be hashed using the SHA256
algorithm. Then the resulted hash must be encoded using
[base64url](https://datatracker.ietf.org/doc/html/rfc4648#section-5) encoding.
(standard base64 encoding with + and / swapped for - and \_ and no padding at
the end)

### 1 - Send your users to the authorization page, to request the access

This is almost identical to step 1 without PKCE with two minute differences in
the query parameters: `code_challenge` and `code_challenge_method`

| Parameter               | Required | Description                                                        |
| ----------------------- | -------- | ------------------------------------------------------------------ |
| `code_challenge_method` | yes      | Must be `S256`.                                                    |
| `code_challenge`        | yes      | The code challenge generated in step 0, derived from code verifier |
| `response_type`         | yes      | Must be `code`. (Same as without PKCE)                             |
| `client_id`             | yes      | (Same as without PKCE)                                             |
| `redirect_uri`          | yes      | (Same as without PKCE)                                             |
| `scope`                 | yes      | (Same as without PKCE)                                             |

### 2 - PoQ redirects the user back to your app

This step is identical to step 2 without PKCE, they will also be redirected with
code and state (if present in step 1) or error and state (if something went
wrong)

### 3 - Request the access token

Again very similar to step 3 without PKCE, but instead of sending
`client_secret` the code verifier, as generated in step 0, is sent. (Reminder,
the body must be encoded with `application/x-www-form-urlencoded`)

| Parameter       | Required | Description                                         |
| --------------- | -------- | --------------------------------------------------- |
| `code_verifier` | yes      | The code verifier generated in step 0               |
| `client_id`     | yes      | (Same as without PKCE)                              |
| `grant_type`    | yes      | Must be `authorization_code`.(Same as without PKCE) |
| `code`          | yes      | Value from step (2). (Same as without PKCE)         |
| `redirect_uri`  | yes      | Same value as step (2). (Same as without PKCE)      |

### 4 & 5 - Refreshing access token and making requests

Nothing changes about refreshing the access token or making requests with the
acquired access token.

## Endpoints available - v2

Typical failed call of an endpoint:

```json
{
  "error": "error_type",
  "error_description": "..."
}
```

### `GET /api/v2/oauth2/users/me`

Fetches a user's account information.

- Scope: `identity` || `email`

```sh
curl -H "Authorization: Bearer <your-token>" https://www.poq.gg/api/v2/oauth2/users/me

# Response: {
#   "id": "XlPgcK8nfObx9wdIz2NU6087pfp2",
#   "gamerTag": "Mike2001",
#   "avatar": <base64 encoded png>,
#   "email": "user@isp.dom"
# }
```

---

### `GET /api/v2/oauth2/wallets/@me`

Fetches a user's wallet information.

- Scope: `wallet`

```sh
curl -H "Authorization: Bearer <your-token>" https://www.poq.gg/api/v2/oauth2/wallets/@me

# Response: {
#   "balance": 1000,
# }
```

---

### `POST /api/v2/oauth2/transactions`

Transfers Quarters from a user's wallet into your app's wallet.

- Scope: `transactions`;

#### Parameters:

| Name          | Type    | Description                                            |
| ------------- | ------- | ------------------------------------------------------ |
| `amount`      | integer | The amount of Quarters to transfer                     |
| `description` | string  | (optional) A label for the transaction.                |
|               |         |                                                        |

- Code example:

```sh
# /!\ On Windows, escape the double-quotes around the payload's fields
# /!\ On Windows 10, the powershell command `curl` isn't the "actual" curl

# Take 20 Quarters from a user
curl -X POST \
  -H "Authorization: Bearer <your-token>" \
  -H "Content-Type: application/json" \
  -d '{ "amount": 20, "description": "Entry fee for ..." }' \
  https://www.poq.gg/api/v2/oauth2/transactions

#  Response: { id }
#  `id`: Quarters transaction id (currently useless)
```

---
