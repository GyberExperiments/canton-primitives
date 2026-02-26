# Canton DeFi Primitives

**Open-source DeFi primitives built natively on Canton Network.**
**The missing execution layer for institutional digital asset trading.**

*From OTC to AMM. From Oracle to Settlement. Production-ready. Open-source.*

---

## What Is This

Reusable DAML smart contracts (primitives) that any Canton dApp can integrate. This repository contains the core contract layer — OTC trading, escrow, collateral management, and settlement — with a Rust SDK for programmatic access.

## Key Metrics

| Metric | Value |
|--------|-------|
| DAML Templates | 5 production + 1 test (1,672 LOC) |
| Contract Choices | 28 total |
| Invariant Checks | 50+ assertions |
| Bug Fixes Documented | P0-10 through P0-20 |
| Test Scenarios | 6 lifecycle tests |
| SDK | Rust (14 crates planned) |
| License | GPL v3 |

## Architecture

```
        CONSUMER APPLICATIONS
┌────────────────────────────────────┐
│  Wallets  │  dApps  │  Bots  │ SDK│
└──────────────────┬─────────────────┘
                   │
       ┌───────────┴───────────┐
       │   REST API + gRPC     │
       │   15+ endpoints       │
       └───────────┬───────────┘
                   │
       ┌───────────┴───────────┐
       │   Rust SDK (14 crates)│
       │   cargo add canton-sdk│
       └───────────┬───────────┘
                   │ gRPC
       ┌───────────┴───────────┐
       │   DAML Primitives     │ ← This repo
       │   1,672 LOC           │
       └───────────┬───────────┘
                   │
       ┌───────────┴───────────┐
       │   Canton Network      │
       │   Sub-tx privacy      │
       └───────────────────────┘
```

## Production Primitives

| Contract | Description | LOC | Choices |
|----------|-------------|-----|---------|
| **OtcTypes** | Core data types — Asset, Price, OtcStatus, CollateralInfo | 106 | — |
| **OtcOffer** | OTC trading — create/accept/cancel/settle with partial fills | 278 | 6 |
| **Escrow** | Three-party escrow — deposit/release/refund/arbitrate | 358 | 10 |
| **Collateral** | Collateral management — lock/release/forfeit with invariant proof | 309 | 7 |
| **Settlement** | Settlement process — multi-confirmation with dispute resolution | 308 | 8 |

## Planned Primitives

- **Matching Engine** — on-chain order book with price-time priority
- **Liquidity Pools** — constant-product AMM (x·y=k) for DAML
- **Price Oracle** — multi-source feeds with median aggregation
- **Atomic Swaps** — two-phase cross-asset swaps with timeout
- **Multi-Party Settlement** — batch netting across N parties
- **Compliance Gateway** — pluggable per-jurisdiction checks

## Quick Start

```bash
cd daml
daml build
daml test
```

## Related

- [Canton DeFi Infrastructure](https://github.com/TheMacroeconomicDao/canton-defi-infrastructure) — Full platform: Rust SDK, REST/gRPC APIs, Next.js frontend

---

Built by [Gybernaty](https://github.com/TheMacroeconomicDao) for Canton Network. Licensed under GPL v3.
