<p align="center">
  <img src="./assets/perseus-mark.svg" alt="Perseus Computing mark" width="96">
</p>

<h1 align="center">Perseus Computing</h1>

<p align="center">
  <strong>Context for the present. Memory for the past. Cryptographic proof for what happened.</strong><br>
  Open-source, local-first cognitive infrastructure for autonomous AI agents.
</p>

<p align="center">
  <a href="https://perseuscomputing.com">Website</a> ·
  <a href="https://github.com/Perseus-Computing-LLC">GitHub</a> ·
  <a href="https://crates.io/crates/harpe">Harpe</a> ·
  <a href="https://crates.io/crates/kibisis">Kibisis</a> ·
  <a href="https://crates.io/crates/argus-ledger">Argus</a>
</p>

---

## Clean-Room Architecture

Autonomous AI agents require verifiable state, durable memory, and tamper-evident auditability. Perseus Computing provides local-first primitives engineered in Rust to form the foundation of verifiable agent cognition.

All primitives are designed clean-room, released under the permissive **MIT License**, and published as independent, composable crates on [crates.io](https://crates.io).

| Layer | Primitive | Purpose | Distribution |
| :--- | :--- | :--- | :--- |
| **Context** | **Harpe** | Deterministic context budgeting, priority scheduling, and dynamic synthesis. | [](https://crates.io/crates/harpe) |
| **Memory** | **Kibisis** | Encrypted cognitive memory vault with FTS5 indexing, versioning, and atomic concurrency leases. | [](https://crates.io/crates/kibisis) |
| **Evidence** | **Argus** | Cryptographic audit ledger with tamper-evident SHA-256 hash chaining and Merkle proofs. | [](https://crates.io/crates/argus-ledger) |



---

## Getting Started

Add the Rust primitives directly to your workspace:



### 1. Harpe — Context Budgeting & Synthesis
Manage strict context windows deterministically without loss of high-priority directives:


### 2. Kibisis — Local Encrypted Memory Vault
Maintain persistent, searchable cognitive memory with atomic multi-agent leases:


### 3. Argus — Cryptographic Provenance Ledger
Record all agent actions, tool calls, and model decisions into an immutable SHA-256 audit chain:


---

## Operating Principles

- **Local-First & Sovereign:** Operates completely offline without external cloud dependencies or telemetry.
- **Inspectable & Tamper-Evident:** SQLite-backed storage and SHA-256 hash chains guarantee auditability.
- **Clean-Room Verification:** Ground-up Rust implementation with zero legacy lineage, licensed under MIT.
- **Framework Agnostic:** Composable across standalone daemons, custom agent runners, or embedded systems.

---

<p align="center">
  <a href="https://perseuscomputing.com">perseuscomputing.com</a> ·
  <a href="https://github.com/Perseus-Computing-LLC">GitHub Organization</a>
</p>
<p align="center"><sub>Clean-room cognitive infrastructure for verifiable AI agents. © 2026 Perseus Computing LLC. MIT License.</sub></p>
