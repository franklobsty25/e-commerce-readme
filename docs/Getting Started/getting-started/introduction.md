---
title: Introduction
deprecated: false
hidden: false
metadata:
  robots: index
---
# Introduction

Welcome to the **Integrations API** — the primary surface for external developers, partner apps, and headless clients that need to read and write store data programmatically.

## What this API is for

Use the Integrations API when you are building:

- A custom storefront or mobile app
- An ERP, inventory, or fulfillment sync
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

Every request is scoped to a single store. Provide store context with:

- `X-Store-Slug` (preferred), or
- `X-Store-Id` (store `publicId` UUID), or
- a Host / custom domain that already resolves to a store

API keys and OAuth tokens are also bound to a store. Missing store context on tenant routes returns `400 Bad Request`.

## Next steps

1. [Quickstart](quickstart.md) — make your first authenticated call
2. [Authentication](authentication.md) — API keys, OAuth, and scopes
3. Browse the **API Reference** (Storefront and Admin OpenAPI specs)
