---
title: Errors
excerpt: Integrations endpoints use standard NestJS JSON error bodies.
deprecated: false
hidden: false
metadata:
  robots: index
---
## Shape

```json
{
  "statusCode": 400,
  "message": "Human-readable error or validation details",
  "error": "Bad Request"
}
```

`message` may be a string or an array of validation messages when the body fails `ValidationPipe` checks (whitelist + transform are enabled globally).

## Common status codes

| Status                      | Typical cause                                               |
| --------------------------- | ----------------------------------------------------------- |
| `400 Bad Request`           | Missing/invalid store context, validation errors, bad input |
| `401 Unauthorized`          | Missing/invalid API key or OAuth token; revoked/expired key |
| `403 Forbidden`             | Authenticated but missing required scopes                   |
| `404 Not Found`             | Unknown resource for this store                             |
| `429 Too Many Requests`     | Rate limit exceeded (see [Rate Limits](rate-limits.md))     |
| `500 Internal Server Error` | Unexpected server failure                                   |

## Auth and scope examples

**Missing or invalid credentials**

```json
{
  "statusCode": 401,
  "message": "Unauthorized"
}
```

**Insufficient scopes**

```json
{
  "statusCode": 403,
  "message": "Missing required scopes: products:write"
}
```

**Store context not resolved**

Tenant-scoped routes return `400` when neither Host nor `X-Store-Slug` / `X-Store-Id` resolves a store.

## Validation

Invalid JSON bodies or fields that fail class-validator rules return `400` with details in `message`. Always send `Content-Type: application/json` for write requests.

## Handling errors in clients

1. Retry only idempotent GETs on `429` / transient `5xx`, with exponential backoff
2. On `401`, refresh OAuth tokens or rotate keys — do not retry blindly
3. On `403`, request additional scopes from the merchant (new key or re-authorize)
4. Log `statusCode` and `message` for support; never log full API keys

## Related

- <Anchor target="_blank" href="https://benchfive-commer.readme.io/update/docs/authentication">Authentication</Anchor>
- [Rate Limits](rate-limits.md)
