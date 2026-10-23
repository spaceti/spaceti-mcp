# Changelog

All notable changes to the Spaceti MCP servers and to this documentation.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/). Server versions follow the version reported in the MCP `initialize` handshake.

## [Unreleased]

## [1.3.0] — 2026-10-20

### Added

- `get_bookings` records now carry the booker's or meeting organiser's name and email address, the meeting title and online-meeting details, unless the organisation has opted out of identified booking analytics (opted-out organisations receive an internal identifier only). Calendar organisers who are not Spaceti users are included.
- `get_bookings` responses report the time filters that were applied.

### Changed

- `get_info` basic documentation describes the booking identity and meeting fields.

## [1.2.0] — 2026-10-09

### Changed

- **`get_info` replaces `select_tenant` and the three resources** (`spaceti://basic-info`, `spaceti://tenants/{tenant}`, `spaceti://user-info`). The server is now stateless: every tenant-scoped tool takes a `tenant` argument, and `get_info` provides the interface documentation (`type: "basic"`), the accessible tenant list (`type: "tenants"`) and a tenant's building catalog (`type: "tenant"`). Scope `mcp:resources` is no longer used.
- Building catalog entries expose `timeZone` and working hours. Analytics results are documented as building-local time; `get_sensor_events` stays UTC.
- Tagged release process; production deploys from tagged builds.

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
