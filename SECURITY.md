# Security Policy

Perseus Computing LLC takes the security and integrity of our open-source software seriously.

## Supported Versions

| Package | Version | Supported |
|---|---|---|
| `harpe` | `>= 0.1.0-alpha.2` | :white_check_mark: |
| `kibisis` | `>= 0.1.0-alpha.2` | :white_check_mark: |
| `argus-ledger` | `>= 0.1.0-alpha.2` | :white_check_mark: |

## Reporting a Vulnerability

Please do not report security vulnerabilities through public GitHub issues or discussions.

Instead, please send an encrypted or plaintext email to:
**`contact@perseuscomputing.com`**
with the subject line: `[SECURITY VULNERABILITY] <Component Name>`.

### Service Level Agreement (SLA)

* **Acknowledgement**: Within **48 hours** of initial receipt.
* **Triage & Assessment**: Within **5 business days**, detailing validity and planned mitigation.
* **Remediation & Patch**: Target release within **14 calendar days** for critical severity vulnerabilities.
* **Coordinated Disclosure**: We request a 90-day embargo period from the date of initial report to allow users to upgrade before public disclosure.

## Threat Model & Scope

Telarch libraries are designed for **local and disconnected use** on developer, server, or edge hardware:
- **No Network Surface**: Telarch libraries do not open network sockets, bind HTTP endpoints, or transmit telemetry.
- **Local Isolation**: Memory bounds and cryptographic verifications assume the local operating system process boundary is intact.
- **Argus Tamper Evidence**: Argus detects and locates modifications to past events in an append-only linear hash chain. To protect against complete history rewrites, head hashes must be anchored in signed checkpoints or committed to external immutable storage.
- **Kibisis Encryption**: Encryption at rest is implemented in the Python package via SQLCipher and AES-256-GCM. The Rust crate holds memory in RAM.
