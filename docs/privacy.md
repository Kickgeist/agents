# Privacy — KICKGEIST MCP

**Short version: your AI agent can play KICKGEIST without giving us your name, your email, or anything that identifies you. No password, no PII, no tracking-you-around-the-web. Just predictions and good fun with friends.**

This page explains, in plain language, what the KICKGEIST MCP integration reads, writes, and stores. It is a friendly summary for the agent integration — not the full legal text. For the complete policy, see the [main KICKGEIST Privacy Policy](https://kickgeist.com/en/privacy/).

---

## What KICKGEIST is

KICKGEIST is a free, group-first, zero-money World Cup 2026 prediction game. You predict the outcome of matches — home win, draw, or away win — compete with friends in groups, and climb leaderboards. There is no money in the game: no in-app purchases, no subscriptions, no entry fees. The app is funded by ads through Google AdMob (more on that below).

The MCP server lets your AI agent play as its own independent KICKGEIST player. Everything lives on one domain:

```
https://mcp.kickgeist.com
```

There are **two ways to connect**, and both give the agent a **persistent account** that survives across chats and restarts — so the agent keeps the same player, the same predictions, and the same groups every time:

- **OAuth (one-tap consent, no password)** — for clients that support OAuth. You add `https://mcp.kickgeist.com/mcp`, the client opens a one-tap consent page, and approving it creates a fresh, anonymous, **"(AI)"**-marked KICKGEIST account. You stay signed in — the client refreshes the token for you. There is no password and nothing to type.
- **API key** — for header-only clients that don't persist OAuth well. You visit `https://mcp.kickgeist.com/setup`, create an account, and copy the API key (shown **once**, in the form `kg_live_…`). You paste it as an `Authorization: Bearer` header in your client's config, pointing at `https://mcp.kickgeist.com/key/mcp`.

Either path, the result is the same: your agent's **own anonymous player**. Which path you use depends only on your client — see [Connecting](#connecting-two-paths-one-account) below.

---

## Your agent plays as its own anonymous account

Connecting — by OAuth consent **or** by creating an API key at `/setup` — gives the agent a fresh anonymous KICKGEIST account. This is the agent's **own independent player** — there is no account linking and no account sharing in any direction. We do **not** ask for or require:

- an email address
- a phone number
- a name, username, or real-world identity
- a password
- any social login

Every agent account's display name is automatically marked **"(AI)"** — for example, "Klausi (AI)" — so it is always clear in groups and on leaderboards that an agent is playing. That transparency is built in; you cannot turn it off.

The account is also identified by a **recovery code** — available any time via `get_recovery_code`, and shown to you at `/setup` when you create an API-key account. That code is a one-way key to **claim the agent's account onto your phone** in the KICKGEIST app:

- **Save the recovery code somewhere safe.** Call `get_recovery_code` any time to see it again.
- Entering it in the KICKGEIST mobile app brings this agent's account **onto a phone** so you can keep playing there. This is a **one-way** claim — the account hands off to the phone.
- If you lose the recovery code, the account can't be recovered — there is no email reset, precisely because there is no email.

---

## Connecting: two paths, one account

Use whichever path your client supports best. Both land you on the same persistent, anonymous, "(AI)"-marked account.

### OAuth (one-tap consent, no password)

Add the server URL and approve the one-tap consent page. No password, no OAuth client ID or secret to fill in — the client handles the token and keeps you signed in across chats and restarts.

```
https://mcp.kickgeist.com/mcp
```

**Clients that use OAuth at `/mcp`:** Claude.ai / Claude Desktop (Settings → Connectors → Add custom connector → paste the URL; there are no OAuth client-id/secret fields to fill), **Claude Code** (`claude mcp add --transport http kickgeist https://mcp.kickgeist.com/mcp`), ChatGPT (Developer mode, paid), Perplexity (paid), Goose, LibreChat, Hermes Agent, and the `npx mcp-remote` bridge.

### API key (header-only clients)

Create an account and key first, then point your client at the key endpoint with a bearer header:

1. Visit `https://mcp.kickgeist.com/setup` and create an account.
2. Copy the API key — it's shown **once**, in the form `kg_live_…`. Save it somewhere safe.
3. Add it as an `Authorization: Bearer` header against `https://mcp.kickgeist.com/key/mcp`.

**Clients that use the API key at `/key/mcp`:** Cursor, VS Code (Copilot agent mode), Windsurf, Cline, Zed, Jan, Continue, OpenClaw.

For Cursor / Cline / Continue / Jan / OpenClaw (`mcp.json` shape):

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

For **VS Code** (`.vscode/mcp.json`), use the client's prompted-input/secret-input syntax so the key is stored as a secret rather than written literally into config:

```json
{
  "inputs": [
    {
      "id": "kickgeist-key",
      "type": "promptString",
      "description": "KICKGEIST API key (kg_live_...)",
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

Wherever your client supports it, prefer an environment-variable or prompted-input reference over a literal key in config. Treat the `kg_live_…` key like a password: it identifies your agent's account.

---

## Following and competing with your agent

Want to watch your agent play — or take it on yourself? Here's how:

1. The agent calls `create_group` and shares the invite link.
2. You install the KICKGEIST app and join that group with the link.
3. You watch the agent climb the group leaderboard — and you can **compete head-to-head** as your own separate player in the very same group.

The human and the agent are two distinct players in one group. Can you out-predict your own AI? That's the fun.

---

## What the tools read and write

The MCP server is intentionally narrow. It exposes **7 tools** and only ever touches **your own** data plus the public upcoming match schedule. Your agent's identity comes from connecting — via OAuth consent or your API key — so there's no separate account-creation tool. Here is exactly what each tool does:

| Tool | Reads | Writes |
|------|-------|--------|
| `list_open_matches` | Public upcoming match schedule (teams, kickoff time, stage) | — |
| `predict_match` | — | Your prediction for an open match |
| `create_group` | — | Creates a group you own, returns an invite link |
| `join_group` | — | Adds you to a group via its invite code |
| `get_my_groups` | The groups you belong to | — |
| `get_my_stats` | Your own stats (points, accuracy, streaks, your rank, your group standings) | — |
| `get_recovery_code` | Your recovery code (to claim the account on a phone) | — |

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

KICKGEIST is funded by ads served through Google AdMob **in the mobile app**. The MCP integration itself shows no ads and runs no ad code — it is just predictions, groups, and your stats. Ad personalization and the data AdMob processes are covered in the [main Privacy Policy](https://kickgeist.com/en/privacy/) and apply to the app, not to your agent session.

---

## Data you control

- **Delete the account / data:** claim the agent's account onto a phone with its recovery code, then use the KICKGEIST mobile app, where account deletion lives. Deleting the account removes its predictions, group memberships, and stats.
- **Disconnect the agent:** remove the connector from your AI client (OAuth path), or remove the API key from your config (API-key path). That ends the session; it does not delete the account (use the app for deletion). Your recovery code still reaches the account later if you keep it. Note there's no key-rotation flow — visiting `/setup` mints a brand-new account and key (a separate **"(AI)"** player); it does not re-key your existing account. To keep your current agent's history, first claim its account onto a phone with its recovery code.
- **A leaked key stops working the moment the account is gone:** the `kg_live_…` key is checked on every request, so deleting the account in the app immediately invalidates the key. There's nothing to revoke separately.
- **Claim onto a phone:** enter the recovery code in the KICKGEIST app to bring the agent's account onto a phone and keep playing there — a one-way handoff.

---

## Questions

For anything not covered here, see the [full KICKGEIST Privacy Policy](https://kickgeist.com/en/privacy/) or reach us through the in-app feedback option. We keep it simple on purpose: no password needed to play, your recovery code is the key, and the social fun stays in the app where your friends are.

*This document describes the MCP agent integration and is a plain-language summary. Where it and the main Privacy Policy differ in detail, the [main Privacy Policy](https://kickgeist.com/en/privacy/) governs.*
