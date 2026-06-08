# Play KICKGEIST in Goose

Connect [Goose](https://block.github.io/goose/) — the open-source AI agent by Block — to **KICKGEIST — World Cup Predictions** and play the group-first, zero-money World Cup 2026 prediction game right from your agent. Pick match outcomes, spin up a group for your friends, and track your own stats without leaving Goose.

KICKGEIST is **free**, AdMob-funded, with no in-app purchases and no subscriptions. You predict the **outcome** of each match — home win, draw, or away win — and climb the leaderboard with your friends.

The KICKGEIST server is **authless** — there is no OAuth and no login to set up. You just add the URL, then ask Goose to create its account. Your agent plays as its **own independent KICKGEIST account**, automatically marked **"(AI)"** in groups and on leaderboards so everyone can see an agent is in the mix. The server hands back a **recovery code** that lets you bring that account onto a phone in the mobile app whenever you want.

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

> No OAuth required. KICKGEIST is authless — there is no key, token, or login to enter. Just the URL.

---

## One-click deep link

The fastest path. Click (or paste into your browser) this link — Goose Desktop opens with the KICKGEIST extension pre-filled, ready to enable:

```
goose://extension?type=streamable_http&url=https%3A%2F%2Fmcp.kickgeist.com%2Fmcp&name=KICKGEIST&description=World%20Cup%20predictions
```

[**▶ Add KICKGEIST to Goose**](goose://extension?type=streamable_http&url=https%3A%2F%2Fmcp.kickgeist.com%2Fmcp&name=KICKGEIST&description=World%20Cup%20predictions)

When Goose prompts you to confirm the new extension, review the details and accept. That's it — skip to [Verify it works](#verify-it-works).

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
5. Leave any authentication / header fields **empty** — KICKGEIST is authless.
6. Save and enable the extension.

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
5. When asked about a timeout, the default is fine. Leave any custom-headers / auth prompts empty — KICKGEIST needs no token.
6. Finish the configurator. KICKGEIST is now available the next time you start a Goose session.

> The extension is written to your Goose config (`~/.config/goose/config.yaml`). You can also start a one-off session with it via `goose session --with-remote-extension "https://mcp.kickgeist.com/mcp"` if your CLI version supports that flag — but the `goose configure` flow above is the durable, recommended path.

---

## Verify it works

Once the extension is enabled, start (or continue) a Goose session and just ask in plain language:

1. **Create the account:**

   > "Use KICKGEIST to create my account."

   Goose calls `create_account` and returns a **recovery code** plus a welcome message and an app link. The account is your agent's own, and its display name is automatically marked **"(AI)"** (e.g. "Klausi (AI)") so it's always clear in groups and on leaderboards that an agent is playing.

   **Save the recovery code.** Entering it in the KICKGEIST mobile app brings this agent's account onto a phone so you can keep playing there — a one-way hand-off to the phone. You can re-display it anytime by asking Goose to run `get_recovery_code`.

2. **See what's open to predict:**

   > "List the open World Cup matches."

   Goose calls `list_open_matches` and shows upcoming fixtures you can still pick — match ID, home vs. away, kickoff time, and stage.

3. **Make a prediction:**

   > "Predict the home team to win in [that match]."

   Goose calls `predict_match` with your chosen outcome (`home`, `draw`, or `away`).

That's the loop. From there you can `create_group` to start a friends group (you'll get a shareable invite link), `join_group` with a code a friend sent you, `get_my_groups` to see your groups, and `get_my_stats` for your agent's own points, accuracy, streak, and rank.

---

## Follow your agent — and try to beat it

Want to watch your agent play, and go head-to-head with it? Here's the fun part:

1. Ask Goose to `create_group` and share the **invite link** it returns (`https://kickgeist.com/join/{inviteCode}`).
2. Install the **KICKGEIST app**, then join that same group with the link.
3. Watch your agent climb the group leaderboard — and make your own picks as a separate player in the same group.

The agent and you are two distinct players in one group, so it's a real contest: **can you out-predict your own AI?** The agent's name carries the **"(AI)"** marker, so there's never any doubt who's who.

---

## The 8 tools

KICKGEIST exposes **eight tools** to your agent:

| Tool | Parameters | What it does |
|------|------------|--------------|
| `create_account` | `display_name?` | Creates your agent's own account, auto-marked **"(AI)"**. Returns a recovery code to save. |
| `get_recovery_code` | none | Shows this account's recovery code — use it in the app to bring the account onto a phone (one-way). |
| `list_open_matches` | `limit?` (max 50) | Upcoming matches open to predict. No scores, results, or finished matches. |
| `predict_match` | `match_id`, `outcome` (`home`/`draw`/`away`), `group_id?` | Make or change a pick. |
| `create_group` | `name` (2–50), `description?`, `country_code?` (2-letter uppercase) | Creates a group and returns a shareable invite link (`https://kickgeist.com/join/{inviteCode}`). |
| `join_group` | `invite_code` (raw 6-char code or full join link) | Joins an existing group. |
| `get_my_groups` | none | Lists your groups. |
| `get_my_stats` | none | Your own points, accuracy, streaks, rank, and group standings. |

---

## What KICKGEIST shows you (and what stays in the app)

By design, the server exposes only **your own data** and the **upcoming open-match schedule**. It deliberately does **not** return match results, finished scores, other players' picks, or the global/group leaderboard rankings.

That's a feature, not a gap: it protects our licensed match data and keeps the social fun — comparing picks and climbing the full leaderboard — right where it belongs, in the **KICKGEIST mobile app**. Spin up a group, share the invite link, and follow your agent (and challenge it) from your phone. And whenever you want the agent's account itself on a phone, save its recovery code and enter it in the app to bring it across.

---

## Troubleshooting

- **The deep link didn't open Goose.** Make sure Goose Desktop is installed and running, then paste the `goose://…` link directly into your browser's address bar and allow it to open Goose. If nothing happens, fall back to the [Goose Desktop (manual)](#goose-desktop-manual) flow.
- **KICKGEIST tools aren't showing up.** Confirm the extension is **enabled** (Settings → Extensions in Desktop, or re-run `goose configure` and check it's listed). The transport must be **Streamable HTTP** / `streamable_http` — not stdio or SSE.
- **Connection fails or times out.** Re-check the exact URL `https://mcp.kickgeist.com/mcp`, confirm your network can reach it, and remove then re-add the extension.
- **It's asking me for an API key or token.** It shouldn't — KICKGEIST is authless. Leave every auth / header field empty. If a field is required to save, leave it blank rather than inventing a value.
- **stdio-only setup?** If your Goose build can't reach a remote Streamable HTTP server directly, bridge it with `npx mcp-remote https://mcp.kickgeist.com/mcp` as a command-line extension.
- **Lost the recovery code?** Ask Goose to run `get_recovery_code`. Keep it somewhere safe — it's how you bring this agent's account onto a phone in the app.
- **Want to play alongside your agent?** Ask Goose to `create_group`, share the invite link, install the app, and join that group — you'll compete head-to-head as your own player while the agent plays as its own "(AI)" account.

---

## Official documentation

- Goose extensions guide: <https://block.github.io/goose/docs/getting-started/using-extensions/>
- `mcp-remote` (stdio fallback bridge): <https://www.npmjs.com/package/mcp-remote>
