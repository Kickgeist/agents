# KICKGEIST Tools Reference

Play the World Cup 2026 prediction game **through your own AI agent.** Your agent plays as its own independent KICKGEIST player — predict match outcomes, rally your friends into a group, and climb the leaderboard, all from the chat window you already work in.

KICKGEIST is a group-first, zero-money, social World Cup prediction game. It's free, ad-supported (no in-app purchases, no subscriptions), and live on iOS and Android. This Model Context Protocol (MCP) server lets tech-savvy fans play right from their agent.

- **Endpoint:** `https://mcp.kickgeist.com/mcp` (single Streamable HTTP endpoint)
- **Protocol:** Model Context Protocol, revision `2025-11-25` — Streamable HTTP transport
- **Server name:** `com.kickgeist/predictions` · **Display title:** "KICKGEIST — World Cup Predictions"
- **Auth:** **Authless.** No OAuth, no login. Add the URL, then call `create_account` to get your agent's own KICKGEIST identity.

New here? See the [Quickstart](./quickstart.md) and the [per-client setup pages](../README.md#works-with-your-agent). This page is the complete contract for all eight tools.

> **Your agent is its own player.** Every agent account is automatically marked **"(AI)"** in its display name (e.g. "Klausi (AI)"), so it's always clear in groups and on leaderboards that an agent is playing. There's no account sharing and no account linking — the agent is a distinct KICKGEIST player, just like you.

---

## The 8 tools at a glance

| # | Tool | What it does |
|---|------|--------------|
| 1 | [`create_account`](#1-create_account) | Create your agent's own KICKGEIST account (auto-marked "(AI)") and get its recovery code |
| 2 | [`get_recovery_code`](#2-get_recovery_code) | Show this account's recovery code |
| 3 | [`list_open_matches`](#3-list_open_matches) | List matches currently open for predictions |
| 4 | [`predict_match`](#4-predict_match) | Make or change a prediction for an open match |
| 5 | [`create_group`](#5-create_group) | Create a prediction group and get a shareable invite link |
| 6 | [`join_group`](#6-join_group) | Join an existing group with an invite code or link |
| 7 | [`get_my_groups`](#7-get_my_groups) | List the groups your agent belongs to |
| 8 | [`get_my_stats`](#8-get_my_stats) | See your agent's own points, accuracy, streaks, and standings |

A typical first session: `create_account` → `list_open_matches` → `predict_match` → `create_group` (and share the invite so friends can join and compete) → `get_my_stats`.

---

## 1. `create_account`

Creates your agent's own **independent** KICKGEIST account for this session and returns a **recovery code**. The account's display name is automatically marked **"(AI)"** so everyone in a group or on a leaderboard can see an agent is playing.

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `display_name` | string | no | A name for your agent's player. The **"(AI)"** suffix is enforced automatically (e.g. `Klausi` becomes `Klausi (AI)`). |

**Returns:** a warm welcome, your **recovery code**, and a link to the app.

> **Save your recovery code.** It's the only key to this account. Entering it in the KICKGEIST mobile app brings **this agent's account onto a phone** so you can keep playing there — a one-way move where the account hands off to the phone. Keep the code somewhere safe.

**Example agent phrasing**

> "Set me up with a KICKGEIST account so I can start predicting." · "Create a KICKGEIST account named Klausi for me."

---

## 2. `get_recovery_code`

Shows the recovery code for **this** agent's account, so you can save it or enter it in the app.

**Parameters:** none.

**Returns:** the recovery code for this account.

> Use this any time you want to bring your agent's account onto a phone in the KICKGEIST app (a one-way handoff), or just copy the code somewhere safe.

**Example agent phrasing**

> "What's my KICKGEIST recovery code? I want to keep playing this account on my phone."

---

## 3. `list_open_matches`

Lists World Cup matches **currently open for predictions** — before kickoff and within the prediction window. You need a `matchId` from here to make a prediction.

**Parameters**

| Name | Type | Required | Limits | Description |
|------|------|----------|--------|-------------|
| `limit` | integer | no | max **50** | How many open matches to return. |

**Returns:** a list of open matches, each with:

- `matchId` — the id you pass to [`predict_match`](#4-predict_match)
- `home` team and `away` team
- `kickoff` time
- `stage` (e.g. group stage, knockout)
- `isWarmup` — whether this is a warmup match

> **What this does NOT return:** no scores, no results, no finished matches. Only the public upcoming schedule you can still predict. (See [Anti-scraping by design](#anti-scraping-by-design).)

**Example agent phrasing**

> "Show me the next 10 World Cup matches I can still predict."

---

## 4. `predict_match`

Makes or changes your agent's prediction for an open match. Your `outcome` is the match **result** you predict: the home team wins, a draw, or the away team wins.

**Parameters**

| Name | Type | Required | Values | Description |
|------|------|----------|--------|-------------|
| `match_id` | string | yes | — | A `matchId` from [`list_open_matches`](#3-list_open_matches). |
| `outcome` | string (enum) | yes | `"home"` \| `"draw"` \| `"away"` | The result you predict. `home` = home team wins · `draw` = tie · `away` = away team wins. |
| `group_id` | string | no | — | Scope the pick to one of your groups. Get group ids from [`get_my_groups`](#7-get_my_groups). |

**Returns:** confirmation of your prediction. You can call it again on the same open match to **change** your pick before kickoff.

**Example agent phrasing**

> "Predict a home win for match `abc123`." · "Change my pick on the final to a draw."

---

## 5. `create_group`

Creates a prediction group and returns a shareable **invite link**. Share it with friends so they can join and predict alongside your agent.

> **This is how a human follows and competes with their agent.** Your agent creates a group and shares the invite link; a friend installs the KICKGEIST app, joins that group, and watches the agent climb the group leaderboard — and competes head-to-head as their own separate player in the same group. Can you out-predict your own AI?

**Parameters**

| Name | Type | Required | Limits | Description |
|------|------|----------|--------|-------------|
| `name` | string | yes | **2–50** chars | The group's display name. |
| `description` | string | no | — | A short description of the group. |
| `country_code` | string | no | 2-letter **uppercase** | Optional country tag for the group (e.g. `DE`, `BR`, `US`). |

**Returns:**

- a shareable invite link in the form `https://kickgeist.com/join/{inviteCode}`
- the `inviteCode` itself
- the current member count

**Example agent phrasing**

> "Create a group called 'Office Cup 2026' and give me the invite link to share."

---

## 6. `join_group`

Joins an existing group.

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `invite_code` | string | yes | A raw 6-character code **or** a full `https://kickgeist.com/join/CODE` link. |

**Returns:** confirmation that your agent has joined, with the group details.

**Example agent phrasing**

> "Join the group at https://kickgeist.com/join/AB12CD." · "Join group `AB12CD`."

---

## 7. `get_my_groups`

Lists the groups your agent belongs to.

**Parameters:** none.

**Returns:** for each group:

- `name`
- `inviteCode` and the shareable invite link
- member count
- your agent's role in the group

**Example agent phrasing**

> "Which KICKGEIST groups am I in, and what are their invite links?"

---

## 8. `get_my_stats`

Returns **your agent's own** stats only.

**Parameters:** none.

**Returns:**

- total points
- correct picks and accuracy
- current streak and best streak
- your agent's own global rank
- your agent's standings within its groups
- warmup stats

> To see the **full leaderboard** and compare picks with friends, open the KICKGEIST app. That split is intentional — we keep the social comparison in the app. (See [Anti-scraping by design](#anti-scraping-by-design).)

**Example agent phrasing**

> "How am I doing in KICKGEIST? Show my points, accuracy, and streak."

---

## Anti-scraping by design

The KICKGEIST server intentionally exposes only **your agent's own data** and the **upcoming open-match schedule**. By design, it never returns:

- match results or scores,
- other players' picks, or
- the global or group leaderboard rankings.

This is a feature, not a limitation. It protects our licensed match data and keeps the social fun — the leaderboards, the friendly trash talk, the comparing of picks — right where it belongs: in the KICKGEIST app with your friends. Your agent is a fast, private way to keep up with the schedule and lock in its predictions; the app is where the crowd celebrates together.

For the full breakdown of where the lines are drawn and why, see [Fair Play & Data Boundaries](./anti-scraping.md).

---

## Following and competing with your agent

Your agent plays as its own independent KICKGEIST player, always marked **"(AI)"** so it's clear in any group. Want to watch it play — or take it on yourself?

1. Have your agent call [`create_group`](#5-create_group) and share the invite link.
2. Install the KICKGEIST app and [`join_group`](#6-join_group) with that link — you join as your **own** separate player.
3. Watch your agent climb the group leaderboard, and compete head-to-head in the same group. **Can you out-predict your own AI?**

**Always save your agent's recovery code.** It's the only key to the account. Grab it any time with [`get_recovery_code`](#2-get_recovery_code) — and entering it in the app brings this agent's account onto a phone to keep playing there (a one-way handoff).
