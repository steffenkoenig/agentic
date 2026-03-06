# Security Policy

## Supported Versions

This repository hosts an agentic automation framework. The following versions are actively maintained and receive security updates:

| Version | Supported |
|---------|-----------|
| `main` (latest) | ✅ Yes |
| Older commits | ❌ No — please update to `main` |

## Reporting a Vulnerability

**Please do not report security vulnerabilities through public GitHub Issues.**

If you discover a security vulnerability in this repository, please disclose it responsibly by using **[GitHub's private vulnerability reporting](https://github.com/steffenkoenig/agentic/security/advisories/new)** feature:

1. Navigate to the **Security** tab of this repository.
2. Click **"Report a vulnerability"**.
3. Fill in the details of the vulnerability (description, steps to reproduce, impact, and any suggested fix).
4. Submit the report — it will be sent privately to the repository maintainers.

Alternatively, you may contact the maintainer directly via the contact information listed on their [GitHub profile](https://github.com/steffenkoenig).

## What to Expect

- **Acknowledgement:** You will receive an acknowledgement within **5 business days**.
- **Assessment:** The maintainer will assess the report and may ask for additional details.
- **Resolution:** A fix will be developed and released as quickly as possible, depending on severity.
- **Disclosure:** Once a fix is available, the vulnerability will be publicly disclosed via a GitHub Security Advisory. Credit will be given to the reporter unless they prefer to remain anonymous.

## Scope

This repository primarily contains GitHub Actions workflows and AI-agent configuration. Security-relevant findings may include, but are not limited to:

- Workflow injection vulnerabilities (e.g., untrusted input used in `run:` steps)
- Privilege escalation through overly broad workflow permissions
- Secrets or credentials accidentally committed to the repository
- Supply-chain risks from pinned Actions or external scripts

## Out of Scope

- Vulnerabilities in third-party Actions or services used by this repository (report those to the respective upstream projects)
- Issues that require physical access to infrastructure
- Social engineering attacks

## Security Best Practices for Contributors

- Follow the principle of least privilege when adding or modifying workflow permissions.
- Pin third-party Actions to a specific commit SHA rather than a mutable tag.
- Never commit secrets, tokens, or credentials — use GitHub Actions secrets instead.
- Review `gh aw compile` output for any unintended permission escalations before merging.
