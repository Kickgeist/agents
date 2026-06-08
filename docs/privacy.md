# Privacy — KICKGEIST MCP

**Short version: you can play KICKGEIST through your AI agent without giving us your name, your email, or anything that identifies you. No login, no PII, no tracking-you-around-the-web. Just predictions and good fun with friends.**

This page explains, in plain language, what the KICKGEIST MCP integration reads, writes, and stores. It is a friendly summary for the agent integration — not the full legal text. For the complete policy, see the [main KICKGEIST Privacy Policy](https://kickgeist.com/privacy).

---

## What KICKGEIST is

KICKGEIST is a free, group-first, zero-money World Cup 2026 prediction game. You predict the outcome of matches — home win, draw, or away win — compete with friends in groups, and climb leaderboards. There is no money in the game: no in-app purchases, no subscriptions, no entry fees. The app is funded by ads through Google AdMob (more on that below).

The MCP server lets you play through your own AI agent. The endpoint is:

```
https://mcp.kickgeist.com/mcp
```

It is **authless** — connecting requires no OAuth and no login. You add the URL, then call `create_account` to get started.

---

## Your account is anonymous

When you call `create_account`, we create a fresh anonymous KICKGEIST account. We do **not** ask for or require:

- an email address
- a phone number
- a name, username, or real-world identity
- a password
- any social login

Instead, the account is identified by a **recovery code** — a string returned to you when the account is created. That code is the key to your account. Anyone who holds it can reach the account; nobody who lacks it can. So:

- **Save your recovery code somewhere safe.** Call `get_recovery_code` any time to see it again.
- Enter it in the KICKGEIST mobile app (or call `link_account` from another agent) to reach the **same** account from your phone or another client. Your account is fully portable between the app and any agent.
- If you lose the recovery code and have no client still linked to the account, the account can't be recovered — there is no email reset, precisely because there is no email.

---

## What the tools read and write

The MCP server is intentionally narrow. It only ever touches **your own** data plus the public upcoming match schedule. Here is exactly what each tool does:

| Tool | Reads | Writes |
|------|-------|--------|
| `create_account` | — | Creates your anonymous account, returns a recovery code |
| `link_account` | Connects this session to an existing account via its recovery code | — |
| `get_recovery_code` | Your recovery code | — |
| `list_open_matches` | Public upcoming match schedule (teams, kickoff time, stage) | — |
| `predict_match` | — | Your prediction for an open match |
| `create_group` | — | Creates a group you own, returns an invite link |
| `join_group` | — | Adds you to a group via its invite code |
| `get_my_groups` | The groups you belong to | — |
| `get_my_stats` | Your own stats (points, accuracy, streaks, your rank, your group standings) | — |

That's the whole surface. The server stores your **predictions**, the **groups** you create or join, and the **stats** derived from your picks. It does not store anything about you as a person, because there is nothing personal to store.

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

- **Delete your account / data:** open the linked account in the KICKGEIST mobile app, where account deletion lives. Deleting the account removes your predictions, group memberships, and stats.
- **Disconnect the agent:** remove the connector from your AI client. That ends the session; it does not delete the account (use the app for deletion). Your recovery code still reaches the account later if you keep it.
- **Move between clients:** use `link_account` with your recovery code on any agent, or enter it in the app — same account, everywhere.

---

## Questions

For anything not covered here, see the [full KICKGEIST Privacy Policy](https://kickgeist.com/privacy) or reach us through the in-app feedback option. We keep it simple on purpose: no email required to play, your recovery code is the key, and the social fun stays in the app where your friends are.

*This document describes the MCP agent integration and is a plain-language summary. Where it and the main Privacy Policy differ in detail, the [main Privacy Policy](https://kickgeist.com/privacy) governs.*
