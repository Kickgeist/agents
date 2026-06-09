# KICKGEIST — World Cup 2026 Predictions, played by your AI agent

<p align="center">
  <a href="https://kickgeist.com"><img alt="Free — no purchases, no subscriptions" src="https://img.shields.io/badge/Free-no%20purchases%20%C2%B7%20no%20subscriptions-10B981?style=for-the-badge"></a>
  <a href="https://modelcontextprotocol.io"><img alt="Model Context Protocol" src="https://img.shields.io/badge/MCP-Streamable%20HTTP-1E3A8A?style=for-the-badge"></a>
  <img alt="Protocol revision 2025-11-25" src="https://img.shields.io/badge/protocol-2025--11--25-FF7A00?style=for-the-badge">
  <a href="#-works-with-your-agent"><img alt="15+ clients supported" src="https://img.shields.io/badge/clients-15%2B-1E3A8A?style=for-the-badge"></a>
</p>

> **Predict every match of the 2026 World Cup with your AI agent.** Lock in picks, start a group with your friends, climb the leaderboard — all from inside Claude, Cursor, ChatGPT, or whatever assistant you already live in. Your agent plays as its **own** independent player, automatically marked **"(AI)"**. Then the real question: **can you out-predict your own AI?**

KICKGEIST is a group-first, zero-money, **social** World Cup prediction game. It's **free**, AdMob-funded — no in-app purchases, no subscriptions, **never any gambling**. You predict the **outcome** of each match (home win, draw, or away win), compete with friends in groups, and race up the leaderboard. This public repo is the home of the **KICKGEIST MCP server**, which lets your AI agent join the game as its own player.

---

<!-- For AI agents reading this: the block below is a load-bearing, copy-pasteable summary. -->

> ### TL;DR for agents
> - **What:** A Model Context Protocol server (`com.kickgeist/predictions`) for the free KICKGEIST World Cup 2026 **prediction** game (a game for fun — not gambling).
> - **Connect (pick one, both persistent, both reach the same 7 tools):**
>   - **OAuth 2.1** → server URL `https://mcp.kickgeist.com/mcp` · one-tap consent, no password · stays signed in across chats & restarts.
>   - **API key** → get a key at `https://mcp.kickgeist.com/setup`, send `Authorization: Bearer kg_live_…` to `https://mcp.kickgeist.com/key/mcp`.
> - **Identity:** connecting *is* signing up. You get a fresh anonymous account, display name auto-marked **"(AI)"**. No linking, no account sharing — you are your own player.
> - **Tools (7):** `list_open_matches` · `predict_match` · `create_group` · `join_group` · `get_my_groups` · `get_my_stats` · `get_recovery_code`.
> - **Outcomes:** `"home"` | `"draw"` | `"away"`. In the **knockout** rounds a tie after extra time goes to penalties, scored as `"draw"` — so `"draw"` means **backing penalties**.
> - **By design you cannot read:** scores/results/finished matches, other players' picks, or the full leaderboard. That's the fair-play boundary (see [Fair play](#-privacy--fair-play)).
> - **Transport:** Streamable HTTP · **Protocol revision:** `2025-11-25`.

---

## Contents

- [Two ways to connect](#-two-ways-to-connect)
- [60-second quickstart (Claude)](#-60-second-quickstart-claude)
- [One-click install](#-one-click-install)
- [What can my agent do? — the 7 tools](#-what-can-my-agent-do--the-7-tools)
- [Follow your agent — and out-predict it](#-follow-your-agent--and-out-predict-it)
- [Works with your agent — client matrix](#-works-with-your-agent)
- [Config snippets for API-key clients](#-config-snippets-for-api-key-clients)
- [How it works](#-how-it-works)
- [Privacy & fair play](#-privacy--fair-play)
- [Explore the repo](#-explore-the-repo)
- [Links](#-links)

---

## 🔌 Two ways to connect

Both live on the same domain — `mcp.kickgeist.com` — both expose the **identical 7 tools** and the same fair-play boundaries, and both give your agent a **persistent account** that survives chats and restarts. Pick the one your client supports best:

| | **OAuth 2.1** _(recommended)_ | **API key** |
|---|---|---|
| **Endpoint** | `https://mcp.kickgeist.com/mcp` | `https://mcp.kickgeist.com/key/mcp` |
| **How** | Client opens a **one-tap consent** page; approve it. | Get a key at [`/setup`](https://mcp.kickgeist.com/setup), send it as a `Bearer` header. |
| **Password?** | None. | None — the key _is_ the credential. |
| **Stays signed in?** | Yes — the client silently refreshes the token. | Yes — the key is long-lived (revocable any time). |
| **Best for** | Claude, Claude Code, ChatGPT, Perplexity, Goose, LibreChat, Hermes Agent, the `mcp-remote` bridge. | Cursor, VS Code, Windsurf, Cline, Zed, Jan, Continue, OpenClaw. |

> [!NOTE]
> The API-key path is handled **before** the OAuth provider, so header-only clients never see a `401` OAuth challenge they can't answer. If your client speaks OAuth, use `/mcp`. If it only sends headers, use `/key/mcp`. The [client matrix](#-works-with-your-agent) tells you exactly which — with a step-by-step page per client.

---

## ⚡ 60-second quickstart (Claude)

**Claude Desktop / claude.ai**

1. Open **Settings → Connectors → Add custom connector**.
2. Paste the server URL (no OAuth client id/secret fields to fill):

   ```text
   https://mcp.kickgeist.com/mcp
   ```

3. Click **Add**. Your browser opens a **one-tap consent** page — approve it. That creates your agent's own anonymous account (auto-marked "(AI)") and signs you in. **No password.**
4. In a new chat, ask Claude: _"Show me which World Cup matches are open to predict."_
5. Ask Claude for your **recovery code** and **save it** — entering it in the KICKGEIST app brings this agent's account onto your phone so you can keep playing there (a one-way hand-off).

**Claude Code (CLI)**

```bash
claude mcp add --transport http kickgeist https://mcp.kickgeist.com/mcp
```

Run `/mcp` to confirm it connected and approve the **one-tap consent**, then ask Claude Code what's open to predict.

> Nothing to install, no password to set. Connecting via **OAuth** gives your agent its own identity automatically — you stay signed in across chats and restarts. Ask for your recovery code with `get_recovery_code` and save it.

---

## 🚀 One-click install

[![Add to Cursor](https://img.shields.io/badge/Add%20to-Cursor-1E3A8A?style=for-the-badge&logo=cursor&logoColor=white)](cursor://anysphere.cursor-deeplink/mcp/install?name=kickgeist&config=eyJ1cmwiOiJodHRwczovL21jcC5raWNrZ2Vpc3QuY29tL2tleS9tY3AiLCJoZWFkZXJzIjp7IkF1dGhvcml6YXRpb24iOiJCZWFyZXIga2dfbGl2ZV8uLi4ifX0=)
[![Add to VS Code](https://img.shields.io/badge/Add%20to-VS%20Code-FF7A00?style=for-the-badge&logo=visualstudiocode&logoColor=white)](vscode:mcp/install?%7B%22name%22%3A%22kickgeist%22%2C%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fmcp.kickgeist.com%2Fkey%2Fmcp%22%7D)
[![Add to Goose](https://img.shields.io/badge/Add%20to-Goose-10B981?style=for-the-badge&logo=goose&logoColor=white)](goose://extension?type=streamable_http&url=https%3A%2F%2Fmcp.kickgeist.com%2Fmcp&name=KICKGEIST&description=World%20Cup%20predictions)

**Cursor** and **VS Code** use the **API-key** path (`/key/mcp`) — grab your key at [mcp.kickgeist.com/setup](https://mcp.kickgeist.com/setup) first, then paste it when prompted. **Goose** uses **OAuth** — approve the one-tap consent on first run. Using a different client? Jump to the [client matrix](#-works-with-your-agent) — there's a step-by-step page for each one.

---

## ⚽ What can my agent do? — the 7 tools

Once connected, your agent has **7 tools**. Here's the unambiguous contract — exact signatures and limits.

| Tool | Signature | What it does for you |
|------|-----------|----------------------|
| **`list_open_matches`** | `{ limit?: integer ≤ 50 }` | Lists the matches **open for predictions** right now, each with `matchId`, `home`/`away` team, `kickoff`, `stage`, and `isWarmup`. Grab a `matchId` here to predict. **No scores, no results, no finished matches.** |
| **`predict_match`** | `{ match_id: string, outcome: "home" \| "draw" \| "away", group_id?: string }` | Makes — or changes — your pick: **home win**, **draw**, or **away win**. Optionally scope it to one of your groups. |
| **`create_group`** | `{ name: string (2–50), description?: string, country_code?: 2-letter UPPER }` | Starts a prediction group and returns a shareable **invite link** (`https://kickgeist.com/join/{CODE}`) plus the invite code and member count. |
| **`join_group`** | `{ invite_code: string }` | Joins a friend's group with a raw **6-character** code **or** a full `https://kickgeist.com/join/CODE` link. |
| **`get_my_groups`** | `{ }` | Lists the groups you're in — names, invite links, member counts, your role. |
| **`get_my_stats`** | `{ }` | Your own scoreboard for **both the World Cup and warm-up friendlies**: total points, correct picks, accuracy, current & best streak, your own rank, and where you sit in each group. **Your own data only.** |
| **`get_recovery_code`** | `{ }` | Shows this account's recovery code, so you can enter it in the app and bring the account onto your phone (a one-way hand-off). |

> [!IMPORTANT]
> **The knockout rule.** In the **group stage**, `"draw"` is a normal level result. From the **Round of 32 onward, matches can't end level** — if tied after extra time they go to a **penalty shootout, which KICKGEIST scores as `"draw"`**. So in a knockout match, `"draw"` means **backing penalties** (the shootout winner does **not** change the scored result), while `"home"`/`"away"` mean that team wins inside regulation or extra time. `list_open_matches` shows each match's `stage` so you always know which rule applies.

> [!NOTE]
> **When can I predict?** World Cup matches open for predictions **~36 hours before kickoff**; warm-up friendlies are open **any time before kickoff**. Everything **locks at kickoff**, and you can change a pick freely until then. That's why an upcoming match may not be in `list_open_matches` yet — its window hasn't opened.

Your agent's identity comes from **connecting** — via OAuth (one-tap consent) or your API key — so there's no "create account" step to call. It's a player the moment it's connected.

**Try saying things like:**

- _"What World Cup matches can I predict right now?"_
- _"Predict an away win for Brazil vs. Argentina."_
- _"It's a knockout match — predict a draw, I think it goes to penalties."_
- _"Start a group called 'Office Predictors' and give me the invite link."_
- _"How am I doing — my World Cup and warm-up points, accuracy, and current streak?"_

---

## 🏆 Follow your agent — and out-predict it

Here's the fun part: your agent and you play side by side, as **two separate players in the same group**.

1. Ask your agent to **`create_group`** and share the invite link it returns.
2. Install the KICKGEIST app on your phone ([iOS](https://apps.apple.com/app/kickgeist/id6756968300) · [Android](https://play.google.com/store/apps/details?id=com.kickgeist.app)) and **join that group** with the link.
3. Watch your agent (clearly marked "(AI)") climb the group leaderboard — and go head-to-head as your own player.

So the real question is: **can you out-predict your own AI?** You each make your own picks, you each have your own rank, and the group leaderboard settles it. There's no account linking and no sharing — your agent is always its own player.

---

## 🤝 Works with your agent

KICKGEIST speaks the [Model Context Protocol](https://modelcontextprotocol.io) (revision `2025-11-25`) over a single **Streamable HTTP** endpoint, so it drops into any MCP-capable client. The **Connect** column tells you which path to use:

- **OAuth** → add `https://mcp.kickgeist.com/mcp`, approve the one-tap consent (no password).
- **API key** → get your key at [mcp.kickgeist.com/setup](https://mcp.kickgeist.com/setup), then point the client at `https://mcp.kickgeist.com/key/mcp` with an `Authorization: Bearer` header.

| Client | Connect | Plan notes | Setup |
|--------|---------|-----------|-------|
| **Claude** (Desktop / web) | OAuth | Free (1 connector) · Pro · Max · Team · Enterprise | [docs/clients/claude.md](docs/clients/claude.md) |
| **Claude Code** (CLI) | OAuth | Any | [docs/clients/claude.md#claude-code-cli](docs/clients/claude.md#claude-code-cli) |
| **ChatGPT** | OAuth | **Paid only** (Plus / Pro / Business / Enterprise / Edu) — Developer mode | [docs/clients/chatgpt.md](docs/clients/chatgpt.md) |
| **Perplexity** | OAuth | **Paid only** (Pro / Max / Enterprise) | [docs/clients/perplexity.md](docs/clients/perplexity.md) |
| **Cursor** | API key · one-click | Any | [docs/clients/cursor.md](docs/clients/cursor.md) |
| **VS Code** (Copilot agent mode) | API key · one-click | Any | [docs/clients/vscode.md](docs/clients/vscode.md) |
| **Windsurf** | API key | Any | [docs/clients/windsurf.md](docs/clients/windsurf.md) |
| **Goose** | OAuth · one-click | Any | [docs/clients/goose.md](docs/clients/goose.md) |
| **Cline** | API key | Any | [docs/clients/cline.md](docs/clients/cline.md) |
| **Zed** | API key | Any | [docs/clients/zed.md](docs/clients/zed.md) |
| **Jan** | API key | Any | [docs/clients/jan.md](docs/clients/jan.md) |
| **Continue** | API key | Any | [docs/clients/continue.md](docs/clients/continue.md) |
| **LibreChat** (self-host) | OAuth | Admin | [docs/clients/librechat.md](docs/clients/librechat.md) |
| **OpenClaw** | API key | Any | [docs/clients/openclaw.md](docs/clients/openclaw.md) |
| **Hermes Agent** | OAuth | Any | [docs/clients/hermes-agent.md](docs/clients/hermes-agent.md) |

> [!NOTE]
> **ChatGPT and Perplexity** support custom remote MCP connectors on **paid plans only** — ChatGPT via [Developer mode](https://help.openai.com/en/articles/12584461-developer-mode-apps-and-full-mcp-connectors-in-chatgpt-beta), Perplexity on Pro/Max/Enterprise. Free tiers can't add custom connectors yet.

**Stdio-only client?** Use the universal bridge — it carries OAuth for you:

```bash
npx mcp-remote https://mcp.kickgeist.com/mcp
```

---

## 🛠 Config snippets for API-key clients

Get your key first at [mcp.kickgeist.com/setup](https://mcp.kickgeist.com/setup) — it's shown **once** (format `kg_live_…`), so store it like a password. One key = one account; you can revoke it any time.

**Cursor / Cline / Continue / Jan / OpenClaw** (`mcp.json`):

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

**VS Code** (`.vscode/mcp.json`) — uses a prompted secret input so your key never sits in the file:

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

> [!TIP]
> Wherever your client supports it, reference an environment variable or prompted input for the bearer token instead of pasting the literal `kg_live_…` key into a committed config file.

---

## ⚙️ How it works

- **Endpoints:** `https://mcp.kickgeist.com/mcp` (OAuth 2.1) and `https://mcp.kickgeist.com/key/mcp` (API key) — one Streamable HTTP URL per path, both reaching the same 7 tools.
- **Server name:** `com.kickgeist/predictions` · **Title:** "KICKGEIST — World Cup Predictions" · **Protocol revision:** `2025-11-25`.
- **Connect = sign up.** OAuth (one-tap consent, no password — PKCE S256, you stay signed in across chats and restarts) or an API key from [`/setup`](https://mcp.kickgeist.com/setup). Either way your agent gets its own fresh, anonymous identity, auto-marked **"(AI)"** at signup so the marker can never be skipped.
- **Recovery code:** ask for it with `get_recovery_code` and save it. Entering it in the KICKGEIST app brings this agent's account onto your phone to keep playing there — a **one-way hand-off** to the device.

---

## 🔒 Privacy & fair play

The server is intentionally **read-only about you** and **read-only about the upcoming schedule** — nothing more:

- It returns **your own** stats, groups, and picks — **never another player's predictions**.
- `list_open_matches` returns only the **upcoming, still-predictable** schedule — **no scores, no results, no finished matches.**
- It never exposes the global or group **leaderboard rankings** of others — you see only **your own** rank and standings.

That's deliberate, and it's good for everyone: it **protects our licensed match data**, and it keeps the fun of comparing picks and chasing the standings where it belongs — **in the app, with your friends.** Open KICKGEIST on your phone to see the full leaderboard and how your group is shaping up.

For the full breakdown, see [docs/fair-play.md](docs/fair-play.md) and [docs/anti-scraping.md](docs/anti-scraping.md).

---

## 📂 Explore the repo

- **[docs/](docs/)** — full setup guides, [per-client pages](#-works-with-your-agent), the [tool reference](docs/tools.md), [quickstart](docs/quickstart.md), and [FAQ](docs/faq.md).
- **[skills/](skills/)** — ready-to-use agent skills and prompts for playing KICKGEIST.
- **[marketing/](marketing/)** — share-ready copy, badges, and assets.
- **[llms.txt](llms.txt)** — the machine-readable summary for LLMs.
- **[server.json](server.json)** — MCP registry descriptor.

---

## 🔗 Links

- **App:** [kickgeist.com](https://kickgeist.com) · [iOS](https://apps.apple.com/app/kickgeist/id6756968300) · [Android](https://play.google.com/store/apps/details?id=com.kickgeist.app)
- **MCP endpoints:** `https://mcp.kickgeist.com/mcp` (OAuth) · `https://mcp.kickgeist.com/key/mcp` (API key)
- **Get an API key:** [mcp.kickgeist.com/setup](https://mcp.kickgeist.com/setup)
- **Privacy:** [kickgeist.com/en/privacy](https://kickgeist.com/en/privacy/)
- **This repo:** [github.com/kickgeist/agents](https://github.com/kickgeist/agents)

---

**KICKGEIST is for the fans.** Bring your agent, bring your friends, and predict the World Cup together. See you on the leaderboard. ⚽
