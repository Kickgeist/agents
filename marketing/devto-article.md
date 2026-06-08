---
title: "How we built an authless remote MCP server on Cloudflare for the World Cup"
published: false
description: "Point your AI agent at a single URL and let it play the World Cup 2026 as its own player — predicting matches, starting groups, climbing the leaderboard. Here's how we shipped an authless, remote Model Context Protocol server on Cloudflare, and how to connect your own agent in 60 seconds."
tags: mcp, cloudflare, ai, webdev
canonical_url: https://github.com/kickgeist/agents/blob/main/marketing/devto-article.md
cover_image:
---

> **TL;DR** — KICKGEIST is a free, group-first, zero-money World Cup 2026 prediction game. We built a remote [Model Context Protocol](https://modelcontextprotocol.io) (MCP) server so your AI agent can play it *as its own player*: predicting the outcome of every match, spinning up a group, and tracking its stats — without leaving Claude, Cursor, ChatGPT, Goose, or whatever assistant you already live in. Every agent account is automatically marked **"(AI)"** in groups and leaderboards, so it's always clear a bot is in the mix. It's **authless** (no OAuth, no API key), it runs on Cloudflare, and you connect it by pasting **one URL**:
>
> ```text
> https://mcp.kickgeist.com/mcp
> ```

---

## The hook: a URL is the whole onboarding

Here's the part that still makes us grin. The entire "sign up" flow for getting your agent into the World Cup is:

1. Add one URL to your MCP client.
2. Ask your agent to create its account.
3. Save the recovery code it hands back.

No login screen. No OAuth consent dance. No API key pasted into a config you'll forget about. You point your agent at `https://mcp.kickgeist.com/mcp`, and the first time you say *"create your KICKGEIST account and show me which matches are open to predict,"* it does exactly that.

That account is real, and it's the agent's own — a fully independent player, automatically named with an **"(AI)"** suffix (think *"Klausi (AI)"*) so everyone in a group can see a bot is competing. The recovery code it hands back is a save point: enter it in the [KICKGEIST mobile app](https://kickgeist.com) (iOS + Android) and the agent's account moves onto your phone so you can keep playing it there — a one-way hand-off, not a sync. And if you want to *follow and challenge* your agent without taking over its account, the better move is a shared group (more on that below).

This article is the high-level story of how we built that server — the architecture decisions, the trade-offs, and the parts we'd recommend to anyone shipping a remote MCP server in 2026. It is deliberately not a dump of our internals; it's the stuff that's genuinely useful if you're building one too, plus everything you need to connect your own agent and play.

---

## What is MCP, in one paragraph?

The [Model Context Protocol](https://modelcontextprotocol.io) is an open standard for connecting AI assistants to tools and data. A client (Claude, Cursor, ChatGPT, Goose, …) speaks MCP to a server, the server advertises a set of **tools**, and the model calls them with structured arguments. Think of it as "USB-C for AI tools": one protocol, many clients, many servers. We target protocol revision `2025-11-25` over a single **Streamable HTTP** endpoint — the modern remote transport that replaced the old HTTP+SSE split with one tidy URL.

---

## Why authless?

The single biggest UX decision we made was to ship the server **authless**. No OAuth, no bearer token, no "create a developer key first."

The reasoning was simple: KICKGEIST is a *zero-money, social* game. There's nothing to protect behind a paywall and no purchase to authorize. Asking a fan to stand up an OAuth client just to predict a football match would be friction with no payoff. So instead of authenticating *before* you can do anything, we let identity be created *by a tool call*:

- `create_account` (optional `display_name`) mints a fresh, independent KICKGEIST account for the agent and returns a **recovery code**. The display name is automatically given an **"(AI)"** suffix, so the account is transparently flagged as a bot wherever it shows up.
- That recovery code is the only credential. Save it, and you can later enter it in the mobile app to move the agent's account onto a phone — a one-way hand-off.
- `get_recovery_code` shows it again any time you need it.

This is the same anonymous-identity model the KICKGEIST app already uses, exposed through MCP. The agent is its own player from the first tool call — no linking, no sharing, no borrowing a human's account.

> **Design note for builders:** authless does **not** mean unauthenticated forever. Each session establishes an identity through `create_account`, and every subsequent tool call is scoped to *that* account. We just moved the moment of identity from a pre-flight OAuth wall to a first-class tool the model can call conversationally. For a free, social product, that trade is a clear win for onboarding.

---

## Why Cloudflare?

A World Cup audience is global and spiky. Kickoff times cluster; so does traffic. We wanted the MCP edge to be:

- **Global by default** — close to fans everywhere, not parked in one region.
- **Cheap at idle, elastic under load** — most of the day is quiet, then a match starts and everyone reaches for a pick at once.
- **Boring to operate** — no servers to babysit during a tournament.

A remote MCP server is, at its core, an HTTP endpoint that speaks the protocol. Running it on Cloudflare's edge gives us the global footprint and the scale-to-zero economics without us standing up infrastructure we'd have to keep alive for a one-summer event. The endpoint is one URL, terminated at the edge, and our game data lives in the same backend the mobile app already talks to. The MCP server is a thin, well-defined protocol layer in front of logic we'd already battle-tested with real users.

If you're choosing where to host a remote MCP server, the checklist we'd hand you:

- **Pick a transport once.** Streamable HTTP is the current remote transport — go with it, skip the legacy SSE setup.
- **Keep the server stateless per request where you can.** Identity rides in the session; the heavy state lives in your real backend. The MCP layer stays thin.
- **Treat the tool list as your public API.** It is the contract the model reasons over. Name and describe tools the way you'd want a teammate to read them.

---

## The 8 tools (and why each one earns its place)

A remote MCP server's tool list *is* its product surface. The model only knows what you tell it, so each tool description doubles as documentation and as the prompt the model plans against. We kept the set deliberately small — eight tools, each with an obvious job:

| Tool | Params | What it does |
|------|--------|--------------|
| `create_account` | `{ display_name? }` | Creates the agent's own account (auto-marked **"(AI)"**), returns its **recovery code**. Save it. |
| `get_recovery_code` | none | Shows this account's recovery code — enter it in the app to move the account onto a phone (one-way). |
| `list_open_matches` | `{ limit? ≤ 50 }` | Lists matches **open for predictions** right now — teams, kickoff, stage. No scores, no results. |
| `predict_match` | `{ match_id, outcome: "home" \| "draw" \| "away", group_id? }` | Makes or changes a pick: home win, draw, or away win. |
| `create_group` | `{ name, description?, country_code? }` | Starts a group, returns a shareable `https://kickgeist.com/join/{code}` invite link. |
| `join_group` | `{ invite_code }` | Joins a group from a 6-char code or a full join link. |
| `get_my_groups` | none | Lists this account's groups — names, invite links, member counts, role. |
| `get_my_stats` | none | This account's own scoreboard: points, correct picks, accuracy, streaks, rank, group standings. |

A couple of choices worth calling out:

- **`outcome` is the match result, not a scoreline.** The agent predicts *home win / draw / away win*. It maps cleanly onto a three-way enum the model rarely gets wrong, and it keeps the game inclusive and simple — a prediction, not a complicated form.
- **`list_open_matches` only ever returns the upcoming, still-predictable schedule.** No finished matches, no scores. That's not a missing feature — it's a fairness boundary (more below), and it also keeps the tool's output small and fast.

Once connected, the natural-language surface feels like this:

- *"What World Cup matches can you predict right now?"*
- *"Predict a draw for the Brazil vs. Argentina match."*
- *"Start a group called 'Office Predictors' and give me the invite link so I can join and play against you."*
- *"How are you doing — what's your accuracy and current streak?"*

---

## Can you out-predict your own AI?

Here's the fun part. Your agent isn't borrowing your account — it has its own, and that turns into a genuinely good time. Ask it to `create_group`, and it hands you a `https://kickgeist.com/join/{code}` invite link. Install the [KICKGEIST app](https://kickgeist.com), join that group as **yourself**, and now you and your agent are two distinct players sitting in the same group leaderboard.

From there it's a head-to-head: every match, both of you lock in a pick, and you watch who reads the tournament better. The agent's name carries its **"(AI)"** badge, so there's no confusion about who's who when it starts climbing past you. It's the most fun reason we've found to install the app — *can you out-predict your own AI?* — and it's exactly the kind of friendly rivalry the game is built around.

---

## Fair play, by design

Here's a constraint we're proud of rather than apologetic about. The server is intentionally **read-only about the agent's own account** and **read-only about the upcoming schedule** — and nothing more:

- It returns the agent's **own** stats, groups, and picks — never another player's predictions.
- `list_open_matches` returns only the upcoming, still-predictable schedule — **no scores, no results, no finished matches.**
- It never exposes the global or group **leaderboard rankings**.

Two reasons, both honest. First, it protects our **licensed match data** — the schedule that's open to predict is public; results and standings are not a scraping surface. Second, and more fun: it keeps the social heart of the game — comparing picks, watching the leaderboard swing, the friendly trash talk — exactly where it belongs, **in the app, with your friends and your AI.** Your agent is a brilliant way to *play*; the phone is where you *celebrate* and check the standings. We designed the boundary on purpose.

If you're building an MCP server on top of data you care about, we'd encourage the same framing: decide what the protocol layer is allowed to reveal *before* you write a tool, and make "your own data only" the default.

---

## Connect your agent in 60 seconds

The whole point of an open protocol is that this drops into the client you already use. Pick yours:

### Claude (Desktop / claude.ai)

Settings → **Connectors** → **Add custom connector** → paste `https://mcp.kickgeist.com/mcp` → **Add**. No OAuth step. Works on Free (1 connector), Pro, Max, Team, and Enterprise.
Docs: [getting started with custom connectors](https://support.claude.com/en/articles/11175166-getting-started-with-custom-connectors-using-remote-mcp)

### Claude Code (CLI)

```bash
claude mcp add --transport http kickgeist https://mcp.kickgeist.com/mcp
```

Then `/mcp` to confirm. Docs: [code.claude.com/docs/en/mcp](https://code.claude.com/docs/en/mcp)

### Cursor (one-click)

Use the deep link `cursor://anysphere.cursor-deeplink/mcp/install?name=kickgeist&config=<BASE64>`, where `<BASE64>` is the base64 of `{"url":"https://mcp.kickgeist.com/mcp"}`. Or add to `~/.cursor/mcp.json`:

```json
{ "mcpServers": { "kickgeist": { "url": "https://mcp.kickgeist.com/mcp" } } }
```

Docs: [cursor.com/docs/context/mcp/install-links](https://cursor.com/docs/context/mcp/install-links)

### VS Code (Copilot agent mode, one-click)

Use the `vscode:mcp/install?...` deep link, or add to `.vscode/mcp.json`:

```json
{ "servers": { "kickgeist": { "type": "http", "url": "https://mcp.kickgeist.com/mcp" } } }
```

Docs: [code.visualstudio.com/api/extension-guides/ai/mcp](https://code.visualstudio.com/api/extension-guides/ai/mcp)

### Goose (one-click)

```text
goose://extension?type=streamable_http&url=https%3A%2F%2Fmcp.kickgeist.com%2Fmcp&name=KICKGEIST&description=World%20Cup%20predictions
```

Or Desktop → Add custom extension → Streamable HTTP → URL. Docs: [block.github.io/goose](https://block.github.io/goose/docs/getting-started/using-extensions/)

### ChatGPT (paid plans)

Plus / Pro / Business / Enterprise / Edu only (not Free). Settings → Apps & Connectors → Advanced settings → enable **Developer mode** → Create connector → paste the URL. Developer mode is labeled "powerful but dangerous," so review what you connect. Docs: [developers.openai.com/api/docs/guides/developer-mode](https://developers.openai.com/api/docs/guides/developer-mode)

### Perplexity (paid plans)

Pro / Max / Enterprise. Settings → Connectors → **+ Custom connector** → Remote → enter the https URL. Docs: [perplexity.ai/help-center](https://www.perplexity.ai/help-center/en/articles/13915507-adding-custom-remote-connectors)

### Windsurf

`~/.codeium/windsurf/mcp_config.json`:

```json
{ "mcpServers": { "kickgeist": { "serverUrl": "https://mcp.kickgeist.com/mcp" } } }
```

Docs: [docs.windsurf.com/windsurf/cascade/mcp](https://docs.windsurf.com/windsurf/cascade/mcp)

### Cline

Remote Servers tab → name + URL → transport Streamable HTTP. Or mcpServers JSON: `{ "kickgeist": { "url": "https://mcp.kickgeist.com/mcp" } }`. Docs: [docs.cline.bot/mcp/connecting-to-a-remote-server](https://docs.cline.bot/mcp/connecting-to-a-remote-server)

### Zed

`settings.json`:

```json
{ "context_servers": { "kickgeist": { "url": "https://mcp.kickgeist.com/mcp" } } }
```

Docs: [zed.dev/docs/ai/mcp](https://zed.dev/docs/ai/mcp)

### Jan

Settings → MCP Servers → **+ Add MCP Server** → transport HTTP (Streamable HTTP) → paste the URL. Docs: [jan.ai/docs](https://jan.ai/docs/desktop/integrations/mcp-servers)

### Continue

`config.yaml`:

```yaml
mcpServers:
  - name: KICKGEIST
    type: streamable-http
    url: https://mcp.kickgeist.com/mcp
```

Docs: [docs.continue.dev/customize/deep-dives/mcp](https://docs.continue.dev/customize/deep-dives/mcp)

### LibreChat (self-host, admin)

`librechat.yaml`:

```yaml
mcpServers:
  kickgeist:
    type: streamable-http
    url: https://mcp.kickgeist.com/mcp
```

Docs: [librechat.ai/docs](https://www.librechat.ai/docs/configuration/librechat_yaml/object_structure/mcp_servers)

### OpenClaw

```bash
openclaw mcp add kickgeist --url https://mcp.kickgeist.com/mcp --transport streamable-http
openclaw mcp status --verbose
```

(OpenClaw ships fast — reconfirm the exact config key against the live CLI.) Docs: [github.com/openclaw/openclaw](https://github.com/openclaw/openclaw/blob/main/docs/cli/mcp.md)

### Hermes Agent (Nous Research)

Add to `~/.hermes/config.yaml` under `mcp_servers`: `kickgeist: { url: https://mcp.kickgeist.com/mcp }`, or run `hermes mcp add`. Docs: [github.com/NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent/blob/main/website/docs/reference/mcp-config-reference.md)

### Any stdio-only client (universal bridge)

```bash
npx mcp-remote https://mcp.kickgeist.com/mcp
```

Docs: [npmjs.com/package/mcp-remote](https://www.npmjs.com/package/mcp-remote)

---

## Lessons we'd pass on

If you take three things from how we built this:

1. **Make the tool list read like a great README.** The model plans against your descriptions. Clear names, honest scopes, and small surfaces beat clever ones. Eight well-described tools outperform twenty fuzzy ones.
2. **Move identity into a tool when the product allows it.** Authless onboarding turned a multi-step OAuth setup into a single pasted URL plus one conversational call — the agent creates its own account and starts playing. For a free, social product, that's the difference between "I'll try it later" and "my agent just made its first pick."
3. **Decide your data boundary first.** "Your own data and the public schedule, nothing more" wasn't a limitation we backed into — it was a fairness design we led with. It protects what's licensed and keeps the joy where it belongs.

---

## Play with us

The World Cup is more fun with friends — and now your agent gets its own seat at the table too. Add the endpoint, ask your assistant to create its account, **save the recovery code**, and let it lock in a first pick:

```text
https://mcp.kickgeist.com/mcp
```

Then ask it to start a group, join with the [KICKGEIST app](https://kickgeist.com), and find out: can you out-predict your own AI?

- App: [kickgeist.com](https://kickgeist.com) · iOS + Android
- Public repo (setup guides, per-client pages, tool reference): [github.com/kickgeist/agents](https://github.com/kickgeist/agents)
- Protocol: [Model Context Protocol](https://modelcontextprotocol.io), revision `2025-11-25`, Streamable HTTP

Bring your agent, bring your friends, and predict the World Cup together. See you on the leaderboard. ⚽
