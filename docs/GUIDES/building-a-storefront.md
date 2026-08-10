---
title: Building a Storefront
excerpt: >-
  This guide outlines a headless storefront using the Integrations Storefront
  API (API key or OAuth), not the cookie-based Store API.
deprecated: false
hidden: false
metadata:
  robots: index
---
## Recommended scopes

```json
[
  "products:read",
  "categories:read",
  "collections:read",
  "colors:read",
  "sizes:read",
  "orders:read",
  "orders:write",
  "discounts:read",
  "shipping:read",
  "payments:read",
  "themes:read",
  "storefront:read",
  "rates:read",
  "marketing:read"
]
```

## Suggested page flow

```text
Home/collection
  → product detail
  → cart
  → shipping + discount
  → create order (checkout)
  → payment redirect
  → verify payment
  → thank-you
```

## 1. Resolve store and theme

```http
GET /api/v1/integrations/theme
GET /api/v1/integrations/pages/{handle}
X-Api-Key: sk_test_...
X-Store-Slug: your-store-slug
```

Scope: `themes:read`.

## 2. Load catalog

```http
GET /api/v1/integrations/products
GET /api/v1/integrations/products/{slug}
GET /api/v1/integrations/collections
GET /api/v1/integrations/categories
```

Use product payloads to render variants (size/color). There is no separate variants API.

## 3. Storefront helpers

```http
GET  /api/v1/integrations/gallery-images
POST /api/v1/integrations/contact
GET  /api/v1/integrations/geo
GET  /api/v1/integrations/currencies
```

Scope: `storefront:read`.

## 4. Cart and checkout

```http
POST /api/v1/integrations/carts
PATCH /api/v1/integrations/carts/{id}
POST /api/v1/integrations/orders
```

See [Creating Orders](creating-orders.md) for the full order body (including required `variants` on each item).

## 5. Shipping and discounts

Use Storefront shipping and discounts endpoints to quote rates and validate codes before `POST /orders`. Pass shipping selection and `discountCode` on the order when applicable.

## 6. Payments

1. Create the order with `redirectUrl` and optional `paymentProvider`
2. Redirect the shopper
3. `GET /api/v1/integrations/payments/verify/{reference}`

Details: <Anchor target="_blank" href="https://benchfive-commer.readme.io/v1.0_GUIDES/update/docs/processing-payments">Processing Payments</Anchor>.

## Security notes

- Keep API keys on a **backend** or BFF. Do not embed `sk_live_` keys in a public browser bundle.
- Prefer short-lived OAuth tokens for multi-tenant apps.
- Always send `X-Store-Slug` (or rely on a host that resolves the store).

## Related guides

- [Managing Products](managing-products.md)
- [Creating Orders](creating-orders.md)
- <Anchor target="_blank" href="https://benchfive-commer.readme.io/v1.0_GUIDES/update/docs/processing-payments">Processing Payments</Anchor>
- <Anchor target="_blank" href="https://benchfive-commer.readme.io/v1.0_GUIDES/update/docs/webhooks">Webhooks</Anchor>
