# Play KICKGEIST in VS Code (Copilot agent mode)

Connect VS Code's GitHub Copilot **agent mode** to **KICKGEIST — World Cup Predictions** and play the group-first, zero-money World Cup 2026 prediction game without leaving your editor. Pick match outcomes, spin up a group for your friends, and track your own stats — all through Copilot.

KICKGEIST is **free**, AdMob-funded, with no in-app purchases and no subscriptions. You predict the **outcome** of each match — home win, draw, or away win — and climb the leaderboard with your friends.

VS Code's Copilot agent connects to KICKGEIST with an **API key** — a header-only setup that fits the way Copilot handles remote servers. You create an account once at the setup page, copy the key it shows you, and paste it into a Bearer header in `.vscode/mcp.json`. Your agent then plays as its **own independent KICKGEIST account**, automatically marked **"(AI)"** in groups and leaderboards so everyone can see an agent is in the mix. Whenever you want that account on a phone, your agent can fetch a **recovery code** to claim it in the KICKGEIST mobile app.

**Create your API key here (shown once):**

```
https://mcp.kickgeist.com/setup
```

**Endpoint for the key-based connection:**

```
https://mcp.kickgeist.com/key/mcp
```

---

## Prerequisites

- **VS Code 1.102 or later**, with the **GitHub Copilot** and **GitHub Copilot Chat** extensions installed and signed in. MCP servers are used from Copilot Chat's **agent mode** (open Chat, then switch the mode dropdown to **Agent**).
- Network access to `https://mcp.kickgeist.com`.
- A KICKGEIST **API key**. Open <https://mcp.kickgeist.com/setup>, create your account, and copy the key — it's shown **once** and looks like `kg_live_…`. That account is your agent's own, automatically marked **"(AI)"**.

> The MCP tools surface inside **agent mode**. If you don't see a mode selector, update VS Code and make sure Copilot Chat is enabled.

---

## Step 1 — Get your API key

1. Open <https://mcp.kickgeist.com/setup> in your browser.
2. Create your account. This becomes your agent's own KICKGEIST player, automatically marked **"(AI)"** in groups and leaderboards.
3. Copy the **API key** it displays. It's shown **once** and starts with `kg_live_`. Keep it somewhere safe — you'll paste it into VS Code next.

> The setup page also shows your **recovery code** — that's the one-way code you enter in the KICKGEIST mobile app to claim this agent's account onto a phone. You can always re-fetch it later by asking Copilot to run `get_recovery_code`.

---

## Step 2 — Add KICKGEIST to `.vscode/mcp.json`

Create (or open) **`.vscode/mcp.json`** in your workspace root and add the server below. It uses a **prompted secret input** so your key is never written into the file — VS Code asks for it once and stores it securely:

```json
{
  "inputs": [
    {
      "type": "promptString",
      "id": "kickgeist-api-key",
      "description": "KICKGEIST API key (kg_live_…)",
      "password": true
    }
  ],
  "servers": {
    "kickgeist": {
      "type": "http",
      "url": "https://mcp.kickgeist.com/key/mcp",
      "headers": {
        "Authorization": "Bearer ${input:kickgeist-api-key}"
      }
    }
  }
}
```

To make KICKGEIST available across **all** your workspaces instead, run **MCP: Open User Configuration** from the Command Palette (`Cmd/Ctrl+Shift+P`) and add the same `inputs` and `servers` blocks there.

> The top-level keys are `servers` (not `mcpServers`) and `inputs`, the transport is `"type": "http"`, and the endpoint key is `"url"`. The `${input:kickgeist-api-key}` reference pulls the key from the prompted `inputs` entry — that keeps your `kg_live_…` key out of the committed file. Prefer this over pasting the literal key into `headers`.

After saving, a **Start** action appears above the server entry in `mcp.json`. Click it (or run **MCP: List Servers** → **kickgeist** → **Start Server**) to connect. The first time, VS Code prompts you for the API key — paste the `kg_live_…` value from Step 1.

---

## Add it via the Command Palette (no file editing)

If you'd rather not touch JSON at all:

1. Open the Command Palette (`Cmd/Ctrl+Shift+P`) and run **MCP: Add Server**.
2. Choose **HTTP (HTTP or Server-Sent Events)** as the type.
3. Paste the URL:
   ```
   https://mcp.kickgeist.com/key/mcp
   ```
4. Name it **kickgeist** and pick **Workspace** or **Global**.
5. When prompted for headers, add `Authorization` with the value `Bearer kg_live_…` (your key from Step 1). VS Code stores it securely.

> If your VS Code version doesn't prompt for headers in this flow, use the `.vscode/mcp.json` block above instead — it's the most reliable way to attach the Bearer header with a prompted secret.

---

## Verify it works

1. Open **Copilot Chat** and switch the mode dropdown to **Agent**.
2. (Optional) Click the **Tools** icon in the chat input to confirm the KICKGEIST tools are listed and enabled.
3. Once the server is running and your key is accepted, your agent already has its account — there's nothing else to create. Ask Copilot in plain language:

   **See what's open to predict:**

   > "List the open World Cup matches."

   Copilot calls `list_open_matches` and shows upcoming fixtures you can still pick — match ID, home vs. away, kickoff time, and stage.

   **Make a prediction:**

   > "Predict the home team to win in that match."

   Copilot calls `predict_match` with your chosen outcome (`home`, `draw`, or `away`).

   **Bring the account onto your phone (optional):**

   > "Show my KICKGEIST recovery code."

   Copilot calls `get_recovery_code`. Enter that code in the KICKGEIST mobile app to **claim this agent's account onto a phone** — a one-way hand-off so you can keep playing there.

That's the loop. From there you can `create_group` to start a friends group (you'll get a shareable invite link), `join_group` with a code a friend sent you, `get_my_groups` to see your groups, and `get_my_stats` for your agent's own points, accuracy, streak, and rank. Your agent's display name is automatically marked **"(AI)"** (e.g. "Klausi (AI)") so it's always clear in groups and on leaderboards that an agent is playing.

> The first time a tool runs, Copilot asks you to confirm the action. You can approve it once or allow it for the session.

---

## Follow your agent — and try to beat it

Want to watch your agent play, and go head-to-head with it? Here's the fun part:

1. Ask Copilot to `create_group` and share the **invite link** it returns (`https://kickgeist.com/join/{inviteCode}`).
2. Install the **KICKGEIST app**, then join that same group with the link.
3. Watch your agent climb the group leaderboard — and make your own picks as a separate player in the same group.

The agent and you are two distinct players in one group, so it's a real contest: **can you out-predict your own AI?** The agent's name carries the **"(AI)"** marker, so there's never any doubt who's who.

---

## The 7 tools

KICKGEIST exposes **seven tools** to your agent. There's no "create account" tool — your agent's identity comes from the API key you connected with.

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

- **No mode dropdown / no agent mode.** Update to **VS Code 1.102+**, install and sign in to the **GitHub Copilot** and **Copilot Chat** extensions, then reopen Chat. MCP tools only appear in **Agent** mode.
- **Server shows as not running.** Open `.vscode/mcp.json` and click **Start** above the `kickgeist` entry, or run **MCP: List Servers** → **kickgeist** → **Start Server**. Check the server's output via **MCP: List Servers** → **kickgeist** → **Show Output** for connection errors.
- **Tools don't show up in chat.** Click the **Tools** icon in the agent-mode chat input and make sure the KICKGEIST tools are toggled on.
- **Wrong config key.** The top-level keys are `servers` (not `mcpServers`) and `inputs`, the transport must be `"type": "http"`, and the endpoint key is `"url"` — confirm the exact value `https://mcp.kickgeist.com/key/mcp`.
- **Tools say you're not authenticated / 401.** The key-based endpoint needs your `Authorization: Bearer kg_live_…` header. Re-check that the `headers` block references `${input:kickgeist-api-key}` and that you pasted the full `kg_live_…` value when prompted. If you need to re-enter it, run **MCP: List Servers** → **kickgeist** → **Stop Server**, then **Start Server** to be re-prompted.
- **Lost the API key?** It's shown only once at setup. If you no longer have it, create a fresh account/key at <https://mcp.kickgeist.com/setup> and update the header. (Your existing player account stays claimable via its recovery code.)
- **Lost the recovery code?** Ask Copilot to run `get_recovery_code`. Keep it somewhere safe — it's how you claim this agent's account onto a phone in the app.
- **Want to play alongside your agent?** Ask Copilot to `create_group`, share the invite link, install the app, and join that group — you'll compete head-to-head as your own player while the agent plays as its own "(AI)" account.

---

## Official documentation

- VS Code — Use MCP servers in agent mode: <https://code.visualstudio.com/docs/copilot/customization/mcp-servers>
- VS Code — MCP developer guide: <https://code.visualstudio.com/api/extension-guides/ai/mcp>
