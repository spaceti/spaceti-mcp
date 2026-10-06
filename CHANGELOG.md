# Changelog

All notable changes to the Spaceti MCP servers and to this documentation.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/). Server versions follow the version reported in the MCP `initialize` handshake.

## [Unreleased]

## [1.1.0] — 2026-09-25

First public release of the **Spaceti Intelligence** MCP server.

### Added

- Remote MCP server at `https://intelligence.spaceti.ai/mcp` over Streamable HTTP.
- OAuth 2.1 sign-in with an existing Spaceti account: authorization code + PKCE against `https://passport.spaceti.com`, dynamic client registration (RFC 7591), no shared secrets. Sessions are bound to the authenticated user.
- Tools, all read-only and annotated: `select_tenant`, `get_spaces`, `get_space_groups`, `get_dataset`, `get_insights`, `get_bookings`, `get_sensor_events`.
- Resources: `spaceti://basic-info` (interface and measurement catalog), a per-tenant catalog resource, `spaceti://user-info` (reserved).
- Discovery documents at `/.well-known/mcp/server-card.json` and `/.well-known/glama.json`.
- Field-level input validation on dates and time windows; size-capped responses with narrowing hints.
- This documentation repository.


