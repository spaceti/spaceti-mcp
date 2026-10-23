# Installing the Spaceti Intelligence MCP: instructions for AI agents

You are an AI agent configuring an MCP client on behalf of a user. This file tells you exactly what to do. It is written for you, not for a human.

## What you are installing

A **remote** MCP server, hosted by Spaceti. There is nothing to download, clone, build, or run. No package, no Docker image, no local process, no environment variables.

| | |
|---|---|
| Server name | `spaceti-intelligence` |
| URL | `https://intelligence.spaceti.ai/mcp` |
| Transport | Streamable HTTP (single endpoint; no SSE fallback) |
| Auth | OAuth 2.1 authorization code + PKCE, dynamic client registration |

## Do not

- **Do not ask the user for an API key, client secret, token or password.** None exist. Authentication is interactive OAuth in a browser, performed by the user, once.
- **Do not add `command`, `args`, `env`, or `npx` fields.** Those are for local stdio servers. This is a remote HTTP server.
- **Do not use `http://`, a trailing slash, or any host other than `intelligence.spaceti.ai`.**
- **Do not attempt to complete the OAuth flow yourself.** Configure the server, then hand off: the client opens a browser window and the user signs in with their existing Spaceti account.

## Configuration

Add this server to whichever config file the client uses.

Most clients (Cursor, Claude Desktop `mcpServers` style):

```json
{
  "mcpServers": {
    "spaceti-intelligence": {
      "url": "https://intelligence.spaceti.ai/mcp"
    }
  }
}
```

VS Code (`.vscode/mcp.json` or user `mcp.json`), which uses `servers` and requires an explicit type:

```json
{
  "servers": {
    "spaceti-intelligence": {
      "type": "http",
      "url": "https://intelligence.spaceti.ai/mcp"
    }
  }
}
```

Claude Code CLI, one command, no file editing:

```
claude mcp add --transport http spaceti-intelligence https://intelligence.spaceti.ai/mcp
```

For clients that only offer a UI (claude.ai, ChatGPT), there is no file to edit. Tell the user to add a custom connector and paste the URL above. See `intelligence/clients.md` for the exact menu paths.

## After configuring

1. Tell the user to restart or reload the client if it does not pick up the change automatically.
2. Tell the user they will be prompted to sign in to Spaceti in a browser, and that this is expected and happens once.
3. Verify the connection by listing tools. You should see exactly seven: `get_info`, `get_dataset`, `get_insights`, `get_bookings`, `get_sensor_events`, `get_spaces`, `get_space_groups`.

## Using the server once connected

**Call `get_info` with `type: "basic"` before calling `get_dataset` or any record tool the first time.** It is the interface contract: measurements, filters, aggregations, time semantics and pagination. The tools will not behave usefully if you guess at their parameters. There are no MCP resources; everything comes through tools.

Then, in order:

1. Call `get_info` with `type: "tenants"` to learn which tenants the user can access, then `type: "tenant"` with the chosen `tenant` to get the building catalog: buildings (with `timeZone` and working hours), floors, space types. The server is stateless: **pass `tenant` on every tenant-scoped call**; nothing is remembered between calls.
2. Use `get_spaces` and `get_space_groups` to resolve human names ("3rd floor", "Marketing") into the numeric IDs the other tools filter on. Do not invent IDs.
3. For aggregated numbers call `get_dataset` with `type: 'scalar'` or `type: 'timeseries'` (`granularity` required for timeseries). Metrics are strings, `"metric"` or `"metric:aggregation"`, e.g. `["temp:avg,max", "co2", "booking_counts:value_count"]`.
4. For individual records call `get_insights`, `get_bookings` or `get_sensor_events`. Prefer `get_dataset` for counts and statistics; use the record tools only when the user needs the underlying events. Continue a paginated result by passing `pagination.next_cursor` back as `cursor` with the same filters.

Constraints worth knowing before you hit them:

- Date ranges use `YYYY-MM-DD` in each building's local time; daily time windows use `HH:mm` and default to the buildings' working hours. `from` must not be later than `to`. Analytics results are already building-local; do not convert. Sensor events are in UTC.
- Results are size-capped. If a request returns a "result too large" error, the error tells you how to narrow it: shorter date range, coarser granularity, fewer buildings, smaller `max_pages`. Follow it rather than retrying the same request.
- Booking records are personal data: unless the organisation has opted out, they carry the booker's or organiser's name, email, meeting title and online-meeting details; opted-out organisations get an internal identifier only. Surface identities only when the user asks about a specific booking or person. Do not volunteer lists of who booked what.

## Requirements the user must already have

A Spaceti workspace with an **Intelligence Pro** license, the integration enabled for their organisation, and an account with access to at least one building. If sign-in succeeds but no tenants are available, this is the reason; the fix is on the Spaceti account side, not in the configuration. Point the user at <mailto:support@spaceti.com>.

## Troubleshooting

| Symptom | Cause | Action |
|---|---|---|
| 401 on every request | Not signed in, or token expired | Re-run the OAuth flow; the client should prompt |
| Sign-in succeeds, `get_info type: "tenants"` returns an empty list | Account has no entitled tenant, integration not enabled, or no Intelligence Pro license | Not a config problem. Contact <mailto:support@spaceti.com> |
| Client reports "no tools" | Config used a stdio shape (`command`/`args`) instead of `url` | Replace with the remote config above |
| `get_dataset` errors about `granularity` | `type: 'timeseries'` without `granularity` | Supply `granularity`, or use `type: 'scalar'` |
| A tool error says the tenant is missing or not accessible | `tenant` not passed, or the account is not a member | Pass `tenant` in every tenant-scoped call; check `get_info type: "tenants"` |

Full setup guide for humans: <https://help.spaceti.com/en/articles/15809628-connecting-the-spaceti-intelligence-mcp-to-your-ai-assistant>
