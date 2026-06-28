
# Security Policy

## Supported Versions

This project does not yet have tagged releases. Security fixes are applied
to the `main` branch only.

## Reporting a Vulnerability

If you discover a security vulnerability in this project, please report it
privately rather than opening a public issue.

- **Preferred**: use GitHub's [private vulnerability reporting](../../security/advisories/new)
  feature (Security tab → "Report a vulnerability").
- **Alternative**: open a GitHub issue with minimal detail and ask for a
  private channel to share specifics.

Please include:
- A description of the vulnerability and its potential impact
- Steps to reproduce, if applicable
- Any suggested fix, if you have one

I'll aim to acknowledge reports within a few days and follow up once the
issue is understood or resolved.

## Scope

This is a small physics simulation library and an accompanying static
web demo. Realistic concerns include dependency vulnerabilities (NumPy,
Matplotlib) or unsafe deserialization if user-supplied data is ever
loaded into the simulation. There is no authentication, user data
storage, or backend service involved.
