# Perseus Computing

**Infrastructure for AI agents you can inspect.**

We build **[Telarch](https://telarch.ai)**: three small open-source libraries that control what an AI agent reads and remembers, and prove what it did. They run on your own hardware, contain no network code, and every benchmark figure we publish comes with its raw data.

| Library | What it does | Learn more |
|---|---|---|
| **[Harpe](https://github.com/Perseus-Computing-LLC/harpe)** | Chooses what goes into each model call under a token budget: pinned items first, then by priority or recency while they fit, with a report of what was kept and dropped. | [telarch.ai/harpe](https://telarch.ai/harpe) |
| **[Kibisis](https://github.com/Perseus-Computing-LLC/kibisis)** | Local, encrypted memory for agents: recall by scope, trust state and date, tombstones for values that must not come back, and one writer at a time with fencing tokens. | [telarch.ai/kibisis](https://telarch.ai/kibisis) |
| **[Argus](https://github.com/Perseus-Computing-LLC/argus)** | A record of what the agent did that shows if anyone changed it: a linear SHA-256 hash chain that names the first edited event. | [telarch.ai/argus](https://telarch.ai/argus) |

## Measured, with raw data

Medians of 5 runs of [telarch-benchmarks](https://github.com/Perseus-Computing-LLC/telarch-benchmarks) at release tag v0.1.0-alpha.8, with synthetic data on one EC2 m8azn.metal-12xl bare-metal server. Every run, its range and the command that reproduces it are on [telarch.ai/evidence](https://telarch.ai/evidence).

- **78.3%** fewer tokens sent to the model over 100,000 simulated agent turns, with 0 turns over the 1,200-token budget (Harpe).
- **0** times two holders had the same lease, in 64,000 contended attempts from 16 and 32 threads (Kibisis).
- **155.4 ms** to find one edited event among 1,000,000 (Argus).

## Standard Long-Term Memory Evaluations

Evaluated across 6,111 questions on four standard public benchmarks with reader `gpt-4o-mini` and official upstream judges. Full-context baselines use exact `o200k` token counting:

| Benchmark | Questions | Encrypted Vault (as shipped) | Plaintext Index | Full Context Baseline (`o200k`) | Delta (Shipped vs Full) |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **LongMemEval-S** | 500 | **72.4%** | **72.4%** | 52.0% | **+20.4 pts** `[+15.6, +25.0]` |
| **LoCoMo (J)** | 1,540 | **70.8%** *(74.2% fit / 75.3% dates)* | 70.8% | **75.5%** | −4.7 pts *(−1.3 fit tie)* `[-3.0, +0.0]` |
| **BEAM 100K** | 400 | **33.9%** | 34.4% | 31.0% | **+2.9 pts** `[-1.1, +6.7]` |
| **BEAM 500K** | 700 | **36.5%** | 36.3% | 29.5% | **+7.1 pts** `[+4.0, +10.3]` |
| **BEAM 1M** | 700 | **33.8%** | **33.8%** | 25.2% | **+8.6 pts** `[+6.0, +11.3]` |
| **BEAM 10M** | 200 | **26.7%** | **26.7%** | 15.9% | **+10.9 pts** `[+4.9, +16.6]` |
| **MemoryAgentBench** | 2,071 | **45.6%** | **45.6%** | 40.7% | **+4.9 pts** `[+2.4, +7.3]` |
| **Total / Macro Avg** | **6,111** | **45.7%** *(46.1% fit)* | **45.7%** | 38.5% | **+7.2 pts overall** |

### Competitive Memory Architecture Retrieval (LongMemEval-S, N=30)

| System | Storage & Cipher | Session Recall | Round Recall | Security & Invariants |
| :--- | :--- | :---: | :---: | :--- |
| **Telarch (`telarch`)** | **SQLCipher AES-256 (0o600)** | **93.33%** | **86.39%** | Zero plaintext on disk; Argus ledger audit; monotonic fencing leases |
| **BM25 RAG (`bm25-rag`)** | Plaintext SQLite FTS5 | 93.33% | 85.28% | Plaintext SQLite; no leases; no ledger |
| **Mem0 (`mem0-local`)** | Unencrypted Flat Facts | 84.72% *(−8.61 pts)* | 74.61% *(−11.78 pts)* | Plaintext facts; loses multi-turn chronology; no ledger |

Every run, its range and the command that reproduces it are published on [telarch.ai/evidence](https://telarch.ai/evidence).

## Plainly

- **Alpha** (Harpe 0.1.0-alpha.7 · Kibisis 0.1.0-alpha.9 · Argus 0.1.0-alpha.5). Interfaces will change.
- **Not independently assessed.** No security review or certification of the libraries has been completed.
- **Encryption at rest is in the Python Kibisis package.** The Rust crate keeps memory in RAM and does not encrypt it.
- **Leases coordinate one machine.** They are not a network lock service.
- **Argus detects edits; it does not prevent them.** Keep the newest hash where the writer cannot change it, and verify against it.

## Company

Perseus Computing LLC · Austin, Texas · CAGE 22JC5 · UEI PJS2LW7HAK35 · NAICS 541715

* **Joint Certification Program:** DD Form 2345 active through August 2031 (self-attested)
* **NIST SP 800-171:** SPRS score 110 of 110, self-assessed August 2026. Not a third-party assessment.

Software is released under the **MIT License**. **Contact:** [contact@perseuscomputing.com](mailto:contact@perseuscomputing.com) for integration, teaming and security reports.
