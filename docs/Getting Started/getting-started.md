---
title: Introduction
excerpt: >-
  Welcome to the Benchfive Commerce Integrations API — the primary surface for
  external developers, partner apps, and headless clients that need to read and
  write store data programmatically.
hidden: false
metadata:
  title: Benchfive Commerce
---
## What this API is for

Use the Integrations API when you are building:

- A custom storefront or mobile app
- An ERP, inventory, or fulfilment sync
- A partner app installed on merchant stores
- Automation that creates or updates orders, products, or customers

All Integrations routes live under:

```text
/api/v1/integrations
```

## Two reference surfaces

The Integrations OpenAPI is split so you can find the right endpoints quickly:

| Surface        | Base path                        | Typical use                                                                                             |
| -------------- | -------------------------------- | ------------------------------------------------------------------------------------------------------- |
| **Storefront** | `/api/v1/integrations/...`       | Catalog reads, cart, checkout (create order), payments verify, shipping rates, themes, gallery, contact |
| **Admin**      | `/api/v1/integrations/admin/...` | Product/inventory CRUD, order management, customers, shipping settings, refunds, dashboard              |

Both surfaces use the **same authentication** (API key or OAuth) and **scopes**. Admin routes generally need write scopes (for example `products:write`).

## How this differs from other APIs

| Audience                        | Auth                                                  | Docs                                   |
| ------------------------------- | ----------------------------------------------------- | -------------------------------------- |
| **Integrations** (you are here) | `X-Api-Key` (`sk_test_` / `sk_live_`) or OAuth Bearer | This portal                            |
| Cookie storefront               | Guest session cookie                                  | Internal Store API (`/api/docs`)       |
| Merchant dashboard              | Merchant JWT                                          | Internal Admin API (`/api/docs/admin`) |

External developers should prefer the Integrations API. Do not use guest session headers for partner integrations.

## Multi-tenant store context

Every request is scoped to a single store. For Integrations, the API key or OAuth token already binds the store — you do not need `X-Store-*` headers. If you do send `X-Store-Slug`, `X-Store-Id`, or hit a Host that resolves a store, it must match the credential or the request returns `403 Forbidden`.

Cookie storefront and other tenant routes that are not Integrations still resolve store via:

- `X-Store-Slug` (preferred), or
- `X-Store-Id` (store `publicId` UUID), or
- a Host / custom domain that already resolves to a store

Missing store context on those non-Integrations tenant routes returns `400 Bad Request`.

## Next steps

1. [Quickstart](https://benchfive-commer.readme.io/v1.0_GUIDES/docs/quickstart) — make your first authenticated call
2. [Authentication](https://benchfive-commer.readme.io/v1.0_GUIDES/docs/authentication) — API keys, OAuth, and scopes
3. Browse the **API Reference** (Storefront and Admin OpenAPI specs)

<br />

<br />
