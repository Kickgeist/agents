# Play KICKGEIST in Claude

Connect Claude to **KICKGEIST — World Cup Predictions** and play the group-first, zero-money World Cup 2026 prediction game straight from your chat. Pick match outcomes, spin up a group for your friends, and track your own stats — all through your agent.

KICKGEIST is **free**, AdMob-funded, with no in-app purchases and no subscriptions. You predict the **outcome** of each match — home win, draw, or away win — and climb the leaderboard with your friends.

This guide covers two ways to connect:

- **[Claude (web + Desktop)](#claude-web--desktop)** — the custom connector for claude.ai and Claude Desktop
- **[Claude Code (CLI)](#claude-code-cli)** — one command in your terminal

The KICKGEIST server is **authless** — there is no OAuth and no login to set up. You just add the URL, then ask Claude to create your account. Your KICKGEIST identity comes from a **recovery code** the server hands you, which also unlocks the same account in the mobile app.

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

> No OAuth required. KICKGEIST is authless — leave the OAuth fields blank.

### Steps (Pro and Max)

1. Open **Settings → Connectors** (also shown as **Customize → Connectors**).
2. Click **+**, then **Add custom connector**.
3. Paste the server URL:
   ```
   https://mcp.kickgeist.com/mcp
   ```
4. Leave **Advanced settings** (OAuth Client ID / Secret) **empty** — KICKGEIST needs no OAuth.
5. Click **Add**.
6. In any chat, click the **+** button, open **Connectors**, and toggle **KICKGEIST** on for that conversation.

### Steps (Team and Enterprise)

1. **Owner:** go to **Organization settings → Connectors** → **Add** → choose **Custom**, then **Web**.
2. Paste `https://mcp.kickgeist.com/mcp`, leave OAuth blank, and click **Add**.
3. **Members:** open **Settings → Connectors**, find **KICKGEIST**, and click **Connect**.
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

   You should see **kickgeist** listed as connected.

> Tip: in `.mcp.json` or `claude mcp add-json`, the transport `type` also accepts `streamable-http` as an alias for `http` — both work for KICKGEIST.

Official docs: <https://code.claude.com/docs/en/mcp>

---

## Verify it works

Once connected (either path), kick off a match by asking Claude in plain language:

1. **Create your account:**

   > "Use KICKGEIST to create my account."

   Claude calls `create_account` and returns a **recovery code** plus a welcome message and app link.

   **Save your recovery code.** It's the key to your account — enter it in the KICKGEIST mobile app (or use `link_account` from another agent) to reach the very same account. You can re-display it anytime by asking Claude to run `get_recovery_code`.

2. **See what's open to predict:**

   > "List the open World Cup matches."

   Claude calls `list_open_matches` and shows upcoming fixtures you can still pick — match ID, home vs. away, kickoff time, and stage.

3. **Make a prediction:**

   > "Predict the home team to win in [that match]."

   Claude calls `predict_match` with your chosen outcome (`home`, `draw`, or `away`).

That's the loop. From there you can `create_group` to start a friends group (you'll get a shareable invite link), `join_group` with a code a friend sent you, `get_my_groups` to see your groups, and `get_my_stats` for your own points, accuracy, streak, and rank.

---

## What KICKGEIST shows you (and what stays in the app)

By design, the server exposes only **your own data** and the **upcoming open-match schedule**. It deliberately does **not** return match results, finished scores, other players' picks, or the global/group leaderboard rankings.

That's a feature, not a gap: it protects our licensed match data and keeps the social fun — comparing picks and climbing the full leaderboard — right where it belongs, in the **KICKGEIST mobile app**. Save your recovery code, drop it into the app, and your agent picks and your phone picks are one and the same account.

---

## Troubleshooting

- **"Add custom connector" is missing (web/Desktop).** On Free, you can have only **one** connector — remove an existing one, then add KICKGEIST. Confirm you're on a plan that supports connectors (Free/Pro/Max/Team/Enterprise).
- **Connector added but tools don't run in a chat.** Enable it per conversation: click **+** in the chat → **Connectors** → toggle **KICKGEIST** on.
- **Team/Enterprise members can't see it.** An **Owner** must add it at the organization level first; members then **Connect** it from their own Connectors settings.
- **`/mcp` shows kickgeist as failed or not connected (Claude Code).** Re-check the exact URL `https://mcp.kickgeist.com/mcp`, confirm your network can reach it, then remove and re-add: `claude mcp remove kickgeist` followed by the `claude mcp add` command above.
- **Don't add OAuth credentials.** KICKGEIST is authless — leaving the OAuth Client ID/Secret blank is correct. If a connector won't add, make sure those advanced fields are empty.
- **Lost your recovery code?** Ask Claude to run `get_recovery_code` while the account is linked. Keep it somewhere safe — it's the only way back into the same account.
- **Want to use the account you already play on your phone?** Ask Claude to `link_account` with that account's recovery code (find it in the app under account / recovery).

---

## Official documentation

- Claude custom connectors (web + Desktop): <https://support.claude.com/en/articles/11175166-getting-started-with-custom-connectors-using-remote-mcp>
- Claude Code MCP: <https://code.claude.com/docs/en/mcp>
