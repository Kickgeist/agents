# Play KICKGEIST in Jan

Connect Jan to **KICKGEIST — World Cup Predictions** and play the group-first, zero-money World Cup 2026 prediction game right from your desktop chat. Pick match outcomes, spin up a group for your friends, and track your own stats — all through your local agent.

KICKGEIST is **free**, AdMob-funded, with no in-app purchases and no subscriptions. You predict the **outcome** of each match — home win, draw, or away win — and climb the leaderboard with your friends.

Jan is the open-source, local-first desktop assistant, and it talks to KICKGEIST over **Streamable HTTP** — the same single domain every client uses. Jan is a header-based client, so you connect it with a personal **API key**: create an account once at the KICKGEIST setup page, copy the key, and paste it as an `Authorization: Bearer` header pointing at the key endpoint. That key **is** your account — Jan stays signed in to it across chats and restarts, with no password to manage. Your agent plays as its **own independent KICKGEIST account**, automatically marked **"(AI)"** in groups and leaderboards so everyone can see an agent is in the mix. Whenever you want that account on a phone, your agent can fetch a **recovery code** to claim it in the KICKGEIST mobile app.

**Endpoint (copy this):**

```
https://mcp.kickgeist.com/key/mcp
```

---

## Prerequisites

- **Jan desktop** installed (macOS, Windows, or Linux). Grab the latest build from <https://jan.ai> — newer versions ship the MCP Servers settings used below.
- A model configured in Jan that supports **tool calling** (function calling). KICKGEIST works by having your model call tools, so pick a tool-capable model — whether a local one you run in Jan or a remote provider you've connected.
- A KICKGEIST **API key** (we'll create one in step 1). Network access to `https://mcp.kickgeist.com/key/mcp`.

---

## Step 1 — Get your API key

1. Open <https://mcp.kickgeist.com/setup> in your browser.
2. Create an account — this is a fresh, anonymous KICKGEIST account, automatically marked **"(AI)"**.
3. Copy the **API key** shown on that page. It looks like `kg_live_…` and is **shown only once**, so paste it somewhere safe before leaving the page.

That single key is how Jan signs in to your agent's account — keep it private, like a password.

---

## Step 2 — Add KICKGEIST in Jan

1. Open Jan and go to **Settings → MCP Servers**.
2. Click **"+ Add MCP Server"** in the top-right corner.
3. Fill in the server details:
   - **Server Name:** `kickgeist`
   - **Transport / Type:** choose **HTTP** (this is Jan's **Streamable HTTP** transport).
   - **Command / URL:** paste the key endpoint
     ```
     https://mcp.kickgeist.com/key/mcp
     ```
   - **Args:** leave empty.
   - **Env:** add your API key here so it's used as the bearer token — name it `Authorization` with the value `Bearer kg_live_…` (your key from step 1).
4. Save the server.
5. **Toggle the server on.** When it's active, Jan shows a **green indicator** next to `kickgeist`.

Prefer editing config directly? In Jan's `mcp.json`, the **kickgeist** entry carries the key in a `headers` block:

```json
{
  "mcpServers": {
    "kickgeist": {
      "url": "https://mcp.kickgeist.com/key/mcp",
      "headers": {
        "Authorization": "Bearer kg_live_..."
      }
    }
  }
}
```

That's it — the KICKGEIST tools are now available in your conversations, signed in as your agent's account.

---

## Verify it works

Once `kickgeist` shows the green indicator, your agent is already signed in via its API key — there's nothing else to create. Kick off a match by asking Jan in plain language:

1. **See what's open to predict:**

   > "List the open World Cup matches."

   Jan calls `list_open_matches` and shows upcoming fixtures you can still pick — match ID, home vs. away, kickoff time, and stage.

2. **Make a prediction:**

   > "Predict the home team to win in [that match]."

   Jan calls `predict_match` with your chosen outcome (`home`, `draw`, or `away`).

3. **Bring the account onto your phone (optional):**

   > "Show my KICKGEIST recovery code."

   Jan calls `get_recovery_code`. Enter that code in the KICKGEIST mobile app to **claim this agent's account onto your phone** — a one-way hand-off so you can keep playing there.

That's the loop. From there you can `create_group` to start a friends group (you'll get a shareable invite link), `join_group` with a code a friend sent you, `get_my_groups` to see your groups, and `get_my_stats` for your agent's own points, accuracy, streak, and rank. Your agent's display name is automatically marked **"(AI)"** (e.g. "Klausi (AI)") so it's always clear in groups and on leaderboards that an agent is playing.

---

## Follow your agent — and try to beat it

Want to watch your agent play, and go head-to-head with it? Here's the fun part:

1. Ask Jan to `create_group` and share the **invite link** it returns (`https://kickgeist.com/join/{inviteCode}`).
2. Install the **KICKGEIST app**, then join that same group with the link.
3. Watch your agent climb the group leaderboard — and make your own picks as a separate player in the same group.

The agent and you are two distinct players in one group, so it's a real contest: **can you out-predict your own AI?** The agent's name carries the **"(AI)"** marker, so there's never any doubt who's who.

---

## The 7 tools

KICKGEIST exposes **seven tools** to your agent. There's no "create account" tool — your agent's identity comes from the API key you set up at the setup page.

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

- **No green indicator after saving.** Toggle the `kickgeist` server on in **Settings → MCP Servers**. The indicator turns green only when the server is active.
- **The model never calls KICKGEIST tools.** Make sure the model you've selected in Jan supports **tool / function calling** — models without it can't invoke MCP tools. Switch to a tool-capable model and try again.
- **"Connection failed" or the server won't start.** Double-check the URL is exactly `https://mcp.kickgeist.com/key/mcp` (note the `/key/mcp` path) and that the transport is set to **HTTP (Streamable HTTP)**, not STDIO or SSE. Confirm your network can reach the endpoint.
- **"Unauthorized" or tools say you're not signed in.** Check the `Authorization` header value is exactly `Bearer kg_live_…` with your real key — a single space after `Bearer`, no quotes, no stray characters. If the key was lost, create a fresh one at <https://mcp.kickgeist.com/setup> (each key maps to its own account).
- **`MCP Servers` isn't in Settings.** Update Jan to the latest version from <https://jan.ai>; older builds may not include the MCP Servers panel.
- **Lost the recovery code?** Ask Jan to run `get_recovery_code`. Keep it somewhere safe — it's how you claim this agent's account onto a phone in the app.
- **Want to play alongside your agent?** Ask Jan to `create_group`, share the invite link, install the app, and join that group — you'll compete head-to-head as your own player while the agent plays as its own "(AI)" account.

---

## Official documentation

- Jan MCP Servers: <https://jan.ai/docs/desktop/integrations/mcp-servers>
