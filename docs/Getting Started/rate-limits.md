---
title: Rate Limits
excerpt: Integrations traffic is rate limited to protect the platform and merchants.
deprecated: false
hidden: false
metadata:
  robots: index
---
## Limits

| Limit            | Window         | Key                                                                                       |
| ---------------- | -------------- | ----------------------------------------------------------------------------------------- |
| **120 requests** | **60 seconds** | API key prefix (first 12 characters of `X-Api-Key` or Bearer `sk_*`), otherwise client IP |

`OPTIONS` requests and simple health paths are excluded.

> The default limiter is in-memory per process. In multi-instance deployments, effective limits may vary unless a shared store is configured.

## Response headers

Successful (and limited) responses include:

| Header                  | Meaning                                  |
| ----------------------- | ---------------------------------------- |
| `X-RateLimit-Limit`     | Max requests per window (`120`)          |
| `X-RateLimit-Remaining` | Remaining requests in the current window |

## Exceeded limit

```http
HTTP/1.1 429 Too Many Requests
Content-Type: application/json

{
  "statusCode": 429,
  "message": "Too many requests"
}
```

## Best practices

- Cache catalog reads when possible
- Back off with jitter on `429`
- Prefer a dedicated API key per integration so limits are not shared with browser traffic by IP
- Use webhooks for order events instead of tight polling (see [Webhooks](../guides/webhooks.md))

## Related

- [Errors](errors.md)
- <Anchor target="_blank" href="https://benchfive-commer.readme.io/update/docs/authentication">Authentication</Anchor>
