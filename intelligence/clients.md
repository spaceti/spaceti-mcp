# Connecting Spaceti Intelligence to your AI assistant

Everything below points at one endpoint:

```
https://intelligence.spaceti.ai/mcp
```

It is a **remote** server. There is nothing to install, no package to run, no API key to paste. The first time you connect, your client opens a browser and you sign in with your existing Spaceti account. Once. You can disconnect at any time from the same place in your client; see [`README.md`](README.md#disconnecting) for what that does.

Requires a Spaceti workspace with an **Intelligence Pro** license and an account with access to at least one building.

---

## Claude (claude.ai, Claude Desktop)

1. **Settings → Connectors → Add custom connector**
2. Name: `Spaceti Intelligence`
3. Remote MCP server URL: `https://intelligence.spaceti.ai/mcp`
4. **Add**, then **Connect** and sign in with your Spaceti account.

Enterprise and Team plans: a workspace owner or admin may need to add the connector at the organization level before it is available to members.

---

## Claude Code

```bash
claude mcp add --transport http spaceti-intelligence https://intelligence.spaceti.ai/mcp
```

Then run `/mcp` inside Claude Code and authenticate. Add `--scope user` to make it available across all your projects instead of just the current one.

---

## ChatGPT

Requires a plan where connectors are available, and developer mode enabled.

1. **Settings → Connectors → Advanced → Developer mode** (enable)
2. **Settings → Connectors → Create**
3. Name: `Spaceti Intelligence`
4. MCP server URL: `https://intelligence.spaceti.ai/mcp`
5. Authentication: **OAuth**
6. Create, then connect and sign in.

---

## Cursor

One-click install:

```
cursor://anysphere.cursor-deeplink/mcp/install?name=spaceti-intelligence&config=eyJ1cmwiOiJodHRwczovL2ludGVsbGlnZW5jZS5zcGFjZXRpLmFpL21jcCJ9
```

Or add it by hand to `~/.cursor/mcp.json` (global) or `.cursor/mcp.json` (per project):

```json
{
  "mcpServers": {
    "spaceti-intelligence": {
      "url": "https://intelligence.spaceti.ai/mcp"
    }
  }
}
```

Then open **Settings → MCP** and sign in.

---

## VS Code (GitHub Copilot agent mode)

Add to `.vscode/mcp.json` in your workspace, or to your user `mcp.json` via **MCP: Open User Configuration** in the command palette. Note that VS Code uses `servers`, not `mcpServers`, and wants an explicit type:

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

Then **Start** the server from the editor gutter or the MCP view and sign in.

---

## Any other MCP client

Most clients that support remote servers accept this shape:

```json
{
  "mcpServers": {
    "spaceti-intelligence": {
      "url": "https://intelligence.spaceti.ai/mcp"
    }
  }
}
```

If the client asks for a transport, choose **Streamable HTTP** (not SSE, not stdio). If it asks for an auth type, choose **OAuth** and leave client ID and secret blank — the server supports dynamic client registration, so the client registers itself.

Clients that only support stdio can bridge with `mcp-remote`:

```json
{
  "mcpServers": {
    "spaceti-intelligence": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://intelligence.spaceti.ai/mcp"]
    }
  }
}
```

Prefer a native remote connection where the client offers one — the bridge is an extra moving part and an extra place for tokens to live.

---

## Gemini Enterprise

Not yet available. This section will be filled in when the Gemini Enterprise integration ships; it is administrator-configured rather than set up per user. Check the [setup guide](https://help.spaceti.com/en/articles/15809628-connecting-the-spaceti-intelligence-mcp-to-your-ai-assistant) for current status.

---

## Verifying it worked

Ask your assistant to list the available Spaceti tools. You should see seven: `get_info`, `get_dataset`, `get_insights`, `get_bookings`, `get_sensor_events`, `get_spaces`, `get_space_groups`.

Then try: *"Using Spaceti, what was the average occupancy of our office last month?"* The assistant should call `get_info` first (tenants, then the building catalog), then `get_dataset`.

## If it does not

| Symptom | Likely cause | Fix |
|---|---|---|
| Browser never opens / stuck "connecting" | Client configured for stdio, or URL typo | Confirm the URL is exactly `https://intelligence.spaceti.ai/mcp`, no trailing slash |
| Sign-in works, but no tenants available | Account not entitled, or no Intelligence Pro license | Contact <mailto:support@spaceti.com> — not a configuration problem |
| Tools appear but every call returns 401 | Token expired | Reconnect / re-authenticate in the client's MCP settings |
| A tool error says the tenant is missing or not accessible | `tenant` not passed | Ask the assistant to call `get_info` with `type: "tenants"` and pass `tenant` on each call |
| Assistant guesses filter IDs and gets nothing | It skipped `get_info` | Ask it to call `get_info` with `type: "tenant"` and use the returned IDs |

Still stuck: <mailto:support@spaceti.com>.
