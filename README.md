# Spaceti MCP

Model Context Protocol servers published by **Spaceti**, the Workplace Experience & Building Intelligence platform.

This repository contains **documentation and client configuration only**. There is no server source code here; the servers are hosted by Spaceti and you connect to them over HTTPS.

## Servers

| Server | Endpoint | Transport | Access | Docs |
|---|---|---|---|---|
| **Spaceti Intelligence** | `https://intelligence.spaceti.ai/mcp` | Streamable HTTP | Read-only workplace analytics, insights, bookings and sensor data | [`intelligence/`](intelligence/) |

More servers may be added here over time. Each gets its own folder.

## What Spaceti Intelligence does

Once connected, you can ask your AI assistant natural-language questions about your workplace and get answers grounded in your organization's live Spaceti data:

- "What was the average occupancy of the Prague office last month?"
- "Which meeting rooms hit their people-count peak yesterday?"
- "Compare desk utilization across floors this quarter."
- "Is CO₂ in the open space within healthy limits this week?"
- "How many room bookings did we have in June, and how many were no-shows?"

It can read occupancy and utilization (hourly or daily, per space or department), people counts, environmental measurements (temperature, humidity, CO₂, noise), booking statistics and individual booking records, detected insight events (unused bookings, unreserved occupancy, stale air) and raw sensor readings. Everything is scoped to the buildings and tenants your Spaceti user is entitled to see.

## Getting started

See [`intelligence/README.md`](intelligence/README.md) for requirements, and [`intelligence/clients.md`](intelligence/clients.md) for copy-paste configuration for Claude, ChatGPT, Cursor, VS Code, Claude Code and other MCP clients.

If your AI agent configures MCP clients on your behalf, point it at [`llms-install.md`](llms-install.md).

## Security and privacy

- **Read-only.** Every tool is annotated `readOnlyHint: true`. The assistant cannot create, modify or delete anything in Spaceti, including bookings.
- **OAuth 2.1.** You sign in with your existing Spaceti account. Authorization code flow with PKCE against `https://passport.spaceti.com`; the MCP server is an OAuth protected resource and publishes its metadata at `https://intelligence.spaceti.ai/.well-known/oauth-protected-resource`. Clients register dynamically (RFC 7591); there is no shared secret and no API key.
- **Permission parity.** Access is limited to the tenants and buildings your Spaceti user is already entitled to, enforced server-side on every request. The integration never exposes more than the same user sees in the Spaceti Dashboard. Your organisation's administrator can enable or disable the integration per organisation, role or user.
- **What the data contains.** Analytics, insight events and sensor readings are space-level and time-based, not tied to named people. Booking records carry an internal user identifier; the booker's name, email address and meeting title are included only where your organisation has enabled non-anonymised booking analytics. No individual precise-location data is available.
- **Scopes.** `mcp:tools`, `mcp:resources`.

How data is handled, retained and disconnected: [Spaceti Privacy Policy, section "AI Assistant Integrations"](https://spaceti.com/mobile/privacy-policy). Certifications and current security documentation: <https://trust.spaceti.com/>.

## Requirements

A Spaceti workspace with an **Intelligence Pro** license, the integration enabled for your organisation, and a user account with access to at least one building. Not a Spaceti customer yet? See <https://www.spaceti.com>.

## Links

| | |
|---|---|
| Setup guide | <https://help.spaceti.com/en/articles/15809628-connecting-the-spaceti-intelligence-mcp-to-your-ai-assistant> |
| Help center | <https://help.spaceti.com/en/> |
| Trust & security | <https://trust.spaceti.com/> |
| Privacy policy | <https://spaceti.com/mobile/privacy-policy> |
| Terms of service | <https://www.spaceti.com/mobile/terms-of-service> |
| Website | <https://www.spaceti.com> |
| Support | <mailto:support@spaceti.com> |

## Reporting problems

Security issues: see [`SECURITY.md`](SECURITY.md). Everything else: open an issue here, or email <support@spaceti.com>.

---

© Spaceti s.r.o. Documentation in this repository is published for use with Spaceti products.
