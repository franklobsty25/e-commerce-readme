---
title: Creating Orders
excerpt: >-
  Checkout on the Integrations API is create order. There is no separate
  `/checkout` resource.
deprecated: false
hidden: false
metadata:
  robots: index
---
## Scopes

| Action                           | Scope                                             |
| -------------------------------- | ------------------------------------------------- |
| Create order (Storefront)        | `orders:write`                                    |
| Get order by number (Storefront) | `orders:read`                                     |
| List / manage orders (Admin)     | `orders:read` (and write scopes where applicable) |
| Cart operations                  | `orders:read` / `orders:write`                    |

## Optional: build a cart first

```http
POST /api/v1/integrations/carts
GET  /api/v1/integrations/carts
GET  /api/v1/integrations/carts/{id}
PATCH /api/v1/integrations/carts/{id}
DELETE /api/v1/integrations/carts/{id}
```

Carts help assemble line items before checkout. You can also create an order directly with `items`.

## Create an order (checkout)

```http
POST /api/v1/integrations/orders
X-Api-Key: sk_test_...
X-Store-Slug: your-store-slug
Content-Type: application/json

{
  "customerName": "Jane Doe",
  "customerEmail": "jane@example.com",
  "customerPhone": "+1234567890",
  "shippingAddress": "123 Main Street, City",
  "redirectUrl": "https://store.example.com/checkout/complete",
  "discountCode": "INNER10",
  "paymentProvider": "PAYSTACK",
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
}
```

Required fields include `customerName`, `customerEmail`, `shippingAddress`, `redirectUrl`, and at least one `items[]` entry with `variants`.

Optional helpers before create:

- Validate discounts: Storefront discounts endpoints
- Quote shipping: Storefront shipping endpoints
- Pass `selectedShippingRate` / `shippingAddressDetails` when using structured shipping

## Create an order (checkout) with full request body

```http
POST /api/v1/integrations/orders
X-Api-Key: sk_test_...
X-Store-Slug: your-store-slug
Content-Type: application/json

{
  "customerName": "Jane Doe",
  "customerEmail": "jane@example.com",
  "customerPhone": "+1234567890",
  "shippingAddress": "123 Main Street, City",
  "shippingAddressDetails": {
    "countryCode": "GH",
    "cityName": "Accra",
    "postalCode": "00233",
    "addressLine1": "Community 16 Junction",
    "addressLine2": "Spintex Road"
  },
  "selectedShippingRate": {
    "method": "local-ghana",
    "productCode": "LOCAL_GHANA",
    "productName": "Local delivery",
    "amount": 120,
    "currency": "GHS",
    "estimatedDeliveryAt": "2026-07-04T17:00:00Z"
  },
  "deliveryNote": "Leave at the front desk",
  "redirectUrl": "https://store.example.com/checkout/complete",
  "discountCode": "INNER10",
  "paymentProvider": "PAYSTACK",
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
  ],
  "isCart": true,
  "marketingAttribution": {
  "eventId": "evt_01J7K2M3N4P5Q6R7S8T9U0",
  "fbp": "fb.1.1704067200000.1234567890",
  "fbc": "fb.1.1704067200000.IwAR0AbCdEfGhIjKlMnOpQrStUvWxYz",
  "ttclid": "E.C.P.v3.AbcDefGhiJklMnoPqrStuVwxYz.1234567890",
  "scCid": "12345678-90ab-cdef-1234-567890abcdef",
  "epik": "dj0yJnU9YWJjZGVmZ2hpams",
  "gclid": "Cj0KCQjwexampleGclidValue",
  "gbraid": "0AAAAAexampleGbraidValue",
  "wbraid": "0AAAAAexampleWbraidValue",
  "clientIp": "203.0.113.42",
  "userAgent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36"
  }
}
```

## Fetch an order

```http
GET /api/v1/integrations/orders/{orderNumber}
X-Api-Key: sk_test_...
X-Store-Slug: your-store-slug
```

## Admin order tools

```http
GET /api/v1/integrations/admin/orders
GET /api/v1/integrations/admin/orders/stats
GET /api/v1/integrations/admin/orders/export
GET /api/v1/integrations/admin/orders/{orderNumber}
```

## After create

Successful order creation may:

1. Return payment redirect/provider details for the shopper
2. Dispatch outbound webhook topic `orders/create` to installed apps (see <Anchor target="_blank" href="https://benchfive-commer.readme.io/v1.0_GUIDES/docs/webhooks">Webhooks</Anchor>)

Continue with [Processing Payments](https://benchfive-commer.readme.io/v1.0_GUIDES/docs/processing-payments) to verify payment status.

## Related

- <Anchor target="_blank" href="https://benchfive-commer.readme.io/v1.0_GUIDES/docs/processing-payments">Processing Payments</Anchor>
- <Anchor target="_blank" href="https://benchfive-commer.readme.io/v1.0_GUIDES/docs/building-a-storefront">Building a Storefront</Anchor>
- <Anchor target="_blank" href="https://benchfive-commer.readme.io/v1.0_GUIDES/docs/webhooks">Webhooks</Anchor>
