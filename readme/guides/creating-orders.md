# Creating Orders

Checkout on the Integrations API is **create order**. There is no separate `/checkout` resource.

## Scopes

| Action | Scope |
|--------|-------|
| Create order (Storefront) | `orders:write` |
| Get order by number (Storefront) | `orders:read` |
| List / manage orders (Admin) | `orders:read` (and write scopes where applicable) |
| Cart operations | `orders:read` / `orders:write` |

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

See the Storefront OpenAPI schemas for the full request body.

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

1. Return payment redirect / provider details for the shopper
2. Dispatch outbound webhook topic `orders/create` to installed apps (see [Webhooks](webhooks.md))

Continue with [Processing Payments](processing-payments.md) to verify payment status.

## Related

- [Processing Payments](processing-payments.md)
- [Building a Storefront](building-a-storefront.md)
- [Webhooks](webhooks.md)
