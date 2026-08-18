---
title: API Keys
excerpt: >-
  API keys are the simplest way for a single store integration to call the
  Integrations API.
deprecated: false
hidden: false
metadata:
  robots: index
---
## Create a key

Merchant Admin endpoint (JWT required — not an Integrations route):

```http
POST /api/v1/admin/api-keys
Authorization: Bearer <merchant_jwt>
Content-Type: application/json

{
  "name": "Inventory sync",
  "scopes": ["products:read", "products:write", "orders:read"],
  "expiresAt": "2027-01-01T00:00:00.000Z"
}
```

| Field       | Required | Description                                                |
| ----------- | -------- | ---------------------------------------------------------- |
| `name`      | Yes      | Label shown in the merchant dashboard                      |
| `scopes`    | No       | Resource scopes. Omit or use `["*"]` for full store access |
| `expiresAt` | No       | ISO-8601 expiry; omit for no expiry                        |

Response includes the raw key **once**. Save it immediately.

## List and revoke

```http
GET /api/v1/admin/api-keys
Authorization: Bearer <merchant_jwt>
```

```http
DELETE /api/v1/admin/api-keys/:id
Authorization: Bearer <merchant_jwt>
```

Revoking a key immediately invalidates Integrations calls that use it.

## Key format

| Prefix     | When issued                 |
| ---------- | --------------------------- |
| `sk_test_` | Non-production environments |
| `sk_live_` | Production environments     |

Treat keys like passwords. Never commit them to source control or expose them in client-side browsers.

## Calling Integrations with a key

```http
GET /api/v1/integrations/products
X-Api-Key: sk_test_...
X-Store-Slug: your-store-slug
```

Equivalent:

```http
GET /api/v1/integrations/products
Authorization: Bearer sk_test_...
X-Store-Slug: your-store-slug
```

## Scopes

Scopes are enforced per route. Common patterns:

| Goal              | Suggested scopes                                                                                   |
| ----------------- | -------------------------------------------------------------------------------------------------- |
| Read-only catalog | `products:read`, `categories:read`, `collections:read`                                             |
| Headless checkout | `products:read`, `orders:read`, `orders:write`, `discounts:read`, `shipping:read`, `payments:read` |
| Inventory sync    | `products:read`, `products:write`                                                                  |
| Full access       | `*`                                                                                                |

See [Authentication](https://benchfive-commer.readme.io/v1.0_GUIDES/docs/authentication) for the full list.

## Rotation

1. Create a new key with the same scopes
2. Deploy the new key to your integration
3. Delete the old key

There is no “reveal again” endpoint for the raw secret.

## Related

- [Quickstart](https://benchfive-commer.readme.io/v1.0_GUIDES/docs/quickstart)
- [OAuth Apps](https://benchfive-commer.readme.io/v1.0_GUIDES/docs/oauth-apps) — for multi-merchant partner apps
- [Rate Limits](https://benchfive-commer.readme.io/v1.0_GUIDES/docs/rate-limits)
