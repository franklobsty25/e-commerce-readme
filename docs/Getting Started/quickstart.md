---
title: Quickstart
excerpt: Get a working Integrations call in a few minutes.
deprecated: false
hidden: false
metadata:
  robots: index
---
## Prerequisites

- A merchant store on the platform
- Access to create an API key (merchant admin JWT), **or** a partner OAuth app installed on the store
- Your API base URL (for example [`https://api.example.com` or `http://localhost:3000`)]()

## 1. Create an API key

As a merchant (Admin API), create a key:

```http
POST /api/v1/admin/api-keys
Authorization: Bearer <merchant_jwt>
Content-Type: application/json

{
  "name": "My integration",
  "scopes": ["products:read", "orders:read", "orders:write"]
}
```

The response includes the raw key **once**. It looks like:

```json
sk_test_...   # non-production
sk_live_...   # production (APP_ENV=production)
```

Store it securely. You cannot retrieve the raw value again.

> Prefer least-privilege scopes. Use `["*"]` only for trusted full-access integrations.

## 2. List products (Storefront)

```bash
curl -sS "$BASE_URL/api/v1/integrations/products" \
  -H "X-Api-Key: sk_test_YOUR_KEY" \
  -H "X-Store-Slug: your-store-slug"
```

You can also send the key as a Bearer token:

```bash
curl -sS "$BASE_URL/api/v1/integrations/products" \
  -H "Authorization: Bearer sk_test_YOUR_KEY" \
  -H "X-Store-Slug: your-store-slug"
```

Required scope: `products:read` (or `*`).

## 3. Create an order (checkout)

Checkout is `POST /api/v1/integrations/orders` (there is no separate checkout resource).

```bash
curl -sS -X POST "$BASE_URL/api/v1/integrations/orders" \
  -H "X-Api-Key: sk_test_YOUR_KEY" \
  -H "X-Store-Slug: your-store-slug" \
  -H "Content-Type: application/json" \
  -d '{
    "customerName": "Jane Doe",
    "customerEmail": "shopper@example.com",
    "shippingAddress": "123 Main Street, City",
    "redirectUrl": "https://store.example.com/checkout/complete",
    "items": [
      {
        "productId": 1,
        "quantity": 1,
        "variants": [
          {
            "productVariantId": 1,
            "sizeId": 1,
            "colorId": 1,
            "quantity": 1
          }
        ]
      }
    ]
  }'
```

Required scope: `orders:write`. Exact body fields follow the OpenAPI schema for create order — see the Storefront API Reference.

## 4. Explore Admin endpoints

To update stock after syncing inventory:

```bash
curl -sS -X PATCH "$BASE_URL/api/v1/integrations/admin/products/1/stock" \
  -H "X-Api-Key: sk_test_YOUR_KEY" \
  -H "X-Store-Slug: your-store-slug" \
  -H "Content-Type: application/json" \
  -d '{ "totalStock": 42 }'
```

Required scope: `products:write`.

## Interactive docs

- Live Swagger UI: `/api/docs/integrations`
- Exported OpenAPI for ReadMe:
  - `docs/readme/openapi/integrations-storefront.json`
  - `docs/readme/openapi/integrations-admin.json`

## Next steps

- [Authentication](authentication.md)
- [API Keys](api-keys.md)
- [Building a Storefront](../guides/building-a-storefront.md)
- [Webhooks](../guides/webhooks.md)
