# Perseus Computing LLC

We build **Telarch**: open-source libraries that control what AI agents read and remember, and prove what they did. They run on your own hardware.

| Library | What it does | Source |
|---|---|---|
| **Harpe** | Keeps each model call under a token ceiling. Highest priority first, while it fits. Pinned items always stay. | [harpe](https://github.com/Perseus-Computing-LLC/harpe) · [docs](https://docs.rs/harpe) |
| **Kibisis** | Local agent memory. One writer per resource, with numbered leases that only go up. The Python package encrypts memory content with AES-256-GCM. | [kibisis](https://github.com/Perseus-Computing-LLC/kibisis) · [docs](https://docs.rs/kibisis) |
| **Argus** | A SHA-256 hash chain of agent actions. Editing any past entry breaks every later one and shows where. | [argus](https://github.com/Perseus-Computing-LLC/argus) · [docs](https://docs.rs/argus-ledger) |

**Site:** [telarch.ai](https://telarch.ai) · **Evidence:** [telarch.ai/evidence](https://telarch.ai/evidence) · **Benchmarks:** [telarch-benchmarks](https://github.com/Perseus-Computing-LLC/telarch-benchmarks)

## What we measured

Medians of three runs on one shared Linux x86_64 machine (rustc 1.97.1), synthetic data. Method, spread and reproduce commands are on the [evidence page](https://telarch.ai/evidence).

| | Result | Scope |
|---|---|---|
| Harpe | 78.3% fewer tokens, 0 turns over the ceiling | 100,000 simulated turns, 1,200-token ceiling |
| Harpe | 2.3 µs median (10 items), 38 µs median (200 items) | In-memory, one call |
| Kibisis | 0 moments with two holders, in 64,000 lease attempts | 16 and 32 competing threads, one process |
| Argus | 652,000 appends per second, 816,000 verifications per second | 1,000,000 events, in memory |
| Argus | An edit at event 500,000 found in 626 ms | Walk from event 1 |

## What Telarch is not, today

- **Alpha** (0.1.0-alpha.2). Interfaces will change.
- **Not independently assessed.** No security or compliance assessment of the libraries has been completed. They are designed for local and disconnected use.
- **Encryption is in the Python package only,** and covers memory content. Category, key and metadata are stored in plain text.
- **Leases coordinate threads inside one process.** They are not a distributed lock service.
- **Argus detects edits.** A fully rewritten chain still verifies, so keep the newest hash somewhere an attacker cannot write.

We keep a [list of earlier claims we retired](https://telarch.ai/evidence#retired) after checking them against the code.

## Company

Perseus Computing LLC · Austin, Texas · CAGE 22JC5 · UEI PJS2LW7HAK35 · NAICS 541715

* **Defense Logistics Agency JCP:** DD2345 Certified Active through August 2031 (Cert #`0092893`, self-assessed/attested)
* **NIST SP 800-171 SPRS Score:** **110/110** (self-assessed August 2026, UID `SB00157695`)
* **CMMC Level 2 Compliance:** **110/110** (self-classified August 2026, UID `S200069604`)

Software is released under the **MIT License**.

**Contact:** [contact@perseuscomputing.com](mailto:contact@perseuscomputing.com) (integration, teaming, security reports)
