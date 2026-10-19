# Security Policy

This policy covers the Telarch libraries from Perseus Computing LLC: Harpe, Kibisis and Argus.

## Supported Versions

| Package | Version | Supported |
|---|---|---|
| `harpe` | `>= 0.1.0-alpha.2` | :white_check_mark: |
| `kibisis` | `>= 0.1.0-alpha.2` | :white_check_mark: |
| `argus-ledger` | `>= 0.1.0-alpha.2` | :white_check_mark: |

## Reporting a Vulnerability

Please do not report security vulnerabilities through public GitHub issues or discussions.

Instead, please email:
**`contact@perseuscomputing.com`**
with the subject line: `[SECURITY VULNERABILITY] <Component Name>`. No PGP key is published.

### Service Level Agreement (SLA)

* **Acknowledgement**: Within **48 hours** of initial receipt.
* **Triage & Assessment**: Within **5 business days**, detailing validity and planned mitigation.
* **Remediation & Patch**: Target release within **14 calendar days** for verified high and critical vulnerabilities.
* **Coordinated Disclosure**: We request a 90-day embargo period from the date of initial report to allow users to upgrade before public disclosure.

## Threat Model & Scope

Telarch libraries are designed for **local and disconnected use** on developer, server, or edge hardware:
- **No Network Surface**: Telarch libraries do not open network sockets, bind HTTP endpoints, or transmit telemetry.
- **Local Isolation**: Memory bounds and cryptographic verifications assume the local operating system process boundary is intact.
- **Argus Tamper Evidence**: Argus detects and locates changes to past events in an append-only linear hash chain. On its own, a truncated chain, or one recomputed by someone who can write the log, still verifies. Sign events with a key, and keep the latest sequence number and hash somewhere the writer cannot change, to detect those too.
- **Kibisis Encryption**: The Python package seals each entry with AES-256-GCM. With `sqlcipher3` installed, SQLCipher also encrypts the whole database file; without it, categories, keys and metadata are stored in plain text. The Rust crate holds memory in RAM and does not encrypt it.
