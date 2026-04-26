---
description: Welcome to the Prova docs.
---

# Welcome to Prova

Prova is **verifiable storage anchored to Ethereum**. You upload a file, pay in USDC on Base, and a network of provers stores your bytes. Every day, those provers post cryptographic proofs to Ethereum that they still hold your data — and they get slashed if they don't.

If a prover lies, the math catches them. If they vanish, the smart contract slashes their stake and another prover steps in. There's no committee to trust. No board you can lobby. Just bytes, proofs, and code.

## What you can do here

| If you want to... | Start at |
|---|---|
| Upload a file in 60 seconds | [Web upload](getting-started/web-upload.md) |
| Use the CLI | [Quickstart](getting-started/quickstart.md) |
| Build an app on Prova | [TypeScript SDK](sdk/) |
| Hit the HTTP API directly | [API reference](api/) |
| Run a prover and earn USDC | [Become a prover](provers/become-a-prover.md) |
| Understand how it works | [How Prova works](concepts/architecture.md) |

## The 30-second pitch

Most decentralized storage projects compete on cryptographic ambition. Prova does the opposite. It takes the proven parts of the Filecoin ecosystem — **PDP** (Provable Data Possession), **CommP** (content commitments), and the **deal marketplace pattern** — and runs them on Base, with USDC for stable pricing and Ethereum for trust and settlement.

No new chain. No sealing. No SNARKs. No new gas token. Just storage that you can verify, retrieve, and pay for in the currency you already use.

## Three core guarantees

- **Possession is provable, every day.** Your file is challenged on-chain on a random schedule. If a prover can't produce the proof, they're slashed.
- **Retrieval is honest.** Pull your bytes back over HTTPS, recompute the piece-CID locally, and verify against what's on-chain. If it doesn't match, you have an automatic dispute.
- **Stake backs everything.** Provers lock PROVA proportional to the bytes they commit. Misbehavior costs them real money.

## Try it now

```bash
curl -fsSL https://get.prova.network | sh
prova auth
prova put ./your-file.tar.gz
```

Three commands and you have a piece-CID anchored to Ethereum. See the [60-second quickstart](getting-started/quickstart.md) for the full walkthrough.

## Status

Prova is **in development**. The website, CLI, and SDK are usable today against a stage server. Mainnet on Base is targeted for H2 2026, post-audit. See the [changelog](reference/changelog.md) for what shipped and when.

## Learn more

- [How Prova works](concepts/architecture.md) — the architecture in one page
- [Continuous proof of storage](concepts/continuous-proof.md) — why daily challenges matter
- [Deal lifecycle](concepts/deal-lifecycle.md) — what happens when you upload
- [Glossary](reference/glossary.md) — every term, defined once

If you're stuck, file an issue at [github.com/prova-network](https://github.com/prova-network). For private coordination — security disclosure, enterprise prover questions — email `hello@prova.network`.
