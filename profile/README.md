# Perseus Computing LLC

We build **Telarch**: open-source libraries that control what AI agents read and remember, and prove what they did. They run on your own hardware.

| Library | What it does | Source |
|---|---|---|
| **Harpe** | Keeps each model call under a token ceiling. Highest priority first, while it fits. Pinned items always stay. | [harpe](https://github.com/Perseus-Computing-LLC/harpe) · [docs](https://docs.rs/harpe) |
| **Kibisis** | Local agent memory. One writer per resource, with numbered leases that only go up. The Python package (`pip install --pre kibisis`) keeps the vault file encrypted on disk. | [kibisis](https://github.com/Perseus-Computing-LLC/kibisis) · [docs](https://docs.rs/kibisis) |
| **Argus** | A SHA-256 hash chain of agent actions. Editing any past entry breaks every later one and shows where. | [argus](https://github.com/Perseus-Computing-LLC/argus) · [docs](https://docs.rs/argus-ledger) |

**Site:** [telarch.ai](https://telarch.ai) · **Evidence:** [telarch.ai/evidence](https://telarch.ai/evidence) · **Benchmarks:** [telarch-benchmarks](https://github.com/Perseus-Computing-LLC/telarch-benchmarks)

## What we measured

From the reports committed in [telarch-benchmarks](https://github.com/Perseus-Computing-LLC/telarch-benchmarks) (commit `ca3dff7`), synthetic data, Linux x86_64, rustc 1.97.1. Three fresh re-runs, the method and the reproduce commands are on the [evidence page](https://telarch.ai/evidence).

| | Result | Scope |
|---|---|---|
| Harpe | 78.3% fewer tokens, 0 turns over the ceiling | 100,000 simulated turns, 1,200-token ceiling |
| Harpe | 1.88 µs median (10 items), 41.16 µs median (200 items) | In-memory, one call |
| Kibisis | 0 moments with two holders, in 64,000 lease attempts | 16 and 32 competing threads, one process |
| Argus | 809,697 appends per second, 2,433,053 verifications per second | 1,000,000 events, in memory |
| Argus | An edit at event 500,000 found in 185.35 ms | Walk from event 1 |

## What Telarch is not, today

- **Alpha** (0.1.0-alpha.2). Interfaces will change.
- **Not independently assessed.** No security or compliance assessment of the libraries has been completed. They are designed for local and disconnected use.
- **Encryption at rest is in the Python Kibisis package only.** It encrypts the whole database file (SQLCipher) and needs a passphrase. In our test, no category, key or content appeared in the raw file. The published Rust crate keeps memory in RAM and does not encrypt.
- **Leases coordinate threads inside one process.** They are not a distributed lock service.
- **Argus detects edits.** A fully rewritten chain still verifies, so keep the newest hash somewhere an attacker cannot write. Anchored verification and optional signing are on `main`, not yet released.

## Company

Perseus Computing LLC · Austin, Texas · CAGE 22JC5 · UEI PJS2LW7HAK35 · NAICS 541715

* **Defense Logistics Agency JCP:** DD2345 certified active through August 2031 (self-attested)
* **NIST SP 800-171:** self-assessment score of 110 submitted to SPRS in August 2026. Not a third-party assessment and not a CMMC certification.

Software is released under the **MIT License**.

**Contact:** [contact@perseuscomputing.com](mailto:contact@perseuscomputing.com) (integration, teaming, security reports)
