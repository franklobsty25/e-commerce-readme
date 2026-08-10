---
title: Managing Products
excerpt: >-
  Use the Integrations Storefront API to read the catalog and the Admin API to
  create and update products.
deprecated: false
hidden: false
metadata:
  robots: index
---
## Scopes

| Action                            | Scope            |
| --------------------------------- | ---------------- |
| List / get products               | `products:read`  |
| Create / update / delete / images | `products:write` |

## Storefront — browse the catalog

```http
GET /api/v1/integrations/products
X-Api-Key: sk_test_...
X-Store-Slug: your-store-slug
```

```http
GET /api/v1/integrations/products/{slug}
X-Api-Key: sk_test_...
X-Store-Slug: your-store-slug
```

Related catalog resources (same auth pattern):

| Resource    | Path                               |
| ----------- | ---------------------------------- |
| Categories  | `/api/v1/integrations/categories`  |
| Collections | `/api/v1/integrations/collections` |
| Colors      | `/api/v1/integrations/colors`      |
| Sizes       | `/api/v1/integrations/sizes`       |

## Admin — manage products

```http
GET /api/v1/integrations/admin/products
POST /api/v1/integrations/admin/products
GET /api/v1/integrations/admin/products/{id}
PATCH /api/v1/integrations/admin/products/{id}
DELETE /api/v1/integrations/admin/products/{id}
```

Also available:

- `GET /api/v1/integrations/admin/products/stats`
- `GET /api/v1/integrations/admin/products/export`
- `POST /api/v1/integrations/admin/products/{id}/images`
- `DELETE /api/v1/integrations/admin/products/{id}/images/{imageId}`

Request and response schemas are in the **Integrations Admin** OpenAPI.

## Variants

There is no standalone `/variants` resource. Variants are nested on product payloads and referenced on order line items via `productVariantId`, `sizeId`, and `colorId`.

When building a storefront:

1. Load the product (Storefront) to discover variant/size/colour options
2. Pass the selected variant IDs when creating an order

## Media

Product images are attached through Admin product image endpoints. Uploaded files are typically served from the static `/media` path. There is no separate Media CRUD API for partners.

## Inventory

Stock and availability updates are covered in [Managing Inventory](managing-inventory.md).

## Related

- <Anchor target="_blank" href="https://benchfive-commer.readme.io/v1.0_GUIDES/docs/managing-inventory">Managing Inventory</Anchor>
- <Anchor target="_blank" href="https://benchfive-commer.readme.io/v1.0_GUIDES/docs/building-a-storefront">Building a Storefront</Anchor>
- Admin OpenAPI tag: **Integrations Admin Products**
- Storefront OpenAPI tag: **Integrations Products**
