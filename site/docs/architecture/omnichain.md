# OmniChain Integration

Cross-chain integration patterns for connecting Canton DeFi Primitives with external blockchains.

---

## Architecture

The OmniChain platform uses a 4-layer architecture:

```
┌──────────────────────────────┐
│       Unified API            │  Public interface
├──────────────────────────────┤
│   Protocol Abstraction       │  Chain-agnostic logic
├──────────────────────────────┤
│     Chain Adapters           │  Per-chain implementations
├──────────────────────────────┤
│       Transport              │  gRPC / HTTP / WebSocket
└──────────────────────────────┘
```

## Integration Patterns

### Bridge Pattern

Lock assets on source chain, mint on destination:

```rust
pub trait Bridge: Send + Sync {
    async fn lock_for_transfer(&self, req: LockRequest) -> Result<LockReceipt>;
    async fn release_from_transfer(&self, proof: TransferProof) -> Result<ReleaseReceipt>;
    async fn verify_transfer(&self, receipt: &LockReceipt) -> Result<bool>;
}
```

### Adapter Pattern

Per-chain adapter implementing a universal interface:

```rust
pub trait ChainAdapter: Send + Sync {
    async fn submit_transaction(&self, tx: UniversalTransaction) -> Result<TxHash>;
    async fn query_state(&self, query: StateQuery) -> Result<StateResult>;
    async fn subscribe_events(&self, filter: EventFilter) -> Result<EventStream>;
}
```

### Router Pattern

Route messages across chains via Canton as the hub:

```rust
pub trait MessageRouter: Send + Sync {
    async fn route(&self, msg: UniversalMessage) -> Result<RouteResult>;
    async fn resolve_path(&self, from: ChainId, to: ChainId) -> Result<RoutePath>;
}
```

## Canton as Hub Chain

Canton Network serves as the central hub for cross-chain operations:

```
Ethereum ──┐
           │
Cosmos ────┤──── Canton Network ────┤──── Settlement
           │    (Privacy + Finality) │
Substrate ─┤                        ├──── Compliance
           │                        │
Solana ────┘                        └──── Audit Trail
```

## Supported Chains (Planned)

| Chain | Adapter | Bridge Protocol |
|-------|---------|----------------|
| Ethereum / EVM | Alloy | LayerZero V2 |
| Cosmos | IBC Client | IBC |
| Substrate | XCMP | Wormhole |
| Solana | SVM | Wormhole |

## Security Model

### Proof Types

```rust
pub enum ProofType {
    Merkle(MerkleProof),
    ZeroKnowledge(ZkProof),
    MultiSignature(MultiSigProof),
    LightClient(LightClientProof),
}
```

### Replay Protection

- Bloom filter for efficient duplicate detection
- Nonce tracking per sender per chain
- Transaction hash deduplication

## Cross-Chain Best Practices

1. Always verify proofs on both source and destination
2. Use timeout mechanisms for all cross-chain operations
3. Implement idempotent handlers for message replay
4. Monitor bridge TVL and set circuit breaker thresholds
5. Use Canton's sub-transaction privacy for sensitive transfers
