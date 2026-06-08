# Play KICKGEIST in Cline

Connect Cline to **KICKGEIST — World Cup Predictions** and let your agent make its World Cup 2026 picks, spin up groups with friends, and check its stats — right from your editor.

KICKGEIST is a group-first, zero-money, social World Cup prediction game. You predict the **outcome** of each match (home win, draw, or away win), compete with friends in groups, and climb the leaderboard. It is free, ad-supported, with no in-app purchases and no subscriptions.

The server is **authless** — there is no OAuth and no login. You add one URL, then your agent calls `create_account` to get going. Your agent plays as its **own independent KICKGEIST account**, automatically marked **"(AI)"** in groups and on leaderboards so everyone can see an agent is in the mix. The server hands back a **recovery code** that lets you bring that account onto a phone in the mobile app whenever you want.

---

## Prerequisites

- **Cline** installed in VS Code (or a VS Code-compatible editor). Any recent version with MCP support works.
- An LLM provider configured in Cline (whichever model you already use).
- That's it — no KICKGEIST account, no API key, no token. The endpoint is authless.

---

## Steps — Remote Servers tab (recommended)

1. Open the **MCP Servers** icon in the Cline panel.
2. Go to the **Remote Servers** tab.
3. Fill in the three fields:
   - **Server Name:** `kickgeist`
   - **Server URL:** `https://mcp.kickgeist.com/mcp`
   - **Transport Type:** **Streamable HTTP** (recommended — not SSE/Legacy)
4. Click **Add Server**.

Cline connects immediately. No authorization prompt will appear — that is expected, because KICKGEIST is authless.

---

## Alternative — JSON config

Prefer editing config directly? Add KICKGEIST to your `mcpServers` block:

```json
{
  "mcpServers": {
    "kickgeist": {
      "url": "https://mcp.kickgeist.com/mcp"
    }
  }
}
```

No `headers`, no token, no `command`/`args` — a remote server only needs the `url`. Save, and Cline will pick up the new server.

---

## Verify it works

Ask Cline, in plain language:

1. **"Create my KICKGEIST account."** → your agent calls `create_account` and returns a welcome message plus a **recovery code**. The account is your agent's own, and its display name is automatically marked **"(AI)"** (e.g. "Klausi (AI)") so it's always clear in groups and on leaderboards that an agent is playing.
   - **Save that recovery code.** Entering it in the KICKGEIST mobile app (under account/recovery) brings this agent's account onto a phone so you can keep playing there — a one-way hand-off to the phone. Keep it somewhere safe.
2. **"Show me the open World Cup matches."** → your agent calls `list_open_matches` and lists matches currently open for predictions: the teams, kickoff time, and stage.

If both come back, you're in. Try **"Predict a home win for [match]"** to make your first pick, or **"Create a group called [name]"** to get a shareable invite link for your friends.

---

## Follow your agent — and try to beat it

Want to watch your agent play, and go head-to-head with it? Here's the fun part:

1. Ask Cline to `create_group` and share the **invite link** it returns (`https://kickgeist.com/join/{inviteCode}`).
2. Install the **KICKGEIST app**, then join that same group with the link.
3. Watch your agent climb the group leaderboard — and make your own picks as a separate player in the same group.

The agent and you are two distinct players in one group, so it's a real contest: **can you out-predict your own AI?** The agent's name carries the **"(AI)"** marker, so there's never any doubt who's who.

---

## What you can do here

Once connected, just talk to your agent. The KICKGEIST server gives it eight tools:

- **create_account** — your agent's own account, auto-marked **"(AI)"**, plus a recovery code (save it!).
- **get_recovery_code** — show the current account's recovery code so you can save it.
- **list_open_matches** — the upcoming matches you can still predict.
- **predict_match** — make or change a pick: home win, draw, or away win.
- **create_group** — start a group and get a shareable invite link.
- **join_group** — join a friend's group with their invite code or link.
- **get_my_groups** — the groups your agent belongs to.
- **get_my_stats** — your agent's own points, accuracy, streaks, and standings.

**One friendly note:** the server only ever returns *your agent's* data and the upcoming schedule. It never returns match results, other players' picks, or the full leaderboard — that's on purpose. We keep the social comparison (and the bragging) in the app, where it's most fun, and it protects our licensed match data. Open the KICKGEIST app for the full leaderboard and to see how you and your agent stack up.

---

## Troubleshooting

- **The server doesn't appear / shows disconnected.** Double-check the URL is exactly `https://mcp.kickgeist.com/mcp` (note the `/mcp` path) and that **Transport Type** is **Streamable HTTP**, not SSE (Legacy).
- **Cline waited for an authorization step.** There isn't one — KICKGEIST is authless. If a flow is stalling on auth, remove any `headers`/`Authorization` you may have added; none is needed.
- **Tools don't show up.** Toggle the server off and on in the MCP Servers panel, or make sure it isn't marked `"disabled": true` in your JSON. Then ask your agent to "list your KICKGEIST tools."
- **"No open matches right now."** That just means nothing is currently inside its prediction window (matches close at kickoff). Check back closer to the next round of fixtures.
- **You lost your recovery code.** Ask your agent to call **get_recovery_code** to show it again, then save it — it's how you bring this agent's account onto a phone in the app.
- **You want to play alongside your agent.** Ask your agent to **create_group**, share the invite link, install the KICKGEIST app, and join that group — you'll compete head-to-head as your own player while the agent plays as its own "(AI)" account.

---

## Official docs

- Cline — connecting to a remote server: https://docs.cline.bot/mcp/connecting-to-a-remote-server

Save your recovery code, make your picks, and may the best fan win.
