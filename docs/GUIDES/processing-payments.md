---
title: Processing Payments
excerpt: >-
  Payment flows on Integrations use the store’s configured providers (for
  example Paystack, Hubtel, Stripe). Partners typically create an order,
  redirect the shopper, then verify payment.
deprecated: false
hidden: false
metadata:
  robots: index
---
## Scopes

| Action                          | Scope                              |
| ------------------------------- | ---------------------------------- |
| List providers / verify payment | `payments:read`                    |
| Admin payment list / refunds    | `payments:read` / `payments:write` |

## Storefront

### List providers

```http
GET /api/v1/integrations/payments/providers
X-Api-Key: sk_test_...
X-Store-Slug: your-store-slug
```

### Verify a payment

After the provider redirects the shopper back to your `redirectUrl`:

```http
GET /api/v1/integrations/payments/verify/{reference}
X-Api-Key: sk_test_...
X-Store-Slug: your-store-slug
```

Use the payment reference returned by order creation / the provider callback.

## Admin

```http
GET /api/v1/integrations/admin/payments
POST /api/v1/integrations/admin/payments/{orderNumber}/refund
```

Refunds require `payments:write`. Exact body fields are in the Admin OpenAPI.

## End-to-end flow

```text
1. POST /api/v1/integrations/orders (include paymentProvider + redirectUrl)
2. Redirect shopper to provider checkout URL from the response
3. Shopper pays
4. GET /api/v1/integrations/payments/verify/{reference}
5. Show success/failure in your UI
```

## Provider webhooks (not partner APIs)

The platform also receives **inbound** payment webhooks from providers:

- `POST /api/v1/paystack-webhook`
- `POST /api/v1/hubtel-webhook`
- `POST /api/v1/stripe-webhook`

These are for the platform to confirm charges with Paystack / Hubtel / Stripe. **Your app should not call them.** For partner notifications, use outbound [Webhooks](webhooks.md) (`orders/create`).

## Related

- <Anchor target="_blank" href="https://benchfive-commer.readme.io/v1.0_GUIDES/update/docs/creating-orders">Creating Orders</Anchor>
- <Anchor target="_blank" href="https://benchfive-commer.readme.io/v1.0_GUIDES/update/docs/webhooks">Webhooks</Anchor>
- Storefront tag: **Integrations Payments**
- Admin tag: **Integrations Admin Payments**
