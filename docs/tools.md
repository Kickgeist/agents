# KICKGEIST Tools Reference

Play the World Cup 2026 prediction game **through your own AI agent.** Predict match outcomes, rally your friends into a group, and climb the leaderboard — all from the chat window you already work in.

KICKGEIST is a group-first, zero-money, social World Cup prediction game. It's free, ad-supported (no in-app purchases, no subscriptions), and live on iOS and Android. This Model Context Protocol (MCP) server lets tech-savvy fans play right from their agent.

- **Endpoint:** `https://mcp.kickgeist.com/mcp` (single Streamable HTTP endpoint)
- **Protocol:** Model Context Protocol, revision `2025-11-25` — Streamable HTTP transport
- **Server name:** `com.kickgeist/predictions` · **Display title:** "KICKGEIST — World Cup Predictions"
- **Auth:** **Authless.** No OAuth, no login. Add the URL, then call `create_account` to get a KICKGEIST identity.

New here? See the [Quickstart](./quickstart.md) and the [per-client setup pages](../README.md#works-with-your-agent). This page is the complete contract for all nine tools.

---

## The 9 tools at a glance

| # | Tool | What it does |
|---|------|--------------|
| 1 | [`create_account`](#1-create_account) | Create a fresh anonymous KICKGEIST account and get your recovery code |
| 2 | [`link_account`](#2-link_account) | Connect this session to an account you already play on |
| 3 | [`get_recovery_code`](#3-get_recovery_code) | Show the recovery code for the linked account |
| 4 | [`list_open_matches`](#4-list_open_matches) | List matches currently open for predictions |
| 5 | [`predict_match`](#5-predict_match) | Make or change a prediction for an open match |
| 6 | [`create_group`](#6-create_group) | Create a prediction group and get a shareable invite link |
| 7 | [`join_group`](#7-join_group) | Join an existing group with an invite code or link |
| 8 | [`get_my_groups`](#8-get_my_groups) | List the groups you belong to |
| 9 | [`get_my_stats`](#9-get_my_stats) | See your own points, accuracy, streaks, and standings |

A typical first session: `create_account` → `list_open_matches` → `predict_match` → `create_group` (and share the invite) → `get_my_stats`.

---

## 1. `create_account`

Creates a fresh **anonymous** KICKGEIST account for this session and returns a **recovery code**.

**Parameters:** none.

**Returns:** a warm welcome, your **recovery code**, and a link to the app.

> **Save your recovery code.** Enter it in the KICKGEIST mobile app (or via [`link_account`](#2-link_account) on another agent) to reach this exact same account — same picks, same groups, same stats. The recovery code is the only key to the account, so keep it somewhere safe.

**Example agent phrasing**

> "Set me up with a KICKGEIST account so I can start predicting."

---

## 2. `link_account`

Connects this session to an **existing** KICKGEIST account using its recovery code. Use this to connect your agent to the account you already play on your phone.

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `recovery_code` | string | yes | The recovery code for the account. Find it in the app under account / recovery, or from when the account was created. |

**Returns:** confirmation that the session is now linked to that account.

**Example agent phrasing**

> "Link my KICKGEIST account — my recovery code is `XXXX-XXXX-XXXX`."

---

## 3. `get_recovery_code`

Shows the recovery code for the **currently linked** account, so you can save it or enter it in the app.

**Parameters:** none.

**Returns:** the recovery code for the active account.

> Use this any time you want to bring your agent-created account into the mobile app, or copy it somewhere safe.

**Example agent phrasing**

> "What's my KICKGEIST recovery code? I want to add this account to the app."

---

## 4. `list_open_matches`

Lists World Cup matches **currently open for predictions** — before kickoff and within the prediction window. You need a `matchId` from here to make a prediction.

**Parameters**

| Name | Type | Required | Limits | Description |
|------|------|----------|--------|-------------|
| `limit` | integer | no | max **50** | How many open matches to return. |

**Returns:** a list of open matches, each with:

- `matchId` — the id you pass to [`predict_match`](#5-predict_match)
- `home` team and `away` team
- `kickoff` time
- `stage` (e.g. group stage, knockout)
- `isWarmup` — whether this is a warmup match

> **What this does NOT return:** no scores, no results, no finished matches. Only the public upcoming schedule you can still predict. (See [Anti-scraping by design](#anti-scraping-by-design).)

**Example agent phrasing**

> "Show me the next 10 World Cup matches I can still predict."

---

## 5. `predict_match`

Makes or changes your prediction for an open match. Your `outcome` is the match **result** you predict: the home team wins, a draw, or the away team wins.

**Parameters**

| Name | Type | Required | Values | Description |
|------|------|----------|--------|-------------|
| `match_id` | string | yes | — | A `matchId` from [`list_open_matches`](#4-list_open_matches). |
| `outcome` | string (enum) | yes | `"home"` \| `"draw"` \| `"away"` | The result you predict. `home` = home team wins · `draw` = tie · `away` = away team wins. |
| `group_id` | string | no | — | Scope the pick to one of your groups. Get group ids from [`get_my_groups`](#8-get_my_groups). |

**Returns:** confirmation of your prediction. You can call it again on the same open match to **change** your pick before kickoff.

**Example agent phrasing**

> "Predict a home win for match `abc123`." · "Change my pick on the final to a draw."

---

## 6. `create_group`

Creates a prediction group and returns a shareable **invite link**. Share it with friends so they can join and predict alongside you.

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

## 7. `join_group`

Joins an existing group.

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `invite_code` | string | yes | A raw 6-character code **or** a full `https://kickgeist.com/join/CODE` link. |

**Returns:** confirmation that you've joined, with the group details.

**Example agent phrasing**

> "Join the group at https://kickgeist.com/join/AB12CD." · "Join group `AB12CD`."

---

## 8. `get_my_groups`

Lists the groups you belong to.

**Parameters:** none.

**Returns:** for each group:

- `name`
- `inviteCode` and the shareable invite link
- member count
- your role in the group

**Example agent phrasing**

> "Which KICKGEIST groups am I in, and what are their invite links?"

---

## 9. `get_my_stats`

Returns **your own** stats only.

**Parameters:** none.

**Returns:**

- total points
- correct picks and accuracy
- current streak and best streak
- your own global rank
- your standings within your groups
- warmup stats

> To see the **full leaderboard** and compare picks with friends, open the KICKGEIST app. That split is intentional — we keep the social comparison in the app. (See [Anti-scraping by design](#anti-scraping-by-design).)

**Example agent phrasing**

> "How am I doing in KICKGEIST? Show my points, accuracy, and streak."

---

## Anti-scraping by design

The KICKGEIST server intentionally exposes only **your own data** and the **upcoming open-match schedule**. By design, it never returns:

- match results or scores,
- other players' picks, or
- the global or group leaderboard rankings.

This is a feature, not a limitation. It protects our licensed match data and keeps the social fun — the leaderboards, the friendly trash talk, the comparing of picks — right where it belongs: in the KICKGEIST app with your friends. Your agent is a fast, private way to keep up with the schedule and lock in your predictions; the app is where the crowd celebrates together.

For the full breakdown of where the lines are drawn and why, see [Fair Play & Data Boundaries](./anti-scraping.md).

---

## Account portability

An account created via an agent is reachable in the **mobile app** through its recovery code, and an app account is reachable from your agent via [`link_account`](#2-link_account). Same picks, same groups, same stats — wherever you play.

**Always save your recovery code.** It's the only key to your account. Grab it any time with [`get_recovery_code`](#3-get_recovery_code).
