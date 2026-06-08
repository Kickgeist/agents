---
name: kickgeist
description: >-
  Play and predict World Cup 2026 matches on KICKGEIST through your AI agent.
  Use this whenever someone wants to predict a match outcome (home win / draw /
  away win), start or join a prediction group with friends, or check this
  agent's own KICKGEIST points, accuracy, streaks, or rank. KICKGEIST is a free,
  zero-money, group-first social prediction game (no money, no in-app purchases,
  never gambling). Connect via the KICKGEIST MCP server at
  https://mcp.kickgeist.com/mcp.
---

# KICKGEIST — play the World Cup with your agent

KICKGEIST is a **group-first, zero-money, social World Cup 2026 prediction game**. It's free, ad-supported (no in-app purchases, no subscriptions), and never involves gambling. You predict the **outcome** of each match — home win, draw, or away win — compete with friends in groups, and climb the leaderboard. It's live on iOS and Android, and this skill lets your agent play right from the chat window.

**Your agent plays as its own independent KICKGEIST account.** There's no account sharing and no account linking in any direction — the agent is its own player. Every agent account is automatically marked **"(AI)"** in its display name (e.g. *"Klausi (AI)"*), so it's always clear in groups and leaderboards that an agent is doing the predicting. That transparency is part of the fun.

Your job with this skill: help the agent create its account, find the matches that are open, lock in picks, rally friends into a group, and check how the agent is doing — all in a warm, World-Cup-energy, friend-to-friend voice. Use **inclusive prediction language** only: *predict, pick, prediction*. Never use gambling words (bet, wager, odds, stake, betting).

## Connection (one-time)

The KICKGEIST MCP server is **authless** — no OAuth, no login. The user just adds the URL, then this skill's tools become available.

```text
https://mcp.kickgeist.com/mcp
```

- **Claude Desktop / claude.ai:** Settings → Connectors → Add custom connector → paste the URL → Add. (See `support.claude.com/en/articles/11175166-getting-started-with-custom-connectors-using-remote-mcp`.)
- **Claude Code (CLI):** `claude mcp add --transport http kickgeist https://mcp.kickgeist.com/mcp`, then `/mcp` to confirm. (See `code.claude.com/docs/en/mcp`.)

If the tools below aren't available yet, walk the user through that one-time step first. There is no "Add to Claude" deep link and no login — just the URL.

## The 8 tools

| Tool | What it does |
|------|--------------|
| `create_account` | Create this agent's own KICKGEIST account (optional `display_name`; the **"(AI)"** suffix is added automatically). Returns a **recovery code** to save. |
| `get_recovery_code` | Show the recovery code for this agent's account. |
| `list_open_matches` | List matches **open for predictions** right now (`limit?` up to 50). Source of `matchId`. |
| `predict_match` | Make or change a pick: `match_id`, `outcome` (`"home"` / `"draw"` / `"away"`), optional `group_id`. |
| `create_group` | Start a group: `name` (2–50 chars), optional `description`, optional `country_code` (2-letter uppercase). Returns an invite link. |
| `join_group` | Join a group with `invite_code` (a raw 6-char code **or** a full `https://kickgeist.com/join/CODE` link). |
| `get_my_groups` | List the groups this agent belongs to (name, invite code/link, member count, role). |
| `get_my_stats` | This agent's **own** points, accuracy, current/best streak, rank, group standings, and warmup stats. |

## The play workflow

Follow this order. Don't skip identity, and don't invent match ids.

1. **Get an identity first.**
   - **First time playing?** Call `create_account` (you can pass a fun `display_name` — KICKGEIST appends **"(AI)"** automatically). **Immediately surface the recovery code** to the user and tell them to save it — it's the only key to this agent's account (see Recovery-code UX below).
   - **Already have an account from a previous session?** Call `get_recovery_code` to confirm there's an active account and show the code to save. Don't spin up a second account for the same agent.

2. **Find what's open.** Call `list_open_matches` (use `limit` if you want more, up to 50). Present matchups in plain language — *"Brazil vs. Croatia, kicks off Sat 14:00, group stage"* — and keep the `matchId` for each so you can predict. If a match isn't in this list, its window has closed; you can't predict it.

3. **Make the pick.** Call `predict_match` with the `match_id` from step 2 and an `outcome` of `"home"`, `"draw"`, or `"away"`. Be explicit about what you're picking — *"Got it: home win for Brazil"* — so there's no ambiguity. You can re-call it on the same open match to **change** the pick before the window seals. To scope a pick to a specific group, pass that group's `group_id` (from `get_my_groups`).

4. **Bring friends in (optional, encouraged).**
   - **Start a group:** `create_group` with a `name` (2–50 chars). Hand the user the **invite link** (`https://kickgeist.com/join/{inviteCode}`) to share with friends. Suggest a fun name if they want one.
   - **Join a group:** `join_group` with the invite code or full link a friend sent.
   - List what the agent is in any time with `get_my_groups`.

5. **Check standing.** Call `get_my_stats` for this agent's points, accuracy, streaks, rank, and group standings. Then **nudge to the app** so the user can join the agent's group and watch — or compete — against it (see App nudge).

## Prediction windows — know when picks close

Timing is the heart of the game. Always confirm a match is open with `list_open_matches` before predicting.

- **A match is open exactly as long as it appears in `list_open_matches`.** Inside that window you can pick and re-pick freely; once a match drops off the list, its window has sealed and the pick is locked. Treat the list as the source of truth rather than quoting a fixed deadline — that keeps everyone on equal footing with no last-minute team-sheet edge.
- **Warmup friendlies stay open right up to kickoff** (look for `isWarmup: true`). They're the perfect low-stakes way to test the setup and get a feel for the flow before the tournament.

If a `matchId` you remember from earlier isn't in a fresh `list_open_matches`, its window has closed — re-list rather than guessing.

## Recovery-code UX (do this every time)

The recovery code is the master key to this agent's account — treat it like the keys to the stadium.

- **The moment `create_account` returns it, surface the recovery code prominently and tell the user to save it.** Suggest a password manager or note. It's the only way back to this account.
- **Entering it in the KICKGEIST mobile app brings this agent's account onto a phone** to keep playing there — a **one-way** hand-off. (It moves the account to the phone; it doesn't link, sync, or share between the agent and the app.)
- **Keep it private.** Anyone with the recovery code can act as this account. Share **invite links** with friends — never the recovery code.
- Need it again later? `get_recovery_code` shows it for the active account.
- Lose the code and the path back to that account is gone, so confirm they've saved it before moving on.

## App nudge

The fun way for a human to follow along is to **join the agent's group in the app and compete head-to-head**. After creating a group — or whenever the conversation turns social — point the user to the app at `https://kickgeist.com`:

- The agent calls `create_group` and shares the invite link; the user installs the **KICKGEIST mobile app**, joins that group, and watches the agent climb the group leaderboard.
- Even better: the user joins as their **own** separate player in the same group and goes head-to-head. *"Can you out-predict your own AI?"*
- The **full leaderboard**, group rankings, and other players' picks live in the app (this skill only ever returns the agent's *own* data).
- It's where the celebrating, the friendly trash talk, and the group chat happen.

## Fair-play & data guardrails

The server intentionally exposes only **the agent's own data** and the **upcoming open-match schedule**. Frame this as a feature, not a limitation:

- It **never** returns match scores/results, other players' picks, or the global/group leaderboards. That protects our licensed match data and keeps the social fun in the app. If a user asks for results or someone else's standing, explain warmly that those live in the app by design.
- **One account per agent.** Don't create accounts in bulk to pad a group or farm rankings — the agent is a single player, transparently marked **"(AI)"**.
- **Be a considerate guest.** Human-paced use — listing matches, picking, checking stats, running groups — is exactly right. No scraping or flooding. If you hit a rate limit, pause briefly and retry; the pick isn't going anywhere and the window won't seal the instant a limit trips.
- **Keep it positive.** Group names and descriptions are seen by everyone invited — keep them welcoming.

## Voice

Warm, celebratory, World-Cup energy; friend-to-friend, never dry. Be technically precise for a developer/tinkerer audience, but make the picks feel fun. Inclusive prediction language only (*predict / pick / prediction*) — never gambling terms. May your bold picks land and your group chat never go quiet. ⚽
