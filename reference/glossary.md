---
icon: book
description: Terms used across the Prova docs and codebase.
---

# Glossary

## Active

A deal is `Active` once the prover has fetched the bytes, verified the cid, and posted the first successful proof. Active deals receive payment per successful proof.

## CID (Content Identifier)

A self-describing, content-addressed identifier. Prova's `piece-cid` is a CIDv1 with multicodec `piece-commitment` and multihash `sha2-256-trunc254-padded`. See [Piece-CIDs](../concepts/piece-cids.md).

## CommP

The piece-commitment hash. A binary Merkle root over the file's bytes (with Fr32 padding). The on-chain `bytes32` representation of the cid. Used to verify proofs.

## Deal

A contract between a client and a prover for storing one piece for one term. Deals are created, settled, and slashed by `StorageMarketplace.sol` on Base.

## Escrow

USDC the client locked into the marketplace contract when proposing the deal. Released to the prover proportionally as proofs land. Refunded to the client if the deal is cancelled or the prover is slashed.

## JTI

The `jti` claim on a JWT — a unique identifier for one issued token. Used by Prova to revoke individual tokens without rotating the global signing key.

## PDP (Proof of Data Possession)

The cryptographic protocol Prova uses to verify a prover still has the bytes. Lighter than PoRep, no sealing, no SNARKs. See [Continuous proof of storage](../concepts/continuous-proof.md).

## Piece

A blob of bytes addressed by a piece-cid. Files larger than the per-piece limit are split client-side into multiple pieces.

## piece-cid

The content-addressed id of a piece. See [Piece-CIDs](../concepts/piece-cids.md).

## Prover

A node operator who runs `provad`, stakes USDC, stores client bytes, and posts daily proofs. Earns USDC per successful proof.

## Redundancy / replication factor

How many independent provers hold a copy of the same piece. Default 4. Higher = more durable, more expensive.

## Slashing

The protocol's punishment for a prover that fails to post proofs. Their staked USDC is destroyed (or partially refunded to the client). See [Resilience](../concepts/resilience.md).

## Stake

USDC a prover locks into `ProverStaking.sol` to register capacity. The stake is the prover's skin in the game; missed proofs eat into it.

## Term

The duration of a deal, in seconds. Default 30 days, max 5 years.

## Token (API)

A bearer credential issued by `/api/auth/signup`. HS256-signed JWT with a `pk_live_` prefix. See [Authentication](../api/authentication.md).
