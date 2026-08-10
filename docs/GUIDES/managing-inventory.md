---
title: Managing Inventory
excerpt: >-
  Inventory is managed on products through Admin Integrations endpoints. There
  is no separate Inventory module.
deprecated: false
hidden: false
metadata:
  robots: index
---
## Scopes

Requires `products:write` (or `*`).

## Update stock

```http
PATCH /api/v1/integrations/admin/products/{id}/stock
X-Api-Key: sk_test_...
X-Store-Slug: your-store-slug
Content-Type: application/json

{
  "totalStock": 50
}
```

`totalStock` must be an integer ≥ 0.

## Update availability

```http
PATCH /api/v1/integrations/admin/products/{id}/availability
X-Api-Key: sk_test_...
X-Store-Slug: your-store-slug
Content-Type: application/json

{
  "isAvailable": false
}
```

Use availability to stop selling a product without deleting it; use stock for quantity sync from an ERP or warehouse.

## Typical sync flow

```text
1. GET /api/v1/integrations/admin/products (or export)
2. Diff against your system of record
3. PATCH .../stock and/or .../availability per product
4. Optionally listen for orders/create webhooks to decrement locally
```

## Related

- [Managing Products](managing-products.md)
- [Webhooks](webhooks.md)
- [Creating Orders](creating-orders.md)
