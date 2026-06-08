# Contributing to KICKGEIST Agents

Thanks for helping fans predict the World Cup through their favorite AI agent. This repo is the **public home** for everything around the KICKGEIST MCP server: install guides, docs, and agent skills. Every contribution makes it easier for the next fan to connect their agent and start picking matches with friends.

If your change helps a fan get connected faster, predict more easily, or rally a group, it belongs here. Let's ship it together.

---

## What lives in this repo

This repo is the **public interface and documentation** for the KICKGEIST predictions MCP server.

- ✅ **Here (public):** install guides per client, usage docs, agent skills, examples, and the public server metadata (`server.json`).
- 🔒 **Not here (private):** the MCP server source code itself. The server is operated by KICKGEIST; this repo never contains its implementation, secrets, or backend.

So when you contribute, you're improving how fans **discover, connect to, and use** the server — not the server internals. That's exactly where community help shines.

---

## The server, in one breath

KICKGEIST is a **group-first, zero-money, social World Cup 2026 prediction game**. It's free, ad-supported, with no in-app purchases and no subscriptions — live on iOS and Android. You predict the **outcome** of matches (home win, draw, or away win), compete with friends in groups, and climb leaderboards.

The MCP integration lets tech-savvy fans play **through an AI agent that has its own KICKGEIST account**.

- **Endpoint:** `https://mcp.kickgeist.com/mcp`
- **Transport:** Streamable HTTP
- **Protocol:** Model Context Protocol, revision `2025-11-25`
- **Server name:** `com.kickgeist/predictions` — display title "KICKGEIST — World Cup Predictions"
- **Auth:** **Authless.** No OAuth, no login. You add the URL, then call `create_account` to get the agent its own anonymous account plus a recovery code.

> **An agent plays as its own independent player.** There's no account linking and no account sharing in any direction — the agent gets a fresh account that's all its own. Its display name is automatically marked **"(AI)"** (e.g. "Klausi (AI)") so everyone in a group or on a leaderboard can see at a glance that an agent is playing. Always remind fans to **save the recovery code** — entering it in the KICKGEIST app brings this agent's account onto a phone so they can keep playing there. That's a **one-way** handoff (the account moves to the phone), never a sync or a shared login.

### How a human follows — and competes with — their agent

This is the fun part, and it's how agents bring their humans into the app: the agent calls `create_group` and shares the invite link. The human installs the KICKGEIST app, joins that same group, and watches the agent climb the group leaderboard — while playing **head-to-head as their own separate player** in the same group. The human and the agent are two distinct players in one group. Lean into it: **"Can you out-predict your own AI?"**

---

## Ways to contribute

### 1. Add an install guide for a new client

This is the most valuable contribution: a clear, copy-paste guide that gets a fan connected in one try.

A great install guide includes:

1. **Plan requirements** — does the client need a paid tier? (e.g., ChatGPT custom connectors are paid-only; Claude Free allows one connector.) Set expectations up front.
2. **The exact steps** — menu path, button names, and where to paste the URL.
3. **A copy-paste block** — config JSON, a CLI command, or a one-click deep link.
4. **The official source** — link the client's own docs so the steps stay verifiable.

Always use the canonical endpoint, verbatim:

```
https://mcp.kickgeist.com/mcp
```

**Example — Claude Code (CLI):**

````markdown
```bash
claude mcp add --transport http kickgeist https://mcp.kickgeist.com/mcp
```
Then run `/mcp` to confirm it connected. Source: code.claude.com/docs/en/mcp
````

**Example — config-file client (Cursor, Windsurf, Zed, Cline, VS Code, etc.):**

```json
{
  "mcpServers": {
    "kickgeist": {
      "url": "https://mcp.kickgeist.com/mcp"
    }
  }
}
```

> Config key names differ per client (`mcpServers`, `servers`, `context_servers`, `serverUrl` vs `url`, optional `"type": "http"`). Copy the shape from that client's official docs and **link the source** so reviewers can verify.

**Universal fallback** for stdio-only clients:

```bash
npx mcp-remote https://mcp.kickgeist.com/mcp
```

#### Install-guide checklist

- [ ] Endpoint is exactly `https://mcp.kickgeist.com/mcp` (no trailing slash variants, no typos).
- [ ] Steps are dated and link to the **client's official documentation**.
- [ ] Paid-plan requirements (if any) are stated clearly and kindly.
- [ ] No OAuth or "Add to Claude" deep link is implied — the server is **authless**, and no magic install link exists. There's no "link your existing account" step — the agent always gets its own account.
- [ ] Code blocks are copy-paste ready and were tried against the live client.
- [ ] Voice follows the style guide below (inclusive prediction language, warm tone).

### 2. Report an issue

Open a GitHub issue when something is wrong or stale. Helpful reports include:

- **Which client** (and version/plan) you were using.
- **What you expected** vs. **what happened**.
- The **exact steps or config** you used.
- Any error text (redact your recovery code and personal data first).

If an install guide drifted because a client changed its UI, that's a perfect issue — or better, a PR.

### 3. Improve the docs

Typos, clearer wording, better examples, a missing edge case, a screenshot that helps — all welcome. Small, focused PRs are easiest to review and ship.

### 4. Contribute an agent skill

Skills that help an agent play KICKGEIST well (onboarding a fan, walking through their first prediction, spinning up a group) are great additions. Keep them aligned with the tool contract below and the voice guide.

---

## The tool contract (don't drift from this)

Docs and skills must describe the tools exactly as the server implements them. There are **8 tools**:

| Tool | Params | What it does |
|------|--------|--------------|
| `create_account` | `display_name?: string` | Creates the agent its own fresh anonymous account, returns a **recovery code** (save it!). The display name is automatically marked **"(AI)"** for transparency. |
| `get_recovery_code` | none | Shows this account's recovery code. Use it in the KICKGEIST app to bring the agent's account onto a phone (one-way). |
| `list_open_matches` | `limit?: integer (max 50)` | Lists matches **currently open** for predictions. Returns `matchId`, home, away, kickoff, stage, `isWarmup`. No scores, no results, no finished matches. |
| `predict_match` | `match_id: string`, `outcome: "home" \| "draw" \| "away"`, `group_id?: string` | Makes or changes the agent's prediction for the match **result**. |
| `create_group` | `name: string (2–50)`, `description?: string`, `country_code?: 2-letter uppercase` | Creates a group, returns a shareable invite link + code + member count. |
| `join_group` | `invite_code: string` | Joins a group (raw 6-char code **or** a full `https://kickgeist.com/join/CODE` link). |
| `get_my_groups` | none | Lists the agent's groups (name, invite code, link, member count, role). |
| `get_my_stats` | none | **The agent's own** stats only: points, correct picks, accuracy, streaks, rank, and its standing in its groups, plus warmup stats. |

### Fair play is a feature, describe it as one

The server intentionally exposes only **your own data** and the **upcoming open-match schedule**. It never returns match results, other players' picks, or the global/group leaderboard. This protects our licensed match data and keeps the social comparison where it sings — in the app. When you write docs, frame this as a deliberate, positive design choice, not a missing feature. The full leaderboard and head-to-head comparison live in the KICKGEIST mobile app.

---

## Style & voice

KICKGEIST sounds like a friend who loves football and wants you in the group chat. Match that energy.

- **Warm, celebratory, friend-to-friend**, with real World Cup energy. Be precise for a developer/AI-tinkerer audience without going dry.
- **Inclusive prediction language only.** Use **prediction / pick / predict**.
- **Never gambling terms.** No *bet, wager, odds, stake, gambling, betting* — anywhere, in any language. KICKGEIST is a social prediction game, not that other category.
- **Brand colors** (if you add visuals): Orange `#FF7A00`, Blue `#1E3A8A`, Green `#10B981`.
- **Be accurate about auth:** it's authless. Don't promise OAuth and don't invent install deep links that don't exist.
- **Be accurate about the account model:** an agent plays as its **own independent account**, auto-marked **"(AI)"**. There's no linking, no sharing, no "play the account you already have." A human follows and competes with their agent by joining the agent's group in the app.
- **Remind fans to save the recovery code** wherever account creation is mentioned — frame it as the one-way way to bring the agent's account onto a phone in the app, never as link/share/sync.

---

## Pull request workflow

1. **Fork** the repo and create a focused branch (e.g., `add-install-guide-jan`).
2. **Make one improvement per PR** — small, scannable diffs are fastest to review and ship.
3. **Verify your steps** against the live client and the live endpoint before opening the PR.
4. **Describe the win in fan terms** — "Jan users can now connect in two clicks," not "added config snippet."
5. **Link your sources** for any install steps so reviewers can confirm them.
6. Open the PR and we'll review together. Thanks for going the extra mile.

---

## Code of conduct

Be kind, be welcoming, assume good intent. We're all here for the same reason: to make predicting the World Cup with friends a little more joyful. Harassment or disrespect of any contributor isn't welcome.

---

**Build for the fans. Ship with care. Go further than anyone asked.**
