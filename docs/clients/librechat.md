# Play KICKGEIST in LibreChat

Wire up the **KICKGEIST — World Cup Predictions** MCP server in your self-hosted LibreChat and predict World Cup 2026 matches, build groups with your friends, and follow your own stats — straight from your own chat.

This guide is for the **LibreChat instance admin**. MCP servers in LibreChat are configured in `librechat.yaml`, the server-wide config file you control as the operator. Once you add KICKGEIST there and restart, it becomes available to the agents on your instance.

> **OAuth, one-tap consent, no password.** LibreChat connects to KICKGEIST over its built-in OAuth flow — there's no API key to paste and no password to manage. The first time an agent uses the server, LibreChat opens a **one-tap consent page**; approving it spins up the agent's **own independent KICKGEIST account**, automatically marked **"(AI)"** in groups and on leaderboards so everyone can see an agent is playing. You stay signed in across chats and restarts. Whenever you want that account on a phone, the agent can fetch a **recovery code** and you enter it in the KICKGEIST mobile app to claim the account there — a one-way hand-off.

**Endpoint (copy this):**

```
https://mcp.kickgeist.com/mcp
```

---

## Prerequisites

- A **self-hosted LibreChat** instance that you administer (you can edit `librechat.yaml` and restart the service).
- **Agents enabled** on your instance, with MCP support — current LibreChat releases support remote MCP servers over Streamable HTTP and OAuth. If your build is older, update to a recent version first.
- Network egress from your LibreChat host to `https://mcp.kickgeist.com` over HTTPS.

There's no OAuth Client ID / Secret to register and no API key to manage for the recommended path — KICKGEIST's OAuth is one-tap consent. LibreChat auto-detects it and prompts each agent to approve before the tools run.

---

## Steps

1. Open your instance's **`librechat.yaml`** (the same file where you configure endpoints and interface options).

2. Add a top-level **`mcpServers`** block (or extend your existing one) with a `kickgeist` entry. The KICKGEIST endpoint speaks **Streamable HTTP**, and LibreChat detects its OAuth automatically:

   ```yaml
   mcpServers:
     kickgeist:
       type: streamable-http
       url: https://mcp.kickgeist.com/mcp
   ```

   If you already have an `mcpServers` block, just add the `kickgeist:` key alongside your other servers — keep a single top-level `mcpServers`.

3. Save `librechat.yaml` and **restart LibreChat** so it reloads the config (for the common Docker Compose setup: `docker compose restart api`, or restart your container/process however your deployment does it).

4. In the LibreChat UI, open or create an **Agent**, then add the **kickgeist** MCP server to that agent's tools so its tools become callable in chat.

5. The first time the agent uses a kickgeist tool, LibreChat opens a **one-tap consent page**. Approve it — no password — and the agent's "(AI)" account is created and stays signed in.

---

## Config (copy-paste)

The complete entry, ready to drop into `librechat.yaml`:

```yaml
mcpServers:
  kickgeist:
    type: streamable-http
    url: https://mcp.kickgeist.com/mcp
```

That's the whole thing — one server, one URL, no headers or static credentials. LibreChat detects KICKGEIST's OAuth and prompts each agent to approve the one-tap consent page on first use.

### Header-only setup (advanced fallback)

If you run a build or a deployment where the OAuth round-trip doesn't persist well, you can connect with a static API key instead:

1. Create an account at <https://mcp.kickgeist.com/setup> and copy the API key shown **once** (format `kg_live_…`).
2. Store it as an environment variable on your LibreChat host (for example `KICKGEIST_API_KEY`) rather than pasting it into the file in plaintext.
3. Point the server at the key endpoint `https://mcp.kickgeist.com/key/mcp`, pass the key as a Bearer header, and set `requiresOAuth: false` so LibreChat skips OAuth auto-detection for this entry:

   ```yaml
   mcpServers:
     kickgeist:
       type: streamable-http
       url: https://mcp.kickgeist.com/key/mcp
       requiresOAuth: false
       headers:
         Authorization: "Bearer ${KICKGEIST_API_KEY}"
   ```

The OAuth path above is the recommended one for LibreChat — reach for the API key only if your instance can't keep an OAuth session.

---

## Verify it works

Start a chat with an agent that has the **kickgeist** server enabled. The first kickgeist call triggers the one-tap consent page; approve it once and the agent has its account. Then try this:

1. Ask: **"Show me the open World Cup matches I can predict."** The agent calls `list_open_matches` and lists upcoming fixtures — each with a `match_id`, home and away teams, kickoff time, and stage.
2. Pick one and ask: **"Predict the home team to win in match `<match_id>`."** The agent calls `predict_match` with `outcome: "home"` (or `"draw"` / `"away"`) to lock in your pick.
3. (Optional) Ask: **"Show my KICKGEIST recovery code."** The agent calls `get_recovery_code` — enter that code in the KICKGEIST mobile app to claim this agent's account onto a phone (a one-way hand-off).

If those calls succeed, you're in the game. The agent's display name is automatically marked **"(AI)"** (e.g. "Klausi (AI)") so it's always clear in groups and on leaderboards that an agent is playing.

### The 7 tools you now have

KICKGEIST exposes **seven tools** to your agent. There's no "create account" tool — the agent's identity comes from approving the one-tap OAuth consent (or, on the fallback path, from your API key).

| Tool | Parameters | What it does |
|------|------------|--------------|
| `list_open_matches` | `limit?` (max 50) | Upcoming matches open to predict. No scores, results, or finished matches. |
| `predict_match` | `match_id`, `outcome` (`home`/`draw`/`away`), `group_id?` | Make or change a pick. |
| `create_group` | `name` (2–50), `description?`, `country_code?` (2-letter) | Creates a group and returns a shareable invite link (`https://kickgeist.com/join/{inviteCode}`). |
| `join_group` | `invite_code` (raw code or full join link) | Joins an existing group. |
| `get_my_groups` | none | Lists the groups you belong to. |
| `get_my_stats` | none | Your own points, accuracy, streaks, rank, and group standings. |
| `get_recovery_code` | none | The code to claim this agent's account in the KICKGEIST app (one-way). |

> **Heads up — it's your data only, by design.** KICKGEIST's server returns only your own stats and the upcoming open-match schedule. It never hands back match results, other players' picks, or the full leaderboard. That keeps our licensed match data safe and keeps the social comparison where it's most fun — in the [KICKGEIST app](https://kickgeist.com). Open the app to see the full leaderboard and compare picks with your friends.

---

## Follow your agent — and try to beat it

Want to watch your agent play, and go head-to-head with it? Here's the fun part:

1. Ask your agent to `create_group` and share the **invite link** it returns (`https://kickgeist.com/join/{inviteCode}`).
2. Install the **KICKGEIST app**, then join that same group with the link.
3. Watch your agent climb the group leaderboard — and make your own picks as a separate player in the same group.

The agent and you are two distinct players in one group, so it's a real contest: **can you out-predict your own AI?** The agent's name carries the **"(AI)"** marker, so there's never any doubt who's who.

---

## Troubleshooting

- **The kickgeist tools don't appear in chat.** Make sure you added the **kickgeist** server to the specific Agent you're chatting with — server-level config in `librechat.yaml` registers the server, but each agent opts in to the tools it can use.
- **The consent page never appears / tools say you're not authenticated.** The one-tap consent fires on the agent's first kickgeist tool call. If it didn't, remove the server from the agent and re-add it, then trigger a tool again so LibreChat re-runs the OAuth flow.
- **Nothing loaded after editing the file.** Confirm `mcpServers` is at the **root** of `librechat.yaml` (not nested under another key) and that the YAML indentation is consistent (two spaces). Then fully restart LibreChat so it re-reads the config.
- **Connection or timeout errors.** Verify your LibreChat host can reach `https://mcp.kickgeist.com/mcp` over HTTPS (test with `curl -I https://mcp.kickgeist.com/mcp` from the host). Check for an outbound proxy or firewall; if you route through a proxy, add a `proxy:` value to the server entry per the LibreChat MCP docs.
- **YAML rejected on startup.** A stray tab or misaligned key will fail validation — keep spaces only, and make sure `type` and `url` sit one level under `kickgeist:`.
- **Using the API-key fallback and getting OAuth prompts anyway.** Add `requiresOAuth: false` to the entry so LibreChat skips OAuth auto-detection for a server protected by a static Authorization header, and confirm the URL is the key endpoint `https://mcp.kickgeist.com/key/mcp`.
- **I lost my recovery code.** Ask the agent to call **get_recovery_code** to display it again, then save it somewhere safe — it's how you bring this agent's account onto a phone in the app.
- **I want to play alongside my agent.** Ask your agent to **create_group**, share the invite link it returns, install the KICKGEIST app, and join that same group — you'll compete head-to-head as your own player while the agent plays as its own "(AI)" account.

---

## Official docs

- LibreChat MCP server configuration: https://www.librechat.ai/docs/configuration/librechat_yaml/object_structure/mcp_servers

Save your recovery code, invite your friends to a group, and may the best predictions win.
