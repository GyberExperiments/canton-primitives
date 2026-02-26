# Canton Network Architecture

Canton Network is a privacy-enabled blockchain designed for institutional use. Understanding its architecture is essential for working with Canton Primitives.

---

## Domain Architecture

Canton uses a domain-based architecture with three core components:

```
┌─────────────────────────────────┐
│          Canton Domain          │
│  ┌───────────┐ ┌────────────┐  │
│  │ Sequencer │ │  Mediator  │  │
│  └───────────┘ └────────────┘  │
│  ┌─────────────────────────┐   │
│  │    Domain Manager       │   │
│  └─────────────────────────┘   │
└─────────────────────────────────┘
         │              │
┌────────┴──┐    ┌──────┴────┐
│Participant│    │Participant│
│   Node 1  │    │   Node 2  │
└───────────┘    └───────────┘
```

- **Sequencer** — orders transactions, ensures global ordering
- **Mediator** — validates transaction results, ensures consistency
- **Domain Manager** — manages domain topology and participant registration
- **Participant Node** — hosts parties and executes DAML contracts

## Transaction Processing

Canton uses a 6-step commit protocol:

1. **Submission** — participant submits command via Ledger API
2. **Interpretation** — DAML engine evaluates the command
3. **Confirmation** — transaction sent to domain for ordering
4. **Validation** — mediator validates, participants confirm
5. **Commit** — all participants commit the result
6. **Event emission** — events delivered to observers via Ledger API

## Sub-Transaction Privacy

Canton's key differentiator — parties only see the parts of a transaction relevant to them:

- Signatories see the full contract
- Observers see the contract but not internal computations
- Non-involved parties see nothing

This is critical for institutional OTC trading where counterparties should not see each other's full portfolios.

## Ledger API Services

| Service Group | Services |
|--------------|----------|
| **Command** | CommandService, CommandSubmissionService, CommandCompletionService |
| **Query** | TransactionService, ActiveContractsService |
| **Management** | PartyManagementService, PackageService |
| **Utility** | VersionService, LedgerIdentityService |

## Security Model

| Component | Technology |
|-----------|-----------|
| Identity | X.509 certificates |
| Signing | Ed25519 / ECDSA (P-256) |
| Encryption | AES-GCM for data at rest |
| Transport | TLS 1.3 for all gRPC |
| Authorization | JWT tokens for API access |

## Performance

- **Throughput:** 1,000+ TPS per domain
- **Latency:** Sub-second finality
- **Scalability:** Horizontal via multi-domain

## Global Synchronizer

The Global Synchronizer connects multiple Canton domains, enabling:

- Cross-domain atomic transactions
- Global asset transfer
- Unified identity across domains

Super Validators operate the Global Synchronizer and validate cross-domain transactions.
