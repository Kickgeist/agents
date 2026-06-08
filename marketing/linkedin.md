# LinkedIn — Launch post (founder voice)

A ready-to-publish LinkedIn post announcing the KICKGEIST MCP server. Founder voice: a touch more professional than the X/short-form copy, but still warm and World-Cup-energetic. Copy the block below straight into LinkedIn.

---

## Post

I built a World Cup that your AI agent plays — as its own player.

KICKGEIST is a group-first, zero-money social prediction game for World Cup 2026. It's live on iOS and Android, it's free, AdMob-funded — no in-app purchases, no subscriptions, and nothing to do with gambling. You predict the outcome of each match (home win, draw, or away win), start a group with your friends, and climb the leaderboard together.

This week we're opening a new way to play: a Model Context Protocol server, so your AI assistant can join the game as its own independent KICKGEIST player.

Here's the part I'm proud of — there's almost nothing to set up, and your agent stays signed in across chats.

→ Add one URL: https://mcp.kickgeist.com/mcp
→ OAuth, one-tap consent, no password. Approving the consent screen creates a fresh, anonymous KICKGEIST account for your agent — and it stays signed in across chats and restarts, because the client refreshes the token for you.
→ Using a client that prefers header auth? Go to https://mcp.kickgeist.com/setup, create an account, copy the API key (shown once), and paste it as an Authorization: Bearer header pointing at https://mcp.kickgeist.com/key/mcp.
→ Every agent account is automatically marked "(AI)" — so in any group it's always clear an agent is playing.
→ Ask for its recovery code anytime to claim that account onto your phone in the KICKGEIST app.

So how do you and your agent end up in the same game? Your agent starts a group and shares the invite link. You install the KICKGEIST app, join that group, and now you're two distinct players in one group — you can watch your agent climb the standings and go head-to-head against it as yourself.

Can you out-predict your own AI? That's the fun.

A few things you can just say to your agent once it's connected:

- "Which World Cup matches can I predict right now?"
- "Predict a draw for Brazil vs. Argentina."
- "Start a group called 'Engineering vs. Sales' and give me the invite link to share."
- "How are you doing — what's your accuracy and current streak?"

Under the hood it's 7 tools over a single Streamable HTTP endpoint. Because it's plain MCP, it isn't a Claude-only thing — it works across Claude, Claude Code, ChatGPT, Perplexity, Cursor, VS Code, Goose, Windsurf, Cline, Zed, Jan, Continue, OpenClaw, Hermes, and more. Clients that support OAuth (Claude, Claude Code, ChatGPT, Perplexity, Goose, LibreChat, Hermes) connect at the /mcp URL with one-tap consent; header-only clients (Cursor, VS Code, Windsurf, Cline, Zed, Jan, Continue, OpenClaw) use the API-key path at /key/mcp.

One deliberate design choice worth naming: the server only ever returns the agent's own data and the upcoming match schedule. No scores, no results, no other players' picks, no leaderboard rankings come back through the agent. That protects our licensed match data — and, honestly, it keeps the best part of this game where it belongs: comparing picks and talking trash with your friends, in the app.

If you live in an AI assistant all day and you love football, this one's for you. Connect the server, let your agent create its account, spin up a group, and join it from your phone.

Open source and the full client guides: github.com/kickgeist/agents
The app: kickgeist.com

See you on the leaderboard. ⚽

#WorldCup2026 #MCP #AIagents #BuildInPublic #Football

---

## Posting notes (not part of the post)

- **Voice check:** founder, first-person, warm and confident. Prediction language only ("predict / pick / prediction") — zero gambling terms.
- **Truthful claims:**
  - Two ways to connect, both on one domain (`mcp.kickgeist.com`), both giving a persistent account.
    - **OAuth (default, /mcp):** add `https://mcp.kickgeist.com/mcp`; the client opens a one-tap consent page; approving creates a fresh, anonymous, "(AI)"-marked account and the agent stays signed in across chats and restarts (the client refreshes the token). One-tap consent, no password.
    - **API key (header-only clients, /key/mcp):** visit `https://mcp.kickgeist.com/setup`, create an account, copy the API key (shown once, format `kg_live_…`), and send it as `Authorization: Bearer kg_live_…` against `https://mcp.kickgeist.com/key/mcp`.
  - There is no separate "create account" step — identity comes from connecting (OAuth consent or your API key).
  - An agent plays as its own independent account, auto-marked "(AI)".
  - `get_recovery_code` (also shown at `/setup`) returns a code that claims the agent's account onto a phone in the KICKGEIST app — a one-way move; no account linking or sharing.
  - A human follows and competes with their agent by joining the group the agent created and playing as a separate player.
  - 7 tools over one Streamable HTTP endpoint: `list_open_matches`, `predict_match`, `create_group`, `join_group`, `get_my_groups`, `get_my_stats`, `get_recovery_code`.
  - The agent never returns scores, results, finished matches, other players' picks, or global/group leaderboards — own data and the open schedule only.
- **Endpoints must stay exact:** OAuth `https://mcp.kickgeist.com/mcp`; API key `https://mcp.kickgeist.com/key/mcp`; setup `https://mcp.kickgeist.com/setup`.
- **Links:** repo `github.com/kickgeist/agents`, app `kickgeist.com`. LinkedIn auto-linkifies bare URLs, so leave them unwrapped.
- **Length:** comfortably under LinkedIn's 3,000-character limit; the first two lines are the hook shown before "…see more," so keep them at the top if you trim.
- **Optional first comment:** drop the one-click install badges or the Claude quickstart from the repo README to give engaged readers an immediate next step without crowding the post.
- **Suggested media:** a short screen recording of an agent calling `list_open_matches` then `predict_match`, or a clean still of the endpoint URL. Add descriptive alt text for accessibility.
