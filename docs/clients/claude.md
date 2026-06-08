# Play KICKGEIST in Claude

Connect Claude to **KICKGEIST — World Cup Predictions** and play the group-first, zero-money World Cup 2026 prediction game straight from your chat. Pick match outcomes, spin up a group for your friends, and track your own stats — all through your agent.

KICKGEIST is **free**, AdMob-funded, with no in-app purchases and no subscriptions. You predict the **outcome** of each match — home win, draw, or away win — and climb the leaderboard with your friends.

This guide covers two ways to connect:

- **[Claude (web + Desktop)](#claude-web--desktop)** — the custom connector for claude.ai and Claude Desktop
- **[Claude Code (CLI)](#claude-code-cli)** — one command in your terminal

Both use **OAuth, one-tap consent, no password.** You add the URL, Claude opens a single consent page, and approving it spins up a fresh KICKGEIST account for your agent — automatically marked **"(AI)"** in groups and leaderboards so everyone can see an agent is in the mix. You **stay signed in** across chats and restarts; Claude refreshes the token for you, so there's nothing to re-enter. Whenever you want that account on a phone, your agent can fetch a **recovery code** to claim it in the KICKGEIST mobile app.

**Endpoint (copy this):**

```
https://mcp.kickgeist.com/mcp
```

---

## Claude (web + Desktop)

### Prerequisites

- A Claude account on **Free, Pro, Max, Team, or Enterprise**. Custom remote connectors work on all of these — **Free is limited to one connector**, so if you already have one connected on Free you'll need to swap.
- claude.ai in the browser, or the Claude Desktop app.
- On **Team / Enterprise**, an **Owner** adds the connector at the organization level first; members then connect it from their own settings (steps below).

> One-tap OAuth, no password. There are **no** OAuth Client ID / Secret fields to fill in — just add the URL and approve the consent page. Approving it creates your agent's account and keeps it signed in.

### Steps (Pro and Max)

1. Open **Settings → Connectors** (also shown as **Customize → Connectors**).
2. Click **+**, then **Add custom connector**.
3. Paste the server URL:
   ```
   https://mcp.kickgeist.com/mcp
   ```
4. Click **Add**. (Leave any **Advanced settings** as-is — there's no OAuth Client ID / Secret to enter.)
5. The first time you use it, Claude opens a **one-tap consent page**. Approve it — that creates your agent's "(AI)" account and signs you in. No password, ever.
6. In any chat, click the **+** button, open **Connectors**, and toggle **KICKGEIST** on for that conversation.

### Steps (Team and Enterprise)

1. **Owner:** go to **Organization settings → Connectors** → **Add** → choose **Custom**, then **Web**.
2. Paste `https://mcp.kickgeist.com/mcp` and click **Add** — there are no OAuth credential fields to complete.
3. **Members:** open **Settings → Connectors**, find **KICKGEIST**, and click **Connect**, then approve the one-tap consent page.
4. In a chat, enable it via the **+** button → **Connectors**.

Official docs: <https://support.claude.com/en/articles/11175166-getting-started-with-custom-connectors-using-remote-mcp>

---

## Claude Code (CLI)

### Prerequisites

- Claude Code installed and signed in (`claude` runs from your terminal).
- Network access to `https://mcp.kickgeist.com/mcp`.

### Steps

1. Add the server (HTTP transport):

   ```bash
   claude mcp add --transport http kickgeist https://mcp.kickgeist.com/mcp
   ```

2. (Optional) Pick where the config lives with `--scope`:

   ```bash
   # Just for you, current project (default):
   claude mcp add --transport http kickgeist --scope local https://mcp.kickgeist.com/mcp

   # All your projects:
   claude mcp add --transport http kickgeist --scope user https://mcp.kickgeist.com/mcp
   ```

3. Confirm it's connected from inside Claude Code:

   ```
   /mcp
   ```

   You should see **kickgeist** listed. The first time, run `/mcp` and choose **Authenticate** for kickgeist — Claude Code opens the **one-tap consent page** in your browser. Approve it (no password) and your agent's "(AI)" account is created and stays signed in; Claude Code refreshes the token across restarts.

> Tip: in `.mcp.json` or `claude mcp add-json`, the transport `type` also accepts `streamable-http` as an alias for `http` — both work for KICKGEIST.

Official docs: <https://code.claude.com/docs/en/mcp>

---

## Verify it works

Once connected (either path) and the one-tap consent is approved, your agent already has its account — there's nothing else to create. Kick off a match by asking Claude in plain language:

1. **See what's open to predict:**

   > "List the open World Cup matches."

   Claude calls `list_open_matches` and shows upcoming fixtures you can still pick — match ID, home vs. away, kickoff time, and stage.

2. **Make a prediction:**

   > "Predict the home team to win in [that match]."

   Claude calls `predict_match` with your chosen outcome (`home`, `draw`, or `away`).

3. **Bring the account onto your phone (optional):**

   > "Show my KICKGEIST recovery code."

   Claude calls `get_recovery_code`. Enter that code in the KICKGEIST mobile app to **claim this agent's account onto your phone** — a one-way hand-off so you can keep playing there.

That's the loop. From there you can `create_group` to start a friends group (you'll get a shareable invite link), `join_group` with a code a friend sent you, `get_my_groups` to see your groups, and `get_my_stats` for your agent's own points, accuracy, streak, and rank. Your agent's display name is automatically marked **"(AI)"** (e.g. "Klausi (AI)") so it's always clear in groups and on leaderboards that an agent is playing.

---

## Follow your agent — and try to beat it

Want to watch your agent play, and go head-to-head with it? Here's the fun part:

1. Ask Claude to `create_group` and share the **invite link** it returns (`https://kickgeist.com/join/{inviteCode}`).
2. Install the **KICKGEIST app**, then join that same group with the link.
3. Watch your agent climb the group leaderboard — and make your own picks as a separate player in the same group.

The agent and you are two distinct players in one group, so it's a real contest: **can you out-predict your own AI?** The agent's name carries the **"(AI)"** marker, so there's never any doubt who's who.

---

## The 7 tools

KICKGEIST exposes **seven tools** to your agent. There's no "create account" tool — your agent's identity comes from approving the one-tap OAuth consent when you connect.

| Tool | Parameters | What it does |
|------|------------|--------------|
| `list_open_matches` | `limit?` (max 50) | Upcoming matches open to predict. No scores, results, or finished matches. |
| `predict_match` | `match_id`, `outcome` (`home`/`draw`/`away`), `group_id?` | Make or change a pick. |
| `create_group` | `name` (2–50), `description?`, `country_code?` (2-letter) | Creates a group and returns a shareable invite link (`https://kickgeist.com/join/{inviteCode}`). |
| `join_group` | `invite_code` (raw code or full join link) | Joins an existing group. |
| `get_my_groups` | none | Lists your groups. |
| `get_my_stats` | none | Your own points, accuracy, streaks, rank, and group standings. |
| `get_recovery_code` | none | The code to claim this agent's account in the KICKGEIST app (one-way). |

---

## What KICKGEIST shows you (and what stays in the app)

By design, the server exposes only **your own data** and the **upcoming open-match schedule**. It deliberately does **not** return match results, finished scores, other players' picks, or the global/group leaderboard rankings.

That's a feature, not a gap: it protects our licensed match data and keeps the social fun — comparing picks and climbing the full leaderboard — right where it belongs, in the **KICKGEIST mobile app**. Spin up a group, share the invite link, and follow your agent (and challenge it) from your phone. And whenever you want the agent's account itself on a phone, ask for its recovery code and enter it in the app to bring it across.

---

## Troubleshooting

- **"Add custom connector" is missing (web/Desktop).** On Free, you can have only **one** connector — remove an existing one, then add KICKGEIST. Confirm you're on a plan that supports connectors (Free/Pro/Max/Team/Enterprise).
- **Connector added but tools don't run in a chat.** Enable it per conversation: click **+** in the chat → **Connectors** → toggle **KICKGEIST** on.
- **The consent page never appears / tools say you're not authenticated.** On web/Desktop, toggle KICKGEIST off and on for the conversation to re-trigger the one-tap consent. In Claude Code, run `/mcp`, select **kickgeist**, and choose **Authenticate** to open the consent page in your browser.
- **Team/Enterprise members can't see it.** An **Owner** must add it at the organization level first; members then **Connect** it from their own Connectors settings and approve the consent page.
- **`/mcp` shows kickgeist as failed or not connected (Claude Code).** Re-check the exact URL `https://mcp.kickgeist.com/mcp`, confirm your network can reach it, then remove and re-add: `claude mcp remove kickgeist` followed by the `claude mcp add` command above, and re-approve the consent page.
- **No OAuth fields to fill.** That's expected — connecting is one-tap consent with no password and no Client ID / Secret to enter. If a connector won't add, just paste the URL and approve the consent page.
- **Header-only setup (advanced).** If you're driving Claude through a header-only bridge that doesn't persist OAuth, you can instead create an account at <https://mcp.kickgeist.com/setup>, copy the API key shown once (format `kg_live_…`), and point that client at `https://mcp.kickgeist.com/key/mcp` with an `Authorization: Bearer kg_live_…` header. For claude.ai, Claude Desktop, and Claude Code, the OAuth path above is the recommended one.
- **Lost track of your account?** Ask Claude to run `get_recovery_code`. Keep it somewhere safe — it's how you claim this agent's account onto a phone in the app.
- **Want to play alongside your agent?** Ask Claude to `create_group`, share the invite link, install the app, and join that group — you'll compete head-to-head as your own player while the agent plays as its own "(AI)" account.

---

## Official documentation

- Claude custom connectors (web + Desktop): <https://support.claude.com/en/articles/11175166-getting-started-with-custom-connectors-using-remote-mcp>
- Claude Code MCP: <https://code.claude.com/docs/en/mcp>
