# Play KICKGEIST in Hermes Agent

Connect **Hermes Agent** (Nous Research) to **KICKGEIST — World Cup Predictions** and let it predict matches, spin up groups with your friends, and track your run up the leaderboard — all from the agent you already run on your own machine.

KICKGEIST is a **free**, group-first, zero-money social World Cup 2026 prediction game. You predict the **outcome** of each match — home win, draw, or away win — compete with friends in groups, and climb the leaderboard. No sign-up flow, no money, no in-app purchases — just World Cup energy in your own agent.

> **Heads up — Hermes Agent, not the Hermes LLMs.** This guide is for **Hermes Agent**, Nous Research's open-source agent runtime ([github.com/NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)). That's a different thing from the **Hermes family of language models** (Hermes 2/3/4, etc.). The models can't add an MCP server on their own — the *agent* is what loads the KICKGEIST connector and calls its tools. You can absolutely run a Hermes model *inside* Hermes Agent; just make sure you're editing the agent's config, covered below.

**Endpoint (copy this):**

```
https://mcp.kickgeist.com/mcp
```

KICKGEIST is **authless** — there's no OAuth, no login, no API key. You add the endpoint, then ask Hermes Agent to create your account. Your identity is a **recovery code** the server hands back, which also unlocks the very same account in the KICKGEIST mobile app.

---

## Prerequisites

- **Hermes Agent installed and running** (the `hermes` CLI). Follow the install steps in the [Hermes Agent repo](https://github.com/NousResearch/hermes-agent) if you haven't yet.
- A model configured for the agent to run (any Hermes model or another provider you've already wired up). KICKGEIST doesn't care which model you use — it just needs the agent to be able to call tools.
- Network access to `https://mcp.kickgeist.com/mcp`.
- That's it. No OAuth, no token, no API key for KICKGEIST.

---

## Step 1 — Open the Hermes Agent config

Hermes Agent reads its MCP servers from a YAML config under your home directory:

```
~/.hermes/config.yaml
```

If the file doesn't exist yet, create it. Open it in your editor of choice.

---

## Step 2 — Add the KICKGEIST server

Under the top-level `mcp_servers:` key, add a `kickgeist` entry pointing at the endpoint. The presence of a `url` is what tells Hermes Agent this is a remote Streamable HTTP server:

```yaml
mcp_servers:
  kickgeist:
    url: "https://mcp.kickgeist.com/mcp"
    enabled: true
```

That's the whole thing — no `headers`, no `auth`, no certificates, because KICKGEIST is authless. If you'd like to be explicit, the full set of remote-server keys Hermes Agent supports looks like this (everything past `url` is optional):

```yaml
mcp_servers:
  kickgeist:
    url: "https://mcp.kickgeist.com/mcp"
    enabled: true
    timeout: 120          # seconds; optional
    connect_timeout: 60   # seconds; optional
    # headers: {}         # leave empty — KICKGEIST needs no auth header
    # auth: oauth         # do NOT set — KICKGEIST is authless
    tools:
      resources: true
      prompts: true
```

> **Leave `auth` unset.** Setting `auth: oauth` would make Hermes Agent attempt an OAuth handshake the server doesn't expect. KICKGEIST is authless — no `auth`, no `headers`.

If you already have other servers under `mcp_servers:`, add the `kickgeist:` entry alongside them — don't create a second `mcp_servers:` block.

### Shortcut — the CLI

If your build of Hermes Agent ships the `mcp add` subcommand, you can let the CLI write that block for you instead of hand-editing the file:

```bash
hermes mcp add kickgeist --url https://mcp.kickgeist.com/mcp
```

Hermes Agent ships fast — if `hermes mcp add` isn't present or the flags differ in your version, just use the YAML in Step 2 (it's the canonical method) and reconfirm the exact subcommand against `hermes mcp --help`.

---

## Step 3 — Reload and connect

1. Save `~/.hermes/config.yaml`.
2. Reload the MCP servers so Hermes Agent picks up the new entry — inside a running session, use:

   ```
   /reload-mcp
   ```

   Or simply restart the agent. Either way, `kickgeist` and its tools become available.

Because KICKGEIST is authless, there's no sign-in prompt — the connection is live the moment Hermes Agent loads the server.

---

## Step 4 — Verify it works

Talk to your agent in plain language. Try, in order:

1. **"Use KICKGEIST to create my account."**
   Hermes Agent calls `create_account` and returns a welcome message plus a **recovery code** and an app link.
   - **Save that recovery code.** Enter it in the KICKGEIST mobile app (or via `link_account` in any other agent) to reach this exact same account. It's the only way back in — keep it somewhere safe. You can re-display it anytime by asking the agent to run `get_recovery_code`.

2. **"List the open World Cup matches."**
   Hermes Agent calls `list_open_matches` and shows the upcoming fixtures you can still predict — each with a `matchId`, the home and away teams, kickoff time, and stage.

3. **"Predict the home team to win in that match."**
   Hermes Agent calls `predict_match` with your chosen outcome (`home`, `draw`, or `away`) for that `matchId`.

If you already play KICKGEIST on your phone, skip `create_account` and instead say **"Link my KICKGEIST account, my recovery code is ABC123."** Hermes Agent calls `link_account` and connects to the account you already use.

---

## What you can do from Hermes Agent

Once connected, the agent has nine tools:

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

A typical flow: *"List the open matches, then predict a home win for the first one."* The agent grabs a `matchId` from `list_open_matches` and passes it to `predict_match` with `outcome: "home"`.

Want to play with friends? *"Create a group called 'Hermes United'."* The agent calls `create_group` and hands you an invite link like `https://kickgeist.com/join/AB12CD` to share. Send it around; friends join with `join_group`.

---

## Troubleshooting

**The server doesn't appear after editing the config.**
Run `/reload-mcp` in the session, or restart Hermes Agent so it re-reads `~/.hermes/config.yaml`. Confirm the YAML is valid — indentation matters in YAML, and a misaligned key under `mcp_servers:` will stop the entry from loading.

**`hermes mcp add` isn't recognized.**
That subcommand may not be in your build — Hermes Agent moves fast. Just edit `~/.hermes/config.yaml` directly with the block from Step 2 (the canonical method), then `/reload-mcp`. Check `hermes mcp --help` for the exact subcommand in your version.

**The agent tried to authenticate and failed.**
Make sure you did **not** set `auth: oauth` (or any `headers`) on the `kickgeist` entry. KICKGEIST is authless — those keys should be absent. Confirm the URL is exactly `https://mcp.kickgeist.com/mcp` (note the trailing `/mcp`).

**The agent can't find the tools.**
Confirm `enabled: true` on the `kickgeist` entry, that the model you've configured supports tool calling, and that `/reload-mcp` (or a restart) ran after your edit.

**You lost your recovery code.**
While still connected, ask the agent to **"show my recovery code"** (`get_recovery_code`) and save it. If the session's already gone, open the KICKGEIST app to recover access.

**The agent can't see scores, results, or the leaderboard.**
That's intentional. The server only exposes your own data and the upcoming open-match schedule — never match results, other players' picks, or the full rankings. We keep the live drama and the social comparison in the app, where it's most fun. Open KICKGEIST on your phone to see the full leaderboard and compare picks with your friends.

---

## Account portability

An account created through Hermes Agent is the same account you use everywhere else. Enter your recovery code in the KICKGEIST mobile app to pick up right where you left off — and predictions you make on your phone show up when the agent calls `get_my_stats`. One account, every surface. Just **save your recovery code.**

---

## Official docs

- Hermes Agent MCP config reference: <https://github.com/NousResearch/hermes-agent/blob/main/website/docs/reference/mcp-config-reference.md>
- Hermes Agent repo: <https://github.com/NousResearch/hermes-agent>
- KICKGEIST MCP endpoint: `https://mcp.kickgeist.com/mcp`
