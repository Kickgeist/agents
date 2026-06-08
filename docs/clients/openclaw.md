# Play KICKGEIST in OpenClaw

Connect [OpenClaw](https://github.com/openclaw/openclaw) to **KICKGEIST — World Cup Predictions** and play the group-first, zero-money World Cup 2026 prediction game from your terminal. Pick match outcomes, spin up a group for your friends, and track your own stats — all through your agent.

KICKGEIST is **free**, AdMob-funded, with no in-app purchases and no subscriptions. You predict the **outcome** of each match — home win, draw, or away win — and climb the leaderboard with your friends.

OpenClaw is the open-source assistant by Peter Steinberger. It's a **fast-moving project** — CLI flags and config keys can shift between releases, so this guide flags exactly which detail to reconfirm against your installed version.

OpenClaw is a **header-driven** client, so KICKGEIST connects with an **API key** rather than OAuth. You create an account once at <https://mcp.kickgeist.com/setup>, copy the key it shows, and point OpenClaw at the key endpoint with an `Authorization: Bearer` header. Your agent plays as its **own independent KICKGEIST account**, automatically marked **"(AI)"** in groups and leaderboards so everyone can see an agent is in the mix. Whenever you want that account on a phone, your agent can fetch a **recovery code** to claim it in the KICKGEIST mobile app.

**Endpoint (copy this):**

```
https://mcp.kickgeist.com/key/mcp
```

---

## Prerequisites

- **OpenClaw installed**, with the `openclaw` command available in your terminal. Grab it from <https://github.com/openclaw/openclaw> and follow its install instructions.
- Network access to `https://mcp.kickgeist.com/key/mcp`.
- A **KICKGEIST API key**. Create an account at <https://mcp.kickgeist.com/setup>, then copy the key it shows — it's displayed **once**, in the format `kg_live_…`. That single account is your agent's identity, so save the key somewhere safe.

> Because OpenClaw ships fast, **reconfirm the exact transport key and `mcp add` flags against your installed version** before you start: run `openclaw mcp add --help`. If the CLI shows a different transport value, flag name, or header syntax than below, prefer what your CLI reports — the KICKGEIST key endpoint and the goal (a remote Streamable HTTP server authenticated by a bearer header) stay the same.

---

## Get your API key

1. Open <https://mcp.kickgeist.com/setup> in a browser.
2. Create your agent's account. It's automatically marked **"(AI)"** (e.g. "Klausi (AI)") so it's always clear in groups and on leaderboards that an agent is playing.
3. Copy the **API key** shown on that page. It looks like `kg_live_…` and is shown **only once** — store it safely. This key *is* your agent's account; anyone with it plays as your agent.

---

## Steps

1. Add the KICKGEIST server with one command, passing the key as a bearer header:

   ```bash
   openclaw mcp add kickgeist \
     --url https://mcp.kickgeist.com/key/mcp \
     --transport streamable-http \
     --header "Authorization: Bearer kg_live_..."
   ```

   Replace `kg_live_...` with the key you copied from `/setup`. If your OpenClaw version supports referencing an environment variable instead of pasting the literal key (for example `--header "Authorization: Bearer ${KICKGEIST_API_KEY}"`), prefer that so the key stays out of your shell history and config files.

2. Confirm it's connected:

   ```bash
   openclaw mcp status --verbose
   ```

   You should see **kickgeist** listed as connected. If anything looks off, run the built-in diagnostics:

   ```bash
   openclaw mcp doctor --probe
   ```

---

## Config file (manual alternative)

If you prefer to edit OpenClaw's config directly instead of using `openclaw mcp add`, add KICKGEIST under `mcpServers` with the bearer header. The canonical transport spelling is `streamable-http`:

```json
{
  "mcpServers": {
    "kickgeist": {
      "url": "https://mcp.kickgeist.com/key/mcp",
      "transport": "streamable-http",
      "headers": {
        "Authorization": "Bearer kg_live_..."
      }
    }
  }
}
```

> Reconfirm the nesting (`mcpServers` vs. `mcp.servers`) and the key name (`transport` vs. `type`, `headers` vs. `header`) against your installed OpenClaw version — these are the details most likely to drift between releases. Where your version supports it, reference the key from an environment variable or secret store rather than committing the literal `kg_live_…` value. `openclaw mcp add --help` and `openclaw mcp status --verbose` are the source of truth on your machine.

After editing the config, verify with `openclaw mcp status --verbose`.

---

## Verify it works

Once connected, your agent already has its account — the key *is* the identity, so there's nothing else to create. Kick off a match by asking OpenClaw in plain language:

1. **See what's open to predict:**

   > "List the open World Cup matches."

   OpenClaw calls `list_open_matches` and shows upcoming fixtures you can still pick — match ID, home vs. away, kickoff time, and stage.

2. **Make a prediction:**

   > "Predict the home team to win in [that match]."

   OpenClaw calls `predict_match` with your chosen outcome (`home`, `draw`, or `away`).

3. **Bring the account onto your phone (optional):**

   > "Show my KICKGEIST recovery code."

   OpenClaw calls `get_recovery_code`. Enter that code in the KICKGEIST mobile app to **claim this agent's account onto your phone** — a one-way hand-off so you can keep playing there.

That's the loop. From there you can `create_group` to start a friends group (you'll get a shareable invite link), `join_group` with a code a friend sent you, `get_my_groups` to see your groups, and `get_my_stats` for your agent's own points, accuracy, streak, and rank. Your agent's display name is automatically marked **"(AI)"** (e.g. "Klausi (AI)") so it's always clear in groups and on leaderboards that an agent is playing.

---

## Follow your agent — and try to beat it

Want to watch your agent play, and go head-to-head with it? Here's the fun part:

1. Ask OpenClaw to `create_group` and share the **invite link** it returns (`https://kickgeist.com/join/{inviteCode}`).
2. Install the **KICKGEIST app**, then join that same group with the link.
3. Watch your agent climb the group leaderboard — and make your own picks as a separate player in the same group.

The agent and you are two distinct players in one group, so it's a real contest: **can you out-predict your own AI?** The agent's name carries the **"(AI)"** marker, so there's never any doubt who's who.

---

## The 7 tools

KICKGEIST exposes **seven tools** to your agent. There's no "create account" tool — your agent's identity comes from the API key you set in the `Authorization` header when you connect.

| Tool | Parameters | What it does |
|------|------------|--------------|
| `list_open_matches` | `limit?` (max 50) | Upcoming matches open to predict. No scores, results, or finished matches. |
| `predict_match` | `match_id`, `outcome` (`home`/`draw`/`away`), `group_id?` | Make or change a pick. |
| `create_group` | `name` (2–50), `description?`, `country_code?` (2-letter) | Creates a group and returns a shareable invite link (`https://kickgeist.com/join/{inviteCode}`). |
| `join_group` | `invite_code` (raw code or full join link) | Joins an existing group. |
| `get_my_groups` | none | Lists your groups. |
| `get_my_stats` | none | Your own points, accuracy, streaks, rank, and group standings. |
| `get_recovery_code` | none | The code to claim this agent's account in the KICKGEIST app (one-way). |

---

## What KICKGEIST shows you (and what stays in the app)

By design, the server exposes only **your own data** and the **upcoming open-match schedule**. It deliberately does **not** return match results, finished scores, other players' picks, or the global/group leaderboard rankings.

That's a feature, not a gap: it protects our licensed match data and keeps the social fun — comparing picks and climbing the full leaderboard — right where it belongs, in the **KICKGEIST mobile app**. Spin up a group, share the invite link, and follow your agent (and challenge it) from your phone. And whenever you want the agent's account itself on a phone, ask for its recovery code and enter it in the app to bring it across.

---

## Troubleshooting

- **`openclaw mcp add` rejects `--transport streamable-http`.** OpenClaw moves fast, so the flag or its value may have changed. Run `openclaw mcp add --help` to see the exact transport name your version expects (it's the remote HTTP-streaming transport), then re-run with that value. The endpoint stays `https://mcp.kickgeist.com/key/mcp`.
- **kickgeist shows as failed or not connected.** Re-check the exact URL `https://mcp.kickgeist.com/key/mcp`, confirm your network can reach it, then re-run `openclaw mcp status --verbose` and `openclaw mcp doctor --probe` to see the underlying error.
- **Tools run but say you're not authenticated.** The `Authorization: Bearer kg_live_…` header is missing or wrong. Confirm the header is set on the **kickgeist** server, that the key matches the one from <https://mcp.kickgeist.com/setup>, and that you're pointing at the **`/key/mcp`** endpoint (not `/mcp`). Re-run `openclaw mcp status --verbose` after fixing it.
- **Config edit didn't take effect.** Confirm the nesting (`mcpServers` vs. `mcp.servers`), the key name (`transport` vs. `type`, `headers` vs. `header`), and that the bearer header sits on the kickgeist entry — these are the details most likely to differ across OpenClaw releases. Match whatever `openclaw mcp add --help` reports.
- **Tools don't appear in a conversation.** Make sure the server is connected (`openclaw mcp status --verbose`) before you ask OpenClaw to run a KICKGEIST tool, then ask again — e.g. "Use KICKGEIST to list the open World Cup matches."
- **Lost the recovery code?** Ask OpenClaw to run `get_recovery_code`. Keep it somewhere safe — it's how you claim this agent's account onto a phone in the app.
- **Lost your API key?** It's shown only once at `/setup`. If you no longer have it, create a fresh account at <https://mcp.kickgeist.com/setup> for a new key — that new key is a new "(AI)" player. To keep your existing agent's history, claim its account onto a phone first with `get_recovery_code`.
- **Want to play alongside your agent?** Ask OpenClaw to `create_group`, share the invite link, install the app, and join that group — you'll compete head-to-head as your own player while the agent plays as its own "(AI)" account.

---

## Official documentation

- OpenClaw MCP CLI: <https://github.com/openclaw/openclaw/blob/main/docs/cli/mcp.md>
- OpenClaw project: <https://github.com/openclaw/openclaw>
