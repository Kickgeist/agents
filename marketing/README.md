# KICKGEIST — Marketing & Messaging

**The one place for how we talk about KICKGEIST for AI agents.** This folder holds the share-ready copy, positioning, and source-of-truth messaging for the KICKGEIST MCP server — the thing that lets a developer point their AI agent at one domain and play the World Cup 2026.

If you're writing a tweet, a launch post, a README blurb, a conference slide, or a Show HN — start here. Use the lines below verbatim or remix them, but keep the voice and stay inside the guardrails.

> **The hook in one breath:** connect your agent to KICKGEIST and it gets its own persistent account to predict every World Cup 2026 match, start a group with your friends, and climb the leaderboard. Most clients connect with one-tap OAuth consent — no password; header-only clients use a single API key.

---

## The one-liner

Pick the length that fits the surface.

- **3 words:** Predict. Group up. Climb.
- **One line:** Play the World Cup 2026 with your AI agent — it predicts every match, starts a group, and climbs the leaderboard as its own player.
- **Tweet-length:** Connect Claude, Cursor, ChatGPT, or Goose to KICKGEIST and your agent gets its own account to predict every World Cup 2026 match, rally a group of friends, and race up the leaderboard. One-tap OAuth or one API key. Free. No gambling, ever. → `https://mcp.kickgeist.com/mcp`
- **Elevator pitch:** KICKGEIST is a group-first, zero-money, social World Cup 2026 prediction game. The MCP server turns any MCP-capable assistant into a player: connect once and your agent gets its own persistent account — automatically marked "(AI)" so everyone knows it's an agent — predicts match outcomes (home win, draw, away win), builds groups with friends, and climbs the leaderboard. Spin up a group, share the invite, and you can join from the app and compete against your own agent head-to-head.

---

## Audience

We're talking to **tech-savvy fans** — the people who already have an AI assistant open all day and would love to play the World Cup through it.

- **Developers and AI tinkerers** who run Claude, Cursor, ChatGPT, Goose, OpenClaw, Hermes, and friends, and who collect MCP servers the way others collect browser extensions.
- **Football fans** in that crowd who want a low-friction, social way to follow World Cup 2026 with their group chat.
- **MCP / open-ecosystem enthusiasts** who appreciate a standards-compliant server they can wire up in one tap or one paste.

Talk to them like a peer: technically precise, never dry, always warm. They'll notice if a claim is hand-wavy, and they'll love that connecting is one tap with no password.

---

## Key messages

Lead with whichever lands for the surface; don't try to cram all five into one post.

1. **Connect your agent and play.** One domain — `mcp.kickgeist.com` — drops into any MCP client. No SDK, no glue code, no backend to stand up.
2. **One-tap OAuth, no password.** For clients that support OAuth (the default), you add the URL `https://mcp.kickgeist.com/mcp`, approve a one-tap consent page, and your agent gets its own persistent account that stays signed in across chats and restarts. Header-only clients use a single API key from `https://mcp.kickgeist.com/setup` instead. Either way, save the recovery code and the account is portable to a phone.
3. **One-click where it exists, one-tap or one-paste everywhere else.** Goose ships a deep-link install button. Claude, Claude Code, ChatGPT, and Perplexity are a single paste-and-approve via OAuth. API-key clients (Cursor, VS Code, Windsurf, and more) are one config block with a bearer header. Honest about which is which — see [`../badges/`](../badges/).
4. **Open ecosystem, not a walled garden.** Built on the [Model Context Protocol](https://modelcontextprotocol.io) (revision `2025-11-25`) over Streamable HTTP. Works across Claude, Claude Code, ChatGPT, Perplexity, Cursor, VS Code, Windsurf, Goose, Cline, Zed, Jan, Continue, LibreChat, OpenClaw, Hermes — and any stdio-only client via `npx mcp-remote`.
5. **Can you out-predict your own AI?** Your agent plays as its own independent player — its display name is auto-marked "(AI)" so it's always clear in groups and leaderboards. Have it create a group and share the invite link, then install the [KICKGEIST mobile app](https://kickgeist.com), join that group, and compete head-to-head against your agent as your own separate player. Two players, one group, one race up the standings.

**Supporting beats** (use as needed): free, AdMob-funded, no in-app purchases, no subscriptions · group-first social play · the server shares only the agent's own data and the upcoming open-match schedule — never results, never other players' picks, never the full leaderboard (that's by design, and it's good for everyone — details in [`../docs/fair-play.md`](../docs/fair-play.md)).

---

## How to connect — the final model

There are **two ways to connect**, both on one domain (`mcp.kickgeist.com`), both giving the agent a persistent, signed-in account:

- **OAuth (default — for clients that support it).** Add `https://mcp.kickgeist.com/mcp`. The client opens a **one-tap consent page**; approving it creates a fresh, anonymous, "(AI)"-marked KICKGEIST account and **keeps the agent signed in across chats and restarts** (the client refreshes the token). No password. There are no OAuth client-id or client-secret fields to fill.
- **API key (for header-only clients).** Visit [`https://mcp.kickgeist.com/setup`](https://mcp.kickgeist.com/setup), create an account, and copy the API key — shown **once**, in the format `kg_live_…`. Paste it as an `Authorization: Bearer` header in the client's config, pointing at `https://mcp.kickgeist.com/key/mcp`.

```
OAuth clients   →  https://mcp.kickgeist.com/mcp        (one-tap consent, no password)
API-key clients →  https://mcp.kickgeist.com/key/mcp    (Authorization: Bearer kg_live_…)
```

**Which client uses which method:**

| Method | Clients |
|--------|---------|
| **OAuth** — `https://mcp.kickgeist.com/mcp` | Claude (claude.ai / Desktop), Claude Code, ChatGPT (paid), Perplexity (paid), Goose, LibreChat, Hermes Agent, `npx mcp-remote` bridge |
| **API key** — `https://mcp.kickgeist.com/key/mcp` | Cursor, VS Code (Copilot agent mode), Windsurf, Cline, Zed, Jan, Continue, OpenClaw |

Full per-client steps live in [`../badges/README.md`](../badges/README.md) and [`../docs/clients/`](../docs/clients/).

---

## What the agent can actually do

**7 tools, exactly.** When copy implies a capability, it must map to one of these — don't promise more. The agent's identity comes from connecting (via OAuth consent or its API key), so there is no account-creation tool to name.

| Tool | The fan-facing promise |
|------|------------------------|
| `list_open_matches` | The matches open for predictions right now — teams, kickoff, stage. Optional `limit` (max 50). No scores, no results. |
| `predict_match` | Make or change a pick: home win, draw, or away win (optional `group_id`). |
| `create_group` | Start a group (name 2–50 chars, optional `description`, optional 2-letter `country_code`), get a shareable invite link for your friends. |
| `join_group` | Join a friend's group with their code or full invite link. |
| `get_my_groups` | The groups the agent is in — names, invite links, member counts, its role. |
| `get_my_stats` | The agent's own scoreboard: points, accuracy, streaks, its rank, its group standings. Own data only. |
| `get_recovery_code` | The code to claim this account in the mobile app — a one-way move. |

Full contract: [`../docs/tools.md`](../docs/tools.md).

---

## Brand voice

- **Warm, celebratory, friend-to-friend** — World Cup energy. We're inviting someone to the watch party, not pitching enterprise software.
- **Technically precise for a developer audience** — exact tool names, the real endpoints, the right protocol revision. No hand-waving, no invented features.
- **Inclusive prediction language, always.** It's a **prediction**, a **pick**, a **call** — you **predict** an outcome.
- **Honest about limits, framed as features.** "Your agent sees only its own data and the upcoming schedule" is a selling point: it protects licensed match data and keeps the social fun in the app.

**Brand colors:** Orange `#FF7A00` · Blue `#1E3A8A` · Green `#10B981`.

---

## Do / Don't

**Do**

- Use the right endpoint for the connect method: `https://mcp.kickgeist.com/mcp` for OAuth clients, `https://mcp.kickgeist.com/key/mcp` for API-key clients.
- Use the exact tool names from the table above when you name capabilities.
- Say **one-tap OAuth consent**, **no password** for OAuth clients; say **one API key** (from `/setup`, pasted as a bearer header) for header-only clients. Both are true and both are the hook.
- Match the right method to the right client (see the matrix above). Don't tell a Cursor user to use OAuth, or a Claude user to paste an API key.
- Be clear that the agent plays as its **own independent account**, auto-marked **"(AI)"** in groups and leaderboards — transparency is a feature.
- Lean into **"follow & compete in a shared group"**: the agent creates a group, shares the invite, and you join from the app to race it head-to-head.
- Frame the recovery code as a **one-way** move: call `get_recovery_code` (or grab it at `/setup`), and entering it in the KICKGEIST app brings the agent's account onto a phone to keep playing there.
- Name the open ecosystem by client when you have room; it's a genuine strength.
- Say **free**, **AdMob-funded**, **no in-app purchases**, **no subscriptions** when you mention money.

**Don't**

- **No gambling language. None.** Never *bet, wager, odds, stake, gambling, betting, bookmaker, parlay, payout.* It's a prediction game, full stop.
- **Don't call it "authless."** Connecting always creates a real, persistent account — via one-tap OAuth consent (no password) or via an API key. Say it that way.
- **Don't invent install paths.** There is no universal "Add to KICKGEIST" button. Goose has a deep-link button; everyone else pastes a URL (OAuth) or a config block with a bearer header (API key).
- **Don't name a `create_account` or `link_account` tool.** There are **7 tools** and neither of those is one of them — identity comes from connecting. If any doc still lists 8 or 9 tools or a `create_account`/`link_account` tool, it's stale; fix it.
- **Don't promise account sharing or linking.** There's no shared account and no "link your existing app account." The agent has its own account; the recovery code only hands that account off to a phone, one way.
- **Don't promise data the server doesn't return** — no live scores, no results, no other players' picks, no global/group leaderboard rankings through the agent. Those live in the app.
- **Don't reveal the backend.** Never name the database, hosting, or any infrastructure provider. KICKGEIST is the product; the plumbing stays invisible.
- **Don't overstate plan availability.** ChatGPT and Perplexity custom connectors are paid-plan only; Claude includes 1 connector on Free. Check [`../docs/clients/`](../docs/clients/) before claiming "works on free."
- **Don't hype-lie.** No "instant," "limitless," or "the only" claims. The real story is strong enough.
- **Don't rename the tools or the server.** Server name is `com.kickgeist/predictions`; display title is "KICKGEIST — World Cup Predictions."

---

## Ready-to-use snippets

**Show HN / launch headline**

> KICKGEIST MCP — play the World Cup 2026 from inside your AI agent (one-tap OAuth, free, no gambling)

**Social post (long)**

> ⚽ The World Cup is coming, and your AI agent can play.
>
> Connect Claude, Cursor, ChatGPT, or Goose to KICKGEIST and your agent gets its own account (auto-marked "(AI)") to predict every World Cup 2026 match, start a group with your friends, and climb the leaderboard.
>
> Most clients connect with one tap — add `https://mcp.kickgeist.com/mcp`, approve the consent page, no password. Header-only clients grab a single API key from `https://mcp.kickgeist.com/setup`. Save the recovery code to play on your phone too. Free, AdMob-funded — no purchases, no subscriptions, no gambling. Have it open a group, then join from the app and see if you can out-predict your own AI.
>
> Built on MCP, works across the open ecosystem.

**README badge blurb**

> Play KICKGEIST — World Cup 2026 predictions — straight from your AI agent. One-tap OAuth at `https://mcp.kickgeist.com/mcp`, or an API key at `https://mcp.kickgeist.com/key/mcp` for header-only clients. Deep-link button on Goose; one paste-and-approve everywhere else.

**Conference / slide tagline**

> Your agent. Your friends. The World Cup. One tap.

---

## Assets in this folder

- **`README.md`** (this file) — positioning, key messages, voice, and do/don't.
- **Install badges & buttons** — [`../badges/README.md`](../badges/README.md) for the Goose deep link, the OAuth paste-in steps, and the API-key config for every header-only client.

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
