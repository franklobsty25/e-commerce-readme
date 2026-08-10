# Changelog

## 2026-08-10 — Integrations docs v1.0

- Initial ReadMe-ready documentation for external developers
- Getting Started: introduction, quickstart, authentication, API keys, OAuth apps, environments, rate limits, errors
- Guides: products, inventory, orders, payments, webhooks, building a storefront
- Exported Integrations OpenAPI split into:
  - **Storefront** — `docs/readme/openapi/integrations-storefront.json`
  - **Admin** — `docs/readme/openapi/integrations-admin.json`
- Regenerate OpenAPI with `npm run openapi:export:integrations`
