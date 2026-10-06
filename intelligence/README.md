# Spaceti Intelligence MCP

Read-only workplace analytics, insights, bookings and sensor data from Spaceti, exposed to any MCP-capable AI assistant.

| | |
|---|---|
| Endpoint | `https://intelligence.spaceti.ai/mcp` |
| Transport | Streamable HTTP (single endpoint, no legacy SSE) |
| Authentication | OAuth 2.1, authorization code + PKCE, dynamic client registration |
| Authorization server | `https://passport.spaceti.com` |
| Scopes | `mcp:tools`, `mcp:resources` |
| Registry identifier | `ai.spaceti/intelligence` |
| Server name / version | `spaceti-intelligence` / `1.1.0` |

**Setup instructions per client: [`clients.md`](clients.md).**
**Machine-readable descriptor: [`server.json`](server.json).**

## Requirements

- A Spaceti workspace with an **Intelligence Pro** license and the AI assistant integration enabled for your organisation.
- A Spaceti user account with access to at least one building.

You sign in with the Spaceti account you already have. There is no API key to request and no separate registration. If sign-in works but no tenants appear, the account is not entitled to Intelligence data or the integration is not enabled for your organisation; contact <mailto:support@spaceti.com>.

## Tools

All seven are read-only. The assistant cannot change anything in Spaceti.

| Tool | What it does |
|---|---|
| `select_tenant` | Selects the tenant used by tenant-scoped resources and tools. Returns that tenant's building catalog. |
| `get_spaces` | Finds spaces in one building by name query. Returns IDs to use as filters. |
| `get_space_groups` | Finds space groups (departments) in one building by name query. Returns IDs to use as filters. |
| `get_dataset` | Retrieves analytics dataset responses (scalar or timeseries) with measurement, date-range and element filters. Aggregated values: occupancy, utilization, people counts, environmental metrics, booking and insight counts. |
| `get_insights` | Retrieves paginated raw insight events: unused ("ghost") bookings, occupied-but-unreserved spaces, stale air. Identified by space and time. |
| `get_bookings` | Retrieves paginated raw booking records: booked space, start and end time, approval and check-in status, and an internal user identifier. |
| `get_sensor_events` | Retrieves paginated raw sensor readings with UTC timestamps and measurement, device and location filters. |

Tools other than `select_tenant` accept an optional `tenant` argument as a fallback when no tenant has been selected in the session.

## Resources

| URI | Contents |
|---|---|
| `spaceti://basic-info` | Describes this interface: available measurements, filters, terms, aggregations, the three record tools and the required call order. Read this first. |
| `spaceti://tenants/{tenant}` | Buildings, floors and space types for one tenant. |
| `spaceti://user-info` | Reserved. Returns no content in this version. |

## What data is available

Always scoped to the buildings and tenants your Spaceti user is entitled to see:

- Occupancy and utilization, hourly or daily, per space or per department
- Average and peak people counts
- Environmental measurements: temperature, humidity, CO₂, noise; and the underlying raw sensor readings
- Booking statistics (counts, utilization) and individual booking records
- Insight events: ghost bookings, unreserved occupancy, stale air
- AV equipment utilization

**About personal data.** Analytics, insight events and sensor readings are space-level and time-based. Booking records include an internal user identifier, which is personal data under GDPR. Where your organisation has enabled non-anonymised booking analytics, booking records also include the booker's name and email address and the meeting title. The integration exposes exactly what the same user can see in the Spaceti Dashboard, nothing more, and does not create or modify bookings. Details: [Privacy Policy, "AI Assistant Integrations"](https://spaceti.com/mobile/privacy-policy).

## Example questions

1. What was the busiest day in our office last week?
2. Show me average CO₂ and temperature for the 3rd floor this month.
3. Which departments use their space the least?
4. How many meeting-room bookings were no-shows last month?
5. Summarize booking utilization for Q2 and suggest where to cut space.

## How a session works

1. **Read `spaceti://basic-info`.** It is the interface contract: measurements, filters, record tools and the required order of calls.
2. **`select_tenant`** to pick the tenant. Everything after this is scoped to that selection.
3. **`get_spaces` / `get_space_groups`** to turn names ("3rd floor", "Marketing") into the numeric IDs the other tools filter on.
4. **`get_dataset`** for aggregated values: `type: 'scalar'` for a single value, `type: 'timeseries'` for histograms (`granularity` required). Metrics are passed as `"metric"` or `"metric:aggregation"`.
5. **`get_insights`, `get_bookings`, `get_sensor_events`** only when individual records are needed. Results are paginated; continue with `pagination.next_cursor` and the same filters. `max_pages` (1–10) controls how many pages one call returns.

Dates are `YYYY-MM-DD`, daily time windows are `HH:mm` and default to the buildings' working hours, and `from` must not be later than `to`. Sensor events use UTC. Responses are size-capped; an oversized result comes back as an error explaining how to narrow the request.

## Disconnecting

You can disconnect the integration at any time by removing the connector in your AI assistant. Disconnecting stops the integration from renewing its credentials; an existing access credential may remain valid until it expires, currently for up to seven days. Your organisation's administrator can disable the integration for the organisation, specific roles or individual users at any time, which stops further access immediately.

## Security

Read-only tools, OAuth 2.1 sign-in with your existing account, tenant isolation enforced server-side, permission parity with the Dashboard. Discovery metadata is published at `https://intelligence.spaceti.ai/.well-known/oauth-protected-resource` and `https://intelligence.spaceti.ai/.well-known/mcp/server-card.json`.

Full posture and certifications: <https://trust.spaceti.com/>

## Support

- Setup guide: <https://help.spaceti.com/en/articles/15809628-connecting-the-spaceti-intelligence-mcp-to-your-ai-assistant>
- Help center: <https://help.spaceti.com/en/>
- Email: <mailto:support@spaceti.com>
