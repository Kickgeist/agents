# Install KICKGEIST in one click

**Play the World Cup with your friends — straight from your AI agent.** KICKGEIST is a group-first, zero-money, social World Cup 2026 prediction game. You predict match outcomes (home win, draw, away win), build groups with friends, and climb the leaderboard. Connecting takes one paste — or, on the clients below, one click.

The server lives at a single endpoint:

```
https://mcp.kickgeist.com/mcp
```

It is **authless** — no OAuth, no login. Add the URL, then call `create_account` to get your anonymous KICKGEIST identity and a **recovery code**. Save that code: enter it in the [KICKGEIST mobile app](https://kickgeist.com) (or via `link_account` in another agent) to play the same account everywhere.

> **Heads-up: there is no single universal "Add to KICKGEIST" link.** Each client wires up MCP its own way. The one-click badges below work for clients that publish a deep-link install scheme (Cursor, VS Code, Goose). For **Claude, ChatGPT, and Perplexity** there is no deep link — you paste the URL into a settings screen (steps below). When in doubt, the raw endpoint above always works.

---

## One-click badges

### Cursor

[![Add to Cursor](https://img.shields.io/badge/Add%20to-Cursor-000000?style=for-the-badge&logo=cursor&logoColor=white)](cursor://anysphere.cursor-deeplink/mcp/install?name=kickgeist&config=eyJ1cmwiOiJodHRwczovL21jcC5raWNrZ2Vpc3QuY29tL21jcCJ9)

**Raw link** (copy into your browser address bar if the badge does not fire):

```
cursor://anysphere.cursor-deeplink/mcp/install?name=kickgeist&config=eyJ1cmwiOiJodHRwczovL21jcC5raWNrZ2Vpc3QuY29tL21jcCJ9
```

The `config` value is the Base64 of `{"url":"https://mcp.kickgeist.com/mcp"}`.

Prefer editing config by hand? Add this to `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "kickgeist": {
      "url": "https://mcp.kickgeist.com/mcp"
    }
  }
}
```

Source: [cursor.com/docs/context/mcp/install-links](https://cursor.com/docs/context/mcp/install-links)

---

### VS Code (Copilot agent mode)

[![Add to VS Code](https://img.shields.io/badge/Add%20to-VS%20Code-007ACC?style=for-the-badge&logo=visualstudiocode&logoColor=white)](vscode:mcp/install?%7B%22name%22%3A%22kickgeist%22%2C%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fmcp.kickgeist.com%2Fmcp%22%7D)

**Raw link:**

```
vscode:mcp/install?%7B%22name%22%3A%22kickgeist%22%2C%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fmcp.kickgeist.com%2Fmcp%22%7D
```

That is the URL-encoded form of `{"name":"kickgeist","type":"http","url":"https://mcp.kickgeist.com/mcp"}`.

Prefer editing config by hand? Add this to `.vscode/mcp.json`:

```json
{
  "servers": {
    "kickgeist": {
      "type": "http",
      "url": "https://mcp.kickgeist.com/mcp"
    }
  }
}
```

Source: [code.visualstudio.com/api/extension-guides/ai/mcp](https://code.visualstudio.com/api/extension-guides/ai/mcp)

---

### Goose

[![Add to Goose](https://img.shields.io/badge/Add%20to-Goose-1A1A2E?style=for-the-badge&logo=goose&logoColor=white)](goose://extension?type=streamable_http&url=https%3A%2F%2Fmcp.kickgeist.com%2Fmcp&name=KICKGEIST&description=World%20Cup%20predictions)

**Raw link:**

```
goose://extension?type=streamable_http&url=https%3A%2F%2Fmcp.kickgeist.com%2Fmcp&name=KICKGEIST&description=World%20Cup%20predictions
```

Prefer the UI? Goose Desktop → **Add custom extension** → transport **Streamable HTTP** → URL `https://mcp.kickgeist.com/mcp`.

Source: [block.github.io/goose/docs/getting-started/using-extensions](https://block.github.io/goose/docs/getting-started/using-extensions/)

---

## GUI-paste clients (no deep link)

These clients do not offer an install URL scheme — you paste the endpoint into a settings screen. It is the same `https://mcp.kickgeist.com/mcp` every time, and still authless.

### Claude (claude.ai / Desktop)

1. **Settings → Connectors → "Add custom connector"**
2. Paste `https://mcp.kickgeist.com/mcp`
3. **Add**. There is no OAuth step.

Works on Free (1 connector), Pro, Max, Team, and Enterprise.
Source: [support.claude.com/en/articles/11175166](https://support.claude.com/en/articles/11175166-getting-started-with-custom-connectors-using-remote-mcp)

### Claude Code (CLI)

```bash
claude mcp add --transport http kickgeist https://mcp.kickgeist.com/mcp
```

Then run `/mcp` to confirm it connected.
Source: [code.claude.com/docs/en/mcp](https://code.claude.com/docs/en/mcp)

### ChatGPT (paid plans only)

Plus, Pro, Business, Enterprise, or Edu — **not** Free.

1. **Settings → Apps & Connectors → Advanced settings**
2. Enable **Developer mode** (OpenAI labels it "powerful but dangerous" — proceed knowingly)
3. **Create connector** → paste `https://mcp.kickgeist.com/mcp`

Source: [developers.openai.com/api/docs/guides/developer-mode](https://developers.openai.com/api/docs/guides/developer-mode)

### Perplexity (paid plans only)

Pro, Max, or Enterprise.

1. **Settings → Connectors → "+ Custom connector"**
2. Choose **Remote**
3. Enter `https://mcp.kickgeist.com/mcp`

Source: [perplexity.ai/help-center/en/articles/13915507](https://perplexity.ai/help-center/en/articles/13915507-adding-custom-remote-connectors)

---

## Other clients (config snippets)

<details>
<summary><strong>Windsurf</strong></summary>

`~/.codeium/windsurf/mcp_config.json`:

```json
{
  "mcpServers": {
    "kickgeist": {
      "serverUrl": "https://mcp.kickgeist.com/mcp"
    }
  }
}
```

Source: [docs.windsurf.com/windsurf/cascade/mcp](https://docs.windsurf.com/windsurf/cascade/mcp)
</details>

<details>
<summary><strong>Cline</strong></summary>

**Remote Servers** tab → name + URL → transport **Streamable HTTP**. Or in JSON:

```json
{
  "kickgeist": {
    "url": "https://mcp.kickgeist.com/mcp"
  }
}
```

Source: [docs.cline.bot/mcp/connecting-to-a-remote-server](https://docs.cline.bot/mcp/connecting-to-a-remote-server)
</details>

<details>
<summary><strong>Zed</strong></summary>

`settings.json`:

```json
{
  "context_servers": {
    "kickgeist": {
      "url": "https://mcp.kickgeist.com/mcp"
    }
  }
}
```

Source: [zed.dev/docs/ai/mcp](https://zed.dev/docs/ai/mcp)
</details>

<details>
<summary><strong>Jan</strong></summary>

**Settings → MCP Servers → "+ Add MCP Server"** → transport **HTTP (Streamable HTTP)** → paste `https://mcp.kickgeist.com/mcp`.

Source: [jan.ai/docs/desktop/integrations/mcp-servers](https://jan.ai/docs/desktop/integrations/mcp-servers)
</details>

<details>
<summary><strong>Continue</strong></summary>

`config.yaml`:

```yaml
mcpServers:
  - name: KICKGEIST
    type: streamable-http
    url: https://mcp.kickgeist.com/mcp
```

Source: [docs.continue.dev/customize/deep-dives/mcp](https://docs.continue.dev/customize/deep-dives/mcp)
</details>

<details>
<summary><strong>LibreChat (self-host admin)</strong></summary>

`librechat.yaml`:

```yaml
mcpServers:
  kickgeist:
    type: streamable-http
    url: https://mcp.kickgeist.com/mcp
```

Source: [librechat.ai/docs/configuration/librechat_yaml/object_structure/mcp_servers](https://librechat.ai/docs/configuration/librechat_yaml/object_structure/mcp_servers)
</details>

<details>
<summary><strong>Universal fallback (stdio-only clients)</strong></summary>

For clients that only speak stdio, bridge to the remote endpoint:

```bash
npx mcp-remote https://mcp.kickgeist.com/mcp
```

Source: [npmjs.com/package/mcp-remote](https://www.npmjs.com/package/mcp-remote)
</details>

---

## First moves once you are connected

1. **`create_account`** — creates your anonymous KICKGEIST account and returns a recovery code. **Save the recovery code** — it is how you reach this account in the mobile app.
2. **`list_open_matches`** — see World Cup matches still open for predictions, with their `matchId`.
3. **`predict_match`** — predict an outcome: `home`, `draw`, or `away`.
4. **`create_group`** / **`join_group`** — start a group or join one with an invite link, then rally your friends.
5. **`get_my_stats`** — check your points, accuracy, and streak.

Already play on your phone? Use **`link_account`** with the recovery code from the app (account/recovery screen) so your agent and your phone share one account.

**Where's the leaderboard?** The server shares only your own data and the upcoming open-match schedule — never results, never other players' picks, never the full rankings. That keeps the licensed match data safe and keeps the social fun where it shines: in the [KICKGEIST app](https://kickgeist.com). Open it to see the full leaderboard and compare picks with your friends.

---

Built for the fans. See you on the leaderboard.
