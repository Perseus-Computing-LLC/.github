# Perseus Computing LLC

We build **Telarch**: open-source libraries that control what AI agents read and remember, and prove what they did. They run on your own hardware.

| Library | What it does | Source |
|---|---|---|
| **Harpe** | Keeps each model call under a token ceiling. Highest priority first, while it fits. Pinned items always stay. | [harpe](https://github.com/Perseus-Computing-LLC/harpe) · [docs](https://docs.rs/harpe) |
| **Kibisis** | Local agent memory. One writer per resource, with numbered leases that only go up. The Python package encrypts the vault file on disk. | [kibisis](https://github.com/Perseus-Computing-LLC/kibisis) · [docs](https://docs.rs/kibisis) |
| **Argus** | A linear SHA-256 hash chain of agent actions. Editing any past entry breaks every later one and shows where. | [argus](https://github.com/Perseus-Computing-LLC/argus) · [docs](https://docs.rs/argus-ledger) |

**Site:** [telarch.ai](https://telarch.ai) · **Evidence:** [telarch.ai/evidence](https://telarch.ai/evidence) · **Benchmarks:** [telarch-benchmarks](https://github.com/Perseus-Computing-LLC/telarch-benchmarks)

## What we measured

From the reports committed in [telarch-benchmarks](https://github.com/Perseus-Computing-LLC/telarch-benchmarks) (commit `ca3dff7`), Linux x86_64, rustc 1.97.1. Three fresh re-runs, the methodology, and reproduction commands are published on the [evidence page](https://telarch.ai/evidence).

| Library | Result | Scope |
|---|---|---|
| **Harpe** | 78.3% fewer tokens, 0 turns over the ceiling | 100,000 simulated turns, 1,200-token ceiling |
| **Harpe** | 1.88 µs median (10 items), 41.16 µs median (200 items) | In-memory, single process |
| **Kibisis** | 0 moments with two holders, across 64,000 lease attempts | 16 and 32 competing threads, one process |
| **Argus** | 809,697 appends per second, 2,433,053 verifications per second | 1,000,000 events, in memory |
| **Argus** | An edit at event 500,000 found in 185.35 ms | Linear walk from event 1 |

## What Telarch is not, today

- **Alpha** (0.1.0-alpha.2 / 0.1.0-alpha.3). Interfaces will change.
- **Not independently assessed.** No security or compliance assessment of the libraries has been completed. They are designed for local and disconnected use.
- **Encryption at rest is in the Python Kibisis package only.** It encrypts the database file using SQLCipher and AES-256-GCM. The Rust crate keeps memory in RAM and does not encrypt.
- **Leases coordinate threads in one process or across local processes via SQLite.** They are not a distributed consensus service.
- **Argus detects edits.** A fully rewritten chain still verifies internally, so head hashes must be anchored in signed checkpoints. Anchored verification and Ed25519 signing are on `main`.

## Company

Perseus Computing LLC · Austin, Texas · CAGE 22JC5 · UEI PJS2LW7HAK35 · NAICS 541715

* **Defense Logistics Agency JCP:** DD2345 certified active through August 2031 (self-assessed/attested)
* **NIST SP 800-171 SPRS Score:** 110/110 (self-assessed August 2026, UID `SB00157695`)

Software is released under the **MIT License**.

**Contact:** [contact@perseuscomputing.com](mailto:contact@perseuscomputing.com) (integration, teaming, security reports)
