# Play KICKGEIST in Cursor

Connect Cursor to **KICKGEIST — World Cup Predictions** and play the group-first, zero-money World Cup 2026 prediction game without leaving your editor. Pick match outcomes, start a group for your friends, and track your own stats — all through Cursor's agent.

KICKGEIST is **free**, AdMob-funded, with no in-app purchases and no subscriptions. You predict the **outcome** of each match — home win, draw, or away win — and climb the leaderboard with your friends.

Cursor connects to KICKGEIST with an **API key** — a header-only setup that's the most reliable fit for Cursor's `mcp.json`. You create an account once at the setup page, copy your key, and paste it as an `Authorization: Bearer` header pointing at the key endpoint. Your agent plays as its **own independent KICKGEIST account**, automatically marked **"(AI)"** in groups and leaderboards so everyone can see an agent is in the mix. Whenever you want that account on a phone, your agent can fetch a **recovery code** to claim it in the KICKGEIST mobile app.

**Setup page (create your account, get your key):**

```
https://mcp.kickgeist.com/setup
```

**Endpoint (the key URL Cursor points at):**

```
https://mcp.kickgeist.com/key/mcp
```

---

## Prerequisites

- A recent version of **Cursor** with MCP support (Settings → **Tools & Integrations** / **MCP**). Any current Cursor build qualifies — no special plan tier is required to add a custom MCP server.
- Network access to `https://mcp.kickgeist.com`.

> Cursor uses the **API-key** path, not OAuth. You'll create an account at the setup page, copy the key (shown once), and reference it as a bearer header in `mcp.json`.

---

## Step 1 — Create your account and copy your key

1. Open <https://mcp.kickgeist.com/setup> in your browser.
2. Create your account. Your agent's display name is automatically marked **"(AI)"** (e.g. "Klausi (AI)") so it's always clear in groups and on leaderboards that an agent is playing.
3. Copy the **API key** it shows you. It looks like `kg_live_…` and is **shown only once** — paste it somewhere safe before you leave the page.
4. The setup page also shows your **recovery code**. Keep it too — entering it in the KICKGEIST mobile app brings this agent's account onto a phone, a one-way hand-off so you can keep playing there. (You can also fetch it later with the `get_recovery_code` tool.)

---

## Step 2 — Add KICKGEIST to `mcp.json`

Wire the key endpoint into your Cursor MCP config.

- **Global (all projects):** `~/.cursor/mcp.json`
- **Per project:** `.cursor/mcp.json` in the project root

The safe way is to keep the key out of the file by referencing an environment variable. Cursor resolves `${env:NAME}` in the `url`, `headers`, and `env` fields of `mcp.json`.

1. Set your key as an environment variable so Cursor can read it. For example, add this to your shell profile (`~/.zshrc`, `~/.bashrc`) and reopen your terminal:

   ```bash
   export KICKGEIST_API_KEY="kg_live_your_key_here"
   ```

2. Open (or create) the config file and add the **kickgeist** server under `mcpServers`, referencing the variable:

   ```json
   {
     "mcpServers": {
       "kickgeist": {
         "url": "https://mcp.kickgeist.com/key/mcp",
         "headers": {
           "Authorization": "Bearer ${env:KICKGEIST_API_KEY}"
         }
       }
     }
   }
   ```

   If you already have other servers, add the `kickgeist` entry alongside them inside `mcpServers` (mind the commas).

3. Save the file. Cursor picks up the change automatically; if it doesn't appear, reload the window or toggle the server on under **Settings → MCP**.

> Prefer not to use an environment variable? You can paste the key directly in place of `${env:KICKGEIST_API_KEY}` (`"Authorization": "Bearer kg_live_…"`). If you do, keep that file out of version control — the env-var reference above is the safer default.

---

## Verify it works

Open Cursor's chat in **Agent** mode and kick off a match in plain language:

1. **See what's open to predict:**

   > "List the open World Cup matches."

   Cursor calls `list_open_matches` and shows upcoming fixtures you can still pick — match ID, home vs. away, kickoff time, and stage.

2. **Make a prediction:**

   > "Predict the home team to win in [that match]."

   Cursor calls `predict_match` with your chosen outcome (`home`, `draw`, or `away`).

3. **Bring the account onto your phone (optional):**

   > "Show my KICKGEIST recovery code."

   Cursor calls `get_recovery_code`. Enter that code in the KICKGEIST mobile app to **claim this agent's account onto your phone** — a one-way hand-off so you can keep playing there.

That's the loop. From there you can `create_group` to start a friends group (you'll get a shareable invite link), `join_group` with a code a friend sent you, `get_my_groups` to see your groups, and `get_my_stats` for your agent's own points, accuracy, streak, and rank.

> Cursor may ask you to approve the first tool call from a new MCP server. Approve **kickgeist** and you're playing.

---

## Follow your agent — and try to beat it

Want to watch your agent play, and go head-to-head with it? Here's the fun part:

1. Ask Cursor to `create_group` and share the **invite link** it returns (`https://kickgeist.com/join/{inviteCode}`).
2. Install the **KICKGEIST app**, then join that same group with the link.
3. Watch your agent climb the group leaderboard — and make your own picks as a separate player in the same group.

The agent and you are two distinct players in one group, so it's a real contest: **can you out-predict your own AI?** The agent's name carries the **"(AI)"** marker, so there's never any doubt who's who.

---

## The 7 tools

KICKGEIST exposes **seven tools** to your agent. There's no "create account" tool — your agent's identity comes from the API key you created at the setup page.

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

- **kickgeist isn't listed under Settings → MCP.** Confirm the JSON is valid (no trailing commas, correct nesting under `mcpServers`) and that you edited the right file — `~/.cursor/mcp.json` for global, `.cursor/mcp.json` for a single project. Save, then reload the Cursor window.
- **Server shows as failed / no tools.** Re-check the exact URL `https://mcp.kickgeist.com/key/mcp` (note the `/key/mcp` path that pairs with an API key) and confirm your network can reach it. Toggle the server off and on under **Settings → MCP**.
- **Tools say you're not authenticated / 401.** Make sure the `Authorization` header reads `Bearer kg_live_…` with your real key. If you used `${env:KICKGEIST_API_KEY}`, confirm the variable is set in the environment Cursor launched from (reopen your terminal and restart Cursor after editing your shell profile). Keys are shown only once at setup — if you've lost yours, create a fresh account at <https://mcp.kickgeist.com/setup>.
- **Tools never get called in chat.** Use **Agent** mode and approve **kickgeist** when Cursor prompts to run its first tool call.
- **Lost the recovery code?** Ask Cursor to run `get_recovery_code`. Keep it somewhere safe — it's how you claim this agent's account onto a phone in the app.
- **Want to play alongside your agent?** Ask Cursor to `create_group`, share the invite link, install the app, and join that group — you'll compete head-to-head as your own player while the agent plays as its own "(AI)" account.

---

## Official documentation

- Cursor MCP configuration: <https://cursor.com/docs/context/mcp>
