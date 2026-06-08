# Fair Play & Data Boundaries

**Short version:** your KICKGEIST agent can do everything that makes the game fun — sign up, browse upcoming matches, make picks, build groups, and check your own stats. It just can't peek at results, other players' picks, or the leaderboard rankings. That's on purpose, and it makes the game better for everyone.

This page is the deep dive on the data boundaries. For the full picture — prediction windows, one-account-per-fan, recovery codes, rate limits, and good sportsmanship — start with [Fair Play](./fair-play.md); this page zooms in on *what your agent can and can't see, and why*. No surprises — just how we keep World Cup 2026 fair and fun.

---

## What your agent CAN see and do

Through the [9 MCP tools](./tools.md), your agent has the full play loop:

- **Your own identity** — create an anonymous account (`create_account`), link the one you already play on your phone (`link_account`), and grab your recovery code (`get_recovery_code`).
- **The upcoming schedule** — every World Cup match currently open for predictions (`list_open_matches`): teams, kickoff time, stage. Everything you need to make a pick.
- **Your predictions** — make or change a pick on any open match (`predict_match`).
- **Your groups** — create one and get a shareable invite link (`create_group`), join a friend's (`join_group`), and list the ones you're in (`get_my_groups`).
- **Your own stats** — points, correct picks, accuracy, streaks, your global rank, and your standings inside your groups (`get_my_stats`).

That's a complete, satisfying way to play the whole tournament from inside your agent.

---

## What your agent CANNOT see — and why that's a good thing

The MCP server deliberately never returns:

| Boundary | What this means |
|---|---|
| **No match results** | `list_open_matches` returns only matches still open for predictions — future kickoffs, before the whistle. No scores, no finished games, no winners. |
| **No other players' picks** | You see only your own predictions. Never a friend's, never a stranger's. |
| **No leaderboard lists** | `get_my_stats` shows *your* rank and *your* group standings — but never the full global or group ranking lists of who's above and below you. |

These aren't gaps we forgot to fill. They're the design.

### Why: protecting licensed match data

KICKGEIST's fixtures, scores, and live results come from a licensed data provider. Re-publishing that data through an open, authless API would break the terms that let us bring you the World Cup in the first place. By exposing only the upcoming schedule you can still predict — and never results — the agent interface respects those terms while still giving you everything you need to play.

### Why: keeping the social joy in the app

The best part of KICKGEIST is the moment you out-predict your friend and the group chat lights up. That magic lives in the app — the leaderboard reveals, the head-to-head comparisons, the streak you've been bragging about all week. We keep the full social comparison there on purpose, so the celebration stays where the whole group can feel it together.

So your agent is the perfect place to *make* picks fast and manage your groups. The app is where you *savor* the standings. Two halves of one experience.

### Why: fair play for the whole pitch

An interface that handed out results, everyone's picks, and the complete leaderboard would be an open invitation to game the system. Keeping those out — for every player, through every client — means nobody gets an edge from scraping. The playing field stays level, which is exactly what makes climbing the leaderboard worth it.

---

## How the boundaries are enforced

This isn't a polite request that copy somewhere asks scrapers to honor — it's structural:

- **Enforced by omission.** No tool in the contract can return a result, a finished match, another member's pick, or a ranking list. The data simply isn't reachable through any tool.
- **`list_open_matches` is results-free by definition.** It returns only matches that are still open for predictions — scheduled, before kickoff, inside the prediction window. A match with a result is, by definition, no longer in that list.
- **Your session sees only your data.** Stats and groups are scoped to the account linked to your session. There is no tool that takes someone else's user ID and hands back their picks or position.

---

## A feature, not a limitation

If you're a developer or AI tinkerer, it's worth saying plainly: yes, this is intentionally narrow, and that's the point.

The KICKGEIST agent interface is built to let you **play** — quickly, scriptably, from whatever client you live in — while the parts that protect our data partners and preserve the group-chat thrill stay exactly where they belong. You get a clean, honest set of tools that do precisely what they say. We get a game that stays fair and a partnership that stays healthy. Your friends get a leaderboard reveal that still gives everyone goosebumps.

That's the trade, and we think it's a great one. Now go make some picks.

---

## See also

- **The full fair-play guide:** [Fair Play](./fair-play.md) — prediction windows, one account per fan, recovery codes, rate limits, and good sportsmanship.
- **What each tool returns:** the [9-tool reference](./tools.md) spells out every parameter and response, including the anti-scraping-by-design boundaries.
- **Get connected:** add `https://mcp.kickgeist.com/mcp` in your client and call `create_account` to start. Save the recovery code it gives you — that's how you reach the same account in the mobile app.
- **The full leaderboard, head-to-head comparisons, and the group-chat moments** all live in the [KICKGEIST app](https://kickgeist.com) on iOS and Android.
