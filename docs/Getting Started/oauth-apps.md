---
title: OAuth Apps
excerpt: >-
  Use OAuth when you build a partner app that merchants install on their stores.
  After install and authorization, call the same Integrations routes with an
  access token instead of a store API key.
deprecated: false
hidden: false
metadata:
  robots: index
---
## Flow overview

```text
1. Register partner
2. Create app (client_id + one-time client_secret)
3. Merchant installs app (optional webhookUrl)
4. Authorize (merchant JWT) → authorization code
5. Exchange code for access + refresh tokens
6. Call /api/v1/integrations/* with Bearer access token
```

## 1. Register a partner

```http
POST /api/v1/partners
Content-Type: application/json

{
  "name": "Acme Integrations",
  "email": "dev@acme.example"
}
```

## 2. Create an app

```http
POST /api/v1/partners/apps
Content-Type: application/json

{
  "partnerId": 1,
  "name": "Acme Sync",
  "redirectUris": ["https://acme.example/oauth/callback"],
  "scopes": ["products:read", "orders:read", "orders:write"],
  "isListed": false
}
```

The response includes `clientId` and a one-time `clientSecret`. Store the secret securely.

## 3. Merchant installs the app

Merchant Admin (JWT):

```http
POST /api/v1/admin/apps/:publicId/install
Authorization: Bearer <merchant_jwt>
Content-Type: application/json

{
  "webhookUrl": "https://acme.example/webhooks/orders"
}
```

If `webhookUrl` is provided, the platform registers an outbound webhook for topic `orders/create`. See [Webhooks](../guides/webhooks.md).

## 4. Authorize

```http
GET /api/v1/oauth/authorize?client_id=...&redirect_uri=...&scope=...&state=...
Authorization: Bearer <merchant_jwt>
```

The merchant must be logged in (JWT). On success, the app receives an authorization code via redirect.

## 5. Exchange the code

```http
POST /api/v1/oauth/token
Content-Type: application/json

{
  "grant_type": "authorization_code",
  "client_id": "app_...",
  "client_secret": "...",
  "code": "...",
  "redirect_uri": "https://acme.example/oauth/callback"
}
```

Refresh:

```http
POST /api/v1/oauth/token
Content-Type: application/json

{
  "grant_type": "refresh_token",
  "client_id": "app_...",
  "client_secret": "...",
  "refresh_token": "..."
}
```

Revoke:

```http
POST /api/v1/oauth/revoke
```

## 6. Call Integrations

```http
GET /api/v1/integrations/admin/orders
Authorization: Bearer <oauth_access_token>
X-Store-Slug: installed-store-slug
```

Scopes on the token must satisfy each route’s `@RequireScopes` requirements.

## Uninstall

```http
DELETE /api/v1/admin/apps/installations/:id
Authorization: Bearer <merchant_jwt>
```

Uninstall revokes tokens and deactivates related webhook subscriptions for that installation.

## When to use API keys instead

| Use API keys                      | Use OAuth                               |
| --------------------------------- | --------------------------------------- |
| Single-store custom integration   | Multi-merchant partner product          |
| Merchant-owned script or ERP sync | App listing / marketplace style install |
| Simplest setup                    | Delegated access with refresh tokens    |

## Related

- [Authentication](authentication.md)
- [API Keys](api-keys.md)
- [Webhooks](../guides/webhooks.md)
