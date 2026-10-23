# Perseus Computing

**Infrastructure for AI agents you can inspect.**

We build **[Telarch](https://telarch.ai)**: three small open-source libraries that control what an AI agent reads and remembers, and prove what it did. They run on your own hardware, contain no network code, and every benchmark figure we publish comes with its raw data.

| Library | What it does | Learn more |
|---|---|---|
| **[Harpe](https://github.com/Perseus-Computing-LLC/harpe)** | Chooses what goes into each model call under a token budget: pinned items first, then by priority or recency while they fit, with a report of what was kept and dropped. | [telarch.ai/harpe](https://telarch.ai/harpe) |
| **[Kibisis](https://github.com/Perseus-Computing-LLC/kibisis)** | Local, encrypted memory for agents: recall by scope, trust state and date, tombstones for values that must not come back, and one writer at a time with fencing tokens. | [telarch.ai/kibisis](https://telarch.ai/kibisis) |
| **[Argus](https://github.com/Perseus-Computing-LLC/argus)** | A record of what the agent did that shows if anyone changed it: a linear SHA-256 hash chain that names the first edited event. | [telarch.ai/argus](https://telarch.ai/argus) |

## Measured, with raw data

Medians of 5 runs of [telarch-benchmarks](https://github.com/Perseus-Computing-LLC/telarch-benchmarks) at release tag v0.1.0-alpha.7, with synthetic data on one EC2 m8azn.metal-12xl bare-metal server. Every run, its range and the command that reproduces it are on [telarch.ai/evidence](https://telarch.ai/evidence).

- **78.3%** fewer tokens sent to the model over 100,000 simulated agent turns, with 0 turns over the 1,200-token budget (Harpe).
- **0** times two holders had the same lease, in 64,000 contended attempts from 16 and 32 threads (Kibisis).
- **154.5 ms** to find one edited event among 1,000,000 (Argus).

## Plainly

- **Alpha** (Harpe 0.1.0-alpha.6 · Kibisis 0.1.0-alpha.9 · Argus 0.1.0-alpha.5). Interfaces will change.
- **Not independently assessed.** No security review or certification of the libraries has been completed.
- **Encryption at rest is in the Python Kibisis package.** The Rust crate keeps memory in RAM and does not encrypt it.
- **Leases coordinate one machine.** They are not a network lock service.
- **Argus detects edits; it does not prevent them.** Keep the newest hash where the writer cannot change it, and verify against it.

## Company

Perseus Computing LLC · Austin, Texas · CAGE 22JC5 · UEI PJS2LW7HAK35 · NAICS 541715

* **Joint Certification Program:** DD Form 2345 active through August 2031 (self-attested)
* **NIST SP 800-171:** SPRS score 110 of 110, self-assessed August 2026. Not a third-party assessment.

Software is released under the **MIT License**. **Contact:** [contact@perseuscomputing.com](mailto:contact@perseuscomputing.com) for integration, teaming and security reports.
