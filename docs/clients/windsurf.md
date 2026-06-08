# Play KICKGEIST in Windsurf

Connect Windsurf's Cascade agent to **KICKGEIST — World Cup Predictions** and let it predict matches, spin up groups with your friends, and track your run to the top of the leaderboard — all from your editor.

KICKGEIST is a free, group-first, zero-money social World Cup 2026 prediction game. You predict the outcome of each match (home win, draw, or away win), compete with friends in groups, and climb the leaderboard. No accounts to sign up for, no money, no in-app purchases — just World Cup energy where you already work.

This guide wires up the **KICKGEIST MCP server** so Cascade can play on your behalf.

---

## Prerequisites

- **Windsurf** with the Cascade panel (any recent version).
- **Enterprise users:** your admin must enable MCP for your team first — turn it on under **Windsurf Settings → Cascade → MCP Servers**. Individual plans have it on by default.
- That's it. KICKGEIST is **authless** — there is no OAuth, no login, no API key. You just add the endpoint.

---

## Step 1 — Open the MCP config

You can configure the server two ways. Pick one.

**Option A — Edit the config file directly (recommended for copy-paste)**

Open this file in your editor:

```
~/.codeium/windsurf/mcp_config.json
```

If the file doesn't exist yet, create it.

**Option B — Use the Windsurf UI**

Click the **MCPs** icon in the top-right of the **Cascade** panel, *or* go to **Windsurf Settings → Cascade → MCP Servers**, then open the raw configuration to paste the JSON below.

---

## Step 2 — Add the KICKGEIST server

Add this block to `mcp_config.json`. Windsurf uses the `serverUrl` key for remote Streamable HTTP servers:

```json
{
  "mcpServers": {
    "kickgeist": {
      "serverUrl": "https://mcp.kickgeist.com/mcp"
    }
  }
}
```

If you already have other servers under `mcpServers`, just add the `"kickgeist"` entry alongside them — don't create a second `mcpServers` block.

---

## Step 3 — Refresh and connect

1. Save `mcp_config.json`.
2. In the Cascade panel, open the **MCPs** menu and click **Refresh** (or reload the config) so Windsurf picks up the new server.
3. You should see **kickgeist** listed with its tools available.

Because KICKGEIST is authless, there is no sign-in prompt — the connection is ready the moment Windsurf loads the server.

---

## Step 4 — Verify it works

Ask Cascade to play. Try, in order:

1. **"Create my KICKGEIST account."** Cascade calls `create_account` and returns a welcome message plus a **recovery code**.
   - **Save that recovery code.** Enter it in the KICKGEIST mobile app (or via `link_account` in any other agent) to reach this exact same account. It's the only way back in — keep it somewhere safe.
2. **"Show me the matches I can predict right now."** Cascade calls `list_open_matches` and lists upcoming World Cup matches open for predictions, each with a `matchId`, the two teams, kickoff time, and stage.

If you already play KICKGEIST on your phone, skip `create_account` and instead say **"Link my KICKGEIST account, my recovery code is ABC123."** Cascade calls `link_account` and connects to the account you already use.

---

## What you can do from Cascade

Once connected, Cascade has nine tools:

| Tool | What it does |
| --- | --- |
| `create_account` | Creates a fresh anonymous account and returns your recovery code. |
| `link_account` | Connects this session to an existing account using its recovery code. |
| `get_recovery_code` | Shows the recovery code for the currently linked account. |
| `list_open_matches` | Lists World Cup matches currently open for predictions (no scores, no results). |
| `predict_match` | Makes or changes your prediction — `home`, `draw`, or `away` — for an open match. |
| `create_group` | Creates a prediction group and returns a shareable invite link. |
| `join_group` | Joins a group using a 6-char invite code or a full join link. |
| `get_my_groups` | Lists the groups you belong to. |
| `get_my_stats` | Shows your own points, accuracy, streak, rank, and group standings. |

A typical flow: *"List the open matches, then predict a home win for the first one."* Cascade grabs a `matchId` from `list_open_matches` and passes it to `predict_match` with `outcome: "home"`.

Want to play with friends? *"Create a group called 'Editor United'."* Cascade calls `create_group` and hands you an invite link like `https://kickgeist.com/join/AB12CD` to share.

---

## Troubleshooting

**The server doesn't appear after editing the config.**
Re-open the **MCPs** menu in the Cascade panel and hit **Refresh**, or reload the Windsurf window. Confirm the JSON is valid — a stray comma or a missing brace will stop the whole file from loading.

**I used `url` instead of `serverUrl` and nothing connected.**
Windsurf accepts both `serverUrl` and `url` for remote HTTP servers, but `serverUrl` is the documented key for Streamable HTTP — use that. Make sure the value is exactly `https://mcp.kickgeist.com/mcp` (note the trailing `/mcp`).

**Cascade can't find the tools.**
Open the MCPs menu and check that **kickgeist** shows as connected. Enterprise users: confirm your admin has enabled MCP under **Windsurf Settings → Cascade → MCP Servers**.

**I lost my recovery code.**
While still connected, ask Cascade to **"show my recovery code"** (`get_recovery_code`) and save it. If you've already lost the session, open the KICKGEIST app to recover access.

**Cascade can't see scores, results, or the leaderboard.**
That's intentional. The server only exposes your own data and the upcoming open-match schedule — never match results, other players' picks, or the full rankings. We keep the social comparison and the live drama in the app, where it's most fun. Open KICKGEIST on your phone to see the full leaderboard and compare picks with your friends.

---

## Account portability

An account created through Cascade is the same account you use everywhere else. Enter your recovery code in the KICKGEIST mobile app to pick up right where you left off — and predictions you make on your phone show up when Cascade calls `get_my_stats`. One account, every surface. Just **save your recovery code.**

---

## Official docs

- Windsurf MCP setup: https://docs.windsurf.com/windsurf/cascade/mcp
- KICKGEIST MCP endpoint: `https://mcp.kickgeist.com/mcp`
