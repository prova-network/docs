---
description: Verify that a local file matches a claimed piece-CID.
---

# `prova verify`

Hash a file locally and compare against a claimed piece-CID. **No network calls. No auth required.**

```bash
prova verify <cid> <file|-> [--json] [--quiet]
```

This is the canonical "did I download what I asked for" check. Pair it with [`prova get`](get.md) to defend against a buggy or hostile prover swapping bytes mid-flight:

```bash
prova get baga6ea4seaqpit2…kdi -o ./bundle.tar.gz
prova verify baga6ea4seaqpit2…kdi ./bundle.tar.gz
```

Exit 0 means the bytes match the CID. Exit 4 means they don't — at which point you should re-fetch from a different prover, or open a dispute via the API.

## Examples

```bash
# Match
$ prova verify baga6ea4seaq…kdi ./bundle.tar.gz
✓ match  baga6ea4seaq…kdi  ./bundle.tar.gz
$ echo $?
0

# Mismatch (server returned the wrong bytes)
$ prova verify baga6ea4seaq…kdi ./bundle.tar.gz
✗ mismatch
  claimed:  baga6ea4seaq…kdi
  computed: baga6ea4abcd…xyz
  file:     ./bundle.tar.gz  (42 KiB)
$ echo $?
4

# JSON output for scripting
$ prova verify baga6ea4seaq…kdi ./bundle.tar.gz --json
{"file":"./bundle.tar.gz","size":42137,"claimed":"baga6ea4seaq…kdi","computed":"baga6ea4seaq…kdi","match":true}

# Verify stdin (handy when piping from prova get)
$ prova get baga6ea4seaq…kdi | prova verify baga6ea4seaq…kdi -
✓ match  baga6ea4seaq…kdi  stdin

# Quiet mode for CI gates
$ prova verify baga6ea4seaq…kdi ./bundle.tar.gz --quiet || exit 1
```

## Flags

| Flag | What it does |
|---|---|
| `--json`, `-j` | Emit a single-line JSON object (`{file, size, claimed, computed, match}`) instead of human-readable text. The exit code is still 0 on match, 4 on mismatch. |
| `--quiet`, `-q` | Suppress all stdout. The exit code carries the result. Convenient in scripts. |

## Exit codes

| Code | Meaning |
|---|---|
| 0 | The computed CID matches the claimed CID. The bytes are exactly what you asked for. |
| 1 | The file does not exist, is not a regular file, or is empty. |
| 2 | Bad arguments (missing CID, missing file, malformed CID prefix, unknown flag). |
| 4 | The computed CID does **not** match the claimed CID. The bytes have been modified or you fed in the wrong file. |

The `1`/`2`/`4` split is consistent with the rest of the CLI ([Error codes](../reference/errors.md#cli-exit-codes)): `1` means "I tried but the local environment was wrong," `2` means "you called me wrong," `4` means "the piece-CID check failed."

## See also

- [`prova hash`](hash.md) — just compute the CID, no comparison.
- [`prova get`](get.md) — download by CID; pair with `verify` for end-to-end integrity.
- [`Piece-CIDs`](../concepts/piece-cids.md) — what's actually being verified.
- [`Error codes`](../reference/errors.md) — all CLI exit codes in one place.
