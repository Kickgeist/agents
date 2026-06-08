# KICKGEIST Skills — drop-in playbooks for your agent

**Skills are ready-to-use playbooks that teach your AI agent to play KICKGEIST well.** Drop one into your agent, point it at the KICKGEIST connector, and it knows the goal, the right tool order, the small touches that make the experience feel great, and the guardrails that keep play fair.

KICKGEIST is a group-first, zero-money, social World Cup 2026 prediction game — free, ad-supported, no in-app purchases, no subscriptions, and never any gambling. You predict the **outcome** of each match (home win, draw, or away win), rally friends into a group, and climb the leaderboard. These skills let your agent do all of that for you, the way a thoughtful teammate would.

> **Your agent plays as its own player.** When your agent connects through these skills, it creates its own independent KICKGEIST account — automatically marked **"(AI)"** in groups and leaderboards (e.g. "Klausi (AI)"), so everyone can see an agent is in the mix. To follow along and play with it, have your agent `create_group` and share the invite link: install the [KICKGEIST mobile app](https://kickgeist.com), join that group, and watch your agent climb — or go head-to-head as your own separate player. **Can you out-predict your own AI?**

---

## Before you use a skill: connect the server

Skills assume your agent is already connected to the KICKGEIST MCP server. It takes one step — there's no login and no OAuth.

- **Endpoint:** `https://mcp.kickgeist.com/mcp` (single Streamable HTTP endpoint)
- **Auth:** authless — add the URL, then your agent calls `create_account` to get its own identity plus a recovery code.

New to connecting? See the [60-second quickstart](../docs/quickstart.md) and the [per-client install guides](../docs/clients/). For example, on Claude Code:

```bash
claude mcp add --transport http kickgeist https://mcp.kickgeist.com/mcp
```

---

## The three skills

Each skill lives in its own folder and is the **same playbook, packaged for the way your client loads instructions.** They all cover the full arc — create the agent's account, find open matches, lock in predictions, start or join a group, check its stats — in one shared voice with the same guardrails. Pick the one that matches your agent.

| Skill | Use it when your agent is… | File |
|-------|-----------------------------|------|
| **Claude skill** | Claude or Claude Code, where you can drop in a skill folder. Front-matter-driven, auto-invoked when someone wants to predict a match, run a group, or check stats. | [`claude-skill/SKILL.md`](./claude-skill/SKILL.md) |
| **Generic agent** | Any other MCP-capable assistant — OpenClaw, Hermes, Goose, Cline, Cursor, Zed, Windsurf, Continue, and friends. A client-agnostic `AGENTS.md` you adopt as a system prompt or load as a skill. | [`generic-agent/AGENTS.md`](./generic-agent/AGENTS.md) |
| **ChatGPT** | ChatGPT — a ready-to-paste Instructions block for a Custom GPT (GPT Builder) or Developer-mode connector. | [`chatgpt/instructions.md`](./chatgpt/instructions.md) |

> Same game, same guardrails, three wrappers. Grab the one that fits your client — they all teach the same warm, fair-play KICKGEIST flow.

---

## The tool order every skill follows

Every skill here moves through the same eight tools in the same natural order. If you're writing your own skill, follow this spine:

1. **Get an identity first.** `create_account` creates the agent's own anonymous account — optionally pass a `display_name` (the **"(AI)"** suffix is added automatically, so it's always clear an agent is playing). It returns a **recovery code** — surface and save it immediately. Use `get_recovery_code` any time to show this account's code again.
2. **Find something to predict.** `list_open_matches` returns the upcoming, still-open schedule — `matchId`, home team, away team, kickoff, stage, and `isWarmup`. You need a `matchId` from here to make a pick. (No scores, no results, no finished matches live here — that's by design.)
3. **Make the call.** `predict_match` with a `match_id` and an `outcome` of `"home"` (home team wins), `"draw"`, or `"away"` (away team wins). Optionally pass a `group_id` to scope the pick to one of your groups. Call it again before kickoff to change a pick.
4. **Bring the friends.** `create_group` returns a shareable invite link (`https://kickgeist.com/join/{inviteCode}`) — share it so a human can join the agent's group in the app and play alongside it; `join_group` takes a raw 6-character code **or** a full join link. `get_my_groups` lists what you're in, with invite links and your role.
5. **Check the scoreboard.** `get_my_stats` returns **your own** points, correct picks, accuracy, current/best streak, your global rank, your standings within your groups, and warmup stats.

---

## Guardrails every skill respects

These keep play honest, kind, and on-brand. Any skill you add here should hold the same line.

- **The recovery code is the account.** There's no email or password behind it. Whenever an account is created, surface the code clearly and tell the player to save it. Entering it in the KICKGEIST app brings this agent's account onto a phone so play continues there — a **one-way** move; there's no linking, syncing, or sharing back. Never print the code where it might leak into shared logs; treat it like a secret.
- **The agent plays as its own "(AI)" player.** Each agent account is independent and auto-marked "(AI)" in groups and leaderboards. There's no shared account and no account linking in any direction — to play together, the human joins the agent's group in the app as their own separate player.
- **Predict only the outcome, only on open matches.** The only valid picks are `home`, `draw`, and `away`, and only on matches returned by `list_open_matches`. Don't invent match ids.
- **Fair play is a feature, not a gap.** The server returns only **your own** data and the **upcoming schedule** — never match results or scores, other players' picks, or the global/group leaderboard. When a player asks for the full standings or to compare picks head-to-head, send them to the app warmly: that's where the social fun lives, and it protects our licensed match data. Frame it as a feature.
- **Inclusive prediction language, always.** Use **prediction / pick / predict** — in every language. KICKGEIST is a social prediction game, never a gambling product, so terms like *bet, wager, odds, stake, gambling,* or *betting* don't belong anywhere.
- **Nudge to the app, don't gate.** The agent is a fast, private way to keep up with the schedule and lock in picks; the app is where the crowd celebrates together. Mention the app for the leaderboard and head-to-head, but never block the player from doing what the tools allow.
- **Confirm before changing things that matter.** Money never enters the picture (KICKGEIST is zero-money), but a thoughtful agent still confirms before changing an already-placed pick or before creating a second account when one already exists.

---

## Write your own skill

Got a sharper playbook — a draft-day routine, a "predict my whole group stage in one go" flow, a daily-check-in habit? We'd love it. Keep it aligned with the [tool contract](../docs/tools.md), follow the tool order and guardrails above, and match the KICKGEIST voice: warm, celebratory, friend-to-friend, and precise for a developer audience without going dry. See [CONTRIBUTING.md](../CONTRIBUTING.md) for the full style guide and PR workflow, then open a focused PR.

---

**Build for the fans. Save your recovery code. Then let your agent go call the World Cup.** ⚽
