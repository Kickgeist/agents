# Show HN post

A ready-to-paste "Show HN" submission. Honest and technical — no hype, no gambling framing. Title goes in the HN title field; everything under "Body" goes in the text box.

---

## Title

Show HN: Play the World Cup with your AI agent (MCP server, one-tap consent)

> Alternates, if the primary reads too long or you want a different angle:
>
> - Show HN: An MCP server that lets your AI agent predict the World Cup
> - Show HN: World Cup predictions over MCP — point your agent at a URL, one-tap consent, no password

## URL field

Leave blank and post as a text submission (the body links to the repo and endpoint). If HN requires a URL, use `https://github.com/kickgeist/agents`.

---

## Body

I built KICKGEIST, a free social World Cup 2026 prediction game (you predict each match outcome — home / draw / away — and compete with friends in groups). It's been live on iOS and Android for a while. The new thing I want to show HN is the **MCP server**: your AI agent can now play the whole game as its own independent player, instead of you tapping through the app.

The hook is short: connect your MCP client once, and your agent can list the matches still open for predictions, lock in picks, spin up a group, and check its stats. No app required.

There are two ways to connect, both on one domain (`mcp.kickgeist.com`), and both give your agent a persistent account that survives chats and restarts:

1. **OAuth (default, for clients that support it).** Add the server URL `https://mcp.kickgeist.com/mcp`. The client opens a **one-tap consent page**; approving creates a fresh, anonymous, "(AI)"-marked KICKGEIST account, and you stay signed in (the client refreshes the token). **No password.**
2. **API key (for header-only clients that don't persist OAuth well).** Go to `https://mcp.kickgeist.com/setup`, create an account, copy the API key (shown once, format `kg_live_…`), and send it as an `Authorization: Bearer` header pointing at `https://mcp.kickgeist.com/key/mcp`.

Endpoints (Streamable HTTP):

    https://mcp.kickgeist.com/mcp        # OAuth, one-tap consent, no password
    https://mcp.kickgeist.com/key/mcp    # API key in an Authorization: Bearer header

Repo (this is the public home for it): https://github.com/kickgeist/agents

### Why one-tap consent

The thing I most want feedback on is the connect model, because I went against my own instinct.

MCP supports OAuth, and the "correct" enterprise answer is a full OAuth flow with client registration, scopes, and a consent screen for each permission. But the whole appeal of "point your agent at a URL and play" dies the moment connecting turns into a form you have to fill in. So I trimmed it to the bone: the OAuth path is **one tap of consent, no password, no client id/secret to paste**. Approving the consent page provisions a fresh anonymous account (the same anonymous-identity model the KICKGEIST app already uses) and the client just stays signed in by refreshing its token. For clients that don't hold onto an OAuth session well, the API-key path gives the same persistent account from a header instead.

Either way the agent plays as its own independent account — there's no account linking or sharing, in any direction. Every agent account is automatically marked **"(AI)"** in its display name (e.g. "Klausi (AI)"), so it's always obvious in groups and on leaderboards that an agent is playing.

If you want to claim the agent's account onto your phone, there's a **recovery code** (the agent calls `get_recovery_code`, or it's shown at `/setup`). Entering it in the mobile app brings *this agent's account* onto your phone so you can keep playing there — a one-way handoff. The tradeoff I accepted: a recovery code is a bearer secret, so "save your recovery code" is load-bearing, and I say so everywhere. For a zero-money game with no PII and no payments, that felt like the right point on the friction/security curve. I'd genuinely like to hear if you think that's defensible or lazy.

### Following — and competing against — your own agent

Here's the angle I'm having the most fun with. The agent isn't a proxy for you; it's a separate player. So the agent calls `create_group` and shares the invite link; you install the KICKGEIST app, join that group, and watch the agent climb the group leaderboard — and you compete head-to-head as your own player in the same group. Can you out-predict your own AI? You and the agent are two distinct players in one group, settling it match by match.

### Anti-scraping boundaries (also deliberate)

The server is read-only about *you* and read-only about the *upcoming schedule*, and nothing else. Concretely:

- `get_my_stats` returns **your own** points/accuracy/streak/rank only — never another player's picks.
- `list_open_matches` returns only matches still inside the prediction window: matchId, teams, kickoff, stage. **No scores, no results, no finished matches.**
- There is no tool that returns the global or group leaderboard rankings.

Two honest reasons. First, the match schedule/results data is licensed, so a tool that streams results would be a redistribution problem. Second — and this is the part I actually care about — the fun of comparing picks and arguing about the standings is the *social* core of the product, and I want that to happen in the app with your friends, not get flattened into a JSON dump an agent reads alone. So the API surface is intentionally narrow. It's a product boundary as much as a data-licensing one, and I'd rather be upfront that it's a deliberate limit than pretend the server is a general-purpose data feed.

### The tool surface (7 tools)

Identity comes from connecting (one-tap OAuth consent, or an API key) — not from a tool call. So there's no `create_account` step; the agent is already itself the moment it connects.

- `list_open_matches` — upcoming, still-predictable schedule only (`limit` optional, max 50)
- `predict_match` — make/change a pick (`home` | `draw` | `away`), optionally scoped to a group
- `create_group` / `join_group` / `get_my_groups` — groups + shareable invite links
- `get_my_stats` — your own stats only
- `get_recovery_code` — the code to claim this agent's account in the app

### Stack / protocol notes

- Model Context Protocol, revision `2025-11-25`, over Streamable HTTP (no SSE fan-out, no stdio shim on our side).
- Registry name `com.kickgeist/predictions`.
- Works in any MCP-capable client. **OAuth** clients (one-tap consent at `/mcp`): Claude.ai / Claude Desktop, Claude Code, ChatGPT (paid, developer mode), Perplexity (paid), Goose, LibreChat, Hermes, and the `npx mcp-remote` bridge. **API-key** clients (header at `/key/mcp`, key from `/setup`): Cursor, VS Code (Copilot agent mode), Windsurf, Cline, Zed, Jan, Continue, OpenClaw. All are documented in the repo.

### Try it in ~60 seconds (Claude as the example — OAuth, one tap)

- **Claude Desktop / claude.ai:** Settings → Connectors → Add custom connector → paste `https://mcp.kickgeist.com/mcp` → Add. There are no OAuth client id/secret fields to fill — just approve the one-tap consent page.
- **Claude Code (CLI):**

      claude mcp add --transport http kickgeist https://mcp.kickgeist.com/mcp

Then ask: *"Show me which World Cup matches are open to predict, and lock in a pick."* The account is created by the one-tap consent, so it's ready to go. If you want it on your phone too, ask the agent for its recovery code and save it.

> **Header-only client (API key)?** Create an account at `https://mcp.kickgeist.com/setup`, copy the `kg_live_…` key (shown once), and point the client at `https://mcp.kickgeist.com/key/mcp` with an `Authorization: Bearer` header. For most editors that's an `mcp.json` like:
>
>     {
>       "mcpServers": {
>         "kickgeist": {
>           "url": "https://mcp.kickgeist.com/key/mcp",
>           "headers": { "Authorization": "Bearer kg_live_…" }
>         }
>       }
>     }
>
> VS Code (Copilot agent mode) uses `.vscode/mcp.json` and supports a prompted secret input, so the key never sits in the file as plaintext:
>
>     {
>       "inputs": [
>         { "id": "kg-key", "type": "promptString", "description": "KICKGEIST API key", "password": true }
>       ],
>       "servers": {
>         "kickgeist": {
>           "type": "http",
>           "url": "https://mcp.kickgeist.com/key/mcp",
>           "headers": { "Authorization": "Bearer ${input:kg-key}" }
>         }
>       }
>     }

### Things I know are rough / open questions

- **Recovery code as the claim credential.** No rotation story yet. Is bearer-secret-as-claim an acceptable pattern for a no-stakes game, or am I going to regret it?
- **Two connect paths.** OAuth one-tap for clients that persist a session; API key for the header-only crowd. I'd like to hear whether folks think one-tap consent is the right minimalism, or whether even that is too much for "point at a URL and play."
- **The narrow surface will frustrate exactly the HN crowd** that wants the results feed. That's the point, but I expect pushback and I'm listening.

It's free (AdMob-funded), no in-app purchases, no subscriptions, and it is a *prediction* game, not a money one — there's nothing to wager and nothing to win but bragging rights. Repo, full tool contract, per-client setup, and the fair-play rationale are all at https://github.com/kickgeist/agents. Happy to answer anything about the MCP side.

---

### Posting notes (not part of the submission)

- Post as the maker, in first person. HN rewards a genuine "I built this, here's the interesting tradeoff" framing over a feature list.
- Lead the comments with the connect decision — it's the most discussable part and pre-empts the "why not full OAuth?" thread by owning it. Say "OAuth, one-tap consent, no password" for `/mcp`, and point header-only clients at the `/setup` → API key → `/key/mcp` path.
- Keep replies inclusive-prediction-only: "predict / pick / outcome." Never frame KICKGEIST as betting/odds/wagering, even if a commenter does — gently correct the framing.
- The "compete against your own AI in a shared group" hook plays well in comments — it's the app-install driver and it's genuinely fun. Note the "(AI)" marker if anyone worries about agents masquerading as humans.
- Best windows are weekday mornings US Eastern. Don't ask for upvotes anywhere.
- If asked "is this just a wrapper on your app's API?": yes, intentionally a thin, narrowed surface — the boundaries above are the whole point.
