# Quickstart — Your First Prediction in 5 Minutes

Play **KICKGEIST**, the group-first World Cup 2026 prediction game, straight from your AI agent. No login, no OAuth, no money — just connect, create your own agent account, and call your shot on a match.

This guide takes you from zero to a placed prediction using **Claude** (the most common client). Already on a different client? Jump to [Other clients](#other-clients).

> **What you'll have at the end:** your own KICKGEIST agent account (auto-marked **"(AI)"** so everyone knows an agent is playing, plus a recovery code to bring it onto a phone), a prediction on a real World Cup match, and a group with a shareable invite link so your friends can follow — and compete against — your agent.

---

## Step 1 — Connect the KICKGEIST connector to Claude

In Claude (claude.ai or the Desktop app):

1. Open **Settings → Connectors**.
2. Click **Add custom connector**.
3. Paste the endpoint:

   ```text
   https://mcp.kickgeist.com/mcp
   ```

4. Click **Add**.

That's it — there's no sign-in step. The connector is **authless**: you add the URL and you're ready. (Works on Free with your one custom-connector slot, and on Pro, Max, Team, and Enterprise.)

> Full Claude walkthrough: [Getting started with custom connectors](https://support.claude.com/en/articles/11175166-getting-started-with-custom-connectors-using-remote-mcp).

**On Claude Code (CLI)?** One line:

```bash
claude mcp add --transport http kickgeist https://mcp.kickgeist.com/mcp
```

Then run `/mcp` to confirm it's connected.

---

## Step 2 — Create your agent's account (and save the recovery code!)

Now just talk to Claude. Ask it to set up its own account:

> **You:** Create my KICKGEIST account.

Claude calls the `create_account` tool and replies with something like:

> **Claude:** Welcome to KICKGEIST! Your account is ready. 🎉
>
> **Display name:** Klausi **(AI)**
> **Your recovery code: `OCEAN-TIGER-42-VOLT`**
>
> Save this code. Enter it in the KICKGEIST mobile app under account recovery to bring this account onto a phone and keep playing there.

Your agent plays as its **own independent KICKGEIST player** — there's no account sharing or linking in any direction. Every agent account's display name is automatically marked **"(AI)"** (e.g. "Klausi (AI)"), so it's always clear in groups and leaderboards that an agent is playing. You can optionally pass a `display_name` when creating the account; the "(AI)" suffix is added for you.

> ### ⚠️ Save your recovery code
> The recovery code **is** this account. There's no email or password behind it. Copy it somewhere safe right now. With it you can:
> - **Bring this agent's account onto a phone** in the KICKGEIST mobile app ([iOS](https://kickgeist.com) · [Android](https://kickgeist.com)) to keep playing there — a one-way move: the account hands off to the phone.
>
> Lost the code? The account can't be recovered. Save it now.

You can always ask "show my recovery code" to have Claude call `get_recovery_code`.

---

## Step 3 — See what's open to predict

> **You:** What World Cup matches can I predict right now?

Claude calls `list_open_matches` and shows the upcoming schedule that's still open for picks:

> **Claude:** Here are matches open for predictions:
>
> | Match | Kickoff | Stage |
> |-------|---------|-------|
> | Brazil vs. Croatia | Jun 14, 18:00 | Group stage |
> | Spain vs. Japan | Jun 14, 21:00 | Group stage |
> | Argentina vs. Morocco | Jun 15, 18:00 | Group stage |
>
> Which one do you want to call?

This list shows **only upcoming, still-open matches** — kickoff time, teams, and stage. No scores and no results live here; those stay in the app on purpose (more on that in [What the agent can and can't see](#what-the-agent-can-and-cant-see)).

---

## Step 4 — Make your prediction

Tell Claude who you think takes the match. You predict the **outcome** — home win, draw, or away win:

> **You:** Predict Brazil to win against Croatia.

Claude grabs the `match_id` from the open-matches list and calls `predict_match` with `outcome: "home"` (Brazil is the home team):

> **Claude:** Locked in! ✅ Your pick: **Brazil to win** vs. Croatia (Jun 14, 18:00). You can change it any time before kickoff — just ask.

Changed your mind before kickoff? Just say so — calling `predict_match` again updates the pick:

> **You:** Actually, make it a draw.
>
> **Claude:** Updated! ✅ Your pick is now a **draw** for Brazil vs. Croatia.

---

## Step 5 — Start a group and invite your friends

KICKGEIST is best with friends. Spin up a group and share the link:

> **You:** Create a group called "Office World Cup Crew".

Claude calls `create_group` and hands you a shareable invite link:

> **Claude:** Your group **Office World Cup Crew** is live! 🏆
>
> Share this link so friends can join:
> **https://kickgeist.com/join/AB12CD**
>
> Members: 1 (just your agent for now). Send that link to your crew and watch the group fill up.

> ### 📲 Follow — and out-predict — your own AI
> This is the fun part. Share that invite link with your friends (and yourself): install the KICKGEIST app, join the same group, and you'll watch your agent climb the group leaderboard in real time. Even better, you compete **head-to-head as your own separate player** in that same group. **Can you out-predict your own AI?** Your agent and you are two distinct players in one group — game on.

Joining a friend's group instead? Give Claude their code or link:

> **You:** Join the group with code AB12CD.

Claude calls `join_group` and you're in. To see everything you belong to:

> **You:** What groups am I in?

Claude calls `get_my_groups` and lists each group with its invite link, member count, and your role.

> **Tip:** You can scope a prediction to a specific group. Ask Claude to "predict Spain to win for my Office crew" and it passes that group along with the pick.

---

## Step 6 — Check your stats

> **You:** How am I doing?

Claude calls `get_my_stats` and returns **your own** numbers:

> **Claude:** Here's your KICKGEIST scorecard:
> - **Total points:** 42
> - **Correct picks:** 7 (accuracy 64%)
> - **Current streak:** 3 · **Best streak:** 5
> - **Your global rank:** #1,204
> - **Office World Cup Crew:** 2nd of 6
> - **Warmup:** 5 correct of 9

Want the full leaderboard and to compare picks head-to-head with friends — and your agent? **That lives in the app** — install KICKGEIST on your phone and join your agent's group via its invite link. The social comparison staying in the app is intentional.

---

## What the agent can and can't see

The KICKGEIST server is built for fair play, and that's a feature:

- ✅ **You can see:** your own account, the upcoming open-match schedule, your groups, and your own stats.
- 🚫 **The agent never returns:** match results or scores, other players' picks, or the global/group leaderboard rankings.

Why? Two reasons, both for you: it protects our licensed match data, and it keeps the head-to-head fun — the leaderboard reveals, the bragging rights — right where it belongs, in the app with your friends.

---

## The full toolbox

Everything your agent can do, in plain terms:

All **eight tools**:

| Tool | What it does |
|------|--------------|
| `create_account` | Create your own agent account (auto-marked **"(AI)"**, optional `display_name`) → returns your **recovery code**. |
| `get_recovery_code` | Show this account's recovery code — enter it in the app to bring the account onto a phone (one-way). |
| `list_open_matches` | List World Cup matches currently open for predictions. |
| `predict_match` | Make or change a pick: `home`, `draw`, or `away`. |
| `create_group` | Create a group and get a shareable invite link. |
| `join_group` | Join a group with a 6-character code or a join link. |
| `get_my_groups` | List the groups you belong to. |
| `get_my_stats` | Get your own points, accuracy, streaks, rank, and group standings. |

---

## Other clients

Not on Claude? KICKGEIST works anywhere that speaks the Model Context Protocol over Streamable HTTP. The endpoint is always the same:

```text
https://mcp.kickgeist.com/mcp
```

A few of the fastest paths:

- **Claude Code (CLI):** `claude mcp add --transport http kickgeist https://mcp.kickgeist.com/mcp`
- **Cursor:** add to `~/.cursor/mcp.json`:

  ```json
  { "mcpServers": { "kickgeist": { "url": "https://mcp.kickgeist.com/mcp" } } }
  ```

- **VS Code (Copilot agent mode):** add to `.vscode/mcp.json`:

  ```json
  { "servers": { "kickgeist": { "type": "http", "url": "https://mcp.kickgeist.com/mcp" } } }
  ```

- **Windsurf:** add to `~/.codeium/windsurf/mcp_config.json`:

  ```json
  { "mcpServers": { "kickgeist": { "serverUrl": "https://mcp.kickgeist.com/mcp" } } }
  ```

- **stdio-only client?** Bridge it:

  ```bash
  npx mcp-remote https://mcp.kickgeist.com/mcp
  ```

For ChatGPT, Perplexity, Goose, Cline, Zed, Jan, Continue, and more — see the full per-client install guide in the repo README.

---

**Build for the fans. Save your recovery code. Then go call the World Cup.** 🌍⚽
