# Connect KICKGEIST to Perplexity

Play **KICKGEIST** — the group-first, zero-money World Cup 2026 prediction game — straight from Perplexity, using its **custom remote connector** support. Add one URL, create an account, and call your shot on a real World Cup match.

> **Heads up — paid plans only.** Custom connectors in Perplexity are a **Pro / Max / Enterprise** feature. That's Perplexity's pricing, not ours — KICKGEIST is free, AdMob-funded, and never charges you a cent. If you're on the free Perplexity tier, you'll connect from a different client instead (see [Other ways in](#other-ways-in)).
>
> **One more thing about the UI.** Perplexity's connector flow is built mostly around connectors that need a sign-in (OAuth). KICKGEIST is **authless** — there's no login, no OAuth, no API key. When the connector form offers an authentication option, choose **No authentication / Open** (or simply leave the auth fields blank). You add the URL and you're in.

---

## Prerequisites

- A **Perplexity Pro, Max, or Enterprise** plan (custom connectors aren't available on the free tier).
- Access to **Perplexity Settings → Connectors** on web or desktop.
- The KICKGEIST endpoint (the only thing you need to paste):

  ```text
  https://mcp.kickgeist.com/mcp
  ```

---

## Steps

1. Open Perplexity and go to **Settings → Connectors**.
2. Click **+ Custom connector**.
3. Choose the **Remote** connector type (this is a remote MCP server, not a local one).
4. Give it a name — for example, **KICKGEIST**.
5. In the URL field, paste the endpoint:

   ```text
   https://mcp.kickgeist.com/mcp
   ```

6. For the authentication option, pick **No authentication / Open** (or leave the auth fields empty). KICKGEIST is authless — there's no OAuth or API key to enter. Your KICKGEIST identity is created later, by a tool call, not by a login.
7. Save / add the connector.

That's the whole setup. There is **no sign-in step** and **no "Add to Claude"-style deep link** for Perplexity — you add the URL and the nine KICKGEIST tools become available to your assistant.

---

## Verify it works

Make sure the KICKGEIST connector is **enabled** for your thread (Perplexity lets you toggle connectors per conversation), then just talk to Perplexity:

1. **Create your account:**

   > **You:** Create my KICKGEIST account.

   Perplexity calls the `create_account` tool and replies with a welcome message, an app link, and — most importantly — your **recovery code** (something like `OCEAN-TIGER-42-VOLT`).

   > ### ⚠️ Save your recovery code
   > The recovery code **is** your account — there's no email or password behind it. Copy it somewhere safe right now. With it you can open the **same account in the KICKGEIST mobile app** ([iOS](https://kickgeist.com) · [Android](https://kickgeist.com)) and reconnect from another agent later via `link_account`. Lost the code with no linked app? The account can't be recovered.

2. **See what's open to predict:**

   > **You:** What World Cup matches can I predict right now?

   Perplexity calls `list_open_matches` and shows the upcoming, still-open schedule — teams, kickoff time, and stage. (No scores or results live here; those stay in the app on purpose.)

If both calls return real data, you're connected and ready to play. From here you can make a prediction (`predict_match` → `home`, `draw`, or `away`), spin up a group with a shareable invite link (`create_group`), join a friend's group (`join_group`), and check your own points, accuracy, and streaks (`get_my_stats`).

**Already play KICKGEIST on your phone?** Don't create a new account — link the one you have:

> **You:** Link my KICKGEIST account, my recovery code is OCEAN-TIGER-42-VOLT.

Perplexity calls `link_account` and your assistant now plays on your existing account.

---

## Troubleshooting

- **"+ Custom connector" isn't there.** Custom connectors require a **Pro / Max / Enterprise** plan. On the free tier the option is hidden — connect from a different client instead (see below).
- **The form is asking me to sign in / for OAuth or an API key.** KICKGEIST doesn't use any of those. Select **No authentication / Open**, or leave the auth fields blank. The connection itself needs no credentials; your account is created by the `create_account` tool after you connect.
- **The assistant says it has no KICKGEIST tools.** Confirm the connector is toggled **on** for the current thread — Perplexity enables connectors per conversation. Start a fresh thread with the connector enabled if needed.
- **The URL was rejected.** Use the endpoint exactly, including the trailing `/mcp`:

  ```text
  https://mcp.kickgeist.com/mcp
  ```

- **`list_open_matches` returns nothing.** That's expected when there are no matches currently inside the prediction window (before kickoff). It lists only upcoming, still-open matches — never finished ones — so the list is empty between windows.
- **I lost my recovery code.** Ask the assistant to "show my recovery code" and it calls `get_recovery_code` for the currently linked account. If no account is linked and you never saved the code, the account can't be recovered — save the new one right away.

---

## Other ways in

On the free Perplexity tier, or just prefer another client? The KICKGEIST endpoint is the same everywhere:

```text
https://mcp.kickgeist.com/mcp
```

- **Claude (claude.ai / Desktop):** Settings → Connectors → **Add custom connector** → paste the URL. Works on Free (your one connector slot) and up.
- **Claude Code (CLI):** `claude mcp add --transport http kickgeist https://mcp.kickgeist.com/mcp`

See the repo README for the full per-client guide.

---

## Official docs

- Perplexity — Adding custom remote connectors: <https://www.perplexity.ai/help-center/en/articles/13915507-adding-custom-remote-connectors>

---

**Build for the fans. Save your recovery code. Then go call the World Cup.** 🌍⚽
