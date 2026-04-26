---
description: Compute the piece-CID of a local file without uploading.
---

# `prova hash`

Compute the piece-CID (CommP) of a local file. **No network calls. No auth required.**

```bash
prova hash <file> [--json]
```

Useful when you want to:

- Pre-compute the CID before [`prova put`](put.md) so you can confirm the server agrees with you.
- Confirm a downloaded file matches the CID you expected — though [`prova verify`](#see-also) is more direct for that.
- Script piece-CID generation in CI without round-tripping to the network.

## Examples

```bash
$ prova hash ./dist.tar.gz
baga6ea4seaqpit2txuhynqdtjys5jdjrn4uf532cuzij4wzrjkolrtom2yczkdi  ./dist.tar.gz

$ prova hash ./dist.tar.gz --json
{"file":"./dist.tar.gz","size":42137,"cid":"baga6ea4seaq…"}
```

The unprefixed text form is column-aligned (`<cid>  <file>`) so it composes with `awk`, `grep`, etc.:

```bash
prova hash *.tar.gz | awk '{print $1}'
```

## Flags

| Flag | What it does |
|---|---|
| `--json`, `-j` | Emit a single-line JSON object (`{file, size, cid}`) instead of human-readable text. |

## Exit codes

| Code | Meaning |
|---|---|
| 0 | Success. |
| 1 | The file does not exist or is not a regular file. |
| 2 | Bad arguments (no file, unknown flag). |

## See also

- [`prova put`](put.md) — actually upload the file and create a deal.
- [`prova verify`](verify.md) — verify a file matches a claimed piece-CID.
- [`Piece-CIDs`](../concepts/piece-cids.md) — what CommP is and how it's computed.
