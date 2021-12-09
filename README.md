# PoQ - OAuth API

The OAuth API allows developers to use the OAuth2 protocol and grant their 3rd party application full or partial access to a `Pocketful Of Quarters` (`PoQ`) user account. This page will guide you through the integration process.

## Create an app

Any `PoQ` user can create a Quarters app. Your account must have been created from [https://www.poq.gg/](https://www.poq.gg/). Once that is done, head to [https://pocketfulofquarters.com/apps/new](https://pocketfulofquarters.com/apps/new), and fill the self-explanatory creation form:

![App creation form](medias/oauth/fill_form.png)

After which you'll be taken to your app's page _(`https://pocketfulofquarters.com/apps/<your_app_id>`)_, where you will find your public and secret keys:

![App infos](medias/oauth/your_app.png)

## Integration via Authorization Flow

### 1 - Send your users to the authorization page, to request the access

```CURL
GET https://www.poq.gg/api/oauth2/authorize?response_type=code&client_id=YOUR_CLIENT_ID&redirect_uri=YOUR_REDIRECT_URL&scope=email
```

| Parameter       | Required | Description                                                   |
| --------------- | -------- | ------------------------------------------------------------- |
| `response_type` | yes      | Must be `code`.                                               |
| `client_id`     | yes      | The value of the `client_id` field, _(see above screenshot)_. |
| `redirect_uri`  | yes      | The URL _(encoded)_ to redirect the users back to your app.   |
| `scope`         | yes      | Space delimited list of scopes                                |
| `state`         | no       | Use against CSRF and click-jacking.                           |

**Available scopes:**

- `identity`: Identity of the PoQ user: `{ id, gamerTag, avatar }`;
- `email`: Same as `identity`, with the additional field `{ email }`.
- `wallet`: Allows for querying info about a user's wallet.
- `transactions`: Allows making transactions in behalf of the user.
- `events`: Allows querying a user's participant status in events.

### 2 - `PocketfulOfQuarters` redirects the users back to your app

If the user approves your authorization request, they will be redirected back to your `redirect_uri` with a temporary code parameter.

```CURL
GET https://potatoheist.com/poq_auth/success?code=eyJhbGciOiJIUzI1NiIsInR5
```

> `redirect_uri` must be in `https`. Non-secure URIs can only be used for testing and will not be supported in production.

### 3 - Request the access token

You can now use the `code` passed to your `redirect_uri` page to request the `PoQ` access token from your backend.

```CURL
POST https://www.poq.gg/api/oauth2/token
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
  scope: "scope_requested_on_step_1"
}
```

### 4 - Refreshing the access token

You can get a new `access_token` when the previous one expires, by calling the same endpoint:

```CURL
POST https://www.poq.gg/api/oauth2/token
```

With the following parameters (`application/x-www-form-urlencoded`):

| Parameter       | Required | Description                                                   |
| --------------- | -------- | ------------------------------------------------------------- |
| `client_id`     | yes      | Value of the `client_id` field, _(see above screenshot)_.     |
| `client_secret` | yes      | Value of the `client_secret` field, _(see above screenshot)_. |
| `grant_type`    | yes      | Must be `refresh_token`.                                      |
| `refresh_token` | yes      | `refresh_token` received from step (3).                       |
| `scope`         | no       | Same scope as initial, or restricted.                         |

(Same response as described in step (3)).

### 5. Make an API call

After you have a valid access token, you can make your first API call:

```curl
curl https://www.poq.gg/api/v1/users/me -H 'Authorization: Bearer <your_access_token>'
```

Example response:

```javascript
{
  id: "aZKj1ae58awWEF63",
  gamerTag: "User1",
  avatar: "ccc954.png", // Full URL: https://www.poq.gg/images/${id}/${avatar}
  email: "user1@example.com", // email scope only
}
```

### 6. Putting it all together

- Create a new development app for yourself:
  https://sandbox.pocketfulofquarters.com/apps/new
- Use http://localhost:7777 as the App Url;
- Save the following code as `test.js`:

```javascript
const { URL } = require("url");
const express = require("express");
const got = require("got").default;

const CLIENT_ID = ""; // insert your client_id here
const CLIENT_SECRET = ""; // insert your client_secret here
const LINK = "https://s2w-dev-firebase.herokuapp.com/"; // for production, please use https://www.poq.gg
const SCOPE = "identity";
const PORT = 7777;

const demo = `http://localhost:${PORT}`;

const url = new URL("/api/oauth2/authorize", LINK);
url.searchParams.set("response_type", "code");
url.searchParams.set("scope", SCOPE);
url.searchParams.set("redirect_uri", demo);
url.searchParams.set("client_id", CLIENT_ID);

const main = `
<html>
  <head>
  </head>
  <body>
    <a href="${url.toString()}">Connect</a>
  </body>
</html>
`;

const app = express();

async function getInfo(code) {
  const response1 = await got.post("api/oauth2/token", {
    prefixUrl: LINK,
    form: {
      code,
      grant_type: "authorization_code",
      client_id: CLIENT_ID,
      client_secret: CLIENT_SECRET,
      redirect_uri: demo,
    },
    responseType: "json",
  });

  const response2 = await got.get("api/v1/users/me", {
    prefixUrl: LINK,
    headers: {
      Authorization: `Bearer ${response1.body.access_token}`,
    },
    responseType: "json",
  });

  return response2.body;
}

app.get("/", (req, res) => {
  if (!req.query.code) {
    res.setHeader("Content-Type", "text/html; charset=UTF-8");
    return res.status(200).send(main);
  } else {
    getInfo(req.query.code)
      .then((response) => res.send(response))
      .catch((error) => res.send(error));
  }
});

app.listen(PORT, () => {
  console.log(`Server is running on ${demo}`);
});
```

- Cut and paste the `client_id` and `client_secret` from the app page, into the code above;
- Install got and express and then run the test code;

```javascript
npm install got express
node test.js
```

- Open http://localhost:7777 in your browser, and click "connect". Authorize the app, and it will show the results of the API call that look something like this:

```json
{
  "id": "XlPgcK8nfObx9wdIz2NU6087pfp2",
  "gamerTag": "Mike2001",
  "avatar": "a51bed3a9b527fe45e9d65c23aa76ece.png"
}
```

- When you are ready to go to production, please repeat these steps but:

  a) Use https://pocketfulofquarters.com/apps/new to create a production app;

  b) In demo.js, replace LINK with:

```javascript
const LINK = "https://www.poq.gg"; // for development, you can use https://s2w-dev-firebase.herokuapp.com/
```

## Integration via Authorization Flow with Proof Key for Code Exchange (PKCE)

If your application has no server component and is a public application (native apps, whether mobile or desktop) it is unadvisable to use the above flow as it would require you to store your application's secret inside of the app, all public applications can be mined for the secrets they contain thus making this unadvisable, instead we offer the same flow with some slight modifications that makes it usable by public applications without the need for storing secret credentials

### 0 - Create a code verifier and derive a code challenge from it

Your application should generate a cryptographically random string between 43 and 128 characters that can only contain standard ASCII latin letters (both upper case and lower case allowed), digits, underscores, hyphens and tildes. It is advised to generate such a string with a cryptographically random number generator with at least 256-bits of entropy. We will call this string code verifier from here on out.

Next to generate the code challenge, the string must be hashed using the SHA256 algorithm. Then the resulted hash must be encoded using [base64url](https://datatracker.ietf.org/doc/html/rfc4648#section-5) encoding. (standard base64 encoding with + and / swapped for - and \_ and no padding at the end)

### 1 - Send your users to the authorization page, to request the access

This is almost identical to step 1 without PKCE with two minute differences in the query parameters: `code_challenge` and `code_challenge_method`

| Parameter               | Required | Description                                                        |
| ----------------------- | -------- | ------------------------------------------------------------------ |
| `code_challenge_method` | yes      | Must be `S256`.                                                    |
| `code_challenge`        | yes      | The code challenge generated in step 0, derived from code verifier |
| `response_type`         | yes      | Must be `code`. (Same as without PKCE)                             |
| `client_id`             | yes      | (Same as without PKCE)                                             |
| `redirect_uri`          | yes      | (Same as without PKCE)                                             |
| `scope`                 | yes      | (Same as without PKCE)                                             |
| `state`                 | no       | (Same as without PKCE)                                             |

### 2 - PoQ redirects the user back to your app

This step is identical to step 2 without PKCE, they will also be redirected with code and state (if present in step 1) or error and state (if something went wrong)

### 3 - Request the access token

Again very similar to step 3 without PKCE, but instead of sending `client_secret` the code verifier, as generated in step 0, is sent. (Reminder, the body must be encoded with `application/x-www-form-urlencoded`)

| Parameter       | Required | Description                                         |
| --------------- | -------- | --------------------------------------------------- |
| `code_verifier` | yes      | The code verifier generated in step 0               |
| `client_id`     | yes      | (Same as without PKCE)                              |
| `grant_type`    | yes      | Must be `authorization_code`.(Same as without PKCE) |
| `code`          | yes      | Value from step (2). (Same as without PKCE)         |
| `redirect_uri`  | yes      | Same value as step (2). (Same as without PKCE)      |

### 4 & 5 - Refreshing access token and making requests

Nothing changes about refreshing the access token or making requests with the acquired access token.

## Endpoints available - v1

Typical failed call of an endpoint:

```json
{
  "error": "error_type",
  "error_description": "..."
}
```

### `GET /api/v1/users/me`

Fetches a user's account information.

- Scope: `identity` || `email`

```sh
curl -H "Authorization: Bearer <your-token>" https://www.poq.gg/api/v1/users/me

# Response: {
#   "id": "XlPgcK8nfObx9wdIz2NU6087pfp2",
#   "gamerTag": "Mike2001",
#   "avatar": "a51bed3a9b527fe45e9d65c23aa76ece.png",
#   "email": "user@isp.dom"
# }
```

---

### `GET /api/v1/wallets/@me`

Fetches a user's wallet information.

- Scope: `wallet`

```sh
curl -H "Authorization: Bearer <your-token>" https://www.poq.gg/api/v1/wallets/@me

# Response: {
#   "balance": 1000,
# }
```

---

### `POST /api/v1/transactions`

Transfers Quarters between your app and a user, or vice versa.

- Scope:

  - user -> app: `transactions`;
  - app -> user: none;

- Parameters:

| Name          | Type    | Description                                            |
| ------------- | ------- | ------------------------------------------------------ |
| `creditUser`  | integer | The Quarters to transfer to the user. Can be negative. |
| `description` | string  | (optional) A label for the transaction.                |
|               |         |                                                        |

- Code example:

```sh
# /!\ On Windows, escape the double-quotes around the payload's fields
# /!\ On Windows 10, the powershell command `curl` isn't the "actual" curl

# Give 20 Quarters to a user
curl -X POST \
  -H "Authorization: Bearer <your-token>" \
  -H "Content-Type: application/json" \
  -d '{"creditUser":20}' \
  https://www.poq.gg/api/v1/transactions


# Take 50 Quarters from a user
curl -X POST \
  -H "Authorization: Bearer <your-token>" \
  -H "Content-Type: application/json" \
  -d '{"creditUser":-50, "description":"Entry fee for ..."}' \
  https://www.poq.gg/api/v1/transactions

#  Response: { id }
#  `id`: Quarters transaction id (currently useless)
```

---

### `GET /api/v1/games`

Returns the list of games supported by PoQ.

- Scope: none.

- Code example:

```sh
curl -H "Authorization: Bearer <your-token>" https://www.poq.gg/api/v1/games

#  Response:
# {
#    games : [
#     {
#       slug,         // The identifier of the game
#       name ,        // The game's name
#       description,  // A short description about the game
#       logo,         // The path to the game's logo
#       cover,        // The path to the game's cover
#       tags, styles  // Game-specific indicators of the game's gameplay types
#     }
#   ]
# }

```

---

### `GET /api/v1/events?{...args}`

Query information about the upcoming events.

- Scope: `events`

- Parameters:

| Name    | Type   | Description                                                                |
| ------- | ------ | -------------------------------------------------------------------------- |
| `game`  | string | (optional) If specified, will return events from that game only.           |
| `after` | Date   | (optional) If specified, will return events starting after the given date. |
|         |        |                                                                            |

- Code example:

```sh

# Get events for the game 'fortnite', starting after '2021-10-24T17:42:23.787Z'
curl -H "Authorization: Bearer <your-token>" \
  https://www.poq.gg/api/v1/events?game=fortnite&after=2021-10-24T17:42:23.787Z

#  Response:
# events : [
#   {
#     id,           // The internal identifier of the event
#     title,        // The event's title
#     hostId,       // The host's internal user id
#     host,         // The host's name
#     hostAvatar,   // The path to the host's avatar
#     date,         // The starting date of the event
#     openSlots,    // How many slots are still available
#     totalSlots,   // The total number of slot
#     fee,          // The normal Quarters fee to enter the event
#     vipFee,       // The Quarters fee to enter the event as VIP
#     game,         // The game being played in the event
#     tag, style    // Game-specific indicators of the event's gameplay type
#   }
# ]

```

---

### `GET /api/v1/events/{event_id}/participants/{user_id}`

Query information about a user's participation information (or lack thereof) in an event

- Scope: `events`

- Code example:

```sh

# Get the participation information of user 789 in event 123
curl -H "Authorization: Bearer <your-token>" \
  https://www.poq.gg/api/v1/events/123/participants/789

#  Response: { id, vip }
#  `id`: The same as the user id
#  `vip`: Whether the user joined the event as a VIP or not
```
