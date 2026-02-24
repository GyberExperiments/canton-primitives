# Canton DeFi Infrastructure Layer

Open-source DeFi primitives built natively on Canton Network.
The missing execution layer for institutional digital asset trading.

**From OTC to AMM. From Oracle to Settlement. Production-ready. Open-source.**

---

## What Is This

Canton DeFi Infrastructure Layer is the shared DeFi execution layer for Canton Network. It provides reusable smart contracts, a Rust SDK, REST/gRPC APIs, and a reference frontend that any Canton dApp can integrate with.

**Live platform:** https://1otc.cc

## Key Metrics

| Metric | Value |
|--------|-------|
| DAML Templates | 10 production (1,666 LOC) |
| Rust SDK | 14 crates (6,894 LOC) |
| REST Endpoints | 15 production |
| Proto Files | 30+ (Ledger API v2) |
| Infrastructure | Kubernetes on dedicated hardware |
| Grant Request | 200,000 Canton Coins |

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
       │   10 DAML Templates   │
       │   1,666 LOC           │
       └───────────┬───────────┘
                   │
       ┌───────────┴───────────┐
       │   Canton Network      │
       │   Sub-tx privacy      │
       └───────────────────────┘
```

## DeFi Primitives

**Existing (Production):**
- OTC Trading (OtcOffer) — full multi-party workflow with partial fills
- Escrow — 10-choice escrow with arbitration
- Collateral — lock/release/forfeit with invariant proof
- Settlement — multi-confirmation tracking
- Treasury Bills — institutional T-Bill tokenization

**Planned (Grant):**
- Matching Engine — on-chain order book with price-time priority
- Liquidity Pools — constant-product AMM (x*y=k) for DAML
- Price Oracle — multi-source feeds with median aggregation
- Atomic Swaps — two-phase cross-asset swaps with timeout
- Multi-Party Settlement — batch netting across N parties
- Compliance Gateway — pluggable per-jurisdiction checks

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Smart Contracts | DAML 2.9.3 |
| SDK | Rust (2021 Edition) |
| gRPC | Tonic 0.13 + Prost |
| HTTP | Axum 0.7 |
| Frontend | Next.js 15 + React 19 |
| Orchestration | Kubernetes |
| Monitoring | Prometheus + Grafana + Loki |

## CIP Compliance

- **CIP-0056** Token Standard — all primitives accept CIP-0056 tokens
- **CIP-0047** Activity Markers — trade events for Featured App tracking
- **CIP-0082** Governance — open-source, Foundation can fork/maintain
- **CIP-0103** Wallet Standard — SDK integrates with compliant wallets

## Development

```bash
# Frontend
pnpm install
pnpm dev

# Build
pnpm build
```

## Deployment

```bash
# Apply K8s manifests
kubectl apply -f k8s/namespace.yaml
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
kubectl apply -f k8s/ingress.yaml
```

## Documentation

- **Technical Specification:** [docs/specification/](./docs/specification/)
- **Site Content:** [docs/landing/](./docs/landing/)

## License

Apache 2.0 — permissive, business-friendly, Foundation can fork and maintain.

---

Built by [Gybernaty](https://github.com/TheMacroeconomicDao) for Canton Network.
