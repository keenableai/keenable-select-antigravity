# Keenable SELECT plugin for Antigravity and Gemini CLI

Ask a question of the live web and get a table back. [Keenable SELECT](https://docs.keenable.ai/select)
answers with one SQL query over live search results: the server runs the
searches, reads the pages, extracts the fields the question needs and returns
rows with their sources. You ask in plain language; the agent writes the query.

The plugin connects your agent to the hosted SELECT MCP server and adds a skill
that tells it when a question is worth a SELECT and how to work with the results.
It needs a Keenable account: you sign in once in the browser, and each query is
billed to that account.

## Install

**Antigravity CLI**

```bash
agy plugin install https://github.com/keenableai/keenable-select-antigravity
```

**Antigravity app and IDE**

Clone the repository into your global plugins folder, or into `.agents/plugins/`
of a workspace to enable it for that project only:

```bash
git clone https://github.com/keenableai/keenable-select-antigravity ~/.gemini/config/plugins/keenable-select
```

**Gemini CLI**

```bash
gemini extensions install https://github.com/keenableai/keenable-select-antigravity
```

On first use, authenticate the `keenable-select` server: a browser sign-in to
your Keenable account opens; approve it and the tools appear. No account yet?
Sign up at [app.keenable.ai/signup](https://app.keenable.ai/signup).

**MCP server only**

To connect the tools without the skill, add this to
`~/.gemini/config/mcp_config.json` (Antigravity):

```json
{
  "mcpServers": {
    "keenable-select": {
      "serverUrl": "https://select.keenable.ai/mcp"
    }
  }
}
```

## Tools

| Tool | What it does |
|---|---|
| `select` | Runs one read-only SQL query over live web search results and returns a table and a result set id. Follow-up queries can read that id instead of searching again. |
| `generate_html_report` | Publishes a report built from named result sets as a web page and returns its link. |
| `revoke_html_report` | Takes a published report down. |

## Skill

| Skill | What it does |
|---|---|
| `keenable-select` | When to use SELECT instead of a single search, how to scope the columns, what to tell the user about time and cost, and when to publish a report. |

## Example prompts

```text
Which open-source vector databases come up most in 2026 comparisons? Give me a
table with license, latest version and a source for each.
```

```text
List AI agent startups that announced a seed round in the last 30 days, with
amount, lead investor and source.
```

```text
Compare the pricing of the main LLM observability tools in one table: free
tier, paid entry price, what is metered.
```

## What to expect

- A query takes from seconds to a few minutes: it searches, reads pages and runs
  an LLM over each one.
- Large tables come back as a preview; the full table stays in the result set,
  kept for 30 days.
- Extraction reports what the pages say and does not fact-check them.

## API key instead of sign-in

To use an API key from the [console](https://app.keenable.ai/console) instead of
the browser sign-in:

```json
{
  "mcpServers": {
    "keenable-select": {
      "serverUrl": "https://select.keenable.ai/mcp",
      "headers": {
        "X-API-Key": "keen_<your_key>"
      }
    }
  }
}
```

## Data and security

The plugin talks only to `https://select.keenable.ai/mcp` over HTTPS. Queries,
and the pages they search and read, are processed by Keenable and billed to the
signed-in account, along with an `X-Keenable-Title` header (`antigravity-select`
or `gemini-cli-select`) that identifies the plugin's traffic. Reports are
published only when you ask for one; anyone with a report's link can open it
until it is revoked. The plugin runs no local code or shell commands and reads
no local files. See the [privacy policy](https://keenable.ai/privacy).

## Resources

- [Keenable SELECT documentation](https://docs.keenable.ai/select)
- [Keenable](https://keenable.ai)
- [Support](https://keenable.ai/contact)

## License

MIT
