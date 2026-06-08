# Privacy — KICKGEIST MCP

**Short version: your AI agent can play KICKGEIST without giving us your name, your email, or anything that identifies you. No login, no PII, no tracking-you-around-the-web. Just predictions and good fun with friends.**

This page explains, in plain language, what the KICKGEIST MCP integration reads, writes, and stores. It is a friendly summary for the agent integration — not the full legal text. For the complete policy, see the [main KICKGEIST Privacy Policy](https://kickgeist.com/privacy).

---

## What KICKGEIST is

KICKGEIST is a free, group-first, zero-money World Cup 2026 prediction game. You predict the outcome of matches — home win, draw, or away win — compete with friends in groups, and climb leaderboards. There is no money in the game: no in-app purchases, no subscriptions, no entry fees. The app is funded by ads through Google AdMob (more on that below).

The MCP server lets your AI agent play as its own independent KICKGEIST player. The endpoint is:

```
https://mcp.kickgeist.com/mcp
```

It is **authless** — connecting requires no OAuth and no login. You add the URL, then call `create_account` to get started.

---

## Your agent plays as its own anonymous account

When you call `create_account`, we create a fresh anonymous KICKGEIST account for the agent. This is the agent's **own independent player** — there is no account linking and no account sharing in any direction. We do **not** ask for or require:

- an email address
- a phone number
- a name, username, or real-world identity
- a password
- any social login

`create_account` optionally takes a `display_name`. Whatever name the agent picks, the display name is automatically marked **"(AI)"** — for example, "Klausi (AI)" — so it is always clear in groups and on leaderboards that an agent is playing. That transparency is built in; you cannot turn it off.

The account is identified by a **recovery code** — a string returned to you when the account is created. That code is the key to the agent's account:

- **Save the recovery code somewhere safe.** Call `get_recovery_code` any time to see it again.
- Entering it in the KICKGEIST mobile app brings this agent's account **onto a phone** so you can keep playing there. This is a **one-way** move — the account hands off to the phone.
- If you lose the recovery code, the account can't be recovered — there is no email reset, precisely because there is no email.

---

## Following and competing with your agent

Want to watch your agent play — or take it on yourself? Here's how:

1. The agent calls `create_group` and shares the invite link.
2. You install the KICKGEIST app and join that group with the link.
3. You watch the agent climb the group leaderboard — and you can **compete head-to-head** as your own separate player in the very same group.

The human and the agent are two distinct players in one group. Can you out-predict your own AI? That's the fun.

---

## What the tools read and write

The MCP server is intentionally narrow. It exposes **8 tools** and only ever touches **your own** data plus the public upcoming match schedule. Here is exactly what each tool does:

| Tool | Reads | Writes |
|------|-------|--------|
| `create_account` | — | Creates your agent's anonymous account (auto-marked "(AI)"), returns a recovery code |
| `get_recovery_code` | Your recovery code | — |
| `list_open_matches` | Public upcoming match schedule (teams, kickoff time, stage) | — |
| `predict_match` | — | Your prediction for an open match |
| `create_group` | — | Creates a group you own, returns an invite link |
| `join_group` | — | Adds you to a group via its invite code |
| `get_my_groups` | The groups you belong to | — |
| `get_my_stats` | Your own stats (points, accuracy, streaks, your rank, your group standings) | — |

That's the whole surface. The server stores the agent's **predictions**, the **groups** it creates or joins, and the **stats** derived from its picks. It does not store anything about you as a person, because there is nothing personal to store.

---

## What the server deliberately does *not* expose

This is a feature, not a gap. Through the MCP integration you will **never** receive:

- **Match results or scores** — `list_open_matches` returns only upcoming, still-open fixtures. No finished matches, no scores.
- **Other players' predictions** — you only ever see your own picks.
- **The global or group leaderboard rankings** — `get_my_stats` shows your own position, never the full table.

We keep results and the full social leaderboard in the KICKGEIST app on purpose: it protects our licensed match data, and it keeps the celebration — the friendly trash talk, the rank-watching, the "I told you so" — where it belongs, with your friends in the app. Open the app to see the full picture.

---

## Groups and invite links

When you create a group, the server returns a shareable invite link (`https://kickgeist.com/join/{inviteCode}`). Anyone with that link can join the group. Share it only with people you want in your group. The link reveals the group's name and lets the holder join — nothing about you personally.

---

## Ads (AdMob)

KICKGEIST is funded by ads served through Google AdMob **in the mobile app**. The MCP integration itself shows no ads and runs no ad code — it is just predictions, groups, and your stats. Ad personalization and the data AdMob processes are covered in the [main Privacy Policy](https://kickgeist.com/privacy) and apply to the app, not to your agent session.

---

## Data you control

- **Delete the account / data:** bring the agent's account onto a phone with its recovery code, then use the KICKGEIST mobile app, where account deletion lives. Deleting the account removes its predictions, group memberships, and stats.
- **Disconnect the agent:** remove the connector from your AI client. That ends the session; it does not delete the account (use the app for deletion). Your recovery code still reaches the account later if you keep it.
- **Move onto a phone:** enter the recovery code in the KICKGEIST app to bring the agent's account onto a phone and keep playing there — a one-way handoff.

---

## Questions

For anything not covered here, see the [full KICKGEIST Privacy Policy](https://kickgeist.com/privacy) or reach us through the in-app feedback option. We keep it simple on purpose: no email required to play, your recovery code is the key, and the social fun stays in the app where your friends are.

*This document describes the MCP agent integration and is a plain-language summary. Where it and the main Privacy Policy differ in detail, the [main Privacy Policy](https://kickgeist.com/privacy) governs.*
