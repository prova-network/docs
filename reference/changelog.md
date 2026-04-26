---
description: What shipped in Prova, by date.
---

# Changelog

A human-readable record of meaningful changes. We don't ceremony every commit here — only releases, breaking changes, and milestones a developer should know about.

The canonical machine record lives in the git history of [github.com/prova-network](https://github.com/prova-network). This page is curated.

---

## Unreleased

Things landing on `main` but not yet tagged.

- **Docs**: Filled in the missing `README.md`, `reference/glossary.md`, `reference/errors.md`, `reference/changelog.md` pages that `SUMMARY.md` linked to but that didn't exist on disk.
- **Docs**: Linked the `provers/hobby.md`, `provers/enterprise.md`, `provers/prosumer.md` tier pages from `SUMMARY.md` so they're discoverable via the sidebar.
- **SDK**: Continued the rename + port from upstream `FilOzone/synapse-sdk`.

## 2026-04-26

The "real PDP, real stake math, real CI" day.

### Contracts

- **Real PDP verifier landed.** `MockProofVerifier` was replaced with the upstream `FilOzone/pdp` fork behind a UUPS proxy. The mock is still available as an opt-in (`PROVA_USE_MOCK_VERIFIER=1`) for fast smoke tests, but real PDP is the default.
- **Stake floor was wrong; fixed.** The original `100 PROVA / GiB` formula meant a 1 PB prover would have needed more than the entire token supply. Switched the unit to per-TiB, dropped the default to `0.1 PROVA / TiB`, and added an `IPriceOracle` (Chainlink-compatible) so the *USD-equivalent* stake floor is enforced regardless of PROVA price. New scaling is sane: 100 TB needs 3,000 PROVA; 1 PB needs 30,720 PROVA; 100 PB needs ~3M PROVA.
- **Slashing burns PROVA.** `ProverStaking.slash()` now calls `ERC20Burnable.burn()` directly. The deprecated `slashedPool` and `withdrawSlashed` are gone. Total supply visibly decreases on every slash.
- **`ProverRewards` is funded at deploy.** Previously every `claim()` would have reverted because the rewards contract had a zero balance.
- **CEI reentrancy fix.** Slither flagged a Checks-Effects-Interactions violation in `possessionProven`. Reordered to strict CEI; 0 slither findings on a clean run now.
- **CI pipeline.** `contracts-ci.yml` runs forge build/test, gas snapshots, lcov coverage, and Slither static analysis with SARIF upload on every push. Currently fails on the GitHub runner with a Solidity compile error that doesn't repro locally — tracked as `prova-network/contracts#1`.
- **Anvil tests:** 106/106 unit + 16/16 PROVA flow + 27/27 deep economics, all passing locally.

### Brand

- **Dual-mode logo.** `prova-mark-light.svg` (deeper teal) for white backgrounds and `prova-mark-dark.svg` (lighter teal) for dark backgrounds. READMEs use `<picture media>` so GitHub picks the right one automatically.

## 2026-04-25

- **Internal security audit (16 findings).** F-01 (account squat via signup with no email verify) and F-02 (no CSRF on token endpoints) are the critical/high items. Both still unfixed at time of writing; fix branch in progress.

## 2026-04-24

- **The pivot.** Prova v1 was a standalone L1 blockchain. v1 is dead. v2 is **Ethereum-native verifiable storage**: no separate chain, no native gas token, no bridge. PROVA stays an ERC-20 on Base forever. All staking, marketplace, and dispute resolution live in Solidity contracts on Ethereum. Storage and proofs live off-chain in the Prova network. See [`PIVOT.md`](https://github.com/prova-network/prova/blob/main/PIVOT.md) and [`PROVA-V2-ARCHITECTURE.md`](https://github.com/prova-network/prova/blob/main/PROVA-V2-ARCHITECTURE.md) in the umbrella repo for the full reasoning.
- **Tokenomics v2.** Killed the May 2026 ICO. New playbook: build → earn points → launch with usage. Storage rewards 40%, points conversion 15%, team 20% (50/50 between the two cofounders, 12-month cliff, 36-month linear vest), ecosystem 10%, strategic 5%, liquidity 5%, treasury 5%. See [`TOKENOMICS-v2.md`](https://github.com/prova-network/prova/blob/main/TOKENOMICS-v2.md).

## Earlier

The pre-pivot history (v1 L1 chain) is tagged at `v0.2-l1-snapshot` and archived on the `archive/v1-l1-chain` branch in the umbrella repo. Most of that code is preserved (the PDP, CommP, and storage primitives transferred forward); the chain machinery was cut.

---

## How to read this

- **Date headings** are days when something user-visible shipped to a public branch.
- **"Unreleased"** is a rolling section of `main` since the last public deployment.
- **Version tags** will appear here once we cut the first real release tag (planned around the Base Sepolia deploy).

If a change is missing that should be here, open an issue at [github.com/prova-network/docs](https://github.com/prova-network/docs/issues) and we'll add it.
