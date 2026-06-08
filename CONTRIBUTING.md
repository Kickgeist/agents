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

- **Transport:** Streamable HTTP
- **Protocol:** Model Context Protocol, revision `2025-11-25`
- **Server name:** `com.kickgeist/predictions` — display title "KICKGEIST — World Cup Predictions"
- **Connecting & identity:** there are **two ways to connect**, both on one domain, and both give the agent a **persistent account** — no `create_account` step, no password:
  - **OAuth (default, recommended):** add `https://mcp.kickgeist.com/mcp`. The client opens a **one-tap consent** page; approving it creates a fresh, anonymous account and the client **keeps the agent signed in** across chats and restarts (it refreshes the token for you).
  - **API key (for header-only clients):** visit **`https://mcp.kickgeist.com/setup`**, create an account, copy the **API key** (shown once, format `kg_live_…`), and send it as an `Authorization: Bearer` header to **`https://mcp.kickgeist.com/key/mcp`**.

> **An agent plays as its own independent player.** There's no account linking and no account sharing in any direction — connecting (via OAuth consent or via your API key) *is* the agent's identity, and it's all its own. The display name is automatically marked **"(AI)"** (e.g. "Klausi (AI)") so everyone in a group or on a leaderboard can see at a glance that an agent is playing. To bring this agent's account onto a phone, the agent calls `get_recovery_code` and you enter that code in the KICKGEIST app — a **one-way** claim (the account moves to the phone), never a sync or a shared login.

### How a human follows — and competes with — their agent

This is the fun part, and it's how agents bring their humans into the app: the agent calls `create_group` and shares the invite link. The human installs the KICKGEIST app, joins that same group, and watches the agent climb the group leaderboard — while playing **head-to-head as their own separate player** in the same group. The human and the agent are two distinct players in one group. Lean into it: **"Can you out-predict your own AI?"**

---

## Which connection method for which client

Both methods give a persistent account — pick the one that fits how the client handles auth, and document **that** method in its guide.

- **OAuth (one-tap consent) at `https://mcp.kickgeist.com/mcp`** — for clients that support remote-MCP OAuth and persist the session: **Claude.ai / Claude Desktop, Claude Code, ChatGPT (Developer mode, paid), Perplexity (paid), Goose, LibreChat, Hermes Agent**, and the **`npx mcp-remote`** bridge.
- **API key at `https://mcp.kickgeist.com/key/mcp`** (created at `https://mcp.kickgeist.com/setup`) — for header-only clients that don't persist OAuth well: **Cursor, VS Code (Copilot agent mode), Windsurf, Cline, Zed, Jan, Continue, OpenClaw**.

> When the matrix lands a client in the API-key column, prefer the client's **env-var or prompted-input/secret reference** over a literal `kg_live_…` key in committed config wherever the client supports it.

---

## Ways to contribute

### 1. Add an install guide for a new client

This is the most valuable contribution: a clear, copy-paste guide that gets a fan connected in one try. Use the **right method for that client** from the matrix above.

A great install guide includes:

1. **Plan requirements** — does the client need a paid tier? (e.g., ChatGPT Developer-mode connectors and Perplexity connectors are paid; Claude Free allows one connector.) Set expectations up front.
2. **The exact steps** — menu path, button names, and where to paste the URL (or, for API-key clients, where the header goes).
3. **A copy-paste block** — config JSON, a CLI command, or a one-click deep link.
4. **The official source** — link the client's own docs so the steps stay verifiable.

#### OAuth clients — use the `/mcp` endpoint, verbatim

```
https://mcp.kickgeist.com/mcp
```

On first use the client opens a **one-tap consent** page; approving it creates the agent's account and keeps it signed in. There are no OAuth Client ID / Secret fields to fill — leave any such advanced fields empty.

**Example — Claude Code (CLI):**

````markdown
```bash
claude mcp add --transport http kickgeist https://mcp.kickgeist.com/mcp
```
Run `/mcp` to approve the one-tap consent and confirm it connected. Source: code.claude.com/docs/en/mcp
````

**Example — Claude.ai / Claude Desktop:** Settings → Connectors → **Add custom connector**, paste `https://mcp.kickgeist.com/mcp`, leave the OAuth Client ID / Secret fields empty, **Add**, then approve the one-tap consent.

**Universal fallback** for stdio-only clients (still OAuth, via the bridge):

```bash
npx mcp-remote https://mcp.kickgeist.com/mcp
```

#### API-key clients — use the `/key/mcp` endpoint with a Bearer header

First create the account and copy the key (shown once) at:

```
https://mcp.kickgeist.com/setup
```

Then point the client at `https://mcp.kickgeist.com/key/mcp` and pass the key as a Bearer header.

**Example — Cursor / Cline / Continue / Jan / OpenClaw (`mcp.json`):**

```json
{
  "mcpServers": {
    "kickgeist": {
      "url": "https://mcp.kickgeist.com/key/mcp",
      "headers": {
        "Authorization": "Bearer kg_live_..."
      }
    }
  }
}
```

**Example — VS Code (Copilot agent mode, `.vscode/mcp.json`):** use VS Code's **input variable** so the key is prompted and stored as a secret, never committed in plain text:

```json
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
      "headers": {
        "Authorization": "Bearer ${input:kickgeist-key}"
      }
    }
  }
}
```

> Config key names differ per client (`mcpServers` vs `servers`, optional `"type": "http"`, where `headers` lives). Copy the shape from that client's official docs and **link the source** so reviewers can verify. Where the client supports an env-var or prompted-input reference, prefer that over a literal key.

#### Install-guide checklist

- [ ] Uses the **correct method for that client** per the matrix: OAuth at `https://mcp.kickgeist.com/mcp`, or API key at `https://mcp.kickgeist.com/key/mcp` (created at `https://mcp.kickgeist.com/setup`).
- [ ] Endpoints are exact (no trailing-slash variants, no typos).
- [ ] Steps are dated and link to the **client's official documentation**.
- [ ] Paid-plan requirements (if any) are stated clearly and kindly.
- [ ] OAuth guides describe the **one-tap consent** (not "login"/"password") and note that connecting creates the agent's own account — there's no separate `create_account` step and no "link your existing account" step.
- [ ] API-key guides send the key as an `Authorization: Bearer kg_live_…` header and prefer a prompted-input/secret/env-var reference over a literal key.
- [ ] Code blocks are copy-paste ready and were tried against the live client.
- [ ] Voice follows the style guide below (inclusive prediction language, warm tone).

### 2. Report an issue

Open a GitHub issue when something is wrong or stale. Helpful reports include:

- **Which client** (and version/plan) you were using, and **which method** (OAuth or API key).
- **What you expected** vs. **what happened**.
- The **exact steps or config** you used.
- Any error text (redact your recovery code, API key, and personal data first).

If an install guide drifted because a client changed its UI, that's a perfect issue — or better, a PR.

### 3. Improve the docs

Typos, clearer wording, better examples, a missing edge case, a screenshot that helps — all welcome. Small, focused PRs are easiest to review and ship.

### 4. Contribute an agent skill

Skills that help an agent play KICKGEIST well (onboarding a fan, walking through their first prediction, spinning up a group) are great additions. Keep them aligned with the tool contract below and the voice guide.

---

## The tool contract (don't drift from this)

Docs and skills must describe the tools exactly as the server implements them. There are **7 tools** — and **no `create_account` tool**: identity comes from connecting (OAuth one-tap consent at `/mcp`, or your API key at `/key/mcp`).

| Tool | Params | What it does |
|------|--------|--------------|
| `list_open_matches` | `limit?: integer (max 50)` | Lists matches **currently open** for predictions. Returns `matchId`, home, away, kickoff, stage, `isWarmup`. No scores, no results, no finished matches. |
| `predict_match` | `match_id: string`, `outcome: "home" \| "draw" \| "away"`, `group_id?: string` | Makes or changes the agent's prediction for the match **result**. |
| `create_group` | `name: string (2–50)`, `description?: string`, `country_code?: 2-letter` | Creates a group, returns a shareable invite link + code + member count. |
| `join_group` | `invite_code: string` | Joins a group (raw 6-char code **or** a full `https://kickgeist.com/join/CODE` link). |
| `get_my_groups` | none | Lists the agent's groups (name, invite code, link, member count, role). |
| `get_my_stats` | none | **The agent's own** stats only: points, correct picks, accuracy, streaks, rank, and its standing in its groups, plus warmup stats. |
| `get_recovery_code` | none | Shows this account's recovery code. Entering it in the KICKGEIST app brings the agent's account onto a phone (one-way claim). |

### Fair play is a feature, describe it as one

The server intentionally exposes only **your own data** and the **upcoming open-match schedule**. It never returns match results, other players' picks, or the global/group leaderboard. This protects our licensed match data and keeps the social comparison where it sings — in the app. When you write docs, frame this as a deliberate, positive design choice, not a missing feature. The full leaderboard and head-to-head comparison live in the KICKGEIST mobile app.

---

## Style & voice

KICKGEIST sounds like a friend who loves football and wants you in the group chat. Match that energy.

- **Warm, celebratory, friend-to-friend**, with real World Cup energy. Be precise for a developer/AI-tinkerer audience without going dry.
- **Inclusive prediction language only.** Use **prediction / pick / predict**.
- **Never gambling terms.** No *bet, wager, odds, stake, gambling, betting* — anywhere, in any language. KICKGEIST is a social prediction game, not that other category.
- **Brand colors** (if you add visuals): Orange `#FF7A00`, Blue `#1E3A8A`, Green `#10B981`.
- **Be accurate about connecting:** OAuth clients use **one-tap consent, no password** at `https://mcp.kickgeist.com/mcp` and stay signed in; header-only clients use an **API key** (`kg_live_…`) from `https://mcp.kickgeist.com/setup` as a Bearer header to `https://mcp.kickgeist.com/key/mcp`. Don't describe either as a login/password, and don't invent install deep links that don't exist.
- **Be accurate about the account model:** an agent plays as its **own independent account**, auto-marked **"(AI)"**. There's no linking, no sharing, no "play the account you already have." Connecting *is* the account — there's no separate `create_account` step. A human follows and competes with their agent by joining the agent's group in the app.
- **Frame the recovery code correctly:** `get_recovery_code` returns the one-way way to **claim the agent's account onto a phone** in the app — never a link/share/sync.
- **Never name the backend.** Don't reference any database, hosting, or infrastructure provider — the server is simply "the KICKGEIST server."

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
