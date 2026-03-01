# Canton DeFi Primitives

Open-source DAML smart contract library for institutional DeFi on [Canton Network](https://www.canton.network/).

Production-ready building blocks for OTC trading, escrow, collateral management, and multi-confirmation settlement — with sub-transaction privacy, mathematical invariant proofs, and full lifecycle testing.

```
1,672 LOC  ·  5 contracts  ·  28 choices  ·  50+ invariant assertions  ·  6 test scenarios
```

---

## Why Canton Primitives

Canton Network provides sub-transaction privacy and multi-party authorization — but there are no reusable DeFi building blocks. This library fills that gap.

**The problem:** Every Canton dApp rebuilds OTC, escrow, and settlement from scratch.

**The solution:** Audited, tested, composable primitives that any Canton application can import and use.

Key design principles:

- **Invariant-first** — every state transition is guarded by mathematical assertions (e.g., collateral conservation law: `currentAmount + lockedAmount == initialAmount`)
- **Multi-party authorization** — all sensitive operations require explicit consent from relevant parties
- **Documented bug fixes** — 6 critical fixes (P0-10 through P0-20) are inline-documented so auditors can trace the reasoning
- **Self-trade prevention** — built-in protection against wash trading

---

## Contracts

### OtcTypes — Core Data Types
`daml/OTC/OtcTypes.daml` · 106 LOC

Shared types used across all contracts:

| Type | Description |
|------|-------------|
| `Asset` | Symbol, amount, chain, optional contract address |
| `Price` | Rate + currency |
| `OtcStatus` | Pending → Active → PartiallyFilled → Filled / Cancelled / Expired / Disputed |
| `CollateralInfo` | ID, asset, lock time, status |
| `SettlementInfo` | Settlement ID, payment proof (SHA-256), confirmations |
| `VolumeLimits` | Min/max order size constraints |
| `AcceptResult` | Trade ID, quantity, price, settlement time, slippage (basis points) |

Built-in validation: `ensureAsset`, `ensurePrice`, `ensureVolumeLimits`.

---

### OtcOffer — OTC Trading
`daml/OTC/OtcOffer.daml` · 278 LOC · 6 choices

Full and partial fill OTC trading with compliance controls.

```
                          ┌──────────┐
                          │ Pending  │
                          └────┬─────┘
                               │ Activate
                          ┌────▼─────┐
                     ┌────│  Active  │────┐
                     │    └────┬─────┘    │
                Accept│        │ Cancel   │Expire
                     │    ┌────▼─────┐    │
                     │    │Cancelled │    │
                ┌────▼────┴──────────┴────▼───┐
                │  Filled / PartiallyFilled   │
                └─────────────────────────────┘
```

**Choices:** Activate · Accept (partial fills, slippage calc) · Cancel · Expire · Settle · Dispute

**Key features:**
- Partial fills with automatic remainder contract creation and recalculated volume limits
- Designated counterparty or open offers
- Compliance fields: `minComplianceLevel`, `allowedJurisdictions`, `auditors`
- Self-trade prevention (initiator ≠ counterparty)
- Expiry time validation

---

### Escrow — Three-Party Escrow with Arbitration
`daml/OTC/Escrow.daml` · 358 LOC · 10 choices

Buyer, seller, and neutral arbiter. Supports partial release, dispute resolution, and auto-expiry.

```
  Created ──Deposit──▶ Deposited ──Release──▶ Released
     │                    │
     │               Dispute│
     │                    ▼
     │                Disputed ──Arbitrate──▶ Arbitrated
     │                                           │
     └──AutoExpire──▶ (archived)      ExecuteArbitration
                                                 │
                                          ▼ (routed by decision)
```

**Choices:** Deposit · Release · PartialRelease · Refund · Dispute · Arbitrate · ExecuteArbitration · CancelEscrow · ExtendDeadline (max 3) · AutoExpire

---

### Collateral — Collateral Management with Conservation Law
`daml/OTC/Collateral.daml` · 309 LOC · 7 choices

The core invariant is checked on **every** state transition:

```
currentAmount + lockedAmount == initialAmount
```

Collateral cannot be created or destroyed — only transferred between states or explicitly withdrawn/forfeited.

```
  Available ──Lock──▶ Locked ──Release──▶ Available
                        │
                    Forfeit│
                        ▼
                  ForfeitedCollateral (new record for beneficiary)
```

**Choices:** Lock (0–365 days) · Release · Forfeit · Withdraw · TopUp · AutoRelease · (creates `ForfeitedCollateral` template on forfeit)

---

### Settlement — Multi-Confirmation Settlement
`daml/OTC/Settlement.daml` · 308 LOC · 8 choices

Payment confirmation flow with blockchain confirmation tracking.

```
  PendingPayment ──ConfirmPayment──▶ PaymentReceived ──AddConfirmation──▶ Confirming
                                                                             │
                                                              (threshold met)│
                                                                             ▼
                                                                        Completed
```

**Choices:** ConfirmPayment · AddConfirmation · CompleteSettlement · DisputeSettlement · ResolveDispute · FailSettlement · ExtendDeadline (max 3) · TimeoutSettlement

**Validation:** `totalAmount == quantity * price.rate`, payment proof ≥ 32 chars (SHA-256), confirmation counter bounded by required threshold.

---

## Testing

`daml/OTC/Test.daml` · 313 LOC · 6 scenarios

| Test | What it covers |
|------|---------------|
| `testOfferLifecycle` | Create → Activate → Accept (full fill) → verify result |
| `testPartialFill` | Accept 3 of 10 ETH → verify remainder contract with 7 ETH |
| `testCancelOffer` | Create → Cancel → verify archived |
| `testCollateralLifecycle` | Create → Lock 2000 → Release → verify conservation invariant |
| `testSelfTradePrevention` | Alice tries to accept own offer → must fail |
| `testOfferExpiry` | Create with expiry → Expire after deadline → verify archived |

```bash
cd daml
daml build
daml test
```

Expected output:
```
Test Summary
  OTC/Test:testOfferLifecycle: ok
  OTC/Test:testPartialFill: ok
  OTC/Test:testCancelOffer: ok
  OTC/Test:testCollateralLifecycle: ok
  OTC/Test:testSelfTradePrevention: ok
  OTC/Test:testOfferExpiry: ok
```

---

## Project Structure

```
canton-primitives/
├── daml/
│   ├── daml.yaml                   # DAML SDK 2.9.3
│   └── OTC/
│       ├── OtcTypes.daml           # Shared data types & validation
│       ├── OtcOffer.daml           # OTC trading (6 choices)
│       ├── Escrow.daml             # 3-party escrow (10 choices)
│       ├── Collateral.daml         # Collateral management (7 choices)
│       ├── Settlement.daml         # Settlement process (8 choices)
│       └── Test.daml               # 6 lifecycle test scenarios
└── site/                           # MkDocs documentation site
    ├── mkdocs.yml
    └── docs/                       # Primitives, architecture, CIP compliance
```

---

## CIP Compliance

| CIP | Standard | How |
|-----|----------|-----|
| CIP-0056 | Token Standard | All primitives accept CIP-0056 tokens |
| CIP-0047 | Activity Markers | Trade events for Featured App tracking |
| CIP-0082 | Governance | GPL v3 — Foundation can fork/maintain |
| CIP-0103 | Wallet Standard | Integrates with compliant wallets |

---

## Planned Primitives

- **Matching Engine** — on-chain order book with price-time priority
- **Liquidity Pools** — constant-product AMM (x·y=k) for DAML
- **Price Oracle** — multi-source feeds with median aggregation
- **Atomic Swaps** — two-phase cross-asset swaps with timeout
- **Multi-Party Settlement** — batch netting across N parties
- **Compliance Gateway** — pluggable per-jurisdiction checks

---

## Documentation

Full documentation is available via MkDocs:

```bash
cd site
pip install mkdocs-material
mkdocs serve
```

Or browse the `site/docs/` directory directly.

---

## Related

- [canton-defi-infrastructure](https://github.com/TheMacroeconomicDao/canton-defi-infrastructure) — Full platform: Rust SDK, REST/gRPC APIs, Next.js frontend

## Contributors

- [GyberExperiment](https://github.com/GyberExperiments)
- [zalomax](https://github.com/zalomax)

## License

GPL v3 — see [LICENSE](./LICENSE).

---

Built by [Gybernaty](https://github.com/GyberExperiments) for Canton Network.
