# X / Twitter launch thread

A ready-to-post launch thread for KICKGEIST's MCP server. 9 posts, dev-flavored, one-tap hook, clear CTA.

- Voice: warm, World-Cup energy, technically precise. Inclusive prediction language only — no gambling terms.
- Each post is kept around the 280-char mark. Counts noted in the comments for your reference (`<!-- ... -->`); strip them before posting.
- Suggested media is noted per post; swap in your own assets from `../badges/` and `../docs/`.

---

## Post 1 — the hook
<!-- 268 chars -->

Point your AI agent at one URL and it plays the World Cup — as its own player. 🌍⚽

KICKGEIST now speaks MCP. Your agent predicts every match, starts a group, climbs the leaderboard — from inside Claude, Cursor, VS Code, or whatever assistant you already live in.

🧵👇

<!-- media: short screen recording of asking an agent "connect to KICKGEIST and show me open matches" -->

---

## Post 2 — what it actually is
<!-- ~300 chars -->

A group-first, social World Cup 2026 prediction game. Free, AdMob-funded — no purchases, no subscriptions, never gambling.

Pick each match: home, draw, or away. In a knockout, a tie after extra time goes to penalties, scored a "draw" — so "draw" backs the shootout, and the winner doesn't change it.

---

## Post 3 — connecting is painless
<!-- 276 chars -->

The part devs will like: connecting is painless.

The easiest path is OAuth — supported by Claude and other OAuth-capable clients. Add the URL, hit one tap on the consent page, no password. You stay signed in across chats and restarts.

https://mcp.kickgeist.com/mcp

Approving spins up a fresh, anonymous "(AI)" player for your agent.

---

## Post 4 — pick your path
<!-- ~307 chars -->

Two ways in, one domain:

👆 OAuth (one tap, no password) → /mcp
Claude, Claude Code, Goose, LibreChat & more. ChatGPT/Perplexity: connectors on paid plans.

🔑 API key → grab one at mcp.kickgeist.com/setup, paste as a Bearer header → /key/mcp
Cursor, VS Code, Windsurf, Cline, Zed, Jan, Continue.

Guides 👇

<!-- media: the "Add to" badges from ../badges/ -->

---

## Post 5 — Claude in 60 seconds
<!-- 246 chars -->

Claude, start to finish (OAuth, one tap):

Desktop/web → Settings → Connectors → Add custom connector → paste https://mcp.kickgeist.com/mcp → approve.

Claude Code:

  claude mcp add --transport http kickgeist https://mcp.kickgeist.com/mcp

Then ask: "show me open matches."

---

## Post 6 — Cursor & VS Code in 60 seconds
<!-- ~306 chars -->

Header-only client? Use an API key:

1. mcp.kickgeist.com/setup → copy the key (shown once, kg_live_…)
2. Point /key/mcp at it with an Authorization: Bearer header. Cursor/Cline → mcp.json; VS Code → a prompted secret, never a literal key.

Setup also shows a recovery code to claim the account in the app.

---

## Post 7 — the 7 tools, plain language
<!-- ~316 chars -->

Once connected, your agent has 7 tools:

• see open matches (WC: 36h before kickoff; friendlies: any time; all lock at kickoff)
• predict home / draw / away
• create a group → invite link
• join a group
• see the groups it's in
• check its own points, accuracy & streaks
• grab a recovery code to claim it in the app

---

## Post 8 — can you out-predict your own AI?
<!-- 275 chars -->

Best bit: your agent plays as its own "(AI)" player. So race it. 🤖🆚🧑

Have it create a group, grab the invite link, then open the KICKGEIST app and join. Now you're both in the same group — two separate players, head-to-head.

Can you out-predict your own AI? 👀

---

## Post 9 — fair play + CTA
<!-- 273 chars -->

By design, the server only ever returns YOUR data + the upcoming schedule. No results, no other players' picks, no leaderboard scraping — the social fun stays in the app, with your friends.

Open ecosystem, MIT, no lock-in.

⭐ Code & install guides:
github.com/kickgeist/agents
