# Integrations documentation (ReadMe)

Markdown Guides and exported OpenAPI specs for publishing the **Integrations API** to [ReadMe](https://docs.readme.com/main/docs/quickstart).

External developers should use `/api/v1/integrations` (API key or OAuth). This package documents **only** the Integrations surface, split into Storefront vs Admin reference specs.

## Portal structure

```text
Getting Started
├── Introduction
├── Quickstart
├── Authentication
├── API Keys
├── OAuth Apps
├── Environments
├── Rate Limits
└── Errors

Guides
├── Managing Products
├── Managing Inventory
├── Creating Orders
├── Processing Payments
├── Webhooks
└── Building a Storefront

API Reference
├── Integrations Storefront  ← openapi/integrations-storefront.json
└── Integrations Admin       ← openapi/integrations-admin.json

Changelog
```

## Files

| Path | Purpose |
|------|---------|
| [getting-started/](getting-started/) | Auth, keys, environments, errors |
| [guides/](guides/) | End-to-end integration tutorials |
| [changelog.md](changelog.md) | Doc / API doc changelog |
| [openapi/integrations-storefront.json](openapi/integrations-storefront.json) | Storefront API Reference |
| [openapi/integrations-admin.json](openapi/integrations-admin.json) | Admin API Reference |

## Regenerate OpenAPI

After changing Integrations controllers or DTOs:

```bash
npm run openapi:export:integrations
```

This writes both JSON files under `openapi/`. Commit the regenerated specs with your API changes.

Live Swagger UI (combined Integrations): `{BASE_URL}/api/docs/integrations`.

## Upload to ReadMe

Follow the [ReadMe quickstart](https://docs.readme.com/main/docs/quickstart):

1. **Create a project** in ReadMe and name it for your Integrations / partner docs.
2. **Add API specs** (two definitions for a clear Storefront vs Admin split):
   - Upload `openapi/integrations-storefront.json` as **Integrations Storefront API**
   - Upload `openapi/integrations-admin.json` as **Integrations Admin API**
3. **Create Guides** matching the folders above. Paste or sync each Markdown file into the corresponding category (Getting Started, Guides, Changelog).
4. Optionally enable [GitHub sync](https://docs.readme.com/main/docs/sync-with-github) so edits flow between this repo and ReadMe (branch names must match ReadMe version names).

Suggested ReadMe categories mirror the tree under **Portal structure**.

## Auth reminder for readers

| Method | Header |
|--------|--------|
| API key | `X-Api-Key: sk_test_…` / `sk_live_…` or `Authorization: Bearer sk_…` |
| OAuth | `Authorization: Bearer <access_token>` |
| Store context | `X-Store-Slug` (preferred) or `X-Store-Id` |

Do not document the cookie Store API or merchant JWT Admin API as the partner surface in this portal.
