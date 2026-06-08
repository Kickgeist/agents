# LinkedIn — Launch post (founder voice)

A ready-to-publish LinkedIn post announcing the KICKGEIST MCP server. Founder voice: a touch more professional than the X/short-form copy, but still warm and World-Cup-energetic. Copy the block below straight into LinkedIn.

---

## Post

I built a World Cup that you play by pointing your AI agent at a URL.

KICKGEIST is a group-first, zero-money social prediction game for World Cup 2026. It's live on iOS and Android, it's free, AdMob-funded — no in-app purchases, no subscriptions, and nothing to do with gambling. You predict the outcome of each match (home win, draw, or away win), start a group with your friends, and climb the leaderboard together.

This week we're opening a second front row: a Model Context Protocol server, so you can play the entire game from inside the AI assistant you already work in.

Here's the part I'm proud of — there's almost nothing to set up.

→ Add one URL: https://mcp.kickgeist.com/mcp
→ No OAuth. No login. No API key. The server is authless.
→ Ask your agent to create your account, and it hands you a recovery code.
→ Drop that recovery code into the KICKGEIST mobile app, and it's the same account. Predict from Claude on your laptop at lunch, check your streak on your phone at the bar that night.

A few things you can just say to your agent once it's connected:

- "Which World Cup matches can I predict right now?"
- "Predict a draw for Brazil vs. Argentina."
- "Start a group called 'Engineering vs. Sales' and give me the invite link to share."
- "How am I doing — what's my accuracy and current streak?"

Under the hood it's nine tools over a single Streamable HTTP endpoint. Because it's plain MCP, it isn't a Claude-only thing — it works across Claude, Claude Code, ChatGPT, Perplexity, Cursor, VS Code, Goose, Windsurf, Cline, Zed, OpenClaw, Hermes, and more, with one-click installs where the client supports them.

One deliberate design choice worth naming: the server only ever returns your own data and the upcoming match schedule. No scores, no results, no other players' picks, no leaderboard rankings come back through the agent. That protects our licensed match data — and, honestly, it keeps the best part of this game where it belongs: comparing picks and talking trash with your friends, in the app.

If you live in an AI assistant all day and you love football, this one's for you. Add the URL, create your account, and bring your group.

Open source and the full client guides: github.com/kickgeist/agents
The app: kickgeist.com

See you on the leaderboard. ⚽

#WorldCup2026 #MCP #AIagents #BuildInPublic #Football

---

## Posting notes (not part of the post)

- **Voice check:** founder, first-person, warm and confident. Prediction language only ("predict / pick / prediction") — zero gambling terms.
- **Truthful claims:** authless (URL only, no OAuth/login/API key); `create_account` returns a recovery code; recovery code links agent ↔ mobile app both ways; 9 tools over one Streamable HTTP endpoint; the agent never returns scores, results, other players' picks, or leaderboard rankings.
- **Endpoint must stay exact:** `https://mcp.kickgeist.com/mcp`.
- **Links:** repo `github.com/kickgeist/agents`, app `kickgeist.com`. LinkedIn auto-linkifies bare URLs, so leave them unwrapped.
- **Length:** comfortably under LinkedIn's 3,000-character limit; the first two lines are the hook shown before "…see more," so keep them at the top if you trim.
- **Optional first comment:** drop the one-click install badges or the Claude quickstart from the repo README to give engaged readers an immediate next step without crowding the post.
- **Suggested media:** a short screen recording of an agent calling `list_open_matches` then `predict_match`, or a clean still of the endpoint URL. Add descriptive alt text for accessibility.
