# Overview

## What Are Canton Primitives?

Canton Primitives is a collection of reusable DAML smart contracts designed for institutional DeFi on Canton Network. These contracts provide the foundational building blocks — OTC trading, escrow, collateral management, and settlement — that any Canton dApp can integrate.

## Why Canton Network?

Canton Network provides unique properties essential for institutional finance:

- **Sub-transaction privacy** — parties only see data relevant to them
- **Composability** — contracts can interact across applications
- **Regulatory compliance** — built-in audit trail and observer pattern
- **Finality** — deterministic execution with no MEV risk

## Contract Stack

The primitives form a layered stack:

```
┌─────────────────────────┐
│      OTC Offer          │  Trade lifecycle
├─────────────────────────┤
│   Escrow  │ Collateral  │  Asset protection
├─────────────────────────┤
│      Settlement         │  Trade finalization
├─────────────────────────┤
│      OTC Types          │  Shared data types
└─────────────────────────┘
```

## Design Principles

### 1. Invariant-First

Every contract enforces mathematical invariants. For example, the Collateral contract maintains:

```
(currentAmount + lockedAmount) == initialAmount
```

This is checked on every state transition — making it impossible to create or destroy collateral through bugs.

### 2. Multi-Party Authorization

Canton's authorization model ensures:

- **Signatories** must approve contract creation
- **Controllers** must authorize choice execution
- **Observers** can see but not modify

### 3. Documented Bug Fixes

All production bug fixes are documented inline (P0-10 through P0-20), showing the exact issue and resolution. This transparency helps auditors and contributors understand the contract evolution.

### 4. Comprehensive Validation

Each choice includes multiple `assertMsg` checks with descriptive error messages. No silent failures — every validation explains what went wrong.

## Tech Stack

| Component | Technology | Version |
|-----------|-----------|---------|
| Smart Contracts | DAML | 2.9.3 |
| Network | Canton Network | — |
| Testing | Daml.Script | Built-in |
| Dependencies | daml-prim, daml-stdlib, daml-script | — |

## CIP Compliance

All primitives are designed to comply with Canton Improvement Proposals:

| CIP | Standard | Integration |
|-----|----------|------------|
| CIP-0056 | Token Standard | All primitives accept CIP-0056 tokens |
| CIP-0047 | Activity Markers | Trade events for Featured App tracking |
| CIP-0082 | Governance | Open-source, Foundation can fork/maintain |
| CIP-0103 | Wallet Standard | SDK integrates with compliant wallets |
