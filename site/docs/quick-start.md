# Quick Start

## Prerequisites

- [DAML SDK 2.9.3](https://docs.daml.com/getting-started/installation.html) installed
- Git

## Clone and Build

```bash
git clone https://github.com/GyberExperiments/canton-primitives.git
cd canton-primitives/daml
daml build
```

## Run Tests

```bash
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

All 6 tests should pass.

## Project Structure

```
daml/
├── daml.yaml           # Project config (SDK 2.9.3)
└── OTC/
    ├── OtcTypes.daml   # Shared data types (106 LOC)
    ├── OtcOffer.daml   # OTC trading template (278 LOC)
    ├── Escrow.daml     # Three-party escrow (358 LOC)
    ├── Collateral.daml # Collateral management (309 LOC)
    ├── Settlement.daml # Settlement process (308 LOC)
    └── Test.daml       # Test scenarios (313 LOC)
```

## Configuration

`daml.yaml` defines the project:

```yaml
sdk-version: 2.9.3
name: canton-primitives
version: 1.0.0
source: OTC
parties:
  - Operator
  - Trader1
  - Trader2
  - Auditor
dependencies:
  - daml-prim
  - daml-stdlib
  - daml-script
```

## Basic Usage Example

### Create an OTC Offer

```haskell
-- Create a public buy offer for 1000 USDC
offer <- submit operator do
  createCmd OtcOffer with
    offerId = "offer-001"
    operator = operator
    initiator = alice
    counterparty = None       -- Public offer
    asset = Asset with
      symbol = "USDC"
      amount = 1000.0
      chain = "Canton"
      contractAddress = None
    price = Price with
      rate = 1.0
      currency = "USD"
    quantity = 1000.0
    side = Buy
    limits = VolumeLimits with
      minAmount = 100.0
      maxAmount = 1000.0
    status = Active
    timestamps = Timestamps with
      created = now
      updated = now
      expiresAt = Some expiry
    collateral = None
    settlementInfo = None
    minComplianceLevel = "RETAIL"
    allowedJurisdictions = ["US", "EU"]
    auditors = [auditor]
```

### Accept the Offer

```haskell
-- Bob accepts the full offer
result <- submit bob do
  exerciseCmd offerCid Accept with
    acceptor = bob
    requestedQuantity = 1000.0
    complianceOk = True
    currentTime = now
    paymentProof = "0xabc123..."
```

### Create Escrow

```haskell
-- Create escrow for the trade
escrow <- submit operator do
  createCmd Escrow with
    escrowId = "escrow-001"
    offerId = "offer-001"
    ...
    amount = 1000.0
    status = Created
```

## Next Steps

- Read the [OTC Offer](primitives/otc-offer.md) specification for full contract details
- Explore [Escrow](primitives/escrow.md) for the 10-choice dispute resolution flow
- Review the [Architecture](architecture/canton-network.md) for Canton Network integration
