# Play KICKGEIST in Continue

Connect [Continue](https://continue.dev) to **KICKGEIST — World Cup Predictions** and play the group-first, zero-money World Cup 2026 prediction game right from your editor. Pick match outcomes, spin up a group for your friends, and track your own stats — all through your agent.

KICKGEIST is **free**, AdMob-funded, with no in-app purchases and no subscriptions. You predict the **outcome** of each match — home win, draw, or away win — and climb the leaderboard with your friends.

The KICKGEIST server is **authless** — there is no OAuth and no login to set up. You just add the URL to your Continue config, then ask Continue (in agent mode) to create your account. Your KICKGEIST identity comes from a **recovery code** the server hands you, which also unlocks the same account in the mobile app.

**Endpoint (copy this):**

```
https://mcp.kickgeist.com/mcp
```

---

## Prerequisites

- **Continue installed** in VS Code or a JetBrains IDE (the extension is free and open-source).
- **Agent mode.** MCP tools in Continue only run in **agent mode** — pick "Agent" in the mode selector before you start playing. Chat and Edit modes won't call the KICKGEIST tools.
- Network access to `https://mcp.kickgeist.com/mcp`.

> No OAuth required. KICKGEIST is authless — there are no API keys, tokens, or secrets to add. Just the URL.

---

## Steps

1. Open (or create) your Continue config. The easiest path is the **workspace** config at `.continue/config.yaml` in your project root. You can also use your global config at `~/.continue/config.yaml`.

2. Add the KICKGEIST server under `mcpServers` with the **streamable HTTP** transport:

   ```yaml
   mcpServers:
     - name: KICKGEIST
       type: streamable-http
       url: https://mcp.kickgeist.com/mcp
   ```

   If you already have an `mcpServers:` list, just add the KICKGEIST entry alongside your existing servers — don't create a second `mcpServers:` key.

3. Save the file. Continue picks up config changes automatically; if the tools don't appear right away, reload your IDE window.

4. Switch the Continue sidebar to **Agent** mode (the mode dropdown above the input box). MCP tools are only available there.

> Prefer a standalone file? Continue also reads per-server YAML from `.continue/mcpServers/`. Drop the same block (the `- name / type / url` entry) into a file like `.continue/mcpServers/kickgeist.yaml` and it works the same way.

---

## Verify it works

In the Continue **agent** chat, kick off a match by asking in plain language:

1. **Create your account:**

   > "Use KICKGEIST to create my account."

   Continue calls `create_account` and returns a **recovery code** plus a welcome message and app link.

   **Save your recovery code.** It's the key to your account — enter it in the KICKGEIST mobile app (or use `link_account` from another agent) to reach the very same account. You can re-display it anytime by asking Continue to run `get_recovery_code`.

2. **See what's open to predict:**

   > "List the open World Cup matches."

   Continue calls `list_open_matches` and shows upcoming fixtures you can still pick — match ID, home vs. away, kickoff time, and stage.

3. **Make a prediction:**

   > "Predict the home team to win in [that match]."

   Continue calls `predict_match` with your chosen outcome (`home`, `draw`, or `away`).

That's the loop. From there you can `create_group` to start a friends group (you'll get a shareable invite link), `join_group` with a code a friend sent you, `get_my_groups` to see your groups, and `get_my_stats` for your own points, accuracy, streak, and rank.

---

## What KICKGEIST shows you (and what stays in the app)

By design, the server exposes only **your own data** and the **upcoming open-match schedule**. It deliberately does **not** return match results, finished scores, other players' picks, or the global/group leaderboard rankings.

That's a feature, not a gap: it protects our licensed match data and keeps the social fun — comparing picks and climbing the full leaderboard — right where it belongs, in the **KICKGEIST mobile app**. Save your recovery code, drop it into the app, and your agent picks and your phone picks are one and the same account.

---

## Troubleshooting

- **The KICKGEIST tools never get called.** Make sure you're in **Agent** mode — MCP tools don't run in Chat or Edit mode in Continue. Switch the mode dropdown to "Agent" and try again.
- **Server doesn't show up after editing the config.** Confirm the block sits under a single top-level `mcpServers:` key and that `type` is exactly `streamable-http`. Save the file, then reload your IDE window so Continue re-reads the config.
- **Two `mcpServers:` keys.** YAML only honors one. Merge KICKGEIST into your existing `mcpServers:` list as another `- name: …` entry rather than adding a second key.
- **Connection fails.** Re-check the exact URL `https://mcp.kickgeist.com/mcp` (note the trailing `/mcp`) and confirm your network can reach it.
- **Don't add credentials.** KICKGEIST is authless — there are no API keys, tokens, or OAuth fields to fill in. The three lines above are the whole config.
- **Lost your recovery code?** Ask Continue (in agent mode, while the account is linked) to run `get_recovery_code`. Keep it somewhere safe — it's the only way back into the same account.
- **Want to use the account you already play on your phone?** Ask Continue to `link_account` with that account's recovery code (find it in the app under account / recovery).

---

## Official documentation

- Continue MCP guide: <https://docs.continue.dev/customize/deep-dives/mcp>
