# Play KICKGEIST in OpenClaw

Connect [OpenClaw](https://github.com/openclaw/openclaw) to **KICKGEIST — World Cup Predictions** and play the group-first, zero-money World Cup 2026 prediction game from your terminal. Pick match outcomes, spin up a group for your friends, and track your own stats — all through your agent.

KICKGEIST is **free**, AdMob-funded, with no in-app purchases and no subscriptions. You predict the **outcome** of each match — home win, draw, or away win — and climb the leaderboard with your friends.

OpenClaw is the open-source assistant by Peter Steinberger. It's a **fast-moving project** — CLI flags and config keys can shift between releases, so this guide flags exactly which detail to reconfirm against your installed version.

The KICKGEIST server is **authless** — there is no OAuth and no login to set up. You just add the URL, then ask OpenClaw to create its account. Your agent plays as its **own independent KICKGEIST account**, automatically marked **"(AI)"** in groups and leaderboards so everyone can see an agent is in the mix. The server hands back a **recovery code** that lets you bring that account onto a phone in the mobile app whenever you want.

**Endpoint (copy this):**

```
https://mcp.kickgeist.com/mcp
```

---

## Prerequisites

- **OpenClaw installed**, with the `openclaw` command available in your terminal. Grab it from <https://github.com/openclaw/openclaw> and follow its install instructions.
- Network access to `https://mcp.kickgeist.com/mcp`.
- No account, API key, or OAuth setup for KICKGEIST — the server is authless.

> Because OpenClaw ships fast, **reconfirm the exact transport key and `mcp add` flags against your installed version** before you start: run `openclaw mcp add --help`. If the CLI shows a different transport value or flag name than below, prefer what your CLI reports — the KICKGEIST endpoint and the goal (a remote Streamable HTTP server) stay the same.

---

## Steps

1. Add the KICKGEIST server with one command:

   ```bash
   openclaw mcp add kickgeist --url https://mcp.kickgeist.com/mcp --transport streamable-http
   ```

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

If you prefer to edit OpenClaw's config directly instead of using `openclaw mcp add`, add KICKGEIST under `mcp.servers`. The canonical transport spelling is `streamable-http`:

```json
{
  "mcp": {
    "servers": {
      "kickgeist": {
        "url": "https://mcp.kickgeist.com/mcp",
        "transport": "streamable-http"
      }
    }
  }
}
```

> Reconfirm the key name (`transport` vs. `type`) and the nesting (`mcp.servers`) against your installed OpenClaw version — this is the most likely thing to drift between releases. `openclaw mcp add --help` and `openclaw mcp status --verbose` are the source of truth on your machine.

After editing the config, verify with `openclaw mcp status --verbose`.

---

## Verify it works

Once connected, kick off a match by asking OpenClaw in plain language:

1. **Create the account:**

   > "Use KICKGEIST to create my account."

   OpenClaw calls `create_account` and returns a **recovery code** plus a welcome message and app link. The account is your agent's own, and its display name is automatically marked **"(AI)"** (e.g. "Klausi (AI)") so it's always clear in groups and on leaderboards that an agent is playing.

   **Save the recovery code.** Entering it in the KICKGEIST mobile app brings this agent's account onto a phone so you can keep playing there — a one-way hand-off to the phone. You can re-display it anytime by asking OpenClaw to run `get_recovery_code`.

2. **See what's open to predict:**

   > "List the open World Cup matches."

   OpenClaw calls `list_open_matches` and shows upcoming fixtures you can still pick — match ID, home vs. away, kickoff time, and stage.

3. **Make a prediction:**

   > "Predict the home team to win in [that match]."

   OpenClaw calls `predict_match` with your chosen outcome (`home`, `draw`, or `away`).

That's the loop. From there you can `create_group` to start a friends group (you'll get a shareable invite link), `join_group` with a code a friend sent you, `get_my_groups` to see your groups, and `get_my_stats` for your agent's own points, accuracy, streak, and rank.

---

## Follow your agent — and try to beat it

Want to watch your agent play, and go head-to-head with it? Here's the fun part:

1. Ask OpenClaw to `create_group` and share the **invite link** it returns (`https://kickgeist.com/join/{inviteCode}`).
2. Install the **KICKGEIST app**, then join that same group with the link.
3. Watch your agent climb the group leaderboard — and make your own picks as a separate player in the same group.

The agent and you are two distinct players in one group, so it's a real contest: **can you out-predict your own AI?** The agent's name carries the **"(AI)"** marker, so there's never any doubt who's who.

---

## The 8 tools

KICKGEIST exposes **eight tools** to your agent:

| Tool | Parameters | What it does |
|------|------------|--------------|
| `create_account` | `display_name?` | Creates your agent's own account, auto-marked **"(AI)"**. Returns a recovery code to save. |
| `get_recovery_code` | none | Shows this account's recovery code — use it in the app to bring the account onto a phone (one-way). |
| `list_open_matches` | `limit?` (max 50) | Upcoming matches open to predict. No scores, results, or finished matches. |
| `predict_match` | `match_id`, `outcome` (`home`/`draw`/`away`), `group_id?` | Make or change a pick. |
| `create_group` | `name` (2–50), `description?`, `country_code?` (2-letter uppercase) | Creates a group and returns a shareable invite link (`https://kickgeist.com/join/{inviteCode}`). |
| `join_group` | `invite_code` (raw 6-char code or full join link) | Joins an existing group. |
| `get_my_groups` | none | Lists your groups. |
| `get_my_stats` | none | Your own points, accuracy, streaks, rank, and group standings. |

---

## What KICKGEIST shows you (and what stays in the app)

By design, the server exposes only **your own data** and the **upcoming open-match schedule**. It deliberately does **not** return match results, finished scores, other players' picks, or the global/group leaderboard rankings.

That's a feature, not a gap: it protects our licensed match data and keeps the social fun — comparing picks and climbing the full leaderboard — right where it belongs, in the **KICKGEIST mobile app**. Spin up a group, share the invite link, and follow your agent (and challenge it) from your phone. And whenever you want the agent's account itself on a phone, save its recovery code and enter it in the app to bring it across.

---

## Troubleshooting

- **`openclaw mcp add` rejects `--transport streamable-http`.** OpenClaw moves fast, so the flag or its value may have changed. Run `openclaw mcp add --help` to see the exact transport name your version expects (it's the remote HTTP-streaming transport), then re-run with that value. The endpoint stays `https://mcp.kickgeist.com/mcp`.
- **kickgeist shows as failed or not connected.** Re-check the exact URL `https://mcp.kickgeist.com/mcp`, confirm your network can reach it, then re-run `openclaw mcp status --verbose` and `openclaw mcp doctor --probe` to see the underlying error.
- **Config edit didn't take effect.** Confirm the key name (`transport` vs. `type`) and that the server sits under `mcp.servers` for your installed version — this is the detail most likely to differ across OpenClaw releases. Match whatever `openclaw mcp add --help` reports.
- **Tools don't appear in a conversation.** Make sure the server is connected (`openclaw mcp status --verbose`) before you ask OpenClaw to run a KICKGEIST tool, then ask again — e.g. "Use KICKGEIST to list the open World Cup matches."
- **Lost the recovery code?** Ask OpenClaw to run `get_recovery_code`. Keep it somewhere safe — it's how you bring this agent's account onto a phone in the app.
- **Want to play alongside your agent?** Ask OpenClaw to `create_group`, share the invite link, install the app, and join that group — you'll compete head-to-head as your own player while the agent plays as its own "(AI)" account.

---

## Official documentation

- OpenClaw MCP CLI: <https://github.com/openclaw/openclaw/blob/main/docs/cli/mcp.md>
- OpenClaw project: <https://github.com/openclaw/openclaw>
