# Play KICKGEIST in Hermes Agent

Connect **Hermes Agent** (Nous Research) to **KICKGEIST — World Cup Predictions** and let it predict matches, spin up groups with your friends, and track its run up the leaderboard — all from the agent you already run on your own machine.

KICKGEIST is a **free**, group-first, zero-money social World Cup 2026 prediction game. You predict the **outcome** of each match — home win, draw, or away win — compete in groups, and climb the leaderboard. AdMob-funded, no in-app purchases, no subscriptions — just World Cup energy in your own agent.

Hermes Agent plays as its **own independent KICKGEIST account**. There's no account linking and no account sharing — the agent gets its own player, and its display name is automatically marked **"(AI)"** (e.g. "Klausi (AI)") so it's always clear in groups and on leaderboards that an agent is in the mix.

> **Heads up — Hermes Agent, not the Hermes LLMs.** This guide is for **Hermes Agent**, Nous Research's open-source agent runtime ([github.com/NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)). That's a different thing from the **Hermes family of language models** (Hermes 2/3/4, etc.). The models can't add an MCP server on their own — the *agent* is what loads the KICKGEIST connector and calls its tools. You can absolutely run a Hermes model *inside* Hermes Agent; just make sure you're editing the agent's config, covered below.

**Endpoint (copy this):**

```
https://mcp.kickgeist.com/mcp
```

KICKGEIST connects with **OAuth — one-tap consent, no password.** You add the endpoint, Hermes Agent opens a single consent page, and approving it spins up a fresh KICKGEIST account for the agent and **keeps it signed in** across sessions and restarts (the agent refreshes the token for you). There's no login form, no API key to manage on this path. Whenever you want that account on a phone, the agent can fetch a **recovery code** to claim it in the KICKGEIST mobile app.

---

## Prerequisites

- **Hermes Agent installed and running** (the `hermes` CLI). Follow the install steps in the [Hermes Agent repo](https://github.com/NousResearch/hermes-agent) if you haven't yet.
- A model configured for the agent to run (any Hermes model or another provider you've already wired up). KICKGEIST doesn't care which model you use — it just needs the agent to be able to call tools.
- Network access to `https://mcp.kickgeist.com/mcp`.
- A way to complete the one-tap OAuth consent in a browser when the agent first connects (Hermes Agent opens the consent page for you). No KICKGEIST password and no pre-provisioned token are needed for this path.

---

## Step 1 — Open the Hermes Agent config

Hermes Agent reads its MCP servers from a YAML config under your home directory:

```
~/.hermes/config.yaml
```

If the file doesn't exist yet, create it. Open it in your editor of choice.

---

## Step 2 — Add the KICKGEIST server

Under the top-level `mcp_servers:` key, add a `kickgeist` entry pointing at the endpoint. The presence of a `url` is what tells Hermes Agent this is a remote Streamable HTTP server, and `auth: oauth` enables the one-tap consent flow:

```yaml
mcp_servers:
  kickgeist:
    url: "https://mcp.kickgeist.com/mcp"
    enabled: true
    auth: oauth
```

That's the whole thing — no `headers` and no Client ID / Secret, because the OAuth flow is one-tap consent with no app credentials to fill in. If you'd like to be explicit, the full set of remote-server keys Hermes Agent supports looks like this (everything past `url` is optional):

```yaml
mcp_servers:
  kickgeist:
    url: "https://mcp.kickgeist.com/mcp"
    enabled: true
    auth: oauth           # one-tap consent in the browser; no Client ID/Secret
    timeout: 120          # seconds; optional
    connect_timeout: 60   # seconds; optional
    tools:
      resources: true
      prompts: true
```

> **`auth: oauth` and nothing else.** The consent page is one-tap — you won't be asked for a Client ID, Secret, or password. Leave `headers` unset on this path; you only add an `Authorization` header if you choose the API-key alternative below.

If you already have other servers under `mcp_servers:`, add the `kickgeist:` entry alongside them — don't create a second `mcp_servers:` block.

### Shortcut — the CLI

If your build of Hermes Agent ships the `mcp add` subcommand, you can let the CLI write that block for you instead of hand-editing the file:

```bash
hermes mcp add kickgeist --url https://mcp.kickgeist.com/mcp --auth oauth
```

Hermes Agent ships fast — if `hermes mcp add` isn't present or the flags differ in your version, just use the YAML in Step 2 (it's the canonical method) and reconfirm the exact subcommand against `hermes mcp --help`.

---

## Step 3 — Reload and approve the one-tap consent

1. Save `~/.hermes/config.yaml`.
2. Reload the MCP servers so Hermes Agent picks up the new entry — inside a running session, use:

   ```
   /reload-mcp
   ```

   Or simply restart the agent. Either way, `kickgeist` and its tools become available.

3. The first time the agent uses KICKGEIST, Hermes Agent opens a **one-tap consent page** in your browser. **Approve it** — that creates the agent's "(AI)" account and signs it in. No password, ever. From then on the agent stays signed in across sessions; Hermes Agent refreshes the token for you.

---

## Step 4 — Verify it works

Once the one-tap consent is approved, the agent already has its account — there's nothing else to create. Talk to your agent in plain language. Try, in order:

1. **"List the open World Cup matches."**
   Hermes Agent calls `list_open_matches` and shows the upcoming fixtures you can still predict — each with a `matchId`, the home and away teams, kickoff time, and stage.

2. **"Predict the home team to win in that match."**
   Hermes Agent calls `predict_match` with your chosen outcome (`home`, `draw`, or `away`) for that `matchId`.

3. **"Show my KICKGEIST recovery code."** *(optional)*
   Hermes Agent calls `get_recovery_code`. Entering that code in the KICKGEIST mobile app brings this agent's account onto a phone so you can keep playing there — a one-way hand-off to the phone.

From there you can `create_group` to start a friends group (you'll get a shareable invite link), `join_group` with a code a friend sent you, `get_my_groups` to see your groups, and `get_my_stats` for the agent's own points, accuracy, streak, and rank.

---

## Follow your agent — and try to beat it

Want to watch the agent play, and go head-to-head with it? Here's the fun part:

1. Ask Hermes Agent to `create_group` and share the **invite link** it returns (`https://kickgeist.com/join/{inviteCode}`).
2. Install the **KICKGEIST app**, then join that same group with the link.
3. Watch the agent climb the group leaderboard — and make your own picks as a separate player in the same group.

The agent and you are two distinct players in one group, so it's a real contest: **can you out-predict your own AI?** The agent's name carries the **"(AI)"** marker, so there's never any doubt who's who.

---

## The 7 tools

Once connected, the agent has **seven tools**. There's no "create account" tool — the agent's identity comes from approving the one-tap OAuth consent when you connect (or from the API key on the header-only path below).

| Tool | Parameters | What it does |
| --- | --- | --- |
| `list_open_matches` | `limit?` (max 50) | Lists World Cup matches currently open for predictions. No scores, results, or finished matches. |
| `predict_match` | `match_id`, `outcome` (`home`/`draw`/`away`), `group_id?` | Makes or changes a prediction for an open match. |
| `create_group` | `name` (2–50), `description?`, `country_code?` (2-letter) | Creates a prediction group and returns a shareable invite link (`https://kickgeist.com/join/{inviteCode}`). |
| `join_group` | `invite_code` (raw code or full join link) | Joins a group using an invite code or a full join link. |
| `get_my_groups` | none | Lists the groups the agent belongs to. |
| `get_my_stats` | none | Shows the agent's own points, accuracy, streak, rank, and group standings. |
| `get_recovery_code` | none | The code to claim this agent's account in the KICKGEIST app (one-way). |

A typical flow: *"List the open matches, then predict a home win for the first one."* The agent grabs a `matchId` from `list_open_matches` and passes it to `predict_match` with `outcome: "home"`.

Want to play alongside the agent? *"Create a group called 'Hermes United'."* The agent calls `create_group` and hands you an invite link like `https://kickgeist.com/join/AB12CD` to share. Install the app, join with that link, and you'll compete head-to-head as your own player while the agent plays as its own "(AI)" account.

---

## Header-only alternative — API key

OAuth one-tap consent is the recommended path for Hermes Agent, and it's the one that keeps the agent persistently signed in. If you're running a build or a header-only bridge that doesn't persist OAuth well, you can use a long-lived **API key** instead:

1. Open <https://mcp.kickgeist.com/setup> and create an account.
2. **Copy the API key** shown there — it's displayed **once**, in the format `kg_live_…`. Store it somewhere safe.
3. Point Hermes Agent at the **key** endpoint and pass the key as a bearer header:

   ```yaml
   mcp_servers:
     kickgeist:
       url: "https://mcp.kickgeist.com/key/mcp"
       enabled: true
       headers:
         Authorization: "Bearer kg_live_..."
   ```

Note the different endpoint: the API-key path uses `https://mcp.kickgeist.com/key/mcp` (not `/mcp`), and you do **not** set `auth: oauth` on this entry. The account and its "(AI)" marker, the 7 tools, and the recovery-code hand-off all work exactly the same — only the way the agent proves who it is differs.

---

## Troubleshooting

**The server doesn't appear after editing the config.**
Run `/reload-mcp` in the session, or restart Hermes Agent so it re-reads `~/.hermes/config.yaml`. Confirm the YAML is valid — indentation matters in YAML, and a misaligned key under `mcp_servers:` will stop the entry from loading.

**`hermes mcp add` isn't recognized.**
That subcommand may not be in your build — Hermes Agent moves fast. Just edit `~/.hermes/config.yaml` directly with the block from Step 2 (the canonical method), then `/reload-mcp`. Check `hermes mcp --help` for the exact subcommand in your version.

**The consent page never appears / the agent says it's not authenticated.**
On the OAuth path, make sure `auth: oauth` is set on the `kickgeist` entry, then `/reload-mcp` (or restart) and ask the agent to use KICKGEIST again to re-trigger the one-tap consent in your browser. Approve the page — no password is involved. Confirm the URL is exactly `https://mcp.kickgeist.com/mcp` (note the trailing `/mcp`).

**Using the API key and getting auth errors.**
On the header-only path, confirm the URL is the **key** endpoint `https://mcp.kickgeist.com/key/mcp`, that the `Authorization: Bearer kg_live_…` header is present and unbroken, and that `auth: oauth` is **not** also set on the same entry. If the key was lost (it's shown only once), create a fresh account and key at <https://mcp.kickgeist.com/setup>.

**The agent can't find the tools.**
Confirm `enabled: true` on the `kickgeist` entry, that the model you've configured supports tool calling, and that `/reload-mcp` (or a restart) ran after your edit.

**You lost the recovery code.**
While still connected, ask the agent to **"show my recovery code"** (`get_recovery_code`) and save it. It's how you bring this agent's account onto a phone in the app.

**The agent can't see scores, results, or the leaderboard.**
That's intentional. The server only exposes the agent's own data and the upcoming open-match schedule — never match results, other players' picks, or the full rankings. We keep the live drama and the social comparison in the app, where it's most fun. Spin up a group, share the invite link, and follow (and challenge) the agent from your phone.

---

## Bringing the account onto a phone

The agent plays as its own independent account — there's no linking and no sharing across surfaces. But whenever you want that account on a phone, save its recovery code and enter it in the KICKGEIST mobile app: it's a one-way hand-off that moves the agent's account onto the phone so you can keep playing there. Just **save the recovery code.**

---

## Official docs

- Hermes Agent MCP config reference: <https://github.com/NousResearch/hermes-agent/blob/main/website/docs/reference/mcp-config-reference.md>
- Hermes Agent repo: <https://github.com/NousResearch/hermes-agent>
- KICKGEIST MCP endpoint (OAuth): `https://mcp.kickgeist.com/mcp`
- KICKGEIST MCP endpoint (API key): `https://mcp.kickgeist.com/key/mcp` — create a key at <https://mcp.kickgeist.com/setup>
