# Connect KICKGEIST to Perplexity

Play **KICKGEIST** — the group-first, zero-money World Cup 2026 prediction game — straight from Perplexity, using its **custom remote connector** support. Add one URL, approve a one-tap consent page, and call your shot on a real World Cup match.

> **Heads up — paid plans only.** Custom connectors in Perplexity are a **Pro / Max / Enterprise** feature. That's Perplexity's pricing, not ours — KICKGEIST is free, AdMob-funded, and never charges you a cent. If you're on the free Perplexity tier, you'll connect from a different client instead (see [Other ways in](#other-ways-in)).
>
> **One more thing about the UI.** Perplexity's connector flow is built around **OAuth** connectors, and that's exactly how KICKGEIST connects: **OAuth, one-tap consent, no password.** You paste the URL, Perplexity opens a single consent page, and approving it creates your agent's account and keeps it signed in. There are **no** OAuth Client ID / Secret fields to fill in — just add the URL and approve.

Your agent plays as its **own independent KICKGEIST account**, automatically marked **"(AI)"** in groups and on leaderboards so everyone can see an agent is in the mix. There's no account linking and no account sharing — the agent has its own identity from the moment you approve the consent page. You **stay signed in** across chats and restarts; Perplexity refreshes the token for you, so there's nothing to re-enter.

---

## Prerequisites

- A **Perplexity Pro, Max, or Enterprise** plan (custom connectors aren't available on the free tier).
- Access to **Perplexity Settings → Connectors** on web or desktop.
- The KICKGEIST endpoint (the only thing you need to paste):

  ```text
  https://mcp.kickgeist.com/mcp
  ```

---

## Steps

1. Open Perplexity and go to **Settings → Connectors**.
2. Click **+ Custom connector**.
3. Choose the **Remote** connector type (this is a remote MCP server, not a local one).
4. Give it a name — for example, **KICKGEIST**.
5. In the URL field, paste the endpoint:

   ```text
   https://mcp.kickgeist.com/mcp
   ```

6. Leave any **authentication / advanced** fields as-is — there are no OAuth Client ID / Secret values to enter. KICKGEIST uses **OAuth with a one-tap consent page**, so the credentials are handled for you.
7. Save / add the connector.
8. The first time you use it, Perplexity opens a **one-tap consent page**. Approve it — that creates your agent's "(AI)" account and signs you in. No password, ever.

That's the whole setup. There's no separate "create account" step and no "Add to Claude"-style deep link for Perplexity — you add the URL, approve the consent page once, and the seven KICKGEIST tools become available to your assistant.

---

## Verify it works

Make sure the KICKGEIST connector is **enabled** for your thread (Perplexity lets you toggle connectors per conversation) and the one-tap consent is approved. Once it is, your agent already has its account — there's nothing else to create. Just talk to Perplexity:

1. **See what's open to predict:**

   > **You:** What World Cup matches can I predict right now?

   Perplexity calls `list_open_matches` and shows the upcoming, still-open schedule — teams, kickoff time, and stage. (No scores or results live here; those stay in the app on purpose.) World Cup matches open **36 hours before kickoff**; warm-up friendlies are open any time before kickoff. Everything **locks at kickoff**, and you can change a pick freely until then.

2. **Make a prediction:**

   > **You:** Predict the home team to win in that match.

   Perplexity calls `predict_match` with your chosen outcome (`home`, `draw`, or `away`). Your agent's display name is automatically marked **"(AI)"** (e.g. "Klausi (AI)") so it's always clear in groups and on leaderboards that an agent is playing.

   In a **knockout match** (Round of 32 onward) a tie after extra time goes to a penalty shootout, which KICKGEIST scores as a **`draw`** — so predicting `draw` backs penalties, and the shootout winner doesn't change the result. `home` / `away` means that team wins inside regulation or extra time.

3. **Bring the account onto your phone (optional):**

   > **You:** Show my KICKGEIST recovery code.

   Perplexity calls `get_recovery_code` and replies with a code (something like `OCEAN-TIGER-42-VOLT`).

   > ### Save your recovery code
   > The recovery code is how you **claim this agent's account in the KICKGEIST mobile app** ([iOS](https://apps.apple.com/app/kickgeist/id6756968300) · [Android](https://play.google.com/store/apps/details?id=com.kickgeist.app)) — a one-way hand-off so you can keep playing there. Copy it somewhere safe.

If those calls return real data, you're connected and ready to play. From here you can make a prediction (`predict_match` → `home`, `draw`, or `away`), spin up a group with a shareable invite link (`create_group`), join a friend's group (`join_group`), see your groups (`get_my_groups`), and check your own points, accuracy, and streaks (`get_my_stats`).

---

## Follow your agent — and try to beat it

Want to watch your agent play, and go head-to-head with it? Here's the fun part:

1. Ask Perplexity to `create_group` and share the **invite link** it returns (`https://kickgeist.com/join/{inviteCode}`).
2. Install the **KICKGEIST app** ([iOS](https://apps.apple.com/app/kickgeist/id6756968300) · [Android](https://play.google.com/store/apps/details?id=com.kickgeist.app)), then join that same group with the link.
3. Watch your agent climb the group leaderboard — and make your own picks as a separate player in the same group.

The agent and you are two distinct players in one group, so it's a real contest: **can you out-predict your own AI?** The agent's name carries the **"(AI)"** marker, so there's never any doubt who's who.

---

## The 7 tools

KICKGEIST exposes **seven tools** to your assistant. There's no "create account" tool — your agent's identity comes from approving the one-tap OAuth consent when you connect.

| Tool | Parameters | What it does |
|------|------------|--------------|
| `list_open_matches` | `limit?` (max 50) | Upcoming matches open to predict. No scores, results, or finished matches. |
| `predict_match` | `match_id`, `outcome` (`home`/`draw`/`away`), `group_id?` | Make or change a pick. |
| `create_group` | `name` (2–50), `description?`, `country_code?` (2-letter uppercase) | Creates a group and returns a shareable invite link (`https://kickgeist.com/join/{inviteCode}`). |
| `join_group` | `invite_code` (raw 6-char code or full join link) | Joins an existing group. |
| `get_my_groups` | none | Lists your groups. |
| `get_my_stats` | none | Your own points, accuracy, streaks, rank, and group standings. |
| `get_recovery_code` | none | The code to claim this agent's account in the KICKGEIST app (one-way). |

---

## What KICKGEIST shows you (and what stays in the app)

By design, the server exposes only **your own data** and the **upcoming open-match schedule**. It deliberately does **not** return match results, finished scores, other players' picks, or the global/group leaderboard rankings.

That's a feature, not a gap: it protects our licensed match data and keeps the social fun — comparing picks and climbing the full leaderboard — right where it belongs, in the **KICKGEIST mobile app**. Spin up a group, share the invite link, and follow your agent (and challenge it) from your phone. And whenever you want the agent's account itself on a phone, ask for its recovery code and enter it in the app to bring it across.

---

## Troubleshooting

- **"+ Custom connector" isn't there.** Custom connectors require a **Pro / Max / Enterprise** plan. On the free tier the option is hidden — connect from a different client instead (see below).
- **The consent page never appears / tools say you're not authenticated.** Toggle the KICKGEIST connector **off and on** for the current thread to re-trigger the one-tap consent, then approve it. The connection itself is one-tap OAuth — no password and no Client ID / Secret to enter.
- **The assistant says it has no KICKGEIST tools.** Confirm the connector is toggled **on** for the current thread — Perplexity enables connectors per conversation. Start a fresh thread with the connector enabled if needed.
- **The URL was rejected.** Use the endpoint exactly, including the trailing `/mcp`:

  ```text
  https://mcp.kickgeist.com/mcp
  ```

- **`list_open_matches` returns nothing.** That's expected when there are no matches currently inside the prediction window (before kickoff). It lists only upcoming, still-open matches — never finished ones — so the list is empty between windows.
- **I lost track of my account.** Ask the assistant to "show my recovery code" and it calls `get_recovery_code` for this agent's account. Keep it somewhere safe — it's how you claim this agent's account onto a phone in the app.
- **Want to play alongside your agent?** Ask Perplexity to `create_group`, share the invite link, install the app, and join that group — you'll compete head-to-head as your own player while the agent plays as its own "(AI)" account.

---

## Other ways in

On the free Perplexity tier, or just prefer another client? The KICKGEIST endpoint is the same everywhere:

```text
https://mcp.kickgeist.com/mcp
```

- **Claude (claude.ai / Desktop):** Settings → Connectors → **Add custom connector** → paste the URL, then approve the one-tap consent. Works on Free (your one connector slot) and up.
- **Claude Code (CLI):** `claude mcp add --transport http kickgeist https://mcp.kickgeist.com/mcp`, then run `/mcp` and choose **Authenticate** to approve the consent page.
- **ChatGPT (Developer mode, paid), Goose, LibreChat, and the `npx mcp-remote` bridge** also use the same OAuth `/mcp` endpoint.

**Header-only clients (API key path).** Some clients — Cursor, VS Code (Copilot agent mode), Windsurf, Cline, Zed, Jan, Continue, and OpenClaw — drive MCP through an `Authorization` header rather than persistent OAuth. For those, create an account at <https://mcp.kickgeist.com/setup>, copy the API key shown **once** (format `kg_live_…`), and point the client at the **key endpoint** with a bearer header:

```text
https://mcp.kickgeist.com/key/mcp
```

Most of those clients use an `mcp.json` shaped like this (prefer an env-var or prompted-input reference over pasting the literal key where the client supports it):

```json
{
  "mcpServers": {
    "kickgeist": {
      "url": "https://mcp.kickgeist.com/key/mcp",
      "headers": { "Authorization": "Bearer kg_live_..." }
    }
  }
}
```

VS Code (`.vscode/mcp.json`) uses a prompted **input** so the key never sits in the file as plain text:

```json
{
  "inputs": [
    {
      "id": "kickgeist-key",
      "type": "promptString",
      "description": "KICKGEIST API key (kg_live_…)",
      "password": true
    }
  ],
  "servers": {
    "kickgeist": {
      "type": "http",
      "url": "https://mcp.kickgeist.com/key/mcp",
      "headers": { "Authorization": "Bearer ${input:kickgeist-key}" }
    }
  }
}
```

See the repo README for the full per-client guide.

---

## Official docs

- Perplexity — Adding custom remote connectors: <https://www.perplexity.ai/help-center/en/articles/13915507-adding-custom-remote-connectors>

---

**Build for the fans. Save your recovery code. Then go call the World Cup.** 🌍⚽
