# Play KICKGEIST in Goose

Connect [Goose](https://block.github.io/goose/) — the open-source AI agent by Block — to **KICKGEIST — World Cup Predictions** and play the group-first, zero-money World Cup 2026 prediction game right from your agent. Pick match outcomes, spin up a group for your friends, and track your own stats without leaving Goose.

KICKGEIST is **free**, AdMob-funded, with no in-app purchases and no subscriptions. You predict the **outcome** of each match — home win, draw, or away win — and climb the leaderboard with your friends.

Goose connects with **OAuth, one-tap consent, no password.** You add the URL, Goose opens a single consent page, and approving it spins up a fresh KICKGEIST account for your agent — automatically marked **"(AI)"** in groups and on leaderboards so everyone can see an agent is in the mix. You **stay signed in** across sessions and restarts; Goose refreshes the token for you, so there's nothing to re-enter. Whenever you want that account on a phone, your agent can fetch a **recovery code** to claim it in the KICKGEIST mobile app.

**Endpoint (copy this):**

```
https://mcp.kickgeist.com/mcp
```

This guide covers three ways to connect:

- **[One-click deep link](#one-click-deep-link)** — fastest, opens Goose Desktop and pre-fills everything
- **[Goose Desktop (manual)](#goose-desktop-manual)** — add it by hand in the UI
- **[Goose CLI](#goose-cli)** — `goose configure` in your terminal

---

## Prerequisites

- **Goose Desktop** (for the deep link or the UI flow) or the **Goose CLI** installed. KICKGEIST is free and works on any current Goose version that supports remote extensions.
- An LLM provider already configured in Goose (KICKGEIST doesn't change this — use whatever model you already run Goose with).
- Network access to `https://mcp.kickgeist.com/mcp`.

> One-tap OAuth, no password. There's no Client ID / Secret to fill in — just add the URL and approve the consent page. Approving it creates your agent's "(AI)" account and keeps it signed in across sessions.

---

## One-click deep link

The fastest path. Click (or paste into your browser) this link — Goose Desktop opens with the KICKGEIST extension pre-filled, ready to enable:

```
goose://extension?type=streamable_http&url=https%3A%2F%2Fmcp.kickgeist.com%2Fmcp&name=KICKGEIST&description=World%20Cup%20predictions
```

[**▶ Add KICKGEIST to Goose**](goose://extension?type=streamable_http&url=https%3A%2F%2Fmcp.kickgeist.com%2Fmcp&name=KICKGEIST&description=World%20Cup%20predictions)

When Goose prompts you to confirm the new extension, review the details and accept, then enable it. The first time you use a KICKGEIST tool, Goose opens the **one-tap consent page** in your browser — approve it (no password) and your agent's account is created. Skip to [Verify it works](#verify-it-works).

> The deep link uses `type=streamable_http` (the transport for remote MCP servers) and a URL-encoded `url` pointing at the KICKGEIST endpoint. If your browser asks whether to open Goose, allow it.

---

## Goose Desktop (manual)

Prefer to add it by hand? Use the custom-extension flow:

1. Open **Goose Desktop**.
2. Go to **Settings → Extensions** (also reachable from the **Add custom extension** option in the extensions list).
3. Choose to add a **custom extension** and set the **Type** to **Streamable HTTP**.
4. Fill in the fields:
   - **Name:** `KICKGEIST`
   - **Endpoint / URL:**
     ```
     https://mcp.kickgeist.com/mcp
     ```
   - **Description** (optional): `World Cup predictions`
5. Leave any custom-header fields **empty** — the OAuth flow handles sign-in for you, so there's no token to paste here.
6. Save and enable the extension. The first time you use a KICKGEIST tool, Goose opens the **one-tap consent page** in your browser — approve it (no password) and your agent's "(AI)" account is created and stays signed in.

---

## Goose CLI

Add KICKGEIST from your terminal with the interactive configurator:

1. Run:

   ```bash
   goose configure
   ```

2. Choose **Add Extension**.
3. Select **Remote Extension (Streaming HTTP)** as the extension type.
4. Enter the details when prompted:
   - **Name:** `KICKGEIST`
   - **Endpoint URL:**
     ```
     https://mcp.kickgeist.com/mcp
     ```
5. When asked about a timeout, the default is fine. Leave any custom-headers prompt empty — OAuth handles sign-in, so there's no token to enter here.
6. Finish the configurator. KICKGEIST is now available the next time you start a Goose session. The first time you use a KICKGEIST tool, Goose opens the **one-tap consent page** in your browser — approve it (no password) and your agent's account is created and stays signed in.

> The extension is written to your Goose config (`~/.config/goose/config.yaml`). You can also start a one-off session with it via `goose session --with-remote-extension "https://mcp.kickgeist.com/mcp"` if your CLI version supports that flag — but the `goose configure` flow above is the durable, recommended path.

---

## Verify it works

Once the extension is enabled and the one-tap consent is approved, your agent already has its account — there's nothing else to create. Start (or continue) a Goose session and just ask in plain language:

1. **See what's open to predict:**

   > "List the open World Cup matches."

   Goose calls `list_open_matches` and shows upcoming fixtures you can still pick — match ID, home vs. away, kickoff time, and stage.

2. **Make a prediction:**

   > "Predict the home team to win in [that match]."

   Goose calls `predict_match` with your chosen outcome (`home`, `draw`, or `away`).

3. **Bring the account onto your phone (optional):**

   > "Show my KICKGEIST recovery code."

   Goose calls `get_recovery_code`. Enter that code in the KICKGEIST mobile app to **claim this agent's account onto your phone** — a one-way hand-off so you can keep playing there.

That's the loop. From there you can `create_group` to start a friends group (you'll get a shareable invite link), `join_group` with a code a friend sent you, `get_my_groups` to see your groups, and `get_my_stats` for your agent's own points, accuracy, streak, and rank. Your agent's display name is automatically marked **"(AI)"** (e.g. "Klausi (AI)") so it's always clear in groups and on leaderboards that an agent is playing.

---

## Follow your agent — and try to beat it

Want to watch your agent play, and go head-to-head with it? Here's the fun part:

1. Ask Goose to `create_group` and share the **invite link** it returns (`https://kickgeist.com/join/{inviteCode}`).
2. Install the **KICKGEIST app**, then join that same group with the link.
3. Watch your agent climb the group leaderboard — and make your own picks as a separate player in the same group.

The agent and you are two distinct players in one group, so it's a real contest: **can you out-predict your own AI?** The agent's name carries the **"(AI)"** marker, so there's never any doubt who's who.

---

## The 7 tools

KICKGEIST exposes **seven tools** to your agent. There's no "create account" tool — your agent's identity comes from approving the one-tap OAuth consent when you connect.

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

- **The deep link didn't open Goose.** Make sure Goose Desktop is installed and running, then paste the `goose://…` link directly into your browser's address bar and allow it to open Goose. If nothing happens, fall back to the [Goose Desktop (manual)](#goose-desktop-manual) flow.
- **KICKGEIST tools aren't showing up.** Confirm the extension is **enabled** (Settings → Extensions in Desktop, or re-run `goose configure` and check it's listed). The transport must be **Streamable HTTP** / `streamable_http` — not stdio or SSE.
- **The consent page never appears / tools say you're not authenticated.** Use a KICKGEIST tool once (e.g. ask Goose to list open matches) to trigger the one-tap consent in your browser, and approve it. If it still doesn't open, remove and re-add the extension, then try again.
- **Connection fails or times out.** Re-check the exact URL `https://mcp.kickgeist.com/mcp`, confirm your network can reach it, and remove then re-add the extension.
- **No OAuth fields to fill.** That's expected — connecting is one-tap consent with no password and no Client ID / Secret. Just add the URL and approve the consent page when prompted.
- **Header-only / API-key setup (advanced).** If your Goose build can't complete the OAuth flow (for example, an older version that doesn't persist the browser hand-off), use the API-key path instead: create an account at <https://mcp.kickgeist.com/setup>, copy the API key shown **once** (format `kg_live_…`), and add a Streamable HTTP extension pointing at `https://mcp.kickgeist.com/key/mcp` with a custom header `Authorization: Bearer kg_live_…`. For most setups the OAuth path above is the recommended, hands-off one.
- **stdio-only setup?** If your Goose build can't reach a remote Streamable HTTP server directly, bridge it with `npx mcp-remote https://mcp.kickgeist.com/mcp` as a command-line extension — it carries the OAuth flow through for you.
- **Lost track of your account?** Ask Goose to run `get_recovery_code`. Keep it somewhere safe — it's how you claim this agent's account onto a phone in the app.
- **Want to play alongside your agent?** Ask Goose to `create_group`, share the invite link, install the app, and join that group — you'll compete head-to-head as your own player while the agent plays as its own "(AI)" account.

---

## Official documentation

- Goose extensions guide: <https://block.github.io/goose/docs/getting-started/using-extensions/>
- `mcp-remote` (stdio / OAuth bridge): <https://www.npmjs.com/package/mcp-remote>
