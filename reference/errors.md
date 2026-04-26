---
description: Error codes returned by the Prova HTTP API, CLI, and SDK.
---

# Error codes

A canonical list of every error you can encounter when using Prova. The API returns errors as JSON with the shape:

```json
{
  "error": "machine_readable_code",
  "message": "Human-readable explanation",
  "details": { /* optional, code-specific */ }
}
```

The CLI maps these to non-zero exit codes; the SDK throws a typed `ProvaError` carrying `code`, `message`, and `details`.

---

## Authentication

| Code | HTTP | Meaning |
|---|---|---|
| `auth_required` | 401 | The endpoint requires a bearer token; none was supplied. |
| `auth_invalid_token` | 401 | The token is malformed or its signature does not match. |
| `auth_token_expired` | 401 | The token's `exp` claim has passed. Mint a new one. |
| `auth_token_revoked` | 401 | The token was explicitly revoked via `POST /api/tokens/revoke`. |
| `auth_email_not_verified` | 403 | Sign-up succeeded but the magic-link verify step is pending. |
| `auth_quota_zero` | 403 | The token is valid but has 0 MB quota; purchase quota first. |

## Quota and billing

| Code | HTTP | Meaning |
|---|---|---|
| `quota_exceeded` | 402 | Upload would push the account past its monthly quota. |
| `payment_required` | 402 | The deal cannot be created because the client has insufficient USDC allowance. |
| `payment_signature_invalid` | 400 | The provided EIP-712 signature does not match the marketplace order. |

## Upload / piece

| Code | HTTP | Meaning |
|---|---|---|
| `piece_too_large` | 413 | The piece exceeds the per-account upload limit. Split it client-side. |
| `piece_too_small` | 400 | Pieces below the minimum (currently 65 KiB) cannot be deal-anchored. Use the unanchored upload endpoint or pad the piece. |
| `piece_cid_mismatch` | 400 | The CommP you computed locally does not match what the server computed. The bytes were modified in transit; retry. |
| `piece_already_exists` | 200 | (Soft error.) An identical piece-CID already exists for this account. The response carries the existing deal info. |

## Retrieval

| Code | HTTP | Meaning |
|---|---|---|
| `piece_not_found` | 404 | No active deal matches this piece-CID. |
| `piece_unavailable` | 503 | The piece exists in the registry but no prover currently serves it. Retry or open a dispute. |
| `piece_proof_pending` | 202 | The piece was just uploaded; the first proof has not yet been submitted. Retry in 30s. |

## Deals and provers

| Code | HTTP | Meaning |
|---|---|---|
| `deal_not_found` | 404 | The deal id does not exist on this chain. |
| `deal_already_active` | 409 | A deal with the same piece-CID + client is already live. |
| `prover_not_registered` | 404 | The address is not in `ProverRegistry`. |
| `prover_paused` | 503 | The prover is temporarily not accepting new deals. |
| `prover_insufficient_stake` | 402 | The prover's bonded stake is below the per-TiB floor. |

## On-chain

| Code | HTTP | Meaning |
|---|---|---|
| `chain_revert` | 502 | The on-chain transaction reverted. `details.reason` carries the revert string. |
| `chain_unsupported` | 400 | The supplied `chainId` is not a Prova-supported chain (currently only Base). |
| `chain_outage` | 503 | The RPC endpoint is unreachable. The proof submission was queued for retry. |

## Disputes

| Code | HTTP | Meaning |
|---|---|---|
| `dispute_window_closed` | 410 | The dispute deadline has passed. Disputes must be opened within 24 hours of the bad proof. |
| `dispute_bond_insufficient` | 402 | The challenger did not post the required bond. |
| `dispute_already_resolved` | 409 | This dispute id has already been adjudicated. |

## Generic / transport

| Code | HTTP | Meaning |
|---|---|---|
| `bad_request` | 400 | The payload failed schema validation. `details` carries the failed field paths. |
| `not_found` | 404 | The resource does not exist or the caller is not entitled to see it. |
| `rate_limited` | 429 | The token exceeded its rate window. Honor the `Retry-After` header. |
| `internal_error` | 500 | An unexpected error happened server-side. Save the `details.requestId` and report it. |
| `not_implemented` | 501 | The endpoint exists but the underlying feature is not yet shipped. |

---

## CLI exit codes

The `prova` CLI maps API errors to POSIX-style exit codes so shell scripts can branch:

| Exit | Cause |
|---|---|
| 0 | Success |
| 1 | Generic CLI error (bad args, parse error, missing file) |
| 2 | `auth_required` / `auth_invalid_token` / `auth_token_expired` |
| 3 | `quota_exceeded` / `payment_required` |
| 4 | `piece_*` errors during upload |
| 5 | `piece_not_found` / `piece_unavailable` during retrieval |
| 6 | `chain_*` errors |
| 7 | `rate_limited` |
| 99 | `internal_error` (treat as bug; please report) |

## SDK error class

The SDK exposes a single `ProvaError` class with `code`, `message`, and an optional `details` payload. Subclasses are reserved for future use; right now `instanceof ProvaError && err.code === '…'` is the canonical pattern.

```ts
import { ProvaError } from '@prova/sdk'

try {
  await prova.storage.upload(bytes)
} catch (err) {
  if (err instanceof ProvaError && err.code === 'quota_exceeded') {
    // top up the account, retry
  } else {
    throw err
  }
}
```

---

If you encounter an error code that's not in this table, file an issue at [github.com/prova-network/docs](https://github.com/prova-network/docs/issues).
