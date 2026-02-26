# Test Scenarios

**Module:** `Test` | **LOC:** 313 | **Scenarios:** 6

Comprehensive lifecycle tests using `Daml.Script`. All tests use the multi-party submission pattern with operator, maker, and taker roles.

---

## Test Summary

| Test | What It Verifies |
|------|-----------------|
| `testOfferLifecycle` | Full flow: create → accept (full fill) |
| `testPartialFill` | Partial fill: accept 3 of 10, remainder stays |
| `testCancelOffer` | Initiator cancels own offer |
| `testCollateralLifecycle` | Create → lock → release collateral |
| `testSelfTradePrevention` | Initiator cannot accept own offer |
| `testOfferExpiry` | Operator expires offer after deadline |

## Running Tests

```bash
cd daml
daml test
```

## Test Details

### testOfferLifecycle

Full OTC offer lifecycle — create an active public offer, counterparty accepts full quantity.

```haskell
-- Create Active offer: 1000 USDC at $1.0
-- Bob accepts full 1000
-- Assert: actualQuantity == 1000.0, actualPrice == 1.0
```

**Parties:** Operator, Alice (maker), Bob (taker), Auditor

### testPartialFill

Partial fill — accept part of an offer, verify remainder.

```haskell
-- Create offer: 10 ETH at $2500
-- Bob accepts 3 ETH (partial)
-- Assert: actualQuantity == 3.0
-- Remainder: new OtcOffer with 7 ETH stays active
```

### testCancelOffer

Cancellation by initiator with reason.

```haskell
-- Create offer: 1 BTC at $45,000
-- Alice cancels with reason "Changed my mind"
-- Contract is archived
```

### testCollateralLifecycle

Full collateral lifecycle — create, lock for trade, release.

```haskell
-- Create: 5000 USDC collateral (Available)
-- Lock: 2000 USDC for offer-001, 48h duration
-- Release: 2000 USDC back to owner
```

**Verifies:** Conservation invariant `(currentAmount + lockedAmount) == initialAmount` holds throughout.

### testSelfTradePrevention

Critical security test — ensures maker cannot fill their own order.

```haskell
-- Create offer by Alice
-- Alice tries to accept her own offer
-- submitMultiMustFail: assertion "Cannot trade with yourself" triggers
```

Uses `submitMultiMustFail` to verify the transaction is rejected.

### testOfferExpiry

Operator expires an offer after its deadline passes.

```haskell
-- Create offer with expiry: Jan 20, 2025
-- Operator expires at: Jan 21, 2025 (past deadline)
-- Contract is archived
```

## Test Patterns

### Multi-Party Submission

All tests use Canton's multi-party submission pattern:

```haskell
-- Create: both signatories submit
offerCid <- submitMulti [operator, alice] [] do
    createCmd OtcOffer with ...

-- Exercise: controller submits, others as readAs
result <- submitMulti [bob] [operator] do
    exerciseCmd offerCid Accept with ...
```

### Assertion Pattern

Results are verified with `assert`:

```haskell
assert (result.actualQuantity == 1000.0)
assert (result.actualPrice == 1.0)
```

### Must-Fail Pattern

Security constraints verified with `submitMultiMustFail`:

```haskell
submitMultiMustFail [alice] [operator] do
    exerciseCmd offerCid Accept with
        acceptor = alice  -- Same as initiator → must fail
```
