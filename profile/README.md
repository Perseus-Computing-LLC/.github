# Perseus Computing

**Infrastructure for AI agents you can inspect.**

We build **[Telarch](https://telarch.ai)**: three small open-source libraries that control what an AI agent reads and remembers, and prove what it did, plus an app that gives ChatGPT and Codex the same tools. The libraries run on your own hardware, contain no network code, and every benchmark figure we publish comes with its raw data.

## What's new

- **Telarch in ChatGPT and Codex** (telarch-openai-plugin v0.1.0, 4 Nov 2026). An MCP server at telarch.ai/mcp with seven tools: fit context into a token budget, decide which memories are current, check a writer's lease, and seal or verify an audit chain. In review with OpenAI for ChatGPT; the Codex plugin installs from GitHub today.
- **Autopoiesis is open source** (autopoiesis v0.1.0, 4 Nov 2026). Early research code for software that repairs its own recurring failures. Version 0.1.0 has the telemetry grouping, Git worktree sandboxes and test gate; the step that writes fixes is not built yet.
- **Two-pass context selection** (harpe 0.1.0-alpha.12, 3 Nov 2026). The model first sees a one-line description of every candidate, then asks for the bodies it needs; Harpe expands them inside the budget and refuses what does not fit. Each selection can write one receipt to an Argus ledger.
- **Portable knowledge bundles** (kibisis 0.1.0-alpha.13, 2 Nov 2026). okf-export writes allowlisted vault rows to an Open Knowledge Format bundle, under an export profile that denies everything else. okf-import brings a bundle back in as unverified candidates. 0.1.0-alpha.14 adds the rule that matches a query to the rows it names.

## Telarch

| Library | What it does | Learn more |
|---|---|---|
| **[Harpe](https://github.com/Perseus-Computing-LLC/harpe)** | Chooses what goes into each model call under a token budget: pinned items first, then by priority or recency while they fit, with a report of what was kept and dropped. Since 0.1.0-alpha.12, two-pass mode lets the model ask for only the bodies it needs. | [telarch.ai/harpe](https://telarch.ai/harpe) |
| **[Kibisis](https://github.com/Perseus-Computing-LLC/kibisis)** | Local, encrypted memory for agents: recall by scope, trust state and date, tombstones for values that must not come back, one writer at a time with fencing tokens and, since 0.1.0-alpha.13, portable knowledge bundles for allowlisted rows. | [telarch.ai/kibisis](https://telarch.ai/kibisis) |
| **[Argus](https://github.com/Perseus-Computing-LLC/argus)** | A record of what the agent did that shows if anyone changed it: a linear SHA-256 hash chain that names the first edited event. | [telarch.ai/argus](https://telarch.ai/argus) |

### Use it

- In Rust or Python, each library is a crate on crates.io and a package on PyPI. See the [quickstart](https://telarch.ai/#quickstart).
- In ChatGPT, the Telarch app ([source](https://github.com/Perseus-Computing-LLC/telarch-openai-plugin)) serves seven tools from `https://telarch.ai/mcp`. It is in review with OpenAI and not listed in ChatGPT yet; until then, add the server in ChatGPT developer mode.
- In Codex, add the repository as a plugin marketplace, then add a plugin:

  ```sh
  codex plugin marketplace add Perseus-Computing-LLC/telarch-openai-plugin
  codex plugin add telarch-local@perseus-telarch-plugins
  ```

  `telarch-local` runs on your machine against the Python libraries. Details: [telarch.ai/chatgpt](https://telarch.ai/chatgpt).

## Autopoiesis

**[Autopoiesis](https://github.com/Perseus-Computing-LLC/autopoiesis)** is early research on software that repairs its own recurring failures. Version 0.1.0 reads JSON Lines or Argus telemetry, groups recurring errors and gives each candidate a Git worktree where your tests run. Writing the fix, real challenge tests, the security checks and a command-line `cycle` that runs the engine are not built yet. The PyPI name belongs to another project, so install from GitHub:

```sh
pip install "git+https://github.com/Perseus-Computing-LLC/autopoiesis"
```

## Measured, with raw data

Medians of 5 runs of [telarch-benchmarks](https://github.com/Perseus-Computing-LLC/telarch-benchmarks) at release tag v0.1.0-alpha.11, with synthetic data on one EC2 m8azn.metal-12xl bare-metal server. Every run, its range and the command that reproduces it are on [telarch.ai/evidence](https://telarch.ai/evidence).

- **78.3%** fewer tokens sent to the model over 100,000 simulated agent turns, with 0 turns over the 1,200-token budget (Harpe).
- **0** times two holders had the same lease, in 64,000 contended attempts from 16 and 32 threads (Kibisis).
- **197.2 ms** to find one edited event among 1,000,000 (Argus).

### Standard long-term memory evaluations

6,111 questions on four public benchmarks, reader `gpt-4o-mini` with the official upstream judges. Full-context baselines use exact `o200k` token counting:

| Benchmark | Questions | Encrypted vault (as shipped) | Plaintext index | Full context (`o200k`) | Shipped vs. full |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **LongMemEval-S** | 500 | **72.4%** | **72.4%** | 52.0% | **+20.4 pts** `[+15.6, +25.0]` |
| **LoCoMo (J)** | 1,540 | **70.8%** *(74.2% fit / 75.3% dates)* | 70.8% | **75.5%** | −4.7 pts *(−1.3 fit tie)* `[-3.0, +0.0]` |
| **BEAM 100K** | 400 | **33.9%** | 34.4% | 31.0% | **+2.9 pts** `[-1.1, +6.7]` |
| **BEAM 500K** | 700 | **36.5%** | 36.3% | 29.5% | **+7.1 pts** `[+4.0, +10.3]` |
| **BEAM 1M** | 700 | **33.8%** | **33.8%** | 25.2% | **+8.6 pts** `[+6.0, +11.3]` |
| **BEAM 10M** | 200 | **26.7%** | **26.7%** | 15.9% | **+10.9 pts** `[+4.9, +16.6]` |
| **MemoryAgentBench** | 2,071 | **45.6%** | **45.6%** | 40.7% | **+4.9 pts** `[+2.4, +7.3]` |
| **Total / macro average** | **6,111** | **45.7%** *(46.1% fit)* | **45.7%** | 38.5% | **+7.2 pts overall** |

A smaller comparison against BM25 retrieval and our own emulation of a flat-fact store (LongMemEval-S, 30 questions) is on [telarch.ai/evidence](https://telarch.ai/evidence#comparisons), with its limits.

## Plainly

- The libraries are alpha (latest releases Harpe 0.1.0-alpha.12 · Kibisis 0.1.0-alpha.14 · Argus 0.1.0-alpha.8), and interfaces will change. The figures above were measured at Harpe 0.1.0-alpha.11 · Kibisis 0.1.0-alpha.12 · Argus 0.1.0-alpha.8.
- No independent security review or certification of the libraries has been completed.
- Encryption at rest is in the Python Kibisis package. The Rust crate keeps memory in RAM and does not encrypt it.
- Leases coordinate one machine. They are not a network lock service.
- Argus detects edits and cannot prevent them. Keep the newest hash where the writer cannot change it, and verify against it.
- The hosted app tools are JavaScript versions of the methods. They keep nothing between calls; for persistent memory, use `telarch-local` or the libraries.

## Company

Perseus Computing LLC · Austin, Texas · CAGE 22JC5 · UEI PJS2LW7HAK35 · NAICS 541715

* **Joint Certification Program:** DD Form 2345 active through August 2031 (self-attested)
* **NIST SP 800-171:** SPRS score 110 of 110, self-assessed August 2026. Not a third-party assessment.

Software is released under the **MIT License**. **Contact:** [contact@perseuscomputing.com](mailto:contact@perseuscomputing.com) for integration, teaming and security reports.
