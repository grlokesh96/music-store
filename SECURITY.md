# Security Policy

## Supported Versions

This project is a demo microservices application. Security updates are applied to the latest version.

| Version | Supported          |
| ------- | ------------------ |
| latest  | :white_check_mark: |
| < 1.0   | :x:                |

## Reporting a Vulnerability

We take security seriously.

**Please do not report security vulnerabilities through public GitHub issues.**

Instead, report vulnerabilities privately:

1. **GitHub Private Vulnerability Reporting** (preferred)
   - Repository → **Security** tab → **Report a vulnerability**

2. **Email**
   - Send details to: security@yourdomain.com

### What to Include

- Description of the vulnerability
- Steps to reproduce
- Potential impact
- Suggested mitigations (optional)

### Response Timeline

- Acknowledgment: Within 48 hours
- Initial Assessment: Within 7 days

## Security Features in This Project

- Services run with non-root containers where possible
- Kubernetes NetworkPolicies can be enabled
- Secrets are managed via Kubernetes secrets / Sealed Secrets
- CI pipeline includes basic container scanning

Thank you for helping keep this project secure.
