---
description: Every Prova term, defined once.
---

# Glossary

The terms you'll meet across the docs, the CLI, the SDK, and the contracts. Defined here once so we can use them precisely everywhere else.

---

## Aggregator

A network role (optional) that batches PDP proofs from multiple provers into a single Ethereum transaction. Reduces gas cost per proof when many provers report on the same epoch. Aggregators may stake and earn a fee share. Anyone can run one.

## Base

The Ethereum L2 where Prova lives. All Prova contracts (`StorageMarketplace`, `ProverRegistry`, `ProverStaking`, `ProofVerifier`, `ContentRegistry`) are deployed on Base. Payments are settled in USDC.

## Bond

The collateral a **challenger** posts when disputing a proof or claiming data unavailability. Returned (with a bounty) if the challenge wins; forfeited otherwise. Prevents griefing.

## Bonded stake

The portion of a prover's PROVA stake that is **actively locked** against committed storage. As long as the prover is committing bytes, this stake is slashable.

## CommP

Short for **Piece Commitment**. A Merkle root of a file's bytes, padded to a power-of-two leaf count, hashed with SHA-256. The same algorithm Filecoin uses. CommP is content-addressed: identical bytes produce identical CommPs everywhere, on any prover, on any client.

CommPs are written into the on-chain `ContentRegistry` and become the canonical name for the piece.

## Continuous proof of storage

The Prova invariant: a file isn't just stored once and forgotten. It's challenged every proving period (typically once per day). If the prover can't produce a Merkle inclusion proof for the random leaf, they're slashed. See [Continuous proof](../concepts/continuous-proof.md).

## Challenger

Anyone who watches provers and disputes their proofs (or proves them unreachable). Challengers stake a **bond** per dispute and earn a portion of the slashed stake plus a small fixed bounty if they win.

## Client

The party paying for storage. Uploads bytes, locks USDC, and (later) retrieves bytes. Has no on-chain stake. May or may not have an Ethereum wallet — most clients just use the API and let a sponsor pay gas.

## Deal

A storage commitment between a client and one or more provers. Lifecycle: `Proposed → Active → Completed | Slashed`. Encoded as a row in `StorageMarketplace`. Pays the prover over time as proofs land.

## ENS

Ethereum Name Service. Prova lets you bind a CommP to an ENS name (e.g., `mysite.eth`) so retrieval URLs are human-readable. Optional but encouraged for `.eth` websites.

## Epoch

A discrete proving period. Currently 24 hours. At each epoch boundary, the on-chain randomness beacon is sampled, and challenges for active deals are computed.

## Gas

The fee paid to Ethereum/Base for transactions. Paid in **ETH on Base**, never in PROVA. PROVA is *not* a gas token.

## Piece-CID

The same as a **CommP**, but encoded as a CID (Content Identifier) for transport over IPFS-style protocols. Prefix `baga6ea4seaq…`. Convertible 1:1 to a 32-byte `bytes32` for on-chain use.

## PDP

**Provable Data Possession.** A cryptographic protocol that lets a verifier check that a prover still holds a file, without downloading the file or the prover sending the whole thing. Prova uses Merkle inclusion proofs over the file's chunked tree. Lightweight, O(log n) gas to verify.

## Prover

A network role that runs `provad`, stores client files, and posts PDP proofs to Ethereum. Stakes PROVA proportional to committed bytes. Earns USDC streaming payments + PROVA emission. Slashable.

## PROVA

The Prova ERC-20 token on Base. Used for **provider stake** and **emission rewards**. **Not** a gas token. **Not** a payment token (clients pay USDC). Fixed supply: 1,000,000,000.

## Proving period

The interval at which a prover must post a proof to keep a deal active. Currently 24 hours. Shorter periods cost more gas; longer periods reduce data-retention guarantees.

## Random beacon

The source of unpredictability used to choose which leaves get challenged each epoch. On Ethereum we use `block.prevrandao` as the default, with optional Chainlink VRF for higher-stakes deployments.

## Slash

The act of burning some or all of a prover's bonded stake when they fail a proof, miss a retrieval, or get caught by a successful challenge. Slashed PROVA is **burned** (sent to `0x000…dead`), not pooled. Total supply visibly decreases on every slash event.

## Stake floor

The minimum PROVA a prover must lock per TiB committed. Computed as `max(provaFloor, usdFloor / oraclePrice)` so the dollar-equivalent stake stays sane even when PROVA price moves.

## Stage server

`p.prova.network` — the testnet substitute we run on Hetzner that behaves like a real prover but doesn't post proofs to mainnet. Used for end-to-end testing of clients and SDKs without burning real gas.

## TGE

Token Generation Event. The moment PROVA tokens become transferable / claimable. Prova's TGE is gated on usage milestones (real revenue, real deals), not calendar dates. See [Tokenomics](https://prova.network/whitepaper).

## Treasury

A multi-sig (Safe) on Base holding 5% of total supply. Spent per governance decision (grants, exchange listings, audit budgets). Has zero discretion to mint or rug — supply is fixed at deploy.

## Unbonding period

The delay (currently 14 days) between a prover requesting to withdraw stake and the stake actually being released. Prevents fast-exit after misbehavior. Slashing during this window comes from unbonding stake first.

## USDC

The payment currency on Prova. Clients lock USDC into deals; provers receive USDC streaming over the deal lifetime; protocol takes a 1% cut. Specifically the Base-native USDC (`0x833589fcd6edb6e08f4c7c32d4f71b54bda02913`).

## VCH (Verifiable Claim Handoff)

Not part of Prova proper, but related: an EIP-712 envelope used by Cortex Protocol to hand off scoped, signed authority between agents. Mentioned occasionally in cross-system docs because Prova storage receipts can be wrapped in a VCH for agent-mediated retrieval. See [Cortex VCH spec](https://cortexprotocol.co/vch).

---

If a term is missing here that you needed, file an issue at [github.com/prova-network/docs](https://github.com/prova-network/docs/issues) — the glossary should be exhaustive.
