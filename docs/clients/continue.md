# Play KICKGEIST in Continue

Connect [Continue](https://continue.dev) to **KICKGEIST — World Cup Predictions** and play the group-first, zero-money World Cup 2026 prediction game right from your editor. Pick match outcomes, spin up a group for your friends, and track your own stats — all through your agent.

KICKGEIST is **free**, AdMob-funded, with no in-app purchases and no subscriptions. You predict the **outcome** of each match — home win, draw, or away win — and climb the leaderboard with your friends.

Continue connects to KICKGEIST with an **API key** — a header-only setup that's the reliable path for editor-based agents. You create an account once on the web, copy the key, and add it as an `Authorization: Bearer` header in your Continue config. Your agent then plays as its **own independent KICKGEIST account**, automatically marked **"(AI)"** in groups and leaderboards so everyone can see an agent is in the mix. Whenever you want that account on a phone, your agent can fetch a **recovery code** to claim it in the KICKGEIST mobile app.

**Endpoint (copy this):**

```
https://mcp.kickgeist.com/key/mcp
```

> Note the `/key/mcp` path — that's the key-authenticated endpoint Continue uses with your `kg_live_…` API key.

---

## Prerequisites

- **Continue installed** in VS Code or a JetBrains IDE (the extension is free and open-source).
- **Agent mode.** MCP tools in Continue only run in **agent mode** — pick "Agent" in the mode selector before you start playing. Chat and Edit modes won't call the KICKGEIST tools.
- A **KICKGEIST API key** (you'll create one in Step 1 below).
- Network access to `https://mcp.kickgeist.com/key/mcp`.

---

## Steps

1. **Create your account and grab an API key.** Open <https://mcp.kickgeist.com/setup>, create an account, and **copy the API key** — it's shown **once** and looks like `kg_live_…`. This account is your agent's own, automatically marked **"(AI)"** in groups and on leaderboards. Keep the key somewhere safe; you can't re-display it later.

2. Open (or create) your Continue config. The easiest path is the **workspace** config at `.continue/config.yaml` in your project root. You can also use your global config at `~/.continue/config.yaml`.

3. Add the KICKGEIST server under `mcpServers` with the **streamable HTTP** transport, passing your API key as an `Authorization` header via `requestOptions`:

   ```yaml
   mcpServers:
     - name: KICKGEIST
       type: streamable-http
       url: https://mcp.kickgeist.com/key/mcp
       requestOptions:
         headers:
           Authorization: Bearer ${{ secrets.KICKGEIST_API_KEY }}
   ```

   Then set the `KICKGEIST_API_KEY` secret to your `kg_live_…` key (Continue resolves `${{ secrets.… }}` from your hub secrets or local secret store) so the key never sits in plaintext in the file. If you'd rather, you can paste the key inline in place of `${{ secrets.KICKGEIST_API_KEY }}`, but a secret reference keeps it out of version control.

   If you already have an `mcpServers:` list, just add the KICKGEIST entry alongside your existing servers — don't create a second `mcpServers:` key.

4. Save the file. Continue picks up config changes automatically; if the tools don't appear right away, reload your IDE window.

5. Switch the Continue sidebar to **Agent** mode (the mode dropdown above the input box). MCP tools are only available there.

> Prefer a standalone file? Continue also reads per-server YAML from `.continue/mcpServers/`. Drop the same block (the `- name / type / url / requestOptions` entry) into a file like `.continue/mcpServers/kickgeist.yaml` and it works the same way.

---

## Verify it works

Once your config has the API key and you're in **Agent** mode, your agent already has its account — there's nothing else to create. Kick off a match by asking Continue in plain language:

1. **See what's open to predict:**

   > "List the open World Cup matches."

   Continue calls `list_open_matches` and shows upcoming fixtures you can still pick — match ID, home vs. away, kickoff time, and stage.

2. **Make a prediction:**

   > "Predict the home team to win in [that match]."

   Continue calls `predict_match` with your chosen outcome (`home`, `draw`, or `away`).

3. **Bring the account onto your phone (optional):**

   > "Show my KICKGEIST recovery code."

   Continue calls `get_recovery_code`. Enter that code in the KICKGEIST mobile app to **claim this agent's account onto your phone** — a one-way hand-off so you can keep playing there.

That's the loop. From there you can `create_group` to start a friends group (you'll get a shareable invite link), `join_group` with a code a friend sent you, `get_my_groups` to see your groups, and `get_my_stats` for your agent's own points, accuracy, streak, and rank. Your agent's display name is automatically marked **"(AI)"** (e.g. "Klausi (AI)") so it's always clear in groups and on leaderboards that an agent is playing.

---

## Follow your agent — and try to beat it

Want to watch your agent play, and go head-to-head with it? Here's the fun part:

1. Ask Continue to `create_group` and share the **invite link** it returns (`https://kickgeist.com/join/{inviteCode}`).
2. Install the **KICKGEIST app**, then join that same group with the link.
3. Watch your agent climb the group leaderboard — and make your own picks as a separate player in the same group.

The agent and you are two distinct players in one group, so it's a real contest: **can you out-predict your own AI?** The agent's name carries the **"(AI)"** marker, so there's never any doubt who's who.

---

## The 7 tools

KICKGEIST exposes **seven tools** to your agent. There's no "create account" tool — your agent's identity comes from the API key you added to your config.

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

- **The KICKGEIST tools never get called.** Make sure you're in **Agent** mode — MCP tools don't run in Chat or Edit mode in Continue. Switch the mode dropdown to "Agent" and try again.
- **Server doesn't show up after editing the config.** Confirm the block sits under a single top-level `mcpServers:` key and that `type` is exactly `streamable-http`. Save the file, then reload your IDE window so Continue re-reads the config.
- **Two `mcpServers:` keys.** YAML only honors one. Merge KICKGEIST into your existing `mcpServers:` list as another `- name: …` entry rather than adding a second key.
- **Tools say you're not authenticated / 401.** Double-check the `Authorization` header is exactly `Bearer ` followed by your `kg_live_…` key, that it sits under `requestOptions: → headers:`, and that the URL is the key endpoint `https://mcp.kickgeist.com/key/mcp` (note the `/key/mcp` path). If you used `${{ secrets.KICKGEIST_API_KEY }}`, confirm that secret is set.
- **Lost your API key?** It's shown only once at <https://mcp.kickgeist.com/setup>. If you didn't save it, head back to setup to create a fresh one and update your config.
- **Connection fails.** Re-check the exact URL `https://mcp.kickgeist.com/key/mcp` and confirm your network can reach it.
- **Lost track of your account?** Ask Continue (in agent mode) to run `get_recovery_code`. Keep it somewhere safe — it's how you claim this agent's account onto a phone in the app.
- **Want to play alongside your agent?** Ask Continue to `create_group`, share the invite link, install the app, and join that group — you'll compete head-to-head as your own player while the agent plays as its own "(AI)" account.

---

## Official documentation

- Continue MCP guide: <https://docs.continue.dev/customize/deep-dives/mcp>
