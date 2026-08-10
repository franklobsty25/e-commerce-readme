---
title: Webhooks
excerpt: >-
  Partner apps can receive outbound HTTP callbacks when certain store events
  happen. Today the implemented topic is `orders/create`.
deprecated: false
hidden: false
metadata:
  robots: index
---
## Outbound vs inbound

| Type                          | Direction                             | Audience                                 |
| ----------------------------- | ------------------------------------- | ---------------------------------------- |
| **Outbound partner webhooks** | Platform → your URL                   | Partner apps (this guide)                |
| **Inbound payment webhooks**  | Paystack / Hubtel / Stripe → platform | Platform only — not for your integration |

Do not confuse partner webhooks with `/api/v1/paystack-webhook`, `hubtel-webhook`, or `stripe-webhook`.

## Subscribe

Provide a `webhookUrl` when the merchant installs your app:

```http
POST /api/v1/admin/apps/:publicId/install
Authorization: Bearer <merchant_jwt>
Content-Type: application/json

{
  "webhookUrl": "https://acme.example/webhooks/orders"
}
```

The platform registers an active subscription for topic `orders/create` on that installation.

## Delivery

When an order is created, the platform POSTs JSON to your URL:

```http
POST https://acme.example/webhooks/orders
Content-Type: application/json
X-Dh-Signature: <hmac-sha256-hex>
X-Dh-Topic: orders/create
```

Example payload:

```json
{
  "orderId": 123,
  "orderNumber": "ORD-1001",
  "total": 199.99,
  "currency": "GHS",
  "customerEmail": "jane@example.com"
}
```

Deliveries are fire-and-forget from the API request path. Design your endpoint to respond quickly and process asynchronously.

## Verify signatures

Each webhook has a secret. The signature is **HMAC-SHA256** of the **raw request body**, hex-encoded, sent in `X-Dh-Signature`.

Node.js example:

```js
import { createHmac, timingSafeEqual } from 'node:crypto';

function verifyDhSignature(rawBody, signatureHex, secret) {
  const expected = createHmac('sha256', secret).update(rawBody).digest('hex');
  const a = Buffer.from(expected, 'hex');
  const b = Buffer.from(signatureHex, 'hex');
  if (a.length !== b.length) return false;
  return timingSafeEqual(a, b);
}
```

Reject requests with missing or invalid signatures.

> Capture the **raw body** before JSON parsing so the HMAC input matches what the platform signed.

## Topics

| Topic                            | Status              |
| -------------------------------- | ------------------- |
| `orders/create`                  | Implemented         |
| Other catalog / uninstall topics | Not implemented yet |

## Uninstall

Uninstalling an app (`DELETE /api/v1/admin/apps/installations/:id`) revokes tokens and deactivates related webhook subscriptions for that installation.

## Related

- [OAuth Apps](../getting-started/oauth-apps.md)
- <Anchor target="_blank" href="https://benchfive-commer.readme.io/v1.0_GUIDES/update/docs/creating-orders">Creating Orders</Anchor>
