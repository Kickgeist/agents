# KICKGEIST — Marketing & Messaging

**The one place for how we talk about KICKGEIST for AI agents.** This folder holds the share-ready copy, positioning, and source-of-truth messaging for the KICKGEIST MCP server — the thing that lets a developer point their AI agent at a single URL and play the World Cup 2026.

If you're writing a tweet, a launch post, a README blurb, a conference slide, or a Show HN — start here. Use the lines below verbatim or remix them, but keep the voice and stay inside the guardrails.

> **The hook in one breath:** add one URL to your agent, and it can predict every World Cup 2026 match, start a group with your friends, and climb the leaderboard — no login, no API key, no OAuth.

---

## The one-liner

Pick the length that fits the surface.

- **3 words:** Predict. Group up. Climb.
- **One line:** Play the World Cup 2026 with your AI agent — predict every match, start a group, climb the leaderboard.
- **Tweet-length:** Point Claude, Cursor, ChatGPT, or Goose at one URL and your agent can predict every World Cup 2026 match, rally a group of friends, and race up the leaderboard. Authless. Free. No gambling, ever. → `https://mcp.kickgeist.com/mcp`
- **Elevator pitch:** KICKGEIST is a group-first, zero-money, social World Cup 2026 prediction game. The MCP server turns any MCP-capable assistant into a way to play: you predict match outcomes (home win, draw, away win), build groups with friends, and climb the leaderboard — all through the agent you already live in. One recovery code links your agent and the mobile app to the same account, so your phone and your AI play the same game.

---

## Audience

We're talking to **tech-savvy fans** — the people who already have an AI assistant open all day and would love to play the World Cup through it.

- **Developers and AI tinkerers** who run Claude, Cursor, ChatGPT, Goose, OpenClaw, Hermes, and friends, and who collect MCP servers the way others collect browser extensions.
- **Football fans** in that crowd who want a low-friction, social way to follow World Cup 2026 with their group chat.
- **MCP / open-ecosystem enthusiasts** who appreciate an authless, standards-compliant server they can wire up in one paste.

Talk to them like a peer: technically precise, never dry, always warm. They'll notice if a claim is hand-wavy, and they'll love that there's no signup wall.

---

## Key messages

Lead with whichever lands for the surface; don't try to cram all five into one post.

1. **Point your agent at a URL and play.** One endpoint — `https://mcp.kickgeist.com/mcp` — drops into any MCP client. No SDK, no glue code, no backend to stand up.
2. **Authless. Genuinely.** No OAuth, no login, no API key. You add the URL, call `create_account`, and you're in. Save the recovery code and you're portable.
3. **One-click where it exists, one-paste everywhere else.** Cursor, VS Code, and Goose ship deep-link install buttons. Claude, ChatGPT, and Perplexity are a single paste into a settings screen. Honest about which is which — see [`../badges/`](../badges/).
4. **Open ecosystem, not a walled garden.** Built on the [Model Context Protocol](https://modelcontextprotocol.io) (revision `2025-11-25`) over Streamable HTTP. Works across Claude, Claude Code, ChatGPT, Perplexity, Cursor, VS Code, Windsurf, Goose, Cline, Zed, Jan, Continue, LibreChat, OpenClaw, Hermes — and any stdio-only client via `npx mcp-remote`.
5. **Your phone and your agent, one account.** A recovery code links an agent-created account to the [KICKGEIST mobile app](https://kickgeist.com) and back again. Predict from your terminal at lunch, check the standings on your phone on the train.

**Supporting beats** (use as needed): free, AdMob-funded, no in-app purchases, no subscriptions · group-first social play · the server shares only your own data and the upcoming open-match schedule — never results, never other players' picks, never the full leaderboard (that's by design, and it's good for everyone — details in [`../docs/fair-play.md`](../docs/fair-play.md)).

---

## What the agent can actually do

Nine tools, exactly. When copy implies a capability, it must map to one of these — don't promise more.

| Tool | The fan-facing promise |
|------|------------------------|
| `create_account` | Fresh anonymous account + a recovery code to save. |
| `link_account` | Connect this agent to the account you already play on your phone. |
| `get_recovery_code` | Show the linked account's recovery code so you can save it. |
| `list_open_matches` | The matches open for predictions right now — teams, kickoff, stage. No scores, no results. |
| `predict_match` | Make or change a pick: home win, draw, or away win. |
| `create_group` | Start a group, get a shareable invite link for your friends. |
| `join_group` | Join a friend's group with their code or link. |
| `get_my_groups` | The groups you're in — names, invite links, member counts, your role. |
| `get_my_stats` | Your own scoreboard: points, accuracy, streaks, your rank, your group standings. |

Full contract: [`../docs/tools.md`](../docs/tools.md).

---

## Brand voice

- **Warm, celebratory, friend-to-friend** — World Cup energy. We're inviting someone to the watch party, not pitching enterprise software.
- **Technically precise for a developer audience** — exact tool names, the real endpoint, the right protocol revision. No hand-waving, no invented features.
- **Inclusive prediction language, always.** It's a **prediction**, a **pick**, a **call** — you **predict** an outcome.
- **Honest about limits, framed as features.** "Your agent sees only your own data and the upcoming schedule" is a selling point: it protects licensed match data and keeps the social fun in the app.

**Brand colors:** Orange `#FF7A00` · Blue `#1E3A8A` · Green `#10B981`.

---

## Do / Don't

**Do**

- Use the exact endpoint: `https://mcp.kickgeist.com/mcp`.
- Use the exact tool names from the table above when you name capabilities.
- Say **authless**, **no login**, **no OAuth**, **no API key** — these are true and they're the hook.
- Distinguish **one-click** (Cursor, VS Code, Goose) from **one-paste** (Claude, ChatGPT, Perplexity). Don't blur them.
- Remind people to **save their recovery code** — it's how they reach the same account in the app.
- Name the open ecosystem by client when you have room; it's a genuine strength.
- Say **free**, **AdMob-funded**, **no in-app purchases**, **no subscriptions** when you mention money.

**Don't**

- **No gambling language. None.** Never *bet, wager, odds, stake, gambling, betting, bookmaker, parlay, payout.* It's a prediction game, full stop.
- **Don't invent install paths.** There is no universal "Add to KICKGEIST" button and no OAuth flow. Don't imply either.
- **Don't promise data the server doesn't return** — no live scores, no results, no other players' picks, no global/group leaderboard rankings through the agent. Those live in the app.
- **Don't overstate plan availability.** ChatGPT and Perplexity custom connectors are paid-plan only; Claude includes 1 connector on Free. Check [`../docs/clients/`](../docs/clients/) before claiming "works on free."
- **Don't hype-lie.** No "instant," "limitless," or "the only" claims. The real story is strong enough.
- **Don't rename the tools or the server.** Server name is `com.kickgeist/predictions`; display title is "KICKGEIST — World Cup Predictions."

---

## Ready-to-use snippets

**Show HN / launch headline**

> KICKGEIST MCP — play the World Cup 2026 from inside your AI agent (authless, free, no gambling)

**Social post (long)**

> ⚽ The World Cup is coming, and your AI agent can play.
>
> Add one URL — `https://mcp.kickgeist.com/mcp` — to Claude, Cursor, ChatGPT, or Goose, and it can predict every World Cup 2026 match, start a group with your friends, and climb the leaderboard.
>
> No login. No OAuth. No API key. Just paste the URL, ask it to create your account, and save your recovery code. Free, AdMob-funded — no purchases, no subscriptions, no gambling. Your phone and your agent share the same account.
>
> Built on MCP, works across the open ecosystem.

**README badge blurb**

> Play KICKGEIST — World Cup 2026 predictions — straight from your AI agent. Authless MCP server at `https://mcp.kickgeist.com/mcp`. One-click on Cursor, VS Code, and Goose; one paste everywhere else.

**Conference / slide tagline**

> Your agent. Your friends. The World Cup. One URL.

---

## Assets in this folder

- **`README.md`** (this file) — positioning, key messages, voice, and do/don't.
- **Install badges & buttons** — [`../badges/README.md`](../badges/README.md) for the one-click deep links (Cursor, VS Code, Goose) and the paste-in steps for every other client.

---

## Source of truth & cross-references

When in doubt, these win over any phrasing here:

- **Repo overview & quickstart:** [`../README.md`](../README.md)
- **Tool contract:** [`../docs/tools.md`](../docs/tools.md)
- **Per-client setup:** [`../docs/clients/`](../docs/clients/)
- **Fair play & privacy:** [`../docs/fair-play.md`](../docs/fair-play.md) · [`../docs/privacy.md`](../docs/privacy.md)
- **Server manifest:** [`../server.json`](../server.json)

---

Built for the fans. Bring your agent, bring your friends, predict the World Cup together. See you on the leaderboard. ⚽
