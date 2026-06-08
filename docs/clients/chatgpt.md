# Connect KICKGEIST to ChatGPT

Play **KICKGEIST**, the group-first World Cup 2026 prediction game, straight from ChatGPT. No login, no OAuth, no money — you add one URL, create an account, and call your shot on a real match.

> **⚠️ Read this first — two things to know up front:**
>
> 1. **Paid plan required.** Custom connectors are available on **ChatGPT Plus, Pro, Business, Enterprise, and Edu** — **not** on the Free plan. If you're on Free, use the [Claude quickstart](../quickstart.md) or another client from the [README](../../README.md) instead.
> 2. **Developer mode is required, and OpenAI labels it "powerful but dangerous."** It unlocks full MCP connectors and is intended for people who understand how to configure and test connectors safely. KICKGEIST is **authless and read-mostly** (it only ever exposes your own data and the upcoming match schedule), but you're still turning on a developer feature — only enable it if you're comfortable doing so.

---

## Prerequisites

- A **paid ChatGPT plan**: Plus, Pro, Business, Enterprise, or Edu. (Free plans cannot add custom connectors.)
- Access to **chatgpt.com on the web** (Developer mode is configured in the web app).
- That's the whole list. KICKGEIST is **authless** — there is no API key, no OAuth, no sign-in.

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

4. For authentication, select **No authentication** — KICKGEIST is authless, so there's nothing to sign in to.
5. Save / create the connector.

### 3. Enable it in your chat

1. Start a new chat (or open the **Developer Mode** tool in the composer).
2. Make sure the **KICKGEIST** connector is toggled **on** for the conversation.

You're connected. Identity comes from the `create_account` tool in the next section — there's no separate login step.

---

## Config reference

KICKGEIST uses a single Streamable HTTP endpoint and no authentication:

| Setting | Value |
|---|---|
| Server URL | `https://mcp.kickgeist.com/mcp` |
| Transport | Streamable HTTP |
| Authentication | None (authless) |

There's no JSON config file or deep link to paste for ChatGPT — everything is entered through the **Apps & Connectors** UI described above.

---

## Verify it works

Talk to ChatGPT in plain language — it'll pick the right KICKGEIST tools.

1. **Create your account:**

   > **You:** Create my KICKGEIST account.

   ChatGPT calls the `create_account` tool and replies with a welcome message, a link to the app, and your **recovery code** (something like `OCEAN-TIGER-42-VOLT`).

   > ### ⚠️ Save your recovery code
   > The recovery code **is** your account — there's no email or password behind it. Copy it somewhere safe right now. With it you can:
   > - Open the **same account in the KICKGEIST mobile app** ([iOS](https://kickgeist.com) · [Android](https://kickgeist.com)) and see your picks on the big screen.
   > - Reconnect from another agent or device later with `link_account`.
   >
   > Lost the code with no linked app? The account can't be recovered. Save it now. (Ask "show my recovery code" any time to have ChatGPT call `get_recovery_code`.)

   **Already play on your phone?** Don't make a new account — link the one you have:

   > **You:** Link my KICKGEIST account, my recovery code is OCEAN-TIGER-42-VOLT.

   ChatGPT calls `link_account` and now plays on your existing account.

2. **List what's open to predict:**

   > **You:** What World Cup matches can I predict right now?

   ChatGPT calls `list_open_matches` and shows the upcoming, still-open schedule — teams, kickoff time, and stage. (No scores or results here; those stay in the app on purpose.)

If both calls return real data, you're fully set up. Make a pick with **"Predict Brazil to win against Croatia"** (`predict_match`), spin up a group with **"Create a group called Office World Cup Crew"** (`create_group`), join a friend's crew with **"Join the group with code AB12CD"** (`join_group`), see everything you belong to with **"What groups am I in?"** (`get_my_groups`), and check your scorecard with **"How am I doing?"** (`get_my_stats`).

The full tool list and example phrasings live in the [Quickstart](../quickstart.md) and [Tools reference](../tools.md).

---

## Troubleshooting

**I don't see "Create connector" or "Advanced settings."**
You're most likely on the **Free plan** (custom connectors are paid-only) or not on the web app. Open **chatgpt.com** on a Plus/Pro/Business/Enterprise/Edu account, then **Settings → Apps & Connectors → Advanced settings → Developer mode**.

**The connector is created but ChatGPT never calls a KICKGEIST tool.**
Confirm the **KICKGEIST** connector is toggled **on** for the current conversation (via the Developer Mode tool in the composer). Then ask plainly, e.g. *"Use KICKGEIST to create my account."*

**Connection or "couldn't reach the server" errors.**
Double-check the URL is exactly `https://mcp.kickgeist.com/mcp` (note the trailing `/mcp`) and that authentication is set to **No authentication** — KICKGEIST is authless, so any credential prompt means the wrong auth mode was selected.

**It says my account isn't found.**
Each new session starts without an identity. Run `create_account` once (or `link_account` with your recovery code) before predicting, joining groups, or checking stats.

**Where's the leaderboard and my friends' picks?**
That's by design — and it's a feature. The connector returns **only your own** account, stats, groups, and the upcoming open-match schedule. It never returns match results, other players' picks, or the global/group rankings. That protects our licensed match data and keeps the head-to-head fun — the reveals, the bragging rights — in the app with your friends. Open KICKGEIST on your phone (same account, via your recovery code) for the full leaderboard.

---

## Official docs

- ChatGPT Developer mode for MCP connectors: <https://developers.openai.com/api/docs/guides/developer-mode>
- KICKGEIST [Quickstart](../quickstart.md) · [Tools reference](../tools.md) · [Fair play](../fair-play.md)

---

**Build for the fans. Save your recovery code. Then go call the World Cup.** 🌍⚽
