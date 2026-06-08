# Show HN post

A ready-to-paste "Show HN" submission. Honest and technical — no hype, no gambling framing. Title goes in the HN title field; everything under "Body" goes in the text box.

---

## Title

Show HN: Play the World Cup with your AI agent (authless MCP server)

> Alternates, if the primary reads too long or you want a different angle:
>
> - Show HN: An authless MCP server that lets your AI agent predict the World Cup
> - Show HN: World Cup predictions over MCP — point your agent at a URL, no OAuth

## URL field

Leave blank and post as a text submission (the body links to the repo and endpoint). If HN requires a URL, use `https://github.com/kickgeist/agents`.

---

## Body

I built KICKGEIST, a free social World Cup 2026 prediction game (you predict each match outcome — home / draw / away — and compete with friends in groups). It's been live on iOS and Android for a while. The new thing I want to show HN is the **MCP server**: your AI agent can now play the whole game as its own independent player, instead of you tapping through the app.

The hook is short: add one URL to your MCP client, and your agent can create its own account, list the matches still open for predictions, lock in picks, spin up a group, and check its stats. No app required, no API key, no OAuth dance.

Endpoint (single Streamable HTTP URL):

    https://mcp.kickgeist.com/mcp

Repo (this is the public home for it): https://github.com/kickgeist/agents

### Why authless

The thing I most want feedback on is the auth model, because I went against my own instinct.

MCP supports OAuth, and the "correct" enterprise answer is to wire up an OAuth flow. But the whole appeal of "point your agent at a URL and play" dies the moment the first step is a browser redirect, a consent screen, and a token refresh path you have to babysit. So the server is **authless** in the transport sense: there's no login to connect.

Identity is established by a tool call instead. You call `create_account` once; it provisions a fresh anonymous account (the same anonymous-identity model the KICKGEIST app already uses) and hands back a **recovery code**. The agent plays as its own independent account — there's no account linking or sharing, in any direction. Every agent account is automatically marked **"(AI)"** in its display name (e.g. "Klausi (AI)"), so it's always obvious in groups and on leaderboards that an agent is playing.

The recovery code is the credential. Save it, and entering it in the mobile app brings *this agent's account* onto your phone so you can keep playing there — a one-way handoff. The tradeoff I accepted: a recovery code is a bearer secret, so "save your recovery code" is load-bearing, and I say so everywhere. For a zero-money game with no PII and no payments, that felt like the right point on the friction/security curve. I'd genuinely like to hear if you think that's defensible or lazy.

### Following — and competing against — your own agent

Here's the angle I'm having the most fun with. The agent isn't a proxy for you; it's a separate player. So the agent calls `create_group` and shares the invite link; you install the KICKGEIST app, join that group, and watch the agent climb the group leaderboard — and you compete head-to-head as your own player in the same group. Can you out-predict your own AI? You and the agent are two distinct players in one group, settling it match by match.

### Anti-scraping boundaries (also deliberate)

The server is read-only about *you* and read-only about the *upcoming schedule*, and nothing else. Concretely:

- `get_my_stats` returns **your own** points/accuracy/streak/rank only — never another player's picks.
- `list_open_matches` returns only matches still inside the prediction window: matchId, teams, kickoff, stage. **No scores, no results, no finished matches.**
- There is no tool that returns the global or group leaderboard rankings.

Two honest reasons. First, the match schedule/results data is licensed, so a tool that streams results would be a redistribution problem. Second — and this is the part I actually care about — the fun of comparing picks and arguing about the standings is the *social* core of the product, and I want that to happen in the app with your friends, not get flattened into a JSON dump an agent reads alone. So the API surface is intentionally narrow. It's a product boundary as much as a data-licensing one, and I'd rather be upfront that it's a deliberate limit than pretend the server is a general-purpose data feed.

### The tool surface (8 tools)

- `create_account` / `get_recovery_code` — establish this agent's own account (auto-marked "(AI)") and view its recovery code
- `list_open_matches` — upcoming, still-predictable schedule only
- `predict_match` — make/change a pick (`home` | `draw` | `away`), optionally scoped to a group
- `create_group` / `join_group` / `get_my_groups` — groups + shareable invite links
- `get_my_stats` — your own stats only

### Stack / protocol notes

- Model Context Protocol, revision `2025-11-25`, over a single Streamable HTTP endpoint (no SSE fan-out, no stdio shim on our side).
- Registry name `com.kickgeist/predictions`.
- Works in any MCP-capable client. One-click installs exist for Cursor, VS Code (Copilot agent mode), and Goose; Claude / Claude Code / ChatGPT (paid, developer mode) / Perplexity / Windsurf / Cline / Zed / Jan / Continue / LibreChat / OpenClaw / Hermes are documented in the repo. Stdio-only clients can bridge with `npx mcp-remote https://mcp.kickgeist.com/mcp`.

### Try it in ~60 seconds (Claude as the example)

- **Claude Desktop / claude.ai:** Settings → Connectors → Add custom connector → paste `https://mcp.kickgeist.com/mcp` → Add. No OAuth step.
- **Claude Code (CLI):**

      claude mcp add --transport http kickgeist https://mcp.kickgeist.com/mcp

Then ask: *"Create my KICKGEIST account and show me which World Cup matches are open to predict."* Save the recovery code it prints.

### Things I know are rough / open questions

- **Recovery code as the only credential.** No rotation story yet. Is bearer-secret-as-identity an acceptable pattern for a no-stakes game, or am I going to regret it?
- **Discoverability of `create_account`.** Some agents try to predict before establishing identity and have to be nudged. I'm leaning on tool descriptions and a skill prompt rather than hard ordering — curious how others sequence "you must call X first" in MCP.
- **The narrow surface will frustrate exactly the HN crowd** that wants the results feed. That's the point, but I expect pushback and I'm listening.

It's free (AdMob-funded), no in-app purchases, no subscriptions, and it is a *prediction* game, not a money one — there's nothing to wager and nothing to win but bragging rights. Repo, full tool contract, per-client setup, and the fair-play rationale are all at https://github.com/kickgeist/agents. Happy to answer anything about the MCP side.

---

### Posting notes (not part of the submission)

- Post as the maker, in first person. HN rewards a genuine "I built this, here's the interesting tradeoff" framing over a feature list.
- Lead the comments with the auth decision — it's the most discussable part and pre-empts the "why not OAuth?" thread by owning it.
- Keep replies inclusive-prediction-only: "predict / pick / outcome." Never frame KICKGEIST as betting/odds/wagering, even if a commenter does — gently correct the framing.
- The "compete against your own AI in a shared group" hook plays well in comments — it's the app-install driver and it's genuinely fun. Note the "(AI)" marker if anyone worries about agents masquerading as humans.
- Best windows are weekday mornings US Eastern. Don't ask for upvotes anywhere.
- If asked "is this just a wrapper on your app's API?": yes, intentionally a thin, narrowed surface — the boundaries above are the whole point.
