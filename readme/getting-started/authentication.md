# Authentication

Integrations requests authenticate with an **API key** or an **OAuth access token**. Scopes control which resources you can access.

## API key

Send the key in either header:

```http
X-Api-Key: sk_test_...
```

or

```http
Authorization: Bearer sk_test_...
```

| Prefix | Environment |
|--------|-------------|
| `sk_test_` | Non-production |
| `sk_live_` | Production (`APP_ENV=production`) |

Keys are stored as hashes. The raw secret is shown only at creation time. Revoked, expired, or suspended keys return `401 Unauthorized`.

See [API Keys](api-keys.md) for creation, scopes, and rotation.

## OAuth access token

Partner apps use OAuth 2.0. After authorization and token exchange:

```http
Authorization: Bearer <oauth_access_token>
```

Do **not** use an `sk_*` value on the OAuth path. OAuth tokens carry the scopes granted at install/authorize time.

See [OAuth Apps](oauth-apps.md).

## How the guard resolves credentials

`IntegrationAuthGuard` accepts:

1. `X-Api-Key` header, or
2. `Authorization: Bearer …` where the token is either an API key (`sk_*`) or an OAuth access token

Missing or invalid credentials → `401 Unauthorized`.

## Scopes

Routes declare required scopes (for example `@RequireScopes('products:read')`). `RequireScopesGuard` enforces them.

| Result | Meaning |
|--------|---------|
| `403` | Authenticated but missing required scopes (`Missing required scopes: …`) |
| Pass | Key/token has the required scope, or `*` (full store access) |

Canonical scope list:

```text
products:read / products:write
categories:read / categories:write
collections:read / collections:write
colors:read / colors:write
sizes:read / sizes:write
orders:read / orders:write
discounts:read / discounts:write
customers:read / customers:write
shipping:read / shipping:write
payments:read / payments:write
settings:read / settings:write
themes:read / themes:write
marketing:read / marketing:write
notifications:read / notifications:write
subscribers:read
dashboard:read
storefront:read
rates:read / rates:write
*
```

Cart endpoints use order scopes (`orders:read` / `orders:write`).

## Store context

In addition to auth, tenant-scoped routes need a store:

| Header | Description |
|--------|-------------|
| `X-Store-Slug` | Preferred human-readable store slug |
| `X-Store-Id` | Store `publicId` (UUID) |

If the request Host already maps to a store (custom domain or `{slug}.{PLATFORM_HOST}`), headers may be omitted.

API keys and OAuth installations are already bound to one store; headers disambiguate when Host does not resolve a tenant.

## What not to use

| Scheme | Audience |
|--------|----------|
| Merchant JWT | Merchant dashboard Admin API only |
| Guest cookie `guest.sid` / session Bearer | Cookie storefront, not Integrations |
| Customer JWT | Customer account endpoints on the Store API |

## Related

- [API Keys](api-keys.md)
- [OAuth Apps](oauth-apps.md)
- [Errors](errors.md)
