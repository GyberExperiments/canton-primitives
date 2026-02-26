# DAML Ledger API v2

Comprehensive reference for the Canton Ledger API — the gRPC interface used by the Rust SDK to interact with DAML contracts.

---

## Service Architecture

```
┌─────────────────────────────────────────────────┐
│                 Ledger API v2                    │
│                                                  │
│  ┌──────────────┐  ┌─────────────────────────┐  │
│  │   Command     │  │        Query            │  │
│  │  Services     │  │       Services          │  │
│  │              │  │                          │  │
│  │ Submit       │  │ TransactionService       │  │
│  │ SubmitAndWait│  │ ActiveContractsService   │  │
│  │ Completion   │  │                          │  │
│  └──────────────┘  └─────────────────────────┘  │
│                                                  │
│  ┌──────────────┐  ┌─────────────────────────┐  │
│  │  Management  │  │       Utility           │  │
│  │  Services    │  │       Services          │  │
│  │              │  │                          │  │
│  │ Party        │  │ VersionService           │  │
│  │ Package      │  │ LedgerIdentityService    │  │
│  └──────────────┘  └─────────────────────────┘  │
└─────────────────────────────────────────────────┘
```

## Command Services

### CommandService

Synchronous command submission — waits for the transaction result.

```protobuf
service CommandService {
    rpc SubmitAndWait(SubmitAndWaitRequest) returns (google.protobuf.Empty);
    rpc SubmitAndWaitForTransaction(SubmitAndWaitRequest) returns (SubmitAndWaitForTransactionResponse);
    rpc SubmitAndWaitForTransactionTree(SubmitAndWaitRequest) returns (SubmitAndWaitForTransactionTreeResponse);
}
```

### CommandSubmissionService

Asynchronous command submission — returns immediately.

```protobuf
service CommandSubmissionService {
    rpc Submit(SubmitRequest) returns (google.protobuf.Empty);
}
```

### CommandCompletionService

Stream command completions for submitted commands.

```protobuf
service CommandCompletionService {
    rpc CompletionStream(CompletionStreamRequest) returns (stream CompletionStreamResponse);
}
```

## Query Services

### TransactionService

Stream transactions and query historical data.

```protobuf
service TransactionService {
    rpc GetTransactions(GetTransactionsRequest) returns (stream GetTransactionsResponse);
    rpc GetTransactionTrees(GetTransactionsRequest) returns (stream GetTransactionTreesResponse);
    rpc GetTransactionByEventId(GetTransactionByEventIdRequest) returns (GetTransactionResponse);
    rpc GetTransactionById(GetTransactionByIdRequest) returns (GetTransactionResponse);
    rpc GetLatestPrunedOffsets(GetLatestPrunedOffsetsRequest) returns (GetLatestPrunedOffsetsResponse);
    rpc GetLedgerEnd(GetLedgerEndRequest) returns (GetLedgerEndResponse);
}
```

### ActiveContractsService

Query current active contracts.

```protobuf
service ActiveContractsService {
    rpc GetActiveContracts(GetActiveContractsRequest) returns (stream GetActiveContractsResponse);
}
```

## Data Types

### DAML Value

The universal value type representing all DAML types:

```protobuf
message Value {
    oneof Sum {
        Record record = 1;
        Variant variant = 2;
        string contract_id_struct = 3;
        List list = 4;
        sint64 int64 = 5;
        string numeric = 6;
        string text = 7;
        sfixed64 timestamp = 8;
        string party = 9;
        bool bool = 10;
        google.protobuf.Empty unit = 11;
        int32 date = 12;
        Optional optional = 13;
        Map map = 14;
        Enum enum = 15;
        GenMap gen_map = 16;
    }
}
```

### Events

```protobuf
message CreatedEvent {
    string event_id = 1;
    string contract_id = 2;
    Identifier template_id = 3;
    Record create_arguments = 4;
    repeated string witness_parties = 5;
    repeated string signatories = 6;
    repeated string observers = 7;
}

message ExercisedEvent {
    string event_id = 1;
    string contract_id = 2;
    Identifier template_id = 3;
    string choice = 4;
    Value choice_argument = 5;
    repeated string acting_parties = 6;
    bool consuming = 7;
    repeated string witness_parties = 8;
    repeated string child_event_ids = 9;
    Value exercise_result = 10;
}
```

## Rust SDK Mapping

The Rust SDK maps protobuf types to idiomatic Rust:

| Protobuf | Rust SDK |
|----------|---------|
| `Value` | `DamlValue` enum |
| `Record` | `DamlValue::Record(Vec<(String, DamlValue)>)` |
| `Identifier` | `Identifier { package_id, module_name, entity_name }` |
| `CreatedEvent` | `CreatedEvent { event_id, contract_id, template_id, arguments }` |
| `Transaction` | `Transaction { transaction_id, command_id, events, offset }` |

### Command Builder

```rust
let commands = CommandBuilder::new("app-id", "workflow-id", "command-id")
    .act_as(party_id)
    .create(template_id, record_arguments)
    .exercise(contract_id, "Accept", choice_arguments)
    .build();
```

### Ergonomic Macros

```rust
let record = daml_record! {
    "offerId" => "offer-001",
    "quantity" => 1000.0,
    "side" => daml_variant!("Buy"),
    "auditors" => daml_list!["auditor1"],
};
```
