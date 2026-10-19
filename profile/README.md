# Perseus Computing LLC

We build **Telarch**: open-source libraries that control what AI agents read and remember, and prove what they did. They run on your own hardware and are designed for local and disconnected use.

| Library | What it does | Source |
|---|---|---|
| **Harpe** | Chooses what goes into each model call under a token budget: pinned items first, then by priority or recency, while they fit. | [harpe](https://github.com/Perseus-Computing-LLC/harpe) |
| **Kibisis** | Local agent memory. One writer per resource, with fencing tokens that only go up. The Python package encrypts vault entries on disk (pip install from the release tag). | [kibisis](https://github.com/Perseus-Computing-LLC/kibisis) |
| **Argus** | Linear SHA-256 hash chaining of agent actions. Editing a past entry is reported with its position; head anchors catch rewritten history. | [argus](https://github.com/Perseus-Computing-LLC/argus) |

**Site:** [telarch.ai](https://telarch.ai) · **Evidence:** [telarch.ai/evidence](https://telarch.ai/evidence) · **Benchmarks:** [telarch-benchmarks](https://github.com/Perseus-Computing-LLC/telarch-benchmarks)

## What we measured

Medians of 5 runs at release tag v0.1.0-alpha.3 of [telarch-benchmarks](https://github.com/Perseus-Computing-LLC/telarch-benchmarks), synthetic data, 13th Gen Intel Core i5-13500, 20 logical CPUs, Linux 6.18, rustc 1.97.1, pinned to the performance-core threads (CPUs 0-11), shared server. Ranges, raw output of every run and the reproduce commands are on the [evidence page](https://telarch.ai/evidence).

| | Result | Scope |
|---|---|---|
| Harpe | 78.3% fewer tokens, 0 turns over the budget | 100,000 turns, 1,200-token budget per turn, compared with keeping every item |
| Harpe | 1.72 µs median call (10 items), 36.32 µs (200 items) | In memory, one call |
| Kibisis | 0 moments with two holders, in 64,000 lease attempts | 16-thread and 32-thread runs combined, one process |
| Argus | 864,364 appends per second, 2,567,911 events verified per second | 1,000,000 events, in memory |
| Argus | An edit at event 500,000 found in 183.6 ms | Walk from event 1 |

## What Telarch is not, today

- **Alpha** (0.1.0-alpha.3). Interfaces will change.
- **Not independently assessed.** No security review or certification of the libraries has been completed.
- **Encryption at rest is in the Python Kibisis package only** (AES-256-GCM per entry, and the whole file with SQLCipher when sqlcipher3 is installed). The Rust crate keeps memory in RAM and does not encrypt.
- **Leases:** the Rust coordinator works across threads in one process; the Python coordinator across processes sharing one database file. Neither is a network lock service.
- **Argus detects edits; it does not prevent them.** Keep the newest hash where the writer cannot change it, and verify against it.

## Company

Perseus Computing LLC · Austin, Texas · CAGE 22JC5 · UEI PJS2LW7HAK35 · NAICS 541715

* **Joint Certification Program:** DD Form 2345 active through August 2031 (self-attested)
* **NIST SP 800-171:** SPRS score 110 of 110, self-assessed August 2026. Not a third-party assessment.

Software is released under the **MIT License**.

**Contact:** [contact@perseuscomputing.com](mailto:contact@perseuscomputing.com) (integration, teaming, security reports)
