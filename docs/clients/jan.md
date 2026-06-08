# Play KICKGEIST in Jan

Connect Jan to **KICKGEIST — World Cup Predictions** and play the group-first, zero-money World Cup 2026 prediction game right from your desktop chat. Pick match outcomes, spin up a group for your friends, and track your own stats — all through your local agent.

KICKGEIST is **free**, AdMob-funded, with no in-app purchases and no subscriptions. You predict the **outcome** of each match — home win, draw, or away win — and climb the leaderboard with your friends.

Jan is the open-source, local-first desktop assistant, and it talks to KICKGEIST over **Streamable HTTP** — the same single endpoint every client uses. The server is **authless**: no OAuth, no login. You add the URL, then ask Jan to create your account. Your KICKGEIST identity comes from a **recovery code** the server hands you, which also unlocks the same account in the mobile app.

**Endpoint (copy this):**

```
https://mcp.kickgeist.com/mcp
```

---

## Prerequisites

- **Jan desktop** installed (macOS, Windows, or Linux). Grab the latest build from <https://jan.ai> — newer versions ship the MCP Servers settings used below.
- A model configured in Jan that supports **tool calling** (function calling). KICKGEIST works by having your model call tools, so pick a tool-capable model — whether a local one you run in Jan or a remote provider you've connected.
- Network access to `https://mcp.kickgeist.com/mcp`.

> No OAuth required. KICKGEIST is authless — there's nothing to put in **Args** or **Env**, just the URL.

---

## Steps

1. Open Jan and go to **Settings → MCP Servers**.
2. Click **"+ Add MCP Server"** in the top-right corner.
3. Fill in the server details:
   - **Server Name:** `kickgeist`
   - **Transport / Type:** choose **HTTP** (this is Jan's **Streamable HTTP** transport).
   - **Command / URL:** paste the endpoint
     ```
     https://mcp.kickgeist.com/mcp
     ```
   - **Args:** leave empty.
   - **Env:** leave empty — KICKGEIST needs no API key and no OAuth.
4. Save the server.
5. **Toggle the server on.** When it's active, Jan shows a **green indicator** next to `kickgeist`.

That's it — the KICKGEIST tools are now available in your conversations.

---

## Verify it works

Once `kickgeist` shows the green indicator, kick off a match by asking Jan in plain language:

1. **Create your account:**

   > "Use KICKGEIST to create my account."

   Jan calls `create_account` and returns a **recovery code** plus a welcome message and an app link.

   **Save your recovery code.** It's the key to your account — enter it in the KICKGEIST mobile app (or use `link_account` from another agent) to reach the very same account. You can re-display it anytime by asking Jan to run `get_recovery_code`.

2. **See what's open to predict:**

   > "List the open World Cup matches."

   Jan calls `list_open_matches` and shows upcoming fixtures you can still pick — match ID, home vs. away, kickoff time, and stage.

3. **Make a prediction:**

   > "Predict the home team to win in [that match]."

   Jan calls `predict_match` with your chosen outcome (`home`, `draw`, or `away`).

That's the loop. From there you can `create_group` to start a friends group (you'll get a shareable invite link), `join_group` with a code a friend sent you, `get_my_groups` to see your groups, and `get_my_stats` for your own points, accuracy, streak, and rank.

---

## What KICKGEIST shows you (and what stays in the app)

By design, the server exposes only **your own data** and the **upcoming open-match schedule**. It deliberately does **not** return match results, finished scores, other players' picks, or the global/group leaderboard rankings.

That's a feature, not a gap: it protects our licensed match data and keeps the social fun — comparing picks and climbing the full leaderboard — right where it belongs, in the **KICKGEIST mobile app**. Save your recovery code, drop it into the app, and your agent picks and your phone picks are one and the same account.

---

## Troubleshooting

- **No green indicator after saving.** Toggle the `kickgeist` server on in **Settings → MCP Servers**. The indicator turns green only when the server is active.
- **The model never calls KICKGEIST tools.** Make sure the model you've selected in Jan supports **tool / function calling** — models without it can't invoke MCP tools. Switch to a tool-capable model and try again.
- **"Connection failed" or the server won't start.** Double-check the URL is exactly `https://mcp.kickgeist.com/mcp` and that the transport is set to **HTTP (Streamable HTTP)**, not STDIO or SSE. Confirm your network can reach the endpoint.
- **Don't add a key or OAuth.** KICKGEIST is authless — leaving **Args** and **Env** empty is correct.
- **`MCP Servers` isn't in Settings.** Update Jan to the latest version from <https://jan.ai>; older builds may not include the MCP Servers panel.
- **Lost your recovery code?** Ask Jan to run `get_recovery_code` while the account is linked. Keep it somewhere safe — it's the only way back into the same account.
- **Want to use the account you already play on your phone?** Ask Jan to `link_account` with that account's recovery code (find it in the app under account / recovery).

---

## Official documentation

- Jan MCP Servers: <https://jan.ai/docs/desktop/integrations/mcp-servers>
