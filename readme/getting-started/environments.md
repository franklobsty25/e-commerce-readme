# Environments

## Base URL

Set your client base URL to the platform API origin (no path suffix):

| Environment | Example |
|-------------|---------|
| Local | `http://localhost:3000` |
| Staging / production | Value of `APP_URL` (for example `https://api.example.com`) |

All Integrations paths are versioned:

```text
{BASE_URL}/api/v1/integrations/...
{BASE_URL}/api/v1/integrations/admin/...
```

## Test vs live API keys

| Key prefix | Issued when |
|------------|-------------|
| `sk_test_` | Non-production (`APP_ENV` is not `production`) |
| `sk_live_` | Production (`APP_ENV=production`) |

Use test keys against non-production API hosts. Do not mix live keys with staging hosts (or the reverse).

OAuth client credentials are environment-specific as well — register apps against the environment you intend to call.

## Store context per environment

Store slugs and `publicId` values differ across environments. Always set:

```http
X-Store-Slug: your-store-slug
```

or

```http
X-Store-Id: <store-publicId-uuid>
```

when Host does not already resolve the tenant.

## Interactive exploration

| Surface | URL |
|---------|-----|
| Combined Integrations Swagger | `{BASE_URL}/api/docs/integrations` |
| Integrations OpenAPI JSON | `{BASE_URL}/api/docs/integrations-json` |
| Exported Storefront OpenAPI | `docs/readme/openapi/integrations-storefront.json` |
| Exported Admin OpenAPI | `docs/readme/openapi/integrations-admin.json` |

Regenerate exports after API changes:

```bash
npm run openapi:export:integrations
```

## Related

- [Quickstart](quickstart.md)
- [Rate Limits](rate-limits.md)
- [Errors](errors.md)
