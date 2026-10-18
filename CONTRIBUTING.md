# Contributing to Telarch

Thank you for your interest in contributing to the Telarch Suite, built by Perseus Computing LLC.

## Core Rules

1. **Every Claim Must Match Code & Evidence**: Quantitative performance, security, and stability claims must cite committed benchmark reports in `telarch-benchmarks` and adhere to `evidence/claims.json`. Unverified performance adjectives are not accepted.
2. **Deterministic & Bounded Execution**: Code must be deterministic. Timeouts, token budgets, and memory bounds must be enforced fail-closed.
3. **No Network Code**: The core libraries operate locally and disconnected. No network dependencies, socket bindings, or telemetry may be introduced.
4. **MIT License**: All contributions are made under the terms of the MIT License.

## Development Workflow

### Rust Crates (`harpe`, `kibisis`, `argus-ledger`)

1. Format code: `cargo fmt --all`
2. Run lints: `cargo clippy --all-targets -- -D warnings`
3. Run tests: `cargo test`
4. Verify supply chain: `cargo audit`

### Python Packages

1. Format and lint with standard tools.
2. Run test suites: `pytest tests`

## Pull Request Guidelines

- Branch names should be descriptive: `feat/<feature>`, `fix/<issue>`, `chore/<task>`.
- Commits must use a clean noreply email address.
- PRs must pass all CI status checks (formatting, lints, stable/MSRV tests, cargo-audit, and security scans).
