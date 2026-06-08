# Fair Play

KICKGEIST is a group-first, zero-money World Cup 2026 prediction game. No money, no in-app purchases — just you, your friends, and the best month of football on the planet. Playing through your own AI agent (via our [MCP server](./quickstart.md), endpoint `https://mcp.kickgeist.com/mcp`) is a first-class way to take part.

These guidelines exist so the game stays fun, fair, and friendly for everyone — whether they predict from the mobile app or from an agent. Read them once, play forever.

## The one rule behind all the rules

**Play like a fan, not like a script.** Make your picks, rib your friends, climb the leaderboard, celebrate the upsets. Everything below just protects that experience for the next fan.

## Prediction windows — know when picks close

Timing is the heart of the game, so it is worth getting right.

- **World Cup matches close before kickoff (the exact window varies by match).** Once a match enters its prediction window you can pick and re-pick freely, but the window seals ahead of the first whistle. This keeps everyone on equal footing — no last-minute information edge, no waiting on a team-sheet leak.
- **Warmup friendlies stay open right up to kickoff.** Warmup matches are our laid-back pre-tournament playground (look for `isWarmup: true` on a match). They are perfect for testing your agent setup and getting a feel for the flow before the real thing.

`list_open_matches` is your source of truth, so you never have to track close-times yourself: it only ever returns matches you can still pick. Always confirm a match is still open there before you predict. If a `matchId` is not in that list, its window has closed.

```text
list_open_matches  →  pick a matchId where the window is open
predict_match      →  outcome: "home" | "draw" | "away"
```

> Heads-up for agents: `list_open_matches` returns the upcoming schedule only — never scores, never results, never finished matches. That is intentional (see [Fair data, by design](#fair-data-by-design)).

## One account = one player

Each KICKGEIST account is one human fan. That is what makes the leaderboard mean something.

- **Create one account for yourself** with `create_account`, then play from it everywhere.
- **Connect your agent to the account you already use on your phone** with `link_account` and your recovery code — don't spin up a second identity for the same person. Your picks, points, streaks, and groups all live on one account, whether you reach it from the app or an agent.
- **Don't create accounts in bulk** to pad a group, farm leaderboard spots, or game rankings. It is unfair to real fans and it is the fastest way to get an account benched.

If you predict on your phone *and* through an agent, that is great — just make sure both point at the **same** account via the recovery code.

## Recovery codes are personal

Your recovery code is the master key to your account. Treat it like the keys to the stadium.

- It is shown when you call `create_account`, and any time after with `get_recovery_code`.
- **Save it somewhere safe.** Enter it in the KICKGEIST mobile app (or via `link_account` in another agent) to reach the same account anywhere.
- **Keep it to yourself.** Anyone with your recovery code can act as you — change your picks, see your stats, manage your groups. Share invite links with friends, never your recovery code.
- Lose your code and you lose the path back to that account, so store it the moment you get it.

## Rate limits & being a good guest

The server is shared by fans all over the world, so it asks every agent to be a considerate guest.

- **Reasonable, human-paced use is welcome.** Listing matches, making and changing picks, checking your stats, running your groups — all normal, all fine.
- **Rate limits are in place** to keep the service fast and fair for everyone. If you hit one, slow down and retry after a short pause rather than hammering the endpoint — your picks aren't going anywhere, and the window won't close on you the instant a limit trips.
- **No automated scraping, flooding, or bulk-account creation.** The server intentionally exposes only your own data and the open schedule, so there is nothing to scrape anyway — but please don't try. Build delightful agent experiences, not abuse.

## Fair data, by design

You might notice the MCP server tells you a lot about *you* and very little about *everyone else*. That is a feature, not a limitation.

- `get_my_stats` returns **your** points, accuracy, streaks, your own global rank, and your standing inside your groups.
- The server **never** returns match results, other players' picks, or the full global/group leaderboards.

Two reasons: it protects our licensed match data, and it keeps the best part of the game — comparing picks, talking trash, celebrating together — right where it belongs, in the [KICKGEIST app](https://kickgeist.com) with your friends. Open the app to see the full leaderboard and how your group is shaping up.

For the full picture of exactly where those data lines sit, how they're enforced structurally, and why each one makes the game better, see [Fair Play & Data Boundaries](./anti-scraping.md).

## Good sportsmanship

This is the fun part, and honestly the whole point.

- **Make your own picks.** Read the matchups, trust your gut, own the result. A bold call that lands feels unbeatable.
- **Bring friends in.** Create a group with `create_group`, share the invite link so friends can `join_group`, and turn the tournament into a month-long group chat where the only prize is pure bragging rights.
- **Lose like a champion, win like a friend.** Upsets happen — that is football. The fan who laughs off a wrong pick and comes back swinging for the next match is the one everyone wants in their group.
- **Keep it positive.** Group names and descriptions are seen by everyone you invite — keep them welcoming.

## Questions or something off?

If a match looks like its window is wrong, a pick won't save, or anything feels unfair, open the [KICKGEIST app](https://kickgeist.com) and send us feedback — we read every message. We want this to be the best, fairest World Cup game in the world, and your report helps us get there.

---

Play like a fan. Bring your friends. May your bold picks land and your group chat never go quiet. See you on the leaderboard. ⚽
