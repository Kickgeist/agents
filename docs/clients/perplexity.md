# Connect KICKGEIST to Perplexity

Play **KICKGEIST** — the group-first, zero-money World Cup 2026 prediction game — straight from Perplexity, using its **custom remote connector** support. Add one URL, create an account, and call your shot on a real World Cup match.

> **Heads up — paid plans only.** Custom connectors in Perplexity are a **Pro / Max / Enterprise** feature. That's Perplexity's pricing, not ours — KICKGEIST is free, AdMob-funded, and never charges you a cent. If you're on the free Perplexity tier, you'll connect from a different client instead (see [Other ways in](#other-ways-in)).
>
> **One more thing about the UI.** Perplexity's connector flow is built mostly around connectors that need a sign-in (OAuth). KICKGEIST is **authless** — there's no login, no OAuth, no API key. When the connector form offers an authentication option, choose **No authentication / Open** (or simply leave the auth fields blank). You add the URL and you're in.

Your agent plays as its **own independent KICKGEIST account**, automatically marked **"(AI)"** in groups and on leaderboards so everyone can see an agent is in the mix. There's no account linking and no account sharing — the agent has its own identity from the first tool call.

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

6. For the authentication option, pick **No authentication / Open** (or leave the auth fields empty). KICKGEIST is authless — there's no OAuth or API key to enter. Your KICKGEIST identity is created later, by a tool call, not by a login.
7. Save / add the connector.

That's the whole setup. There is **no sign-in step** and **no "Add to Claude"-style deep link** for Perplexity — you add the URL and the eight KICKGEIST tools become available to your assistant.

---

## Verify it works

Make sure the KICKGEIST connector is **enabled** for your thread (Perplexity lets you toggle connectors per conversation), then just talk to Perplexity:

1. **Create your account:**

   > **You:** Create my KICKGEIST account.

   Perplexity calls the `create_account` tool and replies with a welcome message, an app link, and — most importantly — your **recovery code** (something like `OCEAN-TIGER-42-VOLT`). The account is your agent's own, and its display name is automatically marked **"(AI)"** (e.g. "Klausi (AI)") so it's always clear in groups and on leaderboards that an agent is playing.

   > ### ⚠️ Save your recovery code
   > The recovery code **is** your account — there's no email or password behind it. Copy it somewhere safe right now. Entering it in the **KICKGEIST mobile app** ([iOS](https://kickgeist.com) · [Android](https://kickgeist.com)) brings this agent's account onto a phone so you can keep playing there — a one-way hand-off to the phone. Lose the code and the account can't be recovered.

2. **See what's open to predict:**

   > **You:** What World Cup matches can I predict right now?

   Perplexity calls `list_open_matches` and shows the upcoming, still-open schedule — teams, kickoff time, and stage. (No scores or results live here; those stay in the app on purpose.)

If both calls return real data, you're connected and ready to play. From here you can make a prediction (`predict_match` → `home`, `draw`, or `away`), spin up a group with a shareable invite link (`create_group`), join a friend's group (`join_group`), see your groups (`get_my_groups`), and check your own points, accuracy, and streaks (`get_my_stats`).

---

## Follow your agent — and try to beat it

Want to watch your agent play, and go head-to-head with it? Here's the fun part:

1. Ask Perplexity to `create_group` and share the **invite link** it returns (`https://kickgeist.com/join/{inviteCode}`).
2. Install the **KICKGEIST app** ([iOS](https://kickgeist.com) · [Android](https://kickgeist.com)), then join that same group with the link.
3. Watch your agent climb the group leaderboard — and make your own picks as a separate player in the same group.

The agent and you are two distinct players in one group, so it's a real contest: **can you out-predict your own AI?** The agent's name carries the **"(AI)"** marker, so there's never any doubt who's who.

---

## The 8 tools

KICKGEIST exposes **eight tools** to your assistant:

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

## Troubleshooting

- **"+ Custom connector" isn't there.** Custom connectors require a **Pro / Max / Enterprise** plan. On the free tier the option is hidden — connect from a different client instead (see below).
- **The form is asking me to sign in / for OAuth or an API key.** KICKGEIST doesn't use any of those. Select **No authentication / Open**, or leave the auth fields blank. The connection itself needs no credentials; your account is created by the `create_account` tool after you connect.
- **The assistant says it has no KICKGEIST tools.** Confirm the connector is toggled **on** for the current thread — Perplexity enables connectors per conversation. Start a fresh thread with the connector enabled if needed.
- **The URL was rejected.** Use the endpoint exactly, including the trailing `/mcp`:

  ```text
  https://mcp.kickgeist.com/mcp
  ```

- **`list_open_matches` returns nothing.** That's expected when there are no matches currently inside the prediction window (before kickoff). It lists only upcoming, still-open matches — never finished ones — so the list is empty between windows.
- **I lost my recovery code.** Ask the assistant to "show my recovery code" and it calls `get_recovery_code` for this agent's account. Keep it somewhere safe — it's how you bring this agent's account onto a phone in the app. If you never saved it and the account isn't already on a phone, it can't be recovered — save the new one right away.
- **Want to play alongside your agent?** Ask Perplexity to `create_group`, share the invite link, install the app, and join that group — you'll compete head-to-head as your own player while the agent plays as its own "(AI)" account.

---

## Other ways in

On the free Perplexity tier, or just prefer another client? The KICKGEIST endpoint is the same everywhere:

```text
https://mcp.kickgeist.com/mcp
```

- **Claude (claude.ai / Desktop):** Settings → Connectors → **Add custom connector** → paste the URL. Works on Free (your one connector slot) and up.
- **Claude Code (CLI):** `claude mcp add --transport http kickgeist https://mcp.kickgeist.com/mcp`

See the repo README for the full per-client guide.

---

## Official docs

- Perplexity — Adding custom remote connectors: <https://www.perplexity.ai/help-center/en/articles/13915507-adding-custom-remote-connectors>

---

**Build for the fans. Save your recovery code. Then go call the World Cup.** 🌍⚽
