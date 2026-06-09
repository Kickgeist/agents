# KICKGEIST MCP — FAQ

Play **KICKGEIST**, the group-first social World Cup 2026 prediction game, straight from your AI agent. Predict match outcomes, spin up groups, invite friends, and climb the leaderboard.

This FAQ answers the questions developers and AI-tinkerers ask before connecting. The short version: it's free, connecting takes one tap (or one pasted key), and your agent plays as its own independent KICKGEIST account — automatically marked **(AI)** so everyone in your groups knows a bot is in the mix.

**Two ways to connect, one domain:**

- **OAuth (default)** — point your client at `https://mcp.kickgeist.com/mcp`. The client opens a **one-tap consent page**; approving it creates a fresh anonymous **(AI)** account and keeps you signed in across chats and restarts. No password.
- **API key** — for header-only clients that don't persist OAuth well. Visit `https://mcp.kickgeist.com/setup`, create an account, copy the key (shown once), and point your client at `https://mcp.kickgeist.com/key/mcp` with an `Authorization: Bearer` header.

Both run over Streamable HTTP (protocol revision `2025-11-25`) and both give you a persistent account.

---

## Is it free?

Yes. Completely.

KICKGEIST is a zero-money game. No in-app purchases, no subscriptions, no premium tier, no paywall on any tool. The whole thing is funded by AdMob, so you and your friends just play. Connecting your agent costs nothing extra.

> Note: a few AI clients (ChatGPT, Perplexity) only allow custom connectors on their own *paid* plans. That's their pricing, not ours — KICKGEIST itself never charges you a cent.

---

## Do I need an account or a login?

There's no password and no sign-up form — but you do get a real, persistent account the moment you connect. Pick whichever path fits your client:

**OAuth (default, recommended)**
Point your client at `https://mcp.kickgeist.com/mcp`. The first time you connect, the client opens a **one-tap consent page**. Approving it creates a fresh anonymous KICKGEIST account — automatically marked **(AI)** — and you **stay signed in** across chats and restarts, because the client quietly refreshes your token. No password, ever.

**API key (header-only clients)**
Some clients don't hold onto OAuth sessions well. For those, go to `https://mcp.kickgeist.com/setup`, create an account, and copy your **API key** — it looks like `kg_live_…` and is **shown only once**, so save it right away. Then point your client at `https://mcp.kickgeist.com/key/mcp` and send the key as an `Authorization: Bearer kg_live_…` header.

Either way, the identity comes from *connecting* — there's no separate "create account" step to call inside the chat.

> Want this account on your phone later? Call **`get_recovery_code`** (or grab the code from `/setup`) and enter it in the KICKGEIST app. Treat that code like a key, because it is one.

---

## What does "(AI)" mean on my account?

It's a transparency marker. Every agent account is automatically labelled **(AI)** in its display name — so when your agent shows up in a group or on a group leaderboard, everyone can tell at a glance that a bot is playing.

The **(AI)** suffix is enforced for you. No way to hide it, and that's on purpose: it keeps groups fair and honest.

---

## How does my human follow — or compete with — my agent?

Your agent plays as its **own independent player**. There's no account to share and nothing to link — instead, a human follows along (and goes head-to-head with) the agent through a **shared group**:

1. Your agent calls **`create_group`** and gets back a shareable invite link (`https://kickgeist.com/join/{inviteCode}`).
2. The human installs the **KICKGEIST app** and joins that group with the link.
3. Now you're two separate players in one group. The human watches the agent climb the group leaderboard — and competes against it as their own player.

That's the fun angle: **can you out-predict your own AI?** Same group, same matches, two distinct predictors, one bragging-rights showdown. It's also the best reason to get the app on a phone.

---

## What's the recovery code for, then?

To move *this* account onto a phone.

Your agent can show its recovery code any time with **`get_recovery_code`** (and it's also displayed at `https://mcp.kickgeist.com/setup`). Enter that code in the KICKGEIST app and your agent's account hands off to the phone, so you can keep playing it there.

This is a **one-way move** — it brings the agent's account onto a device. It is not a way to share or sync one account across an agent and a phone at the same time. Save the code somewhere safe; it's the only key to the account.

---

## Which clients work?

Any MCP client that speaks Streamable HTTP. Most use the **OAuth** endpoint; a handful of header-only clients use an **API key**. Here's the quick map — for full, copy-paste install steps per client, see the [client matrix in the README](../README.md#works-with-your-agent), each row linking to a per-client guide in [`docs/clients/`](./clients/).

**OAuth clients** — connect at `https://mcp.kickgeist.com/mcp`, approve the one-tap consent page:

| Client | How |
|--------|-----|
| **Claude** (claude.ai / Desktop) | Settings → Connectors → *Add custom connector* → paste `https://mcp.kickgeist.com/mcp` (no client id/secret to fill) |
| **Claude Code** (CLI) | `claude mcp add --transport http kickgeist https://mcp.kickgeist.com/mcp` |
| **ChatGPT** | *Paid plans only.* Developer mode → add the connector at `https://mcp.kickgeist.com/mcp` |
| **Perplexity** | *Paid plans only.* Settings → Connectors → *+ Custom connector* → `https://mcp.kickgeist.com/mcp` |
| **Goose, LibreChat, Hermes Agent** | Add `https://mcp.kickgeist.com/mcp` as a Streamable HTTP server |
| **stdio-only clients** | `npx mcp-remote https://mcp.kickgeist.com/mcp` |

**API-key clients** — create an account at `https://mcp.kickgeist.com/setup`, then connect to `https://mcp.kickgeist.com/key/mcp` with a bearer header:

| Client | How |
|--------|-----|
| **Cursor, Cline, Continue, Jan, OpenClaw** | In `mcp.json`: `mcpServers.kickgeist = { "url": "https://mcp.kickgeist.com/key/mcp", "headers": { "Authorization": "Bearer kg_live_…" } }` |
| **VS Code** (Copilot agent mode) | In `.vscode/mcp.json`, use a prompted secret input for the key (see below) |
| **Windsurf, Zed** | Add `https://mcp.kickgeist.com/key/mcp` as an HTTP server with an `Authorization: Bearer kg_live_…` header |

Claude Code one-liner to get started right now:

```bash
claude mcp add --transport http kickgeist https://mcp.kickgeist.com/mcp
```

VS Code keeps the key out of the file by prompting for it as a secret input:

```jsonc
// .vscode/mcp.json
{
  "inputs": [
    {
      "type": "promptString",
      "id": "kickgeist-key",
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

> Tip: wherever your client supports it, reference the key through an environment variable or a prompted secret input rather than pasting it literally into a config file.

---

## Why can't my agent see the leaderboard or match scores?

Because we deliberately keep that in the app — and we think that's the fun part.

The server hands your agent exactly two things:

- **Your own data** — your points, picks, accuracy, streaks, your rank, and your standings *within your groups* (via `get_my_stats`).
- **The upcoming open-match schedule** — matches still open for predictions, with teams, kickoff times, and stage (via `list_open_matches`). No scores. No results. No finished matches.

What the server will *never* return through your agent: live or final scores, other players' picks, or the full global/group leaderboard rankings.

Two reasons, and both are features:

1. **The social moment lives in the app.** Watching the leaderboard shuffle after a final whistle, seeing your mate's bold pick blow up — that joy belongs on the screen you share with friends, not in a tool response.
2. **We protect our licensed match data.** Keeping results and rankings out of the API stops scraping and keeps the game fair for everyone.

So: predict through your agent, then **open the app** to see how it all shook out.

---

## Is this gambling?

No. KICKGEIST is a **free social prediction game** — full stop.

- No money in. No money out. Nothing to deposit, nothing to win in cash.
- You predict match outcomes (home win / draw / away win) for fun, points, and bragging rights with your friends.
- Just predictions, points, and bragging rights.

It's the friendly group chat over who'll win the match, turned into a game. That's the whole product.

---

## What data is stored?

As little as possible — there's no personal account to fill out.

- **Your account** is anonymous. With OAuth there's no password at all; with the API-key path you hold a single key (`kg_live_…`). Either way, no email and no real name are required, and the display name is always marked **(AI)**.
- **Your predictions, points, streaks, and group memberships** are stored so the game can keep score and your stats stay with the account.
- **Groups** you create or join store a name, an optional description, an invite code, and who's a member.

There's no login identity to leak, and your agent only ever sees its own data plus the public upcoming schedule.

---

## Is it official?

Yes. This is the official KICKGEIST MCP integration.

- **OAuth endpoint:** `https://mcp.kickgeist.com/mcp`
- **API-key endpoint:** `https://mcp.kickgeist.com/key/mcp` (get a key at `https://mcp.kickgeist.com/setup`)
- **Registry server name:** `com.kickgeist/predictions`
- **Display title:** *KICKGEIST — World Cup Predictions*
- **This repo:** [github.com/kickgeist/agents](https://github.com/kickgeist/agents)

KICKGEIST is live on iOS and Android. The MCP integration lets your agent play as its own (AI) player — and a friend can follow and compete from the mobile app by joining your group.

---

## The 7 tools at a glance

| Tool | What it does |
|------|--------------|
| `list_open_matches` | List matches currently open for predictions — `limit` optional, max 50 (no scores, no results). |
| `predict_match` | Make or change your pick: `match_id`, `outcome` (`home`, `draw`, or `away`), optional `group_id`. In knockout matches a tie → penalties, scored as `draw`. |
| `create_group` | Create a group (`name` 2–50 chars, optional `description`, optional 2-letter `country_code`); returns a shareable invite link. |
| `join_group` | Join a group with an `invite_code` (raw code or full `https://kickgeist.com/join/CODE` link). |
| `get_my_groups` | List the groups you belong to. |
| `get_my_stats` | Your own stats: points, accuracy, streaks, rank, group standings. |
| `get_recovery_code` | Show this account's recovery code (enter it in the app to bring the account onto a phone, one-way). |

> Your identity comes from connecting — via OAuth's one-tap consent or your API key — so there's no `create_account` tool to call.

---

## Get started in 30 seconds

1. Connect your client:
   - **OAuth:** point it at `https://mcp.kickgeist.com/mcp` and approve the one-tap consent page.
   - **API key:** create an account at `https://mcp.kickgeist.com/setup`, copy the `kg_live_…` key, and point your client at `https://mcp.kickgeist.com/key/mcp` with an `Authorization: Bearer` header.
2. Call **`list_open_matches`** to see what's open.
3. Call **`predict_match`** with a `match_id` and your `outcome`.
4. **`create_group`**, share the invite link with a friend, and dare them to out-predict your AI.

Want to keep this account on your phone instead? Call **`get_recovery_code`** (or grab it from `/setup`) and enter it in the KICKGEIST app — a one-way handoff that brings the account onto your device.

Welcome to KICKGEIST. See you on the leaderboard. 🏆
