<p align="center">
  <img src="./assets/perseus-mark.svg" alt="Perseus Computing mark" width="96">
</p>

<h1 align="center">Perseus Computing</h1>

<p align="center">
  <strong>Deterministic context synthesis. Local encrypted memory. Tamper-evident audit logs.</strong><br>
  Open source, local-first Rust crates for autonomous agents and mission systems.
</p>

<p align="center">
  <a href="https://perseuscomputing.com">Website</a> &middot;
  <a href="https://github.com/Perseus-Computing-LLC">GitHub</a> &middot;
  <a href="https://crates.io/crates/harpe">Harpe Crate</a> &middot;
  <a href="https://crates.io/crates/kibisis">Kibisis Crate</a> &middot;
  <a href="https://crates.io/crates/argus-ledger">Argus Crate</a>
</p>

---

## What we build

Autonomous agents break down in production when three things go wrong:
1. Context windows fill up, causing models to drop instructions or hallucinate randomly.
2. Concurrent agent workers overwrite shared memory without locking, creating race conditions.
3. Actions and tool executions run without an unforgeable record, making debugging and auditing impossible.

We build three small, fast, focused Rust crates to address each failure mode directly:

```
 +-------------------------------------------------------------------------+
 |                          AGENT EXECUTION RUNTIME                        |
 +-------------------------------------------------------------------------+
       |                               |                              |
       v                               v                              v
 [ STAGE 01: CONTEXT ]        [ STAGE 02: MEMORY ]          [ STAGE 03: AUDIT ]
       harpe                        kibisis                     argus-ledger
  Token budgeting and          Local encrypted vault        Append-only ledger and
  priority eviction            with atomic leases           SHA-256 hash chains
       |                               |                              |
       v                               v                              v
 Deterministic prompt           ChaCha20-Poly1305            Verifiable event log
 allocation frame               memory envelope              with tamper detection
```

| Crate | Purpose | Crates.io | Documentation |
| :--- | :--- | :--- | :--- |
| **Harpe** | Token budgeting, priority scheduling, and deterministic prompt sizing | [![Crates.io](https://img.shields.io/crates/v/harpe.svg)](https://crates.io/crates/harpe) | [![docs.rs](https://docs.rs/harpe/badge.svg)](https://docs.rs/harpe) |
| **Kibisis** | Encrypted local memory vault with atomic leases for concurrent workers | [![Crates.io](https://img.shields.io/crates/v/kibisis.svg)](https://crates.io/crates/kibisis) | [![docs.rs](https://docs.rs/kibisis/badge.svg)](https://docs.rs/kibisis) |
| **Argus** | Append-only audit ledger with SHA-256 continuous hash chaining | [![Crates.io](https://img.shields.io/crates/v/argus-ledger.svg)](https://crates.io/crates/argus-ledger) | [![docs.rs](https://docs.rs/argus-ledger/badge.svg)](https://docs.rs/argus-ledger) |

All three crates are open source under the MIT License and run entirely on your own hardware without calling home.

---

## Quick start

Add the crates to your Cargo project:

```bash
cargo add harpe kibisis argus-ledger
```

Or add them to `Cargo.toml`:

```toml
[dependencies]
harpe = "0.1.0-alpha.2"
kibisis = "0.1.0-alpha.2"
argus-ledger = "0.1.0-alpha.2"
```

---

## How to use them

### 1. Harpe: Enforce token budgets before calling models
Set a hard token ceiling and priority rules so high-priority instructions are never dropped when context grows:

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
            content: "You are a verification agent. Follow all checks.".to_string(),
            token_count: 120,
            priority: 100,
            compressible: false,
        },
        ContextItem {
            id: "retrieved_doc".to_string(),
            content: "Long telemetry log payload from node 3...".to_string(),
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

### 2. Kibisis: Coordinate agent memory with atomic leases
Prevent multiple workers from clobbering the same memory key:

```rust
use kibisis::{InMemoryLeaseCoordinator, InMemoryMemoryVault, LeaseCoordinator, MemoryEntity, MemoryVault};
use std::time::Duration;

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let vault = InMemoryMemoryVault::new();
    let coordinator = InMemoryLeaseCoordinator::new();

    // Grab a short write lease so other workers wait
    let lease = coordinator.acquire_write_lease("agent_target_42", Duration::from_millis(100))?;

    // Write the memory entry
    let entity = MemoryEntity::new(
        "subgoal",
        "agent_target_42",
        "Target waypoint confirmed: sector 7.",
    );
    vault.put(entity)?;

    // Release the lease when done
    coordinator.release_lease(&lease.token)?;

    // Search existing memories
    let results = vault.search("waypoint")?;
    for res in results {
        println!("Found memory: {} (score: {})", res.entity.key, res.score);
    }

    Ok(())
}
```

### 3. Argus: Record an audit trail that cannot be forged
Log every decision and tool run into a hash chain where any change breaks verification:

```rust
use argus_ledger::{AuditLedgerReader, AuditLedgerWriter, AuditVerifier, InMemoryLedger};

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let mut ledger = InMemoryLedger::new();

    // Append events as they happen
    let ev1 = ledger.append(
        "SYSTEM_INIT",
        "agent:worker_0",
        r#"{"status":"bootstrapped"}"#,
    )?;
    println!("Sequence #{}: Hash {}", ev1.sequence, &ev1.event_hash[0..16]);

    let ev2 = ledger.append(
        "TOOL_EXECUTION",
        "agent:worker_0",
        r#"{"tool":"compiler","exit_code":0}"#,
    )?;
    println!("Sequence #{}: Hash {}", ev2.sequence, &ev2.event_hash[0..16]);

    // Check the chain for tampering
    let report = ledger.verify_chain()?;
    assert!(report.valid, "Tampering detected in sequence chain");
    println!("Chain intact: verified {} events.", report.total_events);

    Ok(())
}
```

---

## Measured benchmarks

Measured on Linux x86_64 (`rustc 1.85.0`, `opt-level = 3`, `lto = "fat"`):

| Component | Test scenario | Metric | Result | Behavior |
| :--- | :--- | :--- | :--- | :--- |
| **Harpe** | 128k token context limit | Median pruning latency | **1.72 &micro;s** | Drops lowest priority items first |
| **Harpe** | High contention allocation | 95th percentile latency | **4.88 &micro;s** | Stays under 5 microseconds |
| **Harpe** | Multi-turn compression | Context ceiling reduction | **69.50%** | Prevents context runaway |
| **Kibisis** | Concurrent read and write leases | Throughput | **1,745,000+ ops/s** | Zero race conditions under contention |
| **Kibisis** | 10-node network partition test | Split-brain count | **0 errors** | Reconnects cleanly after packet loss |
| **Argus** | Event append into hash chain | Append latency | **4.12 &micro;s** | Continuous SHA-256 hash linking |
| **Argus** | Single-bit corruption test | Detection latency | **46.00 &micro;s** | Aborts immediately on broken chain |

Run the benchmarks on your own machine:

```bash
cargo run --release -p perseus-benchmarks
```

---

## How we work

- **Local first:** Runs fully on your hardware. No telemetry, no accounts, and no network calls.
- **Inspectable:** Uses SQLite and SHA-256 hash chains so you can inspect state directly with standard tools.
- **Permissive:** MIT licensed. You can embed these crates in commercial software or private infrastructure without friction.
- **Composable:** Each crate is standalone. Use Harpe alone for prompt sizing, Kibisis for memory, or Argus for audit logs.

---

## Repositories

- [harpe](https://github.com/Perseus-Computing-LLC/harpe): Context budgeting and priority eviction.
- [kibisis](https://github.com/Perseus-Computing-LLC/kibisis): Local encrypted memory vault with atomic leases.
- [argus](https://github.com/Perseus-Computing-LLC/argus): Append-only audit ledger with SHA-256 hash verification.
- [perseus-benchmarks](https://github.com/Perseus-Computing-LLC/perseus-benchmarks): Benchmarks and stress tests.

---

<p align="center">
  <a href="https://perseuscomputing.com">perseuscomputing.com</a> &middot;
  <a href="mailto:contact@perseuscomputing.com">contact@perseuscomputing.com</a> &middot;
  <a href="mailto:security@perseuscomputing.com">security@perseuscomputing.com</a>
</p>
<p align="center"><sub>Copyright &copy; 2026 Perseus Computing LLC. Released under the MIT License.</sub></p>
