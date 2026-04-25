---
icon: clock-rotate-left
description: What changed, when.
---

# Changelog

Reverse chronological. Tags follow [SemVer](https://semver.org/) for the SDK, CLI, and HTTP API independently.

## v0.4.2 — 2026-04-25

**The clients-first release.** This is the one where Prova actually became usable.

* New: web upload page at [prova.network/upload/](https://prova.network/upload/) — drag-and-drop, no install, no wallet, 100 MB free per file
* New: CLI `@prova-network/cli` (`prova auth | put | get | ls | whoami | logout`)
* New: dashboard at [prova.network/app/](https://prova.network/app/) — token mint, file list, usage chart, revoke
* New: HTTP API tokens (HS256 JWT, scoped, revocable, 1-year expiry)
* New: 6 documented endpoints — `/api/auth/signup`, `/api/upload`, `/p/{cid}`, `/api/files`, `/api/usage`, `/api/tokens/{list,revoke}`
* Wired: domain `prova.network` (apex + www CNAME-flattened to Pages, `p.prova.network` for the staging server with Origin CA cert)
* Wired: docs at `docs.prova.network` (you are here)
* Internal: 16-finding security audit shipped — see `SECURITY-AUDIT-2026-04-25.md` in the repo
* Outstanding: real CommP in browser, magic-link email verify, Origin/CSRF guard, abuse + CSAM hook (all tracked for pre-public-testnet)

## v0.4.1 — 2026-04-24

Visual / brand pass.

* New: live WebGL Earth hero on prova.network
* New: animated architecture and lifecycle diagrams (real motion, not GIFs)
* New: notarial-seal logo
* Apple system font stack
* Both dark and light themes

## v0.4.0 — 2026-04-22

The Ethereum-native pivot.

* Renamed and reframed: Prova is now verifiable storage on Base, not an L1 chain
* `Synapse` → `Prova` rename across the SDK
* Dropped: PoRep, sealing, FVM dependencies
* Forked: FilOzone PDP contracts, adapted to Base + USDC
* Forked: synapse-sdk → @prova-network/sdk

## v0.3 and earlier

The L1 era. Archived under `archive/v1-l1-chain` in the repo.

## Roadmap (next)

* **v0.5** — magic-link email verify, Origin/CSRF guards, abuse hook, CSP/HSTS, real CommP in browser. Also: testnet contracts deployed to Base Sepolia.
* **v0.6** — real on-chain dealmaking from the upload page. Sponsor wallet, sponsor prover. CLI `prova put` returns a real deal-id.
* **v0.7** — wallet-paid uploads. Custom terms. Custom redundancy.
* **v0.8** — public testnet open to all.
* **v1.0** — mainnet. Audit complete. SLAs published.
