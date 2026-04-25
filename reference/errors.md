---
icon: triangle-exclamation
description: Error codes returned by the Prova API.
---

# Error codes

Every Prova API error has a stable `error` machine code and a human-readable `detail`. Match on `error` in code, surface `detail` to humans.

```json
{ "error": "rate_limited", "detail": "Daily quota of 1024 MB reached.", "used": 1073741824, "limit": 1073741824 }
```

## Authentication

| `error` | Status | Meaning |
| --- | --- | --- |
| `no_token` | 401 | No `Authorization: Bearer …` header |
| `invalid_token` | 401 | Token malformed, signature failed, or expired |
| `revoked_token` | 401 | Token was revoked via `/api/tokens/revoke` |
| `insufficient_scope` | 403 | Token doesn't include the scope this endpoint requires |

## Validation

| `error` | Status | Meaning |
| --- | --- | --- |
| `invalid_email` | 400 | Email regex didn't match |
| `invalid_jti` | 400 | `jti` body field missing or malformed |
| `invalid_cid` | 400 | `cid` query param malformed (must match `^[a-z0-9]{8,80}$`) |
| `invalid_json` | 400 | Body isn't valid JSON |

## Limits

| `error` | Status | Meaning |
| --- | --- | --- |
| `too_large` | 413 | File exceeds the tier's per-file limit |
| `rate_limited` | 429 | Sponsored daily IP cap hit |
| `quota_exceeded` | 429 | Authenticated user's daily quota hit |

## Storage

| `error` | Status | Meaning |
| --- | --- | --- |
| `no_body` | 400 | Upload body is empty |
| `empty_body` | 400 | Body present but zero bytes |
| `stage_failed` | 502 | Upstream prover rejected the upload |
| `storage_offline` | 503 | No storage backend bound (configuration error) |

## Operational

| `error` | Status | Meaning |
| --- | --- | --- |
| `auth_offline` | 503 | Token signing key not configured |
| `not_found` | 404 | The requested resource (cid / token / file) doesn't exist or doesn't belong to you |
| `method_not_allowed` | 405 | Wrong HTTP method on this path |

## Reporting an issue

If you hit an error code that isn't documented here, or a code that doesn't match its description, [file an issue](https://github.com/prova-network/prova/issues) with:

* the request URL and method
* the request headers (sanitize the token!)
* the response status + body
* the timestamp (UTC)

We aim to respond within 24h on weekdays.
