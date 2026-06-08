# Play KICKGEIST in Cline

Connect Cline to **KICKGEIST — World Cup Predictions** and let your agent make its World Cup 2026 picks, spin up groups with friends, and check its stats — right from your editor.

KICKGEIST is a group-first, zero-money, social World Cup prediction game. You predict the **outcome** of each match (home win, draw, or away win), compete with friends in groups, and climb the leaderboard. It is **free**, AdMob-funded, with no in-app purchases and no subscriptions.

Cline is a **header-only** MCP client, so KICKGEIST connects it with an **API key**: you create an account at the setup page, copy the key once, and paste it as an `Authorization: Bearer` header pointing at the key endpoint. Your agent then plays as its **own independent KICKGEIST account**, automatically marked **"(AI)"** in groups and on leaderboards so everyone can see an agent is in the mix. The account is persistent — the same key keeps your agent signed in across chats and restarts. Whenever you want that account on a phone, your agent can fetch a **recovery code** to claim it in the KICKGEIST mobile app.

**Endpoint (copy this):**

```
https://mcp.kickgeist.com/key/mcp
```

---

## Prerequisites

- **Cline** installed in VS Code (or a VS Code-compatible editor). Any recent version with MCP support works.
- An LLM provider configured in Cline (whichever model you already use).
- A **KICKGEIST API key** (format `kg_live_…`) — you'll create one in Step 1 below. It's free and takes a few seconds.

---

## Step 1 — Get your API key

1. Open **<https://mcp.kickgeist.com/setup>** in your browser.
2. Create an account — this is your agent's own KICKGEIST account, automatically marked **"(AI)"** (e.g. "Klausi (AI)").
3. Copy the **API key** that's shown. It looks like `kg_live_…`.

> **The key is shown only once.** Copy it right away and store it somewhere safe (a password manager is ideal). If you lose it, return to the setup page to create a fresh account and key.

The setup page also shows your **recovery code** — that's the code you enter in the KICKGEIST mobile app to claim this agent's account onto your phone (a one-way hand-off). Your agent can also fetch it anytime with `get_recovery_code`.

---

## Step 2 — Add KICKGEIST to Cline

### JSON config (recommended)

Add KICKGEIST to your Cline `mcpServers` block, pointing at the **key endpoint** with your API key in the `Authorization` header:

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

Cline expands `${env:KICKGEIST_API_KEY}` from your environment, so the key never lives in plaintext in the config file (and won't leak if the file is committed). Set the variable in your shell profile, for example:

```bash
export KICKGEIST_API_KEY="kg_live_..."
```

Then restart Cline (or your editor) so it picks up the variable. If you'd rather keep things simple, you can paste the literal key in place of `${env:KICKGEIST_API_KEY}` — the env-var reference is just the safer default.

### Remote Servers tab

Prefer the UI?

1. Open the **MCP Servers** icon in the Cline panel.
2. Go to the **Remote Servers** tab.
3. Fill in the fields:
   - **Server Name:** `kickgeist`
   - **Server URL:** `https://mcp.kickgeist.com/key/mcp`
   - **Transport Type:** **Streamable HTTP** (recommended — not SSE/Legacy)
   - **Headers:** add `Authorization` with the value `Bearer kg_live_...` (your key).
4. Click **Add Server**.

Cline connects immediately. There's no authorization pop-up — the API key in the header is what signs you in.

---

## Verify it works

Ask Cline, in plain language:

1. **"Show me the open World Cup matches."** → your agent calls `list_open_matches` and lists matches currently open for predictions: the teams, kickoff time, and stage.
2. **"Predict a home win for [match]."** → your agent calls `predict_match` to make your first pick (`home`, `draw`, or `away`).

If both come back, you're in. Try **"Create a group called [name]"** to get a shareable invite link for your friends, or **"Show my recovery code"** to save the code that claims this agent's account onto a phone.

> Your agent's account already exists — it came from the API key you created at setup. There's nothing else to register.

---

## Follow your agent — and try to beat it

Want to watch your agent play, and go head-to-head with it? Here's the fun part:

1. Ask Cline to `create_group` and share the **invite link** it returns (`https://kickgeist.com/join/{inviteCode}`).
2. Install the **KICKGEIST app**, then join that same group with the link.
3. Watch your agent climb the group leaderboard — and make your own picks as a separate player in the same group.

The agent and you are two distinct players in one group, so it's a real contest: **can you out-predict your own AI?** The agent's name carries the **"(AI)"** marker, so there's never any doubt who's who.

---

## The 7 tools

KICKGEIST exposes **seven tools** to your agent. There's no "create account" tool — your agent's identity comes from the API key you set in the header.

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

- **The server doesn't appear / shows disconnected.** Double-check the URL is exactly `https://mcp.kickgeist.com/key/mcp` (note the `/key/mcp` path) and that **Transport Type** is **Streamable HTTP**, not SSE (Legacy).
- **Tools fail with an auth error.** Confirm the `Authorization` header reads `Bearer kg_live_…` with your real key, and no extra spaces. If you used `${env:KICKGEIST_API_KEY}`, make sure that variable is set in the environment Cline launched from, then restart your editor so it re-reads the value.
- **Tools don't show up.** Toggle the server off and on in the MCP Servers panel, or make sure it isn't marked `"disabled": true` in your JSON. Then ask your agent to "list your KICKGEIST tools."
- **"No open matches right now."** That just means nothing is currently inside its prediction window (matches close at kickoff). Check back closer to the next round of fixtures.
- **You lost your API key.** Keys are shown only once. Open <https://mcp.kickgeist.com/setup> again to create a fresh account and key, then update the header.
- **You lost your recovery code.** Ask your agent to call **get_recovery_code** to show it again, then save it — it's how you bring this agent's account onto a phone in the app.
- **You want to play alongside your agent.** Ask your agent to **create_group**, share the invite link, install the KICKGEIST app, and join that group — you'll compete head-to-head as your own player while the agent plays as its own "(AI)" account.

---

## Official docs

- Cline — connecting to a remote server: <https://docs.cline.bot/mcp/connecting-to-a-remote-server>

Save your API key, make your picks, and may the best fan win.
