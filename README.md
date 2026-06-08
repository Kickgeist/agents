# KICKGEIST — World Cup Predictions, played by your AI agent

**Play the World Cup 2026 with your AI agent.** Predict every match, start a group with your friends, climb the leaderboard — all from inside Claude, Cursor, ChatGPT, or whatever assistant you already live in.

KICKGEIST is a group-first, zero-money, social World Cup prediction game. It's **free**, AdMob-funded — no in-app purchases, no subscriptions, and never any gambling. You predict the **outcome** of each match (home win, draw, or away win), compete with friends in groups, and race up the leaderboard. This repo is the public home for the **KICKGEIST MCP server**, which lets you play through your own AI agent.

> One account, two front rows: anything you do through your agent shows up in the [KICKGEIST mobile app](https://kickgeist.com), and vice-versa. Your phone and your agent play the same game.

---

## 60-second quickstart (Claude)

**Claude Desktop / claude.ai**

1. Open **Settings → Connectors → Add custom connector**
2. Paste the server URL:

   ```text
   https://mcp.kickgeist.com/mcp
   ```

3. Click **Add**. That's it — no login, no OAuth.
4. In a new chat, ask Claude: *"Create my KICKGEIST account and show me which World Cup matches are open to predict."*
5. **Save the recovery code** Claude shows you — drop it into the KICKGEIST app to play the same account on your phone.

**Claude Code (CLI)**

```bash
claude mcp add --transport http kickgeist https://mcp.kickgeist.com/mcp
```

Then run `/mcp` to confirm it's connected, and ask Claude Code to create your account.

> No account to set up, no API key to paste. The server is **authless** — you just add the URL, then call `create_account` to get your identity (and your recovery code). Save that code.

---

## One-click install

[![Add to Cursor](https://img.shields.io/badge/Add%20to-Cursor-1E3A8A?style=for-the-badge&logo=cursor&logoColor=white)](cursor://anysphere.cursor-deeplink/mcp/install?name=kickgeist&config=eyJ1cmwiOiJodHRwczovL21jcC5raWNrZ2Vpc3QuY29tL21jcCJ9)
[![Add to VS Code](https://img.shields.io/badge/Add%20to-VS%20Code-FF7A00?style=for-the-badge&logo=visualstudiocode&logoColor=white)](vscode:mcp/install?%7B%22name%22%3A%22kickgeist%22%2C%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fmcp.kickgeist.com%2Fmcp%22%7D)
[![Add to Goose](https://img.shields.io/badge/Add%20to-Goose-10B981?style=for-the-badge&logo=goose&logoColor=white)](goose://extension?type=streamable_http&url=https%3A%2F%2Fmcp.kickgeist.com%2Fmcp&name=KICKGEIST&description=World%20Cup%20predictions)

Using a different client? Jump to the [client matrix](#works-with-your-agent) below — there's a step-by-step page for each one.

---

## What can my agent do?

Once connected, your agent has **9 tools**. In plain language:

| Tool | What it does for you |
|------|----------------------|
| **`create_account`** | Spins up a fresh anonymous KICKGEIST account and hands you a **recovery code** — your key to the same account in the mobile app. Save it. |
| **`link_account`** | Already play on your phone? Connect this agent to that account using its recovery code. |
| **`get_recovery_code`** | Shows the recovery code for the account you're linked to, so you can save it or enter it in the app. |
| **`list_open_matches`** | Lists the World Cup matches **open for predictions** right now — teams, kickoff time, and stage. This is where you grab a match to predict. |
| **`predict_match`** | Makes or changes your pick for a match: **home win**, **draw**, or **away win**. Optionally scope it to one of your groups. |
| **`create_group`** | Starts a prediction group and returns a shareable **invite link** to send your friends. |
| **`join_group`** | Joins a friend's group with their invite code or link. |
| **`get_my_groups`** | Lists the groups you're in — names, invite links, member counts, your role. |
| **`get_my_stats`** | Your own scoreboard: total points, correct picks, accuracy, streaks, your global rank, and where you sit in each group. |

**Try saying things like:**

- *"What World Cup matches can I predict right now?"*
- *"Predict a draw for Brazil vs. Argentina."*
- *"Start a group called 'Office Predictors' and give me the invite link."*
- *"How am I doing — what's my accuracy and current streak?"*

---

## Works with your agent

KICKGEIST speaks the [Model Context Protocol](https://modelcontextprotocol.io) (revision `2025-11-25`) over a single **Streamable HTTP** endpoint, so it drops into any MCP-capable client.

| Client | Plan notes | Setup |
|--------|-----------|-------|
| **Claude** (Desktop / web) | Free (1 connector) · Pro · Max · Team · Enterprise | [docs/clients/claude.md](docs/clients/claude.md) |
| **Claude Code** (CLI) | Any | [docs/clients/claude.md#claude-code-cli](docs/clients/claude.md#claude-code-cli) |
| **ChatGPT** | Paid only (Plus / Pro / Business / Enterprise / Edu) — Developer mode | [docs/clients/chatgpt.md](docs/clients/chatgpt.md) |
| **Perplexity** | Paid (Pro / Max / Enterprise) | [docs/clients/perplexity.md](docs/clients/perplexity.md) |
| **Cursor** | Any · one-click | [docs/clients/cursor.md](docs/clients/cursor.md) |
| **VS Code** (Copilot agent mode) | Any · one-click | [docs/clients/vscode.md](docs/clients/vscode.md) |
| **Windsurf** | Any | [docs/clients/windsurf.md](docs/clients/windsurf.md) |
| **Goose** | Any · one-click | [docs/clients/goose.md](docs/clients/goose.md) |
| **Cline** | Any | [docs/clients/cline.md](docs/clients/cline.md) |
| **Zed** | Any | [docs/clients/zed.md](docs/clients/zed.md) |
| **Jan** | Any | [docs/clients/jan.md](docs/clients/jan.md) |
| **Continue** | Any | [docs/clients/continue.md](docs/clients/continue.md) |
| **LibreChat** (self-host) | Admin | [docs/clients/librechat.md](docs/clients/librechat.md) |
| **OpenClaw** | Any | [docs/clients/openclaw.md](docs/clients/openclaw.md) |
| **Hermes Agent** | Any | [docs/clients/hermes-agent.md](docs/clients/hermes-agent.md) |

**Stdio-only client?** Use the universal bridge:

```bash
npx mcp-remote https://mcp.kickgeist.com/mcp
```

---

## How it works

- **Endpoint:** `https://mcp.kickgeist.com/mcp` — one Streamable HTTP URL, nothing else to configure.
- **Server name:** `com.kickgeist/predictions` · **Title:** "KICKGEIST — World Cup Predictions"
- **Auth:** none. You add the URL, then `create_account` gives you an anonymous identity plus a recovery code.
- **Portability:** the recovery code links your agent and the mobile app to the **same** account, in either direction.

## Privacy & fair play

The server is intentionally **read-only about you** and **read-only about the upcoming schedule** — nothing more:

- It returns **your own** stats, groups, and picks — never another player's predictions.
- `list_open_matches` returns only the **upcoming, still-predictable** schedule — **no scores, no results, no finished matches.**
- It never exposes the global or group **leaderboard rankings**.

That's deliberate, and it's good for everyone: it protects our licensed match data, and it keeps the fun of comparing picks and trash-talking the standings where it belongs — **in the app, with your friends.** Open KICKGEIST on your phone to see the full leaderboard and how your group is shaping up.

---

## Explore the repo

- **[docs/](docs/)** — full setup guides, per-client pages, and the tool reference.
- **[skills/](skills/)** — ready-to-use agent skills and prompts for playing KICKGEIST.
- **[marketing/](marketing/)** — share-ready copy, badges, and assets.

---

## Links

- App: [kickgeist.com](https://kickgeist.com) · iOS + Android
- MCP endpoint: `https://mcp.kickgeist.com/mcp`
- This repo: [github.com/kickgeist/agents](https://github.com/kickgeist/agents)

---

**KICKGEIST is for the fans.** Bring your agent, bring your friends, and predict the World Cup together. See you on the leaderboard. ⚽
