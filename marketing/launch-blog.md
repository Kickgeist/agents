# Bring your AI agent to the World Cup

The World Cup is the one month every four years when the whole planet watches the same thing at the same time. KICKGEIST is how you and your friends turn that month into a game of your own: predict every match, run a group, climb the leaderboard. It's free, AdMob-funded — no in-app purchases, no subscriptions, and never any gambling. You predict the **outcome** of each match (home win, draw, or away win), and the points and bragging rights follow.

Today we're opening a second front row.

**You can now play KICKGEIST through your own AI agent.** Point Claude, Cursor, ChatGPT, Goose — whatever assistant you already live in — at one URL, and your agent becomes a full KICKGEIST player. It creates your account, sees which matches are still open, locks in your picks, spins up groups for your friends, and reports your stats back to you in plain language. The same account lives in the [KICKGEIST mobile app](https://kickgeist.com) too — one identity, two front rows.

## The hook: it's just a URL

There's no SDK to install, no API key to mint, no OAuth dance. The KICKGEIST MCP server is a single [Model Context Protocol](https://modelcontextprotocol.io) endpoint over Streamable HTTP:

```text
https://mcp.kickgeist.com/mcp
```

That's the whole setup. The server is **authless** — you add the URL, and your agent gets nine tools. Your identity comes from a single tool call, `create_account`, which spins up a fresh anonymous KICKGEIST account and hands you a **recovery code**. Save that code: drop it into the mobile app (or `link_account` from any other agent) and you're playing the exact same account on your phone, on your laptop, anywhere.

No personal data required to start. No email, no password. Just a URL and a recovery code you keep somewhere safe.

## How it works: nine tools, one game

Once connected, your agent can do everything a player does:

- **`create_account`** / **`link_account`** / **`get_recovery_code`** — your identity. Create a new account, connect to the one you already play on your phone, or read back your recovery code to save it.
- **`list_open_matches`** — the matches open for predictions right now: teams, kickoff time, stage. This is where you grab a match to predict.
- **`predict_match`** — your pick for a match: `home`, `draw`, or `away`. Change it any time before kickoff. Optionally scope it to one of your groups.
- **`create_group`** / **`join_group`** / **`get_my_groups`** — the social layer. Start a group, get a shareable invite link, join a friend's group, see where you all stand.
- **`get_my_stats`** — your own scoreboard: total points, correct picks, accuracy, current and best streak, your global rank, and your standing in each group.

In practice it feels like a conversation:

> *"Create my KICKGEIST account and show me which World Cup matches are open to predict."*
>
> *"Predict a draw for the Brazil match, then start a group called 'Office Predictors' and give me the invite link."*
>
> *"How am I doing — what's my accuracy and current streak?"*

Your agent does the rest.

## Quickstart (under a minute)

**Claude Desktop / claude.ai**

1. Open **Settings → Connectors → Add custom connector**.
2. Paste `https://mcp.kickgeist.com/mcp` and click **Add**. No login, no OAuth.
3. In a new chat: *"Create my KICKGEIST account and list the open World Cup matches."*
4. **Save the recovery code** Claude shows you — that's your key to the same account in the app.

**Claude Code (CLI)**

```bash
claude mcp add --transport http kickgeist https://mcp.kickgeist.com/mcp
```

Run `/mcp` to confirm it's connected, then ask Claude Code to create your account.

That's it. Your first pick is one sentence away.

## An open ecosystem, on purpose

MCP is an open standard, so KICKGEIST isn't tied to any single assistant. The same endpoint drops into every MCP-capable client:

- **One-click installs** for **Cursor**, **VS Code** (Copilot agent mode), and **Goose** — a deep link adds the server for you.
- **Paste-the-URL** setups for **ChatGPT** (paid plans, Developer mode), **Perplexity** (paid plans), **Cline**, **Jan**, and more.
- **Config-file** setups for **Windsurf**, **Zed**, **Continue**, and self-hosted **LibreChat**.
- **Open-source agents** like **OpenClaw** and **Hermes** connect with a single CLI command or config block.
- **Stdio-only client?** Bridge it with the universal `npx mcp-remote https://mcp.kickgeist.com/mcp`.

Every client has a step-by-step page in [`docs/clients/`](../docs/clients/). Whatever you already use, there's a path in — and because it's one shared endpoint, your picks land in the same place no matter which agent you played from.

We built it this way because the fun of the World Cup is that everyone shows up. Your agent should be able to, too.

## Fair play, by design

Here's the part we're proud of: the server is intentionally **read-only about you** and **read-only about the upcoming schedule**, and nothing more.

- `list_open_matches` returns only the **upcoming, still-predictable** matches — **no scores, no results, no finished matches.**
- `get_my_stats` returns **your own** numbers — never another player's picks.
- It never exposes the global or group **leaderboard rankings**.

That's deliberate, and it's good for everyone. It protects our licensed match data, and it keeps the heart of the game — comparing picks, watching the standings swing, the group-chat trash talk — exactly where it belongs: **in the app, with your friends.** Open KICKGEIST on your phone to see the full leaderboard and how your group is shaping up.

Your agent is a brilliant teammate for making picks and running your group. The celebration is still a human sport.

## Get in the game

The World Cup belongs to the fans. Now it belongs to your agent, too.

1. Add `https://mcp.kickgeist.com/mcp` to your assistant.
2. Ask it to create your account — and **save the recovery code.**
3. Make your first pick, start a group, and send your friends the invite link.

Bring your agent, bring your friends, and predict the World Cup together. See you on the leaderboard. ⚽

---

- App: [kickgeist.com](https://kickgeist.com) · iOS + Android
- MCP endpoint: `https://mcp.kickgeist.com/mcp`
- Repo: [github.com/kickgeist/agents](https://github.com/kickgeist/agents)
