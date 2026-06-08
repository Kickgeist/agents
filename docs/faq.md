# KICKGEIST MCP — FAQ

Play **KICKGEIST**, the group-first social World Cup 2026 prediction game, straight from your AI agent. Predict match outcomes, spin up groups with friends, and climb the leaderboard — no logins, no forms, no friction.

This FAQ answers the questions developers and AI-tinkerers ask before connecting. The short version: it's free, it's authless, and the account you make here is the same one you play on your phone.

**Endpoint:** `https://mcp.kickgeist.com/mcp` — a single Streamable HTTP MCP endpoint (protocol revision `2025-11-25`).

---

## Is it free?

Yes. Completely.

KICKGEIST is a zero-money game. No in-app purchases, no subscriptions, no premium tier, no paywall on any tool. The whole thing is funded by AdMob, so you and your friends just play. Connecting your agent costs nothing extra.

> Note: a few AI clients (ChatGPT, Perplexity) only allow custom connectors on their own *paid* plans. That's their pricing, not ours — KICKGEIST itself never charges you a cent.

---

## Do I need an account or a login?

No login. No OAuth. No sign-up form.

The server is **authless** — you connect by adding the URL, and that's it. To get an identity you simply call one tool:

- **`create_account`** — makes a fresh anonymous KICKGEIST account for your session and returns a **recovery code**.

That recovery code *is* your account. There's no email, no password, nothing to remember except that one code.

> **Save your recovery code the moment you get it.** It's the only way back into your account — on another agent, a new session, or in the mobile app. Treat it like a key, because it is one.

If you already play KICKGEIST on your phone, you don't even need `create_account` — see the next question.

---

## Can I use the account I already play on my phone?

Yes — and you should. Your phone account and your agent are the *same* KICKGEIST identity.

1. Open the KICKGEIST app on your phone.
2. Find your **recovery code** under the account / recovery section.
3. In your agent, call **`link_account`** with that code:

```text
link_account  { "recovery_code": "YOUR-CODE-FROM-THE-APP" }
```

Now your agent and your phone share one account: predictions you make through your agent show up in the app, and vice-versa. Same points, same groups, same streak.

Going the other direction works too. If you created your account *here* with `create_account`, type that recovery code into the mobile app to pick up the same game on your phone. Lost track of it mid-session? Call **`get_recovery_code`** to display the linked account's code again.

---

## Which clients work?

Any MCP client that speaks Streamable HTTP. Add the endpoint `https://mcp.kickgeist.com/mcp` and you're in — no OAuth step.

For full, copy-paste install steps per client, see the [client matrix in the README](../README.md#works-with-your-agent) — each row links to a per-client guide in [`docs/clients/`](./clients/). The quick map:

| Client | How |
|--------|-----|
| **Claude** (claude.ai / Desktop) | Settings → Connectors → *Add custom connector* → paste the URL |
| **Claude Code** (CLI) | `claude mcp add --transport http kickgeist https://mcp.kickgeist.com/mcp` |
| **ChatGPT** | *Paid plans only.* Settings → Apps & Connectors → enable Developer mode → Create connector |
| **Perplexity** | *Paid plans only.* Settings → Connectors → *+ Custom connector* → Remote |
| **Cursor** | One-click deep link, or add to `~/.cursor/mcp.json` |
| **VS Code** (Copilot agent mode) | One-click `vscode:mcp/install`, or `.vscode/mcp.json` |
| **Windsurf, Zed, Cline, Continue, Jan, Goose, LibreChat** | Add the URL as a Streamable HTTP server in config |
| **stdio-only clients** | `npx mcp-remote https://mcp.kickgeist.com/mcp` |

Claude Code one-liner to get started right now:

```bash
claude mcp add --transport http kickgeist https://mcp.kickgeist.com/mcp
```

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
- No money in, no money out — just predictions, points, and bragging rights.

It's the friendly group chat over who'll win the match, turned into a game. That's the whole product.

---

## What data is stored?

As little as possible — there's no personal account to fill out.

- **Your account** is anonymous, keyed to a recovery code. No email, no password, no name required.
- **Your predictions, points, streaks, and group memberships** are stored so the game can keep score and your stats follow you across your agent and the app.
- **Groups** you create or join store a name, an optional description, an invite code, and who's a member.

There's no login identity to leak, and your agent only ever sees your own data plus the public upcoming schedule.

---

## Is it official?

Yes. This is the official KICKGEIST MCP integration.

- **Endpoint:** `https://mcp.kickgeist.com/mcp`
- **Registry server name:** `com.kickgeist/predictions`
- **Display title:** *KICKGEIST — World Cup Predictions*
- **This repo:** [github.com/kickgeist/agents](https://github.com/kickgeist/agents)

KICKGEIST is live on iOS and Android. The MCP integration lets you play through your own AI agent, fully in sync with the mobile app.

---

## The 9 tools at a glance

| Tool | What it does |
|------|--------------|
| `create_account` | Create a fresh anonymous account; returns your recovery code. **Save it.** |
| `link_account` | Connect this session to an existing account using its recovery code. |
| `get_recovery_code` | Show the recovery code for the currently linked account. |
| `list_open_matches` | List matches currently open for predictions (no scores, no results). |
| `predict_match` | Make or change your pick: `home`, `draw`, or `away`. |
| `create_group` | Create a group; returns a shareable invite link. |
| `join_group` | Join a group with an invite code or link. |
| `get_my_groups` | List the groups you belong to. |
| `get_my_stats` | Your own stats: points, accuracy, streaks, rank, group standings. |

---

## Get started in 30 seconds

1. Add the endpoint to your client: `https://mcp.kickgeist.com/mcp`
2. Call **`create_account`** → **save the recovery code**.
3. Call **`list_open_matches`** to see what's open.
4. Call **`predict_match`** with a `match_id` and your `outcome`.
5. **`create_group`**, share the invite link with friends, and may the best fan win.

Already playing on your phone? Skip step 2 — call **`link_account`** with your recovery code from the app and pick up right where you left off.

Welcome to KICKGEIST. See you on the leaderboard. 🏆
