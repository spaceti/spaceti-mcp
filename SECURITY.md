# Security Policy

## Reporting a vulnerability

Report suspected vulnerabilities in the Spaceti Intelligence MCP server, or in this documentation, to **<support@spaceti.com>** with `SECURITY` in the subject line.

Please include enough detail to reproduce: the endpoint or configuration involved, what you observed, and what you expected. If you have a proof of concept, include it.

Do **not** open a public GitHub issue for a suspected vulnerability, and please do not include real customer data, access tokens, or authorization codes in your report.

We will acknowledge your report and keep you updated on our assessment. Please give us a reasonable opportunity to remediate before any public disclosure.

## Scope

In scope:

- `https://intelligence.spaceti.ai` — the MCP server and its `/.well-known` discovery documents
- `https://passport.spaceti.com` — the OAuth 2.1 authorization server used to sign in
- The documentation and client configuration in this repository

Out of scope:

- Denial of service and volumetric testing
- Findings that require a compromised end-user device or browser
- Reports generated solely by automated scanners with no demonstrated impact

## Security posture

The Spaceti Intelligence MCP server is read-only, authenticated with OAuth 2.1 (authorization code + PKCE, dynamic client registration), and tenant-scoped server-side. It exposes aggregate workplace analytics only — no per-person data.

Our certifications, subprocessors and current security documentation are published at **<https://trust.spaceti.com/>**.
