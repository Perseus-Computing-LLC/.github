<p align="center">
  <img src="./assets/perseus-mark.svg" alt="Perseus Computing mark" width="96">
</p>

<h1 align="center">Perseus Computing</h1>

<p align="center">
  <strong>Deterministic context synthesis. Local encrypted memory. Tamper-evident audit provenance.</strong><br>
  Open-source, local-first cognitive infrastructure for autonomous AI agents.
</p>

<p align="center">
  <a href="https://perseuscomputing.com">Website</a> &middot;
  <a href="https://github.com/Perseus-Computing-LLC">GitHub</a> &middot;
  <a href="https://crates.io/crates/harpe">Harpe Crate</a> &middot;
  <a href="https://crates.io/crates/kibisis">Kibisis Crate</a> &middot;
  <a href="https://crates.io/crates/argus-ledger">Argus Crate</a>
</p>

---

## Clean-Room Architecture

Autonomous AI agents require verifiable state, durable memory, and tamper-evident auditability. Perseus Computing provides local-first primitives engineered in pure Rust to form the foundation of verifiable agent cognition.

All primitives are designed clean-room, released under the permissive **MIT License**, and published as independent, composable crates on [crates.io](https://crates.io).

```
 +-------------------------------------------------------------------------+
 |                      AUTONOMOUS AGENT RUNTIME                            |
 +-------------------------------------------------------------------------+
       |                               |                              |
       v                               v                              v
 [ STAGE 01: CONTEXT ]        [ STAGE 02: MEMORY ]          [ STAGE 03: AUDIT ]
       harpe                        kibisis                     argus-ledger
  Token Budgeting &            Encrypted Vault &             Append-Only Ledger &
  Priority Scheduling          SWMR Concurrency Leases       SHA-256 Provenance
       |                               |                              |
       v                               v                              v
 Deterministic Prompt           ChaCha20-Poly1305             Cryptographic Merkle
 Allocation Frame               Memory Envelope               Audit Receipt
```

| Layer | Primitive | Role | Crates.io | Documentation |
| :--- | :--- | :--- | :--- | :--- |
| **Context** | **Harpe** | Deterministic context budgeting, priority scheduling, and dynamic synthesis. | [![Crates.io](https://img.shields.io/crates/v/harpe.svg)](https://crates.io/crates/harpe) | [![docs.rs](https://docs.rs/harpe/badge.svg)](https://docs.rs/harpe) |
| **Memory** | **Kibisis** | Encrypted cognitive memory vault with versioning and atomic concurrency leases. | [![Crates.io](https://img.shields.io/crates/v/kibisis.svg)](https://crates.io/crates/kibisis) | [![docs.rs](https://docs.rs/kibisis/badge.svg)](https://docs.rs/kibisis) |
| **Evidence** | **Argus** | Cryptographic audit ledger with tamper-evident SHA-256 hash chaining. | [![Crates.io](https://img.shields.io/crates/v/argus-ledger.svg)](https://crates.io/crates/argus-ledger) | [![docs.rs](https://docs.rs/argus-ledger/badge.svg)](https://docs.rs/argus-ledger) |

---

## Workspace Installation

Add the complete cognitive stack to your Cargo workspace:

```bash
cargo add harpe kibisis argus-ledger
```

Or declare individual primitives in `Cargo.toml`:

```toml
[dependencies]
harpe = "0.1.0-alpha.1"
kibisis = "0.1.0-alpha.1"
argus-ledger = "0.1.0-alpha.1"
```

---

## Core Primitives & Code Interfaces

### 1. Harpe: Context Window Budgeting & Synthesis
Manage strict context windows deterministically without loss of high-priority directives:

```rust
use harpe::{ContextBudgetEngine, ContextItem, PrunePolicy, TokenBudget};

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let budget = TokenBudget {
        total_budget: 4096,
        reserved_completion: 1024,
        reserved_tools: 512,
        safety_margin: 128,
    };

    let engine = ContextBudgetEngine::new(budget, PrunePolicy::LowestPriority);

    let items = vec![
        ContextItem {
            id: "system_prompt".to_string(),
            content: "You are a mission-critical verification agent.".to_string(),
            token_count: 120,
            priority: 100,
            compressible: false,
        },
        ContextItem {
            id: "retrieved_doc".to_string(),
            content: "Historical telemetry log payload data...".to_string(),
            token_count: 3200,
            priority: 10,
            compressible: true,
        },
    ];

    let report = engine.synthesize(items)?;
    println!(
        "Allocated {} tokens (pruned {} items)",
        report.allocated_tokens, report.pruned_item_ids.len()
    );

    Ok(())
}
```

### 2. Kibisis: Local Encrypted Memory Vault
Maintain persistent, searchable cognitive memory with atomic multi-agent leases:

```rust
use kibisis::{InMemoryLeaseCoordinator, InMemoryMemoryVault, LeaseCoordinator, MemoryEntity, MemoryVault};
use std::time::Duration;

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let vault = InMemoryMemoryVault::new();
    let coordinator = InMemoryLeaseCoordinator::new();

    // Acquire atomic write lease for key
    let lease = coordinator.acquire_write_lease("agent_target_42", Duration::from_millis(100))?;

    // Store encrypted memory entity
    let entity = MemoryEntity::new(
        "subgoal",
        "agent_target_42",
        "Target waypoint confirmed: sector 7.",
    );
    vault.put(entity)?;

    // Release lease
    coordinator.release_lease(&lease.token)?;

    // Retrieve and search
    let results = vault.search("waypoint")?;
    for res in results {
        println!("Found memory entity: {} (score: {})", res.entity.key, res.score);
    }

    Ok(())
}
```

### 3. Argus: Cryptographic Provenance Ledger
Record all agent actions, tool calls, and model decisions into an immutable SHA-256 audit chain:

```rust
use argus_ledger::{AuditLedgerReader, AuditLedgerWriter, AuditVerifier, InMemoryLedger};

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let mut ledger = InMemoryLedger::new();

    // Append verified execution events
    let ev1 = ledger.append(
        "SYSTEM_INITIALIZATION",
        "agent:perseus-core",
        r#"{"status":"bootstrapped"}"#,
    )?;
    println!("Sequence #{}: Hash {}", ev1.sequence, &ev1.event_hash[0..16]);

    let ev2 = ledger.append(
        "TOOL_EXECUTION",
        "agent:perseus-core",
        r#"{"tool":"compiler","exit_code":0}"#,
    )?;
    println!("Sequence #{}: Hash {}", ev2.sequence, &ev2.event_hash[0..16]);

    // Verify tamper-evident cryptographic continuity
    let report = ledger.verify_chain()?;
    assert!(report.valid, "Tamper detected in sequence chain");
    println!("Chain validated: {} events verified intact.", report.total_events);

    Ok(())
}
```

---

## Empirical Benchmarks & Telemetry

Evaluated on bare-metal Linux x86_64 (`rustc 1.85.0`, `opt-level = 3`, `lto = "fat"`):

| Subsystem | Evaluation Condition | Metric | Measurement | Verification Invariant |
| :--- | :--- | :--- | :--- | :--- |
| **Harpe** | 128k Token Context Limit | p50 Pruning Latency | **1.72 &micro;s** | Deterministic priority eviction |
| **Harpe** | High Contention Allocation | p95 Pruning Latency | **4.88 &micro;s** | Strictly bounded sub-5&micro;s curve |
| **Harpe** | Multi-Turn Dynamic Compression | Ceiling Reduction | **69.50%** | Context runaway prevention |
| **Kibisis** | Concurrent Read/Write Leases | Throughput | **1,745,000+ ops/s** | Zero race conditions under contention |
| **Kibisis** | 10-Node Swarm Network Partition | Split-Brain Count | **0 Errors** | Immediate convergence under packet loss |
| **Argus** | Event Append into Merkle DAG | Append Latency | **4.12 &micro;s** | Continuous SHA-256 hash chain |
| **Argus** | Single-Bit Tamper Inspection | Detection Latency | **46.00 &micro;s** | Immediate fail-closed state abort |

Reproduce locally across the workspace:

```bash
cargo run --release -p perseus-benchmarks
```

---

## Operating Principles

- **Local-First & Sovereign:** Operates completely offline without external cloud dependencies or remote telemetry.
- **Inspectable & Tamper-Evident:** SQLite-backed storage and SHA-256 hash chains guarantee mathematical auditability.
- **Clean-Room Verification:** Ground-up pure Rust implementation with zero legacy lineage, licensed under MIT.
- **Framework Agnostic:** Composable across standalone daemons, custom agent runners, or embedded enclaves.

---

## Repositories

- [harpe](https://github.com/Perseus-Computing-LLC/harpe): Deterministic context synthesis and token budgeting.
- [kibisis](https://github.com/Perseus-Computing-LLC/kibisis): Local-first encrypted cognitive memory vault with atomic leases.
- [argus](https://github.com/Perseus-Computing-LLC/argus): Tamper-evident append-only SHA-256 provenance and audit ledger.
- [perseus-benchmarks](https://github.com/Perseus-Computing-LLC/perseus-benchmarks): Microsecond performance and partition stress tests.

---

<p align="center">
  <a href="https://perseuscomputing.com">perseuscomputing.com</a> &middot;
  <a href="mailto:contact@perseuscomputing.com">contact@perseuscomputing.com</a> &middot;
  <a href="mailto:security@perseuscomputing.com">security@perseuscomputing.com</a>
</p>
<p align="center"><sub>Clean-room cognitive infrastructure for verifiable AI agents. &copy; 2026 Perseus Computing LLC. MIT License.</sub></p>
