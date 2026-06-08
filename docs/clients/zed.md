# Play KICKGEIST in Zed

Connect Zed's AI assistant to **KICKGEIST — World Cup Predictions** and play the group-first, zero-money World Cup 2026 prediction game without leaving your editor. Pick match outcomes, spin up a group for your friends, and track your own stats — all through Zed's Agent Panel.

KICKGEIST is **free**, AdMob-funded, with no in-app purchases and no subscriptions. You predict the **outcome** of each match — home win, draw, or away win — and climb the leaderboard with your friends.

The KICKGEIST server is **authless** — there is no OAuth and no login to set up. You just add the URL, then ask the agent to create your account. Your KICKGEIST identity comes from a **recovery code** the server hands you, which also unlocks the same account in the mobile app.

**Endpoint (copy this):**

```
https://mcp.kickgeist.com/mcp
```

---

## Prerequisites

- A recent build of **Zed** with the **Agent Panel** (the AI assistant). Zed is free on macOS, Linux, and Windows.
- Network access to `https://mcp.kickgeist.com/mcp`.
- No KICKGEIST account, API key, or token needed up front — the server is authless, and you create your account on the first call.

> In Zed, remote MCP servers are configured under the **`context_servers`** key in `settings.json`. KICKGEIST is reachable by URL alone — no `command`, no `headers`, no OAuth.

---

## Steps

1. Open your Zed `settings.json`:
   - Use the Command Palette (`Cmd-Shift-P` on macOS, `Ctrl-Shift-P` on Linux/Windows) and run **`zed: open settings`**, **or**
   - From the menu, choose **Zed → Settings → Open Settings** (macOS) / **File → Settings → Open Settings**.

2. Add KICKGEIST under the `context_servers` key. If you already have other entries in `settings.json`, merge this block in rather than replacing the file:

   ```json
   {
     "context_servers": {
       "kickgeist": {
         "url": "https://mcp.kickgeist.com/mcp"
       }
     }
   }
   ```

3. Save the file. Zed picks up the new server automatically.

4. Confirm it loaded: open the **Agent Panel**, then open its **Settings** view — run **`agent: open settings`** from the Command Palette, or use the menu in the top-right of the Agent Panel. You should see **kickgeist** listed with a **green indicator dot**, meaning the server is active and connected.

> Tip: you can also add the server from that same Agent Panel settings view via the **Add Custom Server** button — pasting the URL there writes the equivalent `context_servers` entry for you.

---

## Verify it works

In the **Agent Panel**, talk to the assistant in plain language:

1. **Create your account:**

   > "Use KICKGEIST to create my account."

   The agent calls `create_account` and returns a **recovery code** plus a welcome message and app link.

   **Save your recovery code.** It's the key to your account — enter it in the KICKGEIST mobile app (or use `link_account` from another agent) to reach the very same account. You can re-display it anytime by asking the agent to run `get_recovery_code`.

2. **See what's open to predict:**

   > "List the open World Cup matches."

   The agent calls `list_open_matches` and shows upcoming fixtures you can still pick — match ID, home vs. away, kickoff time, and stage.

3. **Make a prediction:**

   > "Predict the home team to win in [that match]."

   The agent calls `predict_match` with your chosen outcome (`home`, `draw`, or `away`).

That's the loop. From there you can `create_group` to start a friends group (you'll get a shareable invite link), `join_group` with a code a friend sent you, `get_my_groups` to see your groups, and `get_my_stats` for your own points, accuracy, streak, and rank.

---

## What KICKGEIST shows you (and what stays in the app)

By design, the server exposes only **your own data** and the **upcoming open-match schedule**. It deliberately does **not** return match results, finished scores, other players' picks, or the global/group leaderboard rankings.

That's a feature, not a gap: it protects our licensed match data and keeps the social fun — comparing picks and climbing the full leaderboard — right where it belongs, in the **KICKGEIST mobile app**. Save your recovery code, drop it into the app, and your agent picks and your phone picks are one and the same account.

---

## Troubleshooting

- **kickgeist isn't listed in the Agent Panel settings.** Re-open `settings.json` and confirm the entry sits under the top-level `context_servers` key, the JSON is valid (no trailing commas, all braces closed), and the server name `kickgeist` is spelled correctly. Save again — Zed reloads on save.
- **The indicator dot isn't green.** Hover the server in the Agent Panel settings to see its status. Double-check the URL is exactly `https://mcp.kickgeist.com/mcp` and that your network can reach it, then save the file again to retry the connection.
- **Tools don't run in a thread.** Make sure you're chatting in the **Agent Panel** (the AI assistant), and that kickgeist shows as active in its settings view before you ask for a prediction.
- **Don't add a `command`, `headers`, or OAuth.** KICKGEIST is authless and remote — a single `url` is all it needs. Adding a `command` would tell Zed to launch a local process, which won't work here.
- **Lost your recovery code?** Ask the agent to run `get_recovery_code` while the account is linked. Keep it somewhere safe — it's the only way back into the same account.
- **Want to use the account you already play on your phone?** Ask the agent to `link_account` with that account's recovery code (find it in the app under account / recovery).

---

## Official documentation

- Zed MCP / context servers: <https://zed.dev/docs/ai/mcp>
