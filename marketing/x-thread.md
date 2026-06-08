# X / Twitter launch thread

A ready-to-post launch thread for KICKGEIST's MCP server. 8 posts, dev-flavored, one-click hook, clear CTA.

- Voice: warm, World-Cup energy, technically precise. Inclusive prediction language only — no gambling terms.
- Each post is kept around the 280-char mark. Counts noted in the comments for your reference (`<!-- ... -->`); strip them before posting.
- Suggested media is noted per post; swap in your own assets from `../badges/` and `../docs/`.

---

## Post 1 — the hook
<!-- 270 chars -->

Point your AI agent at one URL and it plays the World Cup — as its own player. 🌍⚽

KICKGEIST now speaks MCP. Your agent predicts every match, starts a group, climbs the leaderboard — from inside Claude, Cursor, ChatGPT, or whatever assistant you already live in.

🧵👇

<!-- media: short screen recording of asking an agent "create my KICKGEIST account and show me open matches" -->

---

## Post 2 — what it actually is
<!-- 256 chars -->

What it is: a group-first, zero-money, social World Cup 2026 prediction game.

Free, AdMob-funded. No purchases, no subscriptions, never any gambling.

You predict the outcome of each match — home win, draw, or away win — and race your friends up the leaderboard.

---

## Post 3 — the authless magic
<!-- 268 chars -->

The part devs will like: it's authless.

No OAuth dance. No API key to paste. You add the endpoint:

https://mcp.kickgeist.com/mcp

…then your agent calls `create_account` and gets a recovery code. Save it — pop it into the KICKGEIST app to bring this agent's account onto your phone. Done.

---

## Post 4 — one-click install
<!-- 247 chars -->

One-click for the big clients:

▶️ Cursor — deep-link install
▶️ VS Code (Copilot agent mode) — one click
▶️ Goose — one click

Everyone else (Claude, ChatGPT, Perplexity, Windsurf, Cline, Zed, Jan, Continue…) is a single URL paste.

Buttons in the repo 👇

<!-- media: the three "Add to" badges from ../badges/ -->

---

## Post 5 — Claude in 60 seconds
<!-- 233 chars -->

Claude, start to finish:

Desktop/web → Settings → Connectors → Add custom connector → paste the URL → Add.

Claude Code:

  claude mcp add --transport http kickgeist https://mcp.kickgeist.com/mcp

Then just ask: "create my account and show me open matches."

---

## Post 6 — the 8 tools, plain language
<!-- 270 chars -->

Once connected, your agent has 8 tools. In human terms:

• create its own account (auto-marked "(AI)")
• see matches open to predict
• predict home / draw / away
• create a group → get a shareable invite link
• join a group
• check its own points, accuracy & streaks

You just talk. It does the calling.

---

## Post 7 — can you out-predict your own AI?
<!-- 277 chars -->

Best bit: your agent plays as its own "(AI)" player. So race it. 🤖🆚🧑

Have it create a group, grab the invite link, then install the KICKGEIST app and join. Now you're both in the same group — two separate players, head-to-head.

Can you out-predict your own AI? 👀

---

## Post 8 — fair play + CTA
<!-- 274 chars -->

By design, the server only ever returns YOUR data + the upcoming schedule. No results, no other players' picks, no leaderboard scraping — the social fun stays in the app, with your friends.

Open ecosystem, MIT, no lock-in.

⭐ Code & install guides:
github.com/kickgeist/agents
