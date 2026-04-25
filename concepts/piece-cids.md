---
icon: hashtag
description: How content addressing works in Prova.
---

# Piece-CIDs

A **piece-cid** is the content-addressed identifier for a file stored on Prova. Two clients uploading the same bytes get the same piece-cid. Identical files always produce identical cids.

## Format

A piece-cid looks like:

```
bafy4it2que4irvsdl4art2xrqoqiu5vz2tpqnlqvd6zefedfwnhbdfq
```

It's a CID v1 with the multicodec set to `piece-commitment` (`0xf101`) and the multihash set to `sha2-256-trunc254-padded` (`0x1012`). The whole thing is base32-encoded.

Browser uploads currently use a simpler scheme (SHA-256 truncated to 32 bytes, base32-encoded with a `bafy` prefix) as a placeholder. The on-chain commitment uses real CommP. The migration to in-browser CommP is tracked in [the changelog](../reference/changelog.md).

## Why content addressing

Content addressing means the **identifier is derived from the bytes**, not assigned by some central registry. Three properties matter:

1. **Verifiable.** Anyone can recompute the cid from the bytes and check the prover is serving the right file. If the prover lies, you notice immediately.
2. **De-duplicating.** If you and a thousand other people upload the same file, the network stores one copy. You each get your own deal, but the bytes are shared.
3. **Permanent.** The cid never changes. As long as the bytes exist, the address resolves.

## How to compute a piece-cid

### From the CLI

```bash
prova put ./file.bin
# the CLI computes the cid client-side and prints it
```

### From the SDK

```ts
import { computePieceCid } from '@prova-network/sdk'
const cid = await computePieceCid(bytes)
```

### From scratch

```python
import hashlib
from base64 import b32encode

# Read your file
data = open('file.bin', 'rb').read()

# SHA-256 the bytes
h = hashlib.sha256(data).digest()

# base32 encode, truncate to 52 chars, prefix with 'bafy'
ALPHA = 'abcdefghijklmnopqrstuvwxyz234567'
def b32(b):
    bits = 0; val = 0; out = ''
    for byte in b:
        val = (val << 8) | byte
        bits += 8
        while bits >= 5:
            out += ALPHA[(val >> (bits - 5)) & 31]
            bits -= 5
    if bits: out += ALPHA[(val << (5 - bits)) & 31]
    return out

cid = 'bafy' + b32(h)[:52]
print(cid)
```

(This is the placeholder algorithm. The real CommP scheme adds Fr32 padding and a Merkle hash; see [the spec](https://prova.network/specs.html) for details.)

## Verify a retrieval

If you fetch a piece and want to confirm the prover served the right bytes:

```bash
# Fetch
curl -O https://prova.network/p/bafy…q4kr

# Recompute
prova hash ./bafy…q4kr
# should print the same cid
```

(`prova hash` is on the [CLI roadmap](../reference/changelog.md). Until then, hash by hand.)

## Why de-duplication is good (and slightly weird)

If you upload the same file as someone else, Prova doesn't double-charge the prover for storage. They store one copy. **But** each of you has your own deal — your own retention term, your own retrieval rights, your own escrow. So the prover earns from both deals while only spending the disk cost once. This is the right incentive: more clients on the same piece = more revenue per byte for the prover, encouraging cheaper pricing.

The only weird side effect: a malicious actor can upload **the same cid as you** to a different prover and "front-run" your storage. Doesn't matter — the bytes are the bytes. They didn't see your content, they just happened to know its hash. Two parties with the same hash can both store the same bytes; they end up with two independent deals on identical content. The fact that the cid is content-addressed makes this safe.
