# Play KICKGEIST through any stdio-only client (mcp-remote fallback)

Some MCP clients can only talk to **local, stdio** servers — they don't yet
speak the Streamable HTTP transport that KICKGEIST uses. `mcp-remote` is the
universal bridge: a tiny npm package that runs locally, speaks stdio to your
client on one side, and proxies to our remote HTTPS endpoint on the other.

If your client already supports remote HTTP servers natively, use its own guide
instead — it's simpler and needs nothing installed. Reach for this page only
when your client offers a `command` / `args` style config and no remote URL
field.

KICKGEIST is the group-first, zero-money World Cup 2026 prediction game: you
predict match outcomes, build groups with friends, and climb the leaderboards —
all from your agent. Free, ad-supported, no purchases, no logins.

---

## Prerequisites

- **Node.js 18 or higher** installed and on your `PATH`. Check with `node -v`.
  `npx` ships with Node, so there's nothing else to install — `mcp-remote` is
  fetched on first run.
- An MCP client that supports **stdio / local command** servers via a
  `mcpServers` (or equivalent) config block.
- No KICKGEIST account or login needed up front. The endpoint is **authless** —
  you connect first, then create your account with a tool call.

---

## Steps

1. Confirm Node is ready: run `node -v` and make sure it prints `v18` or higher.

2. Open your client's MCP configuration file (the one that lists `mcpServers`
   with `command` / `args` entries — e.g. a `claude_desktop_config.json`,
   `mcp.json`, or your client's settings JSON).

3. Add the KICKGEIST entry shown in [Config](#config) below.

4. Save the file and fully restart your client so it picks up the new server.

5. Ask your agent to verify the connection — see
   [Verify it works](#verify-it-works).

---

## Config

Add this server to your client's `mcpServers` block:

```json
{
  "mcpServers": {
    "kickgeist": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://mcp.kickgeist.com/mcp"
      ]
    }
  }
}
```

If your client already has other servers, just add the `"kickgeist"` key
alongside them inside the existing `"mcpServers"` object.

### One-liner (for clients that take a raw command instead of JSON)

```bash
npx mcp-remote https://mcp.kickgeist.com/mcp
```

### Forcing the transport (only if the default fails)

`mcp-remote` defaults to `http-first` (HTTP transport, falling back to SSE).
Our endpoint is Streamable HTTP, so the default is correct and you normally
don't need this. If you hit transport negotiation errors, pin it to HTTP:

```json
{
  "mcpServers": {
    "kickgeist": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://mcp.kickgeist.com/mcp",
        "--transport",
        "http-only"
      ]
    }
  }
}
```

---

## Verify it works

After restarting, ask your agent two things:

1. **"Create my KICKGEIST account."** — this calls `create_account`, which
   spins up a fresh anonymous account and returns a **recovery code**.
   **Save that recovery code.** Enter it in the KICKGEIST mobile app (or via
   `link_account` on another agent) to reach this exact same account — your
   picks, groups, and stats follow you everywhere.

2. **"Show me the open World Cup matches."** — this calls `list_open_matches`
   and returns upcoming fixtures you can still predict (match ID, home team,
   away team, kickoff, stage). Pick one and say
   **"Predict a home win for that match"** to fire `predict_match`.

If both return real World Cup data, the bridge is live. From there you can
`create_group`, `join_group`, and check `get_my_stats`. The full leaderboard
and your friends' picks live in the app on purpose — that's where the social
celebration happens.

---

## Troubleshooting

- **`command not found: npx` / Node errors** — Node isn't installed or is below
  18. Install the latest LTS from nodejs.org and re-run `node -v`.

- **Client shows the server but no tools appear** — make sure you fully quit and
  reopened the client; many clients only read MCP config at startup. Confirm the
  JSON is valid (no trailing commas) and that `"kickgeist"` sits inside
  `"mcpServers"`.

- **First call is slow** — `npx` downloads `mcp-remote` on the first run and
  caches it. Subsequent launches are fast.

- **Transport or connection errors** — add `"--transport", "http-only"` to the
  `args` as shown above. If a corporate proxy or firewall blocks the connection,
  confirm `https://mcp.kickgeist.com/mcp` is reachable from your machine.

- **Stale cached bridge** — to force the newest `mcp-remote`, change `args` to
  `["mcp-remote@latest", "https://mcp.kickgeist.com/mcp"]`.

- **"How do I get back to my account?"** — ask your agent to run
  `get_recovery_code`, or open the KICKGEIST app and enter the recovery code you
  saved at signup. Same account, both places.

---

## Official docs

- `mcp-remote` package: https://www.npmjs.com/package/mcp-remote
- KICKGEIST endpoint: `https://mcp.kickgeist.com/mcp`

See you on the leaderboard. 🟠
