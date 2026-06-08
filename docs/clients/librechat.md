# Play KICKGEIST in LibreChat

Wire up the **KICKGEIST — World Cup Predictions** MCP server in your self-hosted LibreChat and predict World Cup 2026 matches, build groups with your friends, and climb the leaderboard — straight from your own chat.

This guide is for the **LibreChat instance admin**. MCP servers in LibreChat are configured in `librechat.yaml`, the server-wide config file you control as the operator. Once you add KICKGEIST there and restart, it becomes available to the agents on your instance.

> **Authless — no OAuth, no login.** You don't sign in to connect. You create your KICKGEIST identity by asking your agent to call the `create_account` tool, which returns a **recovery code**. Save that code — it's how you reach the same account in the KICKGEIST mobile app (and anywhere else).

---

## Prerequisites

- A **self-hosted LibreChat** instance that you administer (you can edit `librechat.yaml` and restart the service).
- **Agents enabled** on your instance, with MCP support — current LibreChat releases support remote MCP servers over Streamable HTTP. If your build is older, update to a recent version first.
- Network egress from your LibreChat host to `https://mcp.kickgeist.com` over HTTPS.

No API key, no secret, no OAuth client is needed for KICKGEIST — the server is authless.

---

## Steps

1. Open your instance's **`librechat.yaml`** (the same file where you configure endpoints and interface options).

2. Add a top-level **`mcpServers`** block (or extend your existing one) with a `kickgeist` entry. The KICKGEIST endpoint speaks **Streamable HTTP**:

   ```yaml
   mcpServers:
     kickgeist:
       type: streamable-http
       url: https://mcp.kickgeist.com/mcp
   ```

   If you already have an `mcpServers` block, just add the `kickgeist:` key alongside your other servers — keep a single top-level `mcpServers`.

3. Save `librechat.yaml` and **restart LibreChat** so it reloads the config (for the common Docker Compose setup: `docker compose restart api`, or restart your container/process however your deployment does it).

4. In the LibreChat UI, open or create an **Agent**, then add the **kickgeist** MCP server to that agent's tools so its tools become callable in chat.

---

## Config (copy-paste)

The complete entry, ready to drop into `librechat.yaml`:

```yaml
mcpServers:
  kickgeist:
    type: streamable-http
    url: https://mcp.kickgeist.com/mcp
```

That's the whole thing — one server, one URL, no headers or credentials. The endpoint is a single Streamable HTTP URL: `https://mcp.kickgeist.com/mcp`.

---

## Verify it works

Start a chat with an agent that has the **kickgeist** server enabled and try this:

1. Ask: **"Create my KICKGEIST account."** The agent calls `create_account` and returns a welcome message plus your **recovery code**. **Save the recovery code now** — enter it in the KICKGEIST mobile app (or via `link_account` on another agent) to reach this exact same account.
2. Ask: **"Show me the open World Cup matches I can predict."** The agent calls `list_open_matches` and lists upcoming fixtures — each with a `matchId`, home and away teams, kickoff time, and stage.
3. Pick one and ask: **"Predict the home team to win in match `<matchId>`."** The agent calls `predict_match` with `outcome: "home"` (or `"draw"` / `"away"`) to lock in your pick.

If those three calls succeed, you're in the game.

### The 9 tools you now have

- **create_account** — make a fresh anonymous KICKGEIST account; returns your recovery code.
- **link_account** — connect this session to an existing account using its recovery code.
- **get_recovery_code** — show the recovery code for the currently linked account.
- **list_open_matches** — list matches currently open for predictions (upcoming schedule only).
- **predict_match** — make or change your prediction: `home`, `draw`, or `away`.
- **create_group** — start a prediction group; returns a shareable invite link.
- **join_group** — join a group with a 6-character code or a `https://kickgeist.com/join/...` link.
- **get_my_groups** — list the groups you belong to.
- **get_my_stats** — your own points, accuracy, streaks, global rank, and group standings.

> **Heads up — it's your data only, by design.** KICKGEIST's server returns only your own stats and the upcoming open-match schedule. It never hands back match results, other players' picks, or the full leaderboard. That keeps our licensed match data safe and keeps the social comparison where it's most fun — in the [KICKGEIST app](https://kickgeist.com). Open the app to see the full leaderboard and compare picks with your friends.

---

## Troubleshooting

- **The kickgeist tools don't appear in chat.** Make sure you added the **kickgeist** server to the specific Agent you're chatting with — server-level config in `librechat.yaml` registers the server, but each agent opts in to the tools it can use.
- **Nothing loaded after editing the file.** Confirm `mcpServers` is at the **root** of `librechat.yaml` (not nested under another key) and that the YAML indentation is consistent (two spaces). Then fully restart LibreChat so it re-reads the config.
- **Connection or timeout errors.** Verify your LibreChat host can reach `https://mcp.kickgeist.com/mcp` over HTTPS (test with `curl -I https://mcp.kickgeist.com/mcp` from the host). Check for an outbound proxy or firewall; if you route through a proxy, add a `proxy:` value to the server entry per the LibreChat MCP docs.
- **YAML rejected on startup.** A stray tab or misaligned key will fail validation — keep spaces only, and make sure `type` and `url` sit one level under `kickgeist:`.
- **I lost my recovery code.** While your session is still linked, ask the agent to call **get_recovery_code** to display it again, then save it somewhere safe.
- **I want to use my phone account here.** Open the KICKGEIST app, grab your recovery code from the account/recovery screen, then ask your agent to **link_account** with it — your picks, groups, and stats follow you.

---

## Official docs

- LibreChat MCP server configuration: https://www.librechat.ai/docs/configuration/librechat_yaml/object_structure/mcp_servers

Save your recovery code, invite your friends to a group, and may the best predictions win.
