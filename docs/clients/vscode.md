# Play KICKGEIST in VS Code (Copilot agent mode)

Connect VS Code's GitHub Copilot **agent mode** to **KICKGEIST — World Cup Predictions** and play the group-first, zero-money World Cup 2026 prediction game without leaving your editor. Pick match outcomes, spin up a group for your friends, and track your own stats — all through Copilot.

KICKGEIST is **free**, AdMob-funded, with no in-app purchases and no subscriptions. You predict the **outcome** of each match — home win, draw, or away win — and climb the leaderboard with your friends.

The KICKGEIST server is **authless** — there's no OAuth and no login to set up. You just add the URL, then ask Copilot to create your account. Your KICKGEIST identity comes from a **recovery code** the server hands you, which also unlocks the same account in the mobile app.

**Endpoint (copy this):**

```
https://mcp.kickgeist.com/mcp
```

---

## Prerequisites

- **VS Code 1.102 or later**, with the **GitHub Copilot** and **GitHub Copilot Chat** extensions installed and signed in. MCP servers are used from Copilot Chat's **agent mode** (open Chat, then switch the mode dropdown to **Agent**).
- Network access to `https://mcp.kickgeist.com/mcp`.
- No OAuth, no account setup, no API key. KICKGEIST is authless — you're just adding a URL.

> The MCP tools surface inside **agent mode**. If you don't see a mode selector, update VS Code and make sure Copilot Chat is enabled.

---

## Option A — One-click install link

The fastest path. Open this link and VS Code will offer to add the KICKGEIST server:

```
vscode:mcp/install?%7B%22name%22%3A%22kickgeist%22%2C%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fmcp.kickgeist.com%2Fmcp%22%7D
```

That link is the URL-encoded form of this server definition:

```json
{ "name": "kickgeist", "type": "http", "url": "https://mcp.kickgeist.com/mcp" }
```

**Steps:**

1. Click the link above (or paste it into your browser's address bar and press Enter).
2. Your browser asks to open **Visual Studio Code** — allow it.
3. VS Code opens with a prompt to install the **kickgeist** MCP server. Review it and click **Install**.
4. Choose where to save it — **Workspace** (this project's `.vscode/mcp.json`) or **Global** (your user profile).
5. Open **Copilot Chat**, switch the mode dropdown to **Agent**, and you're ready.

> If clicking the link does nothing, your OS may not have the `vscode:` protocol registered yet — launch VS Code once manually, then try again, or use Option B below.

---

## Option B — Add it by hand (`.vscode/mcp.json`)

Prefer to edit config directly, or want it committed with your project? Create (or open) **`.vscode/mcp.json`** in your workspace root and add:

```json
{
  "servers": {
    "kickgeist": {
      "type": "http",
      "url": "https://mcp.kickgeist.com/mcp"
    }
  }
}
```

To make KICKGEIST available across **all** your workspaces instead, run **MCP: Open User Configuration** from the Command Palette (`Cmd/Ctrl+Shift+P`) and add the same `kickgeist` block there.

> The top-level key is `servers` (not `mcpServers`), the transport is `"type": "http"`, and the endpoint key is `"url"`. There's no `headers` block to fill in — KICKGEIST is authless.

After saving, a **Start** action appears above the server entry in `mcp.json`. Click it (or run **MCP: List Servers** → **kickgeist** → **Start Server**) to connect.

---

## Add it via the Command Palette (no file editing)

If you'd rather not touch JSON at all:

1. Open the Command Palette (`Cmd/Ctrl+Shift+P`) and run **MCP: Add Server**.
2. Choose **HTTP (HTTP or Server-Sent Events)** as the type.
3. Paste the URL:
   ```
   https://mcp.kickgeist.com/mcp
   ```
4. Name it **kickgeist** and pick **Workspace** or **Global**.
5. VS Code writes the config and starts the server for you.

---

## Verify it works

1. Open **Copilot Chat** and switch the mode dropdown to **Agent**.
2. (Optional) Click the **Tools** icon in the chat input to confirm the KICKGEIST tools are listed and enabled.
3. Ask Copilot in plain language:

   **Create your account:**

   > "Use KICKGEIST to create my account."

   Copilot calls `create_account` and returns a **recovery code** plus a welcome message and app link.

   **Save your recovery code.** It's the key to your account — enter it in the KICKGEIST mobile app (or use `link_account` from another agent) to reach the very same account. You can re-display it anytime by asking Copilot to run `get_recovery_code`.

   **See what's open to predict:**

   > "List the open World Cup matches."

   Copilot calls `list_open_matches` and shows upcoming fixtures you can still pick — match ID, home vs. away, kickoff time, and stage.

   **Make a prediction:**

   > "Predict the home team to win in that match."

   Copilot calls `predict_match` with your chosen outcome (`home`, `draw`, or `away`).

That's the loop. From there you can `create_group` to start a friends group (you'll get a shareable invite link), `join_group` with a code a friend sent you, `get_my_groups` to see your groups, and `get_my_stats` for your own points, accuracy, streak, and rank.

> The first time a tool runs, Copilot asks you to confirm the action. You can approve it once or allow it for the session.

---

## What KICKGEIST shows you (and what stays in the app)

By design, the server exposes only **your own data** and the **upcoming open-match schedule**. It deliberately does **not** return match results, finished scores, other players' picks, or the global/group leaderboard rankings.

That's a feature, not a gap: it protects our licensed match data and keeps the social fun — comparing picks and climbing the full leaderboard — right where it belongs, in the **KICKGEIST mobile app**. Save your recovery code, drop it into the app, and your agent picks and your phone picks are one and the same account.

---

## Troubleshooting

- **The one-click link does nothing.** The `vscode:` protocol may not be registered until VS Code has launched at least once. Open VS Code manually, then retry the link — or use Option B (`.vscode/mcp.json`).
- **No mode dropdown / no agent mode.** Update to **VS Code 1.102+**, install and sign in to the **GitHub Copilot** and **Copilot Chat** extensions, then reopen Chat. MCP tools only appear in **Agent** mode.
- **Server shows as not running.** Open `.vscode/mcp.json` and click **Start** above the `kickgeist` entry, or run **MCP: List Servers** → **kickgeist** → **Start Server**. Check the server's output via **MCP: List Servers** → **kickgeist** → **Show Output** for connection errors.
- **Tools don't show up in chat.** Click the **Tools** icon in the agent-mode chat input and make sure the KICKGEIST tools are toggled on.
- **Wrong config key.** The top-level key is `servers` (not `mcpServers`), the transport must be `"type": "http"`, and the endpoint key is `"url"` — confirm the exact value `https://mcp.kickgeist.com/mcp`.
- **Don't add headers or OAuth.** KICKGEIST is authless — there's nothing to authenticate. Leave any headers/auth fields empty.
- **Lost your recovery code?** Ask Copilot to run `get_recovery_code` while the account is linked. Keep it somewhere safe — it's the only way back into the same account.
- **Want to use the account you already play on your phone?** Ask Copilot to `link_account` with that account's recovery code (find it in the app under account / recovery).

---

## Official documentation

- VS Code — Use MCP servers in agent mode: <https://code.visualstudio.com/docs/copilot/customization/mcp-servers>
- VS Code — MCP developer guide: <https://code.visualstudio.com/api/extension-guides/ai/mcp>
