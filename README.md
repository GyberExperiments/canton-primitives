# DeFi Infrastructure for Canton Network

Open-source DeFi primitives built natively on Canton Network.
The missing execution layer for institutional digital asset trading.

**From OTC to AMM. From Oracle to Settlement. Production-ready. Open-source.**

Licensed under **GPL v3**.

---

## What Is This

Reusable DAML smart contracts (primitives) that any Canton dApp can integrate. This repository contains the core contract layer — OTC trading, escrow, collateral management, and settlement.

## OTC Contracts

| Contract | Description | LOC |
|----------|-------------|-----|
| `OtcTypes.daml` | Core data types — Asset, Price, OtcStatus, CollateralInfo | 106 |
| `OtcOffer.daml` | OTC trading template — create/accept/cancel/settle offers with partial fills | 278 |
| `Escrow.daml` | Three-party escrow — deposit/release/refund/arbitrate with 10 choices | 358 |
| `Collateral.daml` | Collateral management — lock/release/forfeit with invariant proof | 309 |
| `Settlement.daml` | Settlement process — multi-confirmation tracking with dispute resolution | 308 |
| `Test.daml` | Test scenarios — lifecycle, partial fill, cancellation, expiry | 313 |

## Planned Primitives

- **Matching Engine** — on-chain order book with price-time priority
- **Liquidity Pools** — constant-product AMM (x*y=k) for DAML
- **Price Oracle** — multi-source feeds with median aggregation
- **Atomic Swaps** — two-phase cross-asset swaps with timeout
- **Multi-Party Settlement** — batch netting across N parties
- **Compliance Gateway** — pluggable per-jurisdiction checks

## Structure

```
daml/
├── daml.yaml          # DAML project config (SDK 2.9.3)
└── OTC/
    ├── OtcTypes.daml
    ├── OtcOffer.daml
    ├── Escrow.daml
    ├── Collateral.daml
    ├── Settlement.daml
    └── Test.daml
```

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Smart Contracts | DAML 2.9.3 |
| Network | Canton Network (sub-transaction privacy) |

## CIP Compliance

- **CIP-0056** Token Standard — all primitives accept CIP-0056 tokens
- **CIP-0047** Activity Markers — trade events for Featured App tracking
- **CIP-0082** Governance — open-source, Foundation can fork/maintain
- **CIP-0103** Wallet Standard — integrates with compliant wallets

## Build

```bash
cd daml
daml build
daml test
```

## Related Repositories

- [canton-defi-infrastructure](https://github.com/TheMacroeconomicDao/canton-defi-infrastructure) — Full platform: Rust SDK, REST/gRPC APIs, Next.js frontend

## Contributors

- [GyberExperiment](https://github.com/GyberExperiments)
- [zalomax](https://github.com/zalomax)

## License

GPL v3 — see [LICENSE](./LICENSE).

---

Built by [Gybernaty](https://github.com/TheMacroeconomicDao) for Canton Network.

