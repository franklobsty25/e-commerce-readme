---
title: Authentication
excerpt: >-
  Integrations requests authenticate with an API key or an OAuth access token.
  Scopes control which resources you can access.
deprecated: false
hidden: false
metadata:
  robots: index
---
## API key

Send the key in either header:

```http
X-Api-Key: sk_test_...
```

or

```http
Authorization: Bearer sk_test_...
```

| Prefix     | Environment    |
| ---------- | -------------- |
| `sk_test_` | Non-production |
| `sk_live_` | Production     |

Keys are stored as hashes. The raw secret is shown only at creation time. Revoked, expired, or suspended keys return `401 Unauthorized`.

See [API Keys](https://benchfive-commer.readme.io/v1.0_GUIDES/docs/api-keys) for creation, scopes, and rotation.

## OAuth access token

Partner apps use OAuth 2.0. After authorization and token exchange:

```http
Authorization: Bearer <oauth_access_token>
```

Do **not** use an `sk_*` value on the OAuth path. OAuth tokens carry the scopes granted at install/authorize time.

See [OAuth Apps](https://benchfive-commer.readme.io/v1.0_GUIDES/docs/oauth-apps).

## How the guard resolves credentials

`IntegrationAuthGuard` accepts:

1. `X-Api-Key` header, or
2. `Authorization: Bearer …` where the token is either an API key (`sk_*`) or an OAuth access token

Missing or invalid credentials → `401 Unauthorized`.

## Scopes

Routes declare required scopes (for example `@RequireScopes('products:read')`). `RequireScopesGuard` enforces them.

| Result | Meaning                                                                  |
| ------ | ------------------------------------------------------------------------ |
| `403`  | Authenticated but missing required scopes (`Missing required scopes: …`) |
| Pass   | Key/token has the required scope, or `*` (full store access)             |

Canonical scope list:

```text
products:read / products:write
categories:read / categories:write
collections:read / collections:write
colors:read / colors:write
sizes:read / sizes:write
orders:read / orders:write
discounts:read / discounts:write
customers:read / customers:write
shipping:read / shipping:write
payments:read / payments:write
settings:read / settings:write
themes:read / themes:write
marketing:read / marketing:write
notifications:read / notifications:write
team:read / team:write
subscribers:read
dashboard:read
storefront:read
rates:read / rates:write
*
```

Cart endpoints use order scopes (`orders:read` / `orders:write`). Storefront customer register/login/profile uses `storefront:read`. Merchant customer list/disable uses `customers:read` / `customers:write` on Admin Integrations.

## Customer JWT (optional shopper identity)

Customer account endpoints live on Integrations Storefront:

```text
POST /api/v1/integrations/auth/customer/register
POST /api/v1/integrations/auth/customer/login
POST /api/v1/integrations/auth/customer/logout
GET  /api/v1/integrations/auth/customer/me
GET  /api/v1/integrations/customers/me/addresses
GET  /api/v1/integrations/customers/me/orders
```

Login and register authenticate the **store** with an API key or OAuth token. They return a customer JWT (`role: customer`). Authenticated customer routes and optional cart/checkout identity use **two headers**:

```http
X-Api-Key: sk_test_...
Authorization: Bearer <customer_jwt>
```

Do not put the API key and the customer JWT in the same `Authorization` header. A customer JWT alone on `/integrations/*` returns `401 Unauthorized` because `IntegrationAuthGuard` treats Bearer as an OAuth token.

Cart create and checkout (`POST /integrations/carts`, `POST /integrations/orders`) accept the same optional customer JWT. Without it, those routes stay guest-session based (`X-Session-Id`).

OAuth apps that also need a shopper JWT must send an API key in `X-Api-Key` for authenticated customer and cart/checkout calls (Bearer is occupied by the JWT).

Logout uses the same dual headers:

```http
POST /api/v1/integrations/auth/customer/logout
X-Api-Key: sk_test_...
Authorization: Bearer <customer_jwt>
```

The response is `{ "loggedOut": true }`. The current customer JWT is revoked server-side and later requests with that token return `401 Unauthorized`. After logout, drop the JWT on the client. Cart and checkout without it stay guest-session based (`X-Session-Id`).

## Store context

API keys and OAuth installations are bound to one store. Integrations routes take the tenant from that credential — store headers are not required.

| Header         | Description                                          |
| -------------- | ---------------------------------------------------- |
| `X-Store-Slug` | Optional; must match the credential store if sent    |
| `X-Store-Id`   | Optional store `publicId` (UUID); must match if sent |

If Host already maps to a store and it differs from the key/token store, the request returns `403 Forbidden`.

## What not to use

| Scheme                                         | Audience                                                                             |
| ---------------------------------------------- | ------------------------------------------------------------------------------------ |
| Merchant JWT                                   | Merchant dashboard Admin API only                                                    |
| Guest cookie `guest.sid` / session Bearer      | Cookie storefront, not Integrations                                                  |
| Customer JWT in Bearer **without** `X-Api-Key` | Store API customer accounts only; Integrations requires the store key in `X-Api-Key` |

## Related

- [API Keys](https://benchfive-commer.readme.io/v1.0_GUIDES/docs/api-keys)
- [OAuth Apps](https://benchfive-commer.readme.io/v1.0_GUIDES/docs/oauth-apps)
- [Errors](https://benchfive-commer.readme.io/v1.0_GUIDES/docs/errors)
