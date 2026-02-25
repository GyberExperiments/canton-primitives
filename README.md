# Canton Primitives — OTC Smart Contracts

Open-source DAML smart contracts for OTC trading on Canton Network.

Licensed under **Apache 2.0**.

---

## Contracts

| Contract | Description | LOC |
|----------|-------------|-----|
| `OtcTypes.daml` | Core data types — Asset, Price, OtcStatus, CollateralInfo | 106 |
| `OtcOffer.daml` | OTC trading template — create/accept/cancel/settle offers with partial fills | 278 |
| `Escrow.daml` | Three-party escrow — deposit/release/refund/arbitrate with 10 choices | 358 |
| `Collateral.daml` | Collateral management — lock/release/forfeit with invariant proof | 309 |
| `Settlement.daml` | Settlement process — multi-confirmation tracking with dispute resolution | 308 |
| `Test.daml` | Test scenarios — lifecycle, partial fill, cancellation, expiry | 313 |

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

## Requirements

- DAML SDK 2.9.3
- Canton Network participant node

## Build

```bash
cd daml
daml build
daml test
```

## Contributors

- [GyberExperiment](https://github.com/GyberExperiments)
- [zalomax](https://github.com/zalomax)

## License

Apache 2.0 — see [LICENSE](./LICENSE).

---

Built by [Gybernaty](https://github.com/TheMacroeconomicDao) for Canton Network.
