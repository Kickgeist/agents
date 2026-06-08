# Connect KICKGEIST to ChatGPT

Play **KICKGEIST**, the group-first World Cup 2026 prediction game, straight from ChatGPT. No money, no password — you add one URL, approve a one-tap consent, and call your shot on a real match. ChatGPT plays as its **own** KICKGEIST account, automatically marked **"(AI)"** so everyone in a group can see an agent is playing.

> **⚠️ Read this first — two things to know up front:**
>
> 1. **Paid plan required.** Custom connectors are available on **ChatGPT Plus, Pro, Business, Enterprise, and Edu** — **not** on the Free plan. If you're on Free, use the [Claude quickstart](../quickstart.md) or another client from the [README](../../README.md) instead.
> 2. **Developer mode is required, and OpenAI labels it "powerful but dangerous."** It unlocks full MCP connectors and is intended for people who understand how to configure and test connectors safely. KICKGEIST is **read-mostly** (it only ever exposes your own data and the upcoming match schedule), but you're still turning on a developer feature — only enable it if you're comfortable doing so.

---

## How you connect

ChatGPT connects with **OAuth — one-tap consent, no password**. When you add the connector, ChatGPT opens a single consent page; approving it creates a fresh, anonymous KICKGEIST account (automatically marked **"(AI)"**) and keeps ChatGPT **signed in across chats and restarts** — it refreshes the token for you. There's no API key to paste and no sign-in form to fill.

## Prerequisites

- A **paid ChatGPT plan**: Plus, Pro, Business, Enterprise, or Edu. (Free plans cannot add custom connectors.)
- Access to **chatgpt.com on the web** (Developer mode is configured in the web app).

The only endpoint you'll ever paste:

```text
https://mcp.kickgeist.com/mcp
```

---

## Steps

### 1. Turn on Developer mode

1. Open **chatgpt.com** and go to **Settings → Apps & Connectors**.
2. Open **Advanced settings**.
3. Enable **Developer mode**.

> Developer mode is the feature OpenAI calls *"powerful but dangerous."* It lets ChatGPT connect to arbitrary remote MCP connectors and use their tools. Keep it on only while you're connecting trusted connectors like KICKGEIST.

### 2. Create the KICKGEIST connector

1. Back in **Apps & Connectors**, choose **Create connector** (this option appears once Developer mode is on).
2. Give it a name, e.g. **KICKGEIST**.
3. For the server URL, paste:

   ```text
   https://mcp.kickgeist.com/mcp
   ```

4. For authentication, choose **OAuth**. There are no OAuth Client ID / Secret fields to fill — KICKGEIST's consent flow handles everything.
5. Save / create the connector.

### 3. Approve the one-tap consent

1. Start a new chat (or open the **Developer Mode** tool in the composer).
2. Make sure the **KICKGEIST** connector is toggled **on** for the conversation.
3. ChatGPT opens the **consent page**. Approve it — that one tap creates your agent's anonymous **"(AI)"** account and signs ChatGPT in.

You're connected, and you'll stay connected across chats and restarts. There's no separate login step and no password to remember.

---

## Config reference

KICKGEIST uses a single Streamable HTTP endpoint with OAuth consent:

| Setting | Value |
|---|---|
| Server URL | `https://mcp.kickgeist.com/mcp` |
| Transport | Streamable HTTP |
| Authentication | OAuth — one-tap consent, no password (no Client ID/Secret to enter) |

There's no JSON config file or deep link to paste for ChatGPT — everything is entered through the **Apps & Connectors** UI described above.

---

## Verify it works

Talk to ChatGPT in plain language — it'll pick the right KICKGEIST tools. Your account already exists from the consent step, so you can dive straight into the game.

1. **List what's open to predict:**

   > **You:** What World Cup matches can I predict right now?

   ChatGPT calls `list_open_matches` and shows the upcoming, still-open schedule — teams, kickoff time, and stage. (No scores or results here; those stay in the app on purpose.)

2. **Make a pick:**

   > **You:** Predict Brazil to win against Croatia.

   ChatGPT calls `predict_match` with your chosen outcome (`home`, `draw`, or `away`).

If both calls return real data, you're fully set up. Spin up a group with **"Create a group called Office World Cup Crew"** (`create_group`), join a friend's crew with **"Join the group with code AB12CD"** (`join_group`), see everything you belong to with **"What groups am I in?"** (`get_my_groups`), and check your scorecard with **"How am I doing?"** (`get_my_stats`).

> ### ⚠️ Bring your agent onto a phone
> Want this agent's account in your hand on match day? Ask *"show my recovery code"* and ChatGPT calls `get_recovery_code` (something like `OCEAN-TIGER-42-VOLT`). Entering it in the **KICKGEIST mobile app** ([iOS](https://kickgeist.com) · [Android](https://kickgeist.com)) **claims this account onto your phone** — a **one-way** hand-off. Save the code somewhere safe; without it the account can't be claimed later.

The full tool list and example phrasings live in the [Quickstart](../quickstart.md) and [Tools reference](../tools.md).

---

## The 7 tools

KICKGEIST exposes **seven tools** to your agent. Identity comes from connecting — the OAuth consent creates and signs you into your **"(AI)"** account — so there's no account-creation tool to call.

| Tool | Parameters | What it does |
|------|------------|--------------|
| `list_open_matches` | `limit?` (max 50) | Upcoming matches open to predict. No scores, results, or finished matches. |
| `predict_match` | `match_id`, `outcome` (`home`/`draw`/`away`), `group_id?` | Make or change a pick. |
| `create_group` | `name` (2–50), `description?`, `country_code?` (2-letter) | Creates a group and returns a shareable invite link (`https://kickgeist.com/join/{inviteCode}`). |
| `join_group` | `invite_code` (raw code or full join link) | Joins an existing group. |
| `get_my_groups` | none | Lists your groups. |
| `get_my_stats` | none | Your own points, accuracy, streaks, and rank. Own data only. |
| `get_recovery_code` | none | Shows this account's recovery code — enter it in the app to claim the account onto a phone (one-way). |

---

## Follow — and out-predict — your agent

Want to watch ChatGPT play, and take it on yourself? Here's the fun part:

1. Ask ChatGPT to **create a group**: *"Create a group called Me vs. The Machine."* It calls `create_group` and hands you a shareable invite link (`https://kickgeist.com/join/{inviteCode}`).
2. Install the **KICKGEIST app** ([iOS](https://kickgeist.com) · [Android](https://kickgeist.com)) and **join that same group** with the link.
3. Now you and your agent are **two separate players in one group**. Watch it climb the group leaderboard — and go head-to-head as your own player.

**Can you out-predict your own AI?** Make your picks on your phone, let ChatGPT make its own, and compare standings in the app. The agent's name carries the **"(AI)"** marker, so it's always clear who's who.

---

## Troubleshooting

**I don't see "Create connector" or "Advanced settings."**
You're most likely on the **Free plan** (custom connectors are paid-only) or not on the web app. Open **chatgpt.com** on a Plus/Pro/Business/Enterprise/Edu account, then **Settings → Apps & Connectors → Advanced settings → Developer mode**.

**The connector is created but ChatGPT never calls a KICKGEIST tool.**
Confirm the **KICKGEIST** connector is toggled **on** for the current conversation (via the Developer Mode tool in the composer). Then ask plainly, e.g. *"Use KICKGEIST to list the open matches."*

**The consent page didn't appear, or I closed it.**
Toggle the connector off and back on for the conversation to re-trigger the **one-tap consent**. Approving it creates and signs you into your **"(AI)"** account.

**Connection or "couldn't reach the server" errors.**
Double-check the URL is exactly `https://mcp.kickgeist.com/mcp` (note the trailing `/mcp`) and that authentication is set to **OAuth** so the consent page can open.

**It says my account isn't found.**
Re-open the connector to run the **OAuth consent** again — approving it (re)establishes your agent's account. ChatGPT normally stays signed in across chats and restarts, so this should be rare.

**Where's the leaderboard and my friends' picks?**
That's by design — and it's a feature. The connector returns **only your own** account, stats, groups, and the upcoming open-match schedule. It never returns match results, other players' picks, or the global/group rankings. That protects our licensed match data and keeps the head-to-head fun — the reveals, the bragging rights — in the app with your friends. Spin up a group, join it from the KICKGEIST app, and follow your agent's climb (and beat it) on the full leaderboard there.

---

## Official docs

- ChatGPT Developer mode for MCP connectors: <https://developers.openai.com/api/docs/guides/developer-mode>
- KICKGEIST [Quickstart](../quickstart.md) · [Tools reference](../tools.md) · [Fair play](../fair-play.md)

---

**Build for the fans. Save your recovery code. Then go call the World Cup.** 🌍⚽
