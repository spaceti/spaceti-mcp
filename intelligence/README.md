# Spaceti Intelligence MCP

Read-only workplace analytics, insights, bookings and sensor data from Spaceti, exposed to any MCP-capable AI assistant.

| | |
|---|---|
| Endpoint | `https://intelligence.spaceti.ai/mcp` |
| Transport | Streamable HTTP (single endpoint, no legacy SSE) |
| Authentication | OAuth 2.1, authorization code + PKCE, dynamic client registration |
| Authorization server | `https://passport.spaceti.com` |
| Scopes | `mcp:tools` |
| Registry identifier | `ai.spaceti/intelligence` |
| Server name / version | `spaceti-intelligence` / `1.3.0` |

**Setup instructions per client: [`clients.md`](clients.md).**
**Machine-readable descriptor: [`server.json`](server.json).**

## Requirements

- A Spaceti workspace with an **Intelligence Pro** license and the AI assistant integration enabled for your organisation.
- A Spaceti user account with access to at least one building.

You sign in with the Spaceti account you already have. There is no API key to request and no separate registration. If sign-in works but `get_info` with `type: "tenants"` returns an empty list, the account is not entitled to Intelligence data or the integration is not enabled for your organisation; contact <mailto:support@spaceti.com>.

## Tools

All seven are read-only and stateless. The assistant cannot change anything in Spaceti. Every tenant-scoped tool takes a `tenant` argument; nothing is selected or remembered between calls.

| Tool | What it does |
|---|---|
| `get_info` | Reference information. `type: "basic"` returns the interface documentation (measurements, units, aggregations, filters, time semantics, pagination). `type: "tenants"` lists the tenants your account can access. `type: "tenant"` with `tenant` returns that tenant's building catalog: buildings with `timeZone` and working hours, floors, space types, and the numeric IDs the other tools filter on. |
| `get_spaces` | Finds spaces in one building by name query. Returns IDs to use as filters. |
| `get_space_groups` | Finds space groups (departments) in one building by name query. Returns IDs to use as filters. |
| `get_dataset` | Aggregated analytics (scalar or timeseries): occupancy, utilization, people counts, environmental metrics, booking and insight counts. Metrics are strings, `"metric"` or `"metric:aggregation"`, e.g. `["temp:avg,max", "co2", "booking_counts:value_count"]`. |
| `get_insights` | Paginated raw insight events: unused ("ghost") bookings, occupied-but-unreserved spaces, stale air. Identified by space and time. |
| `get_bookings` | Paginated raw booking records: booked space, start and end time, approval and check-in status, and, unless the organisation has opted out, the booker's or meeting organiser's name and email, the meeting title and online-meeting details. |
| `get_sensor_events` | Paginated raw sensor readings with UTC timestamps and measurement, device and location filters. |

## What data is available

Always scoped to the buildings and tenants your Spaceti user is entitled to see:

- Occupancy and utilization, hourly or daily, per space or per department
- Average and peak people counts
- Environmental measurements: temperature, humidity, CO₂, noise; and the underlying raw sensor readings
- Booking statistics (counts, utilization) and individual booking records
- Insight events: ghost bookings, unreserved occupancy, stale air
- AV equipment utilization

**About personal data.** Analytics, insight events and sensor readings are space-level and time-based. Booking records identify the person who made the booking or organised the meeting (name and email address, which may belong to a calendar organiser who is not a Spaceti user), the meeting title and whether it was an online meeting, unless your organisation has opted out of identified booking analytics; opted-out organisations see an internal identifier only. The integration exposes exactly what the same user can see in the Spaceti Dashboard, nothing more, and does not create or modify bookings. Details: [Privacy Policy, "AI Assistant Integrations"](https://spaceti.com/mobile/privacy-policy).

## Example questions

1. What was the busiest day in our office last week?
2. Show me average CO₂ and temperature for the 3rd floor this month.
3. Which departments use their space the least?
4. How many meeting-room bookings were no-shows last month?
5. Summarize booking utilization for Q2 and suggest where to cut space.

## How a session works

1. **`get_info`** with `type: "tenants"` if the tenant is unknown, then `type: "tenant"` to get building, floor and space-type IDs. `type: "basic"` is the interface contract; read it once when unfamiliar.
2. **`get_spaces` / `get_space_groups`** to turn names ("3rd floor", "Marketing") into the numeric IDs the other tools filter on.
3. **`get_dataset`** for aggregated values: `type: 'scalar'` for a single value, `type: 'timeseries'` for histograms (`granularity` required).
4. **`get_insights`, `get_bookings`, `get_sensor_events`** only when individual records are needed. Results are paginated; continue with `pagination.next_cursor` and the same filters. `max_pages` (1–10) controls how many pages one call returns.

Time semantics: dates are `YYYY-MM-DD` in each building's local time; daily time windows are `HH:mm` and default to the buildings' working hours; `from` must not be later than `to`. Analytics results are already in building-local time and need no conversion, including across buildings in different time zones. `get_sensor_events` uses UTC. Responses are size-capped; an oversized result comes back as an error explaining how to narrow the request.

## Disconnecting

You can disconnect the integration at any time by removing the connector in your AI assistant. Disconnecting stops the integration from renewing its credentials; an existing access credential may remain valid until it expires, currently for up to seven days. Your organisation's administrator can disable the integration for the organisation, specific roles or individual users at any time, which stops further access immediately.

## Security

Read-only tools, OAuth 2.1 sign-in with your existing account, tenant isolation enforced server-side, permission parity with the Dashboard. Discovery metadata is published at `https://intelligence.spaceti.ai/.well-known/oauth-protected-resource` and `https://intelligence.spaceti.ai/.well-known/mcp/server-card.json`.

Full posture and certifications: <https://trust.spaceti.com/>

## Support

- Setup guide: <https://help.spaceti.com/en/articles/15809628-connecting-the-spaceti-intelligence-mcp-to-your-ai-assistant>
- Help center: <https://help.spaceti.com/en/>
- Email: <mailto:support@spaceti.com>
