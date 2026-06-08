# Play KICKGEIST in Cursor

Connect Cursor to **KICKGEIST — World Cup Predictions** and play the group-first, zero-money World Cup 2026 prediction game without leaving your editor. Pick match outcomes, start a group for your friends, and track your own stats — all through Cursor's agent.

KICKGEIST is **free**, AdMob-funded, with no in-app purchases and no subscriptions. You predict the **outcome** of each match — home win, draw, or away win — and climb the leaderboard with your friends.

The KICKGEIST server is **authless** — there is no OAuth and no login to set up. You just add the URL, then ask Cursor to create your account. Your KICKGEIST identity comes from a **recovery code** the server hands you, which also unlocks the same account in the mobile app.

**Endpoint (copy this):**

```
https://mcp.kickgeist.com/mcp
```

---

## Prerequisites

- A recent version of **Cursor** with MCP support (Settings → **Tools & Integrations** / **MCP**). Any current Cursor build qualifies — no special plan tier is required to add a custom MCP server.
- Network access to `https://mcp.kickgeist.com/mcp`.

> No OAuth required. KICKGEIST is authless — there are no credentials to paste, no login flow to complete.

---

## Option A — One-click deep link (fastest)

Open this link and Cursor will prompt you to install the **kickgeist** server:

```
cursor://anysphere.cursor-deeplink/mcp/install?name=kickgeist&config=eyJ1cmwiOiJodHRwczovL21jcC5raWNrZ2Vpc3QuY29tL21jcCJ9
```

Steps:

1. Click the deep link above (or paste it into your browser's address bar and press Enter).
2. Your browser asks for permission to open **Cursor** — allow it.
3. Cursor shows an install prompt for the **kickgeist** server. Click **Install** (or **Add**) to confirm.
4. Done — the server is added with the endpoint baked in.

> **How the link is built (for the curious):** the `config` value is the base64 encoding of the server's transport config object, exactly as it appears in `mcp.json`:
>
> ```json
> {"url":"https://mcp.kickgeist.com/mcp"}
> ```
>
> Base64 of that object is `eyJ1cmwiOiJodHRwczovL21jcC5raWNrZ2Vpc3QuY29tL21jcCJ9`, which is what you see after `&config=` in the link.

---

## Option B — Edit `mcp.json` by hand

Prefer to wire it up yourself? Add KICKGEIST to your Cursor MCP config.

- **Global (all projects):** `~/.cursor/mcp.json`
- **Per project:** `.cursor/mcp.json` in the project root

1. Open (or create) the file.
2. Add the **kickgeist** server under `mcpServers`:

   ```json
   {
     "mcpServers": {
       "kickgeist": {
         "url": "https://mcp.kickgeist.com/mcp"
       }
     }
   }
   ```

   If you already have other servers, just add the `kickgeist` entry alongside them inside `mcpServers` (mind the commas).

3. Save the file. Cursor picks up the change automatically; if it doesn't appear, reload the window or toggle the server on under **Settings → MCP**.

---

## Verify it works

Open Cursor's chat in **Agent** mode and kick off a match in plain language:

1. **Create your account:**

   > "Use KICKGEIST to create my account."

   Cursor calls `create_account` and returns a **recovery code** plus a welcome message and app link.

   **Save your recovery code.** It's the key to your account — enter it in the KICKGEIST mobile app (or use `link_account` from another agent) to reach the very same account. You can re-display it anytime by asking Cursor to run `get_recovery_code`.

2. **See what's open to predict:**

   > "List the open World Cup matches."

   Cursor calls `list_open_matches` and shows upcoming fixtures you can still pick — match ID, home vs. away, kickoff time, and stage.

3. **Make a prediction:**

   > "Predict the home team to win in [that match]."

   Cursor calls `predict_match` with your chosen outcome (`home`, `draw`, or `away`).

That's the loop. From there you can `create_group` to start a friends group (you'll get a shareable invite link), `join_group` with a code a friend sent you, `get_my_groups` to see your groups, and `get_my_stats` for your own points, accuracy, streak, and rank.

> Cursor may ask you to approve the first tool call from a new MCP server. Approve **kickgeist** and you're playing.

---

## What KICKGEIST shows you (and what stays in the app)

By design, the server exposes only **your own data** and the **upcoming open-match schedule**. It deliberately does **not** return match results, finished scores, other players' picks, or the global/group leaderboard rankings.

That's a feature, not a gap: it protects our licensed match data and keeps the social fun — comparing picks and climbing the full leaderboard — right where it belongs, in the **KICKGEIST mobile app**. Save your recovery code, drop it into the app, and your agent picks and your phone picks are one and the same account.

---

## Troubleshooting

- **The deep link doesn't open Cursor.** Make sure Cursor is installed and has launched at least once so the `cursor://` handler is registered, then click the link again. As a fallback, use **Option B** and edit `mcp.json` directly.
- **kickgeist isn't listed under Settings → MCP.** Confirm the JSON is valid (no trailing commas, correct nesting under `mcpServers`) and that you edited the right file — `~/.cursor/mcp.json` for global, `.cursor/mcp.json` for a single project. Save, then reload the Cursor window.
- **Server shows as failed / no tools.** Re-check the exact URL `https://mcp.kickgeist.com/mcp` (note the trailing `/mcp`) and confirm your network can reach it. Toggle the server off and on under **Settings → MCP**.
- **Tools never get called in chat.** Use **Agent** mode and approve **kickgeist** when Cursor prompts to run its first tool call.
- **Don't add OAuth credentials.** KICKGEIST is authless — there are no client ID/secret fields to fill in. A plain `{"url": "https://mcp.kickgeist.com/mcp"}` entry is all it needs.
- **Lost your recovery code?** Ask Cursor to run `get_recovery_code` while the account is linked. Keep it somewhere safe — it's the only way back into the same account.
- **Want to use the account you already play on your phone?** Ask Cursor to `link_account` with that account's recovery code (find it in the app under account / recovery).

---

## Official documentation

- Cursor MCP install links: <https://cursor.com/docs/context/mcp/install-links>
