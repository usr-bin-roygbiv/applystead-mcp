# Applystead MCP server

Search open jobs from employers' own career sites, straight from ChatGPT, Claude or any MCP client.

Applystead's MCP server is a remote [Model Context Protocol](https://modelcontextprotocol.io) server. Its three job search tools are read-only and open to anyone: they search the open roles Applystead publishes from employers' own career sites, list a company's open jobs, and check whether a job posting is still open. Searching needs no install, no API key and no sign-in. Applystead members can also sign in to use their own account from an AI agent; see the docs.

- **Server URL:** `https://applystead.com/mcp`
- **Transport:** Streamable HTTP (one JSON-RPC 2.0 message per POST, one JSON response back)
- **Authentication:** none for job search (up to 60 requests a minute from one address); account tools ask you to sign in (OAuth)
- **Docs:** https://applystead.com/developers

## Add it

### Claude (custom connector)

1. In Claude, open **Customize → Connectors**, choose **+ Add**, then **Add custom connector**.
2. Name it `Applystead`, paste `https://applystead.com/mcp` and choose **Continue**, then **Continue** again.
3. Under **Authentication** choose **Sign in when needed**, keep **Use Claude's published identity** as the OAuth client, then **Add**. Job search works without signing in.

### ChatGPT

1. In ChatGPT, open **Plugins**, choose the plus button, then **Add custom MCP server**.
2. Enter `Applystead` and `https://applystead.com/mcp`, set the authentication to **OAuth**, confirm the warning and choose **Create as a plugin**.
3. Adding your own MCP server depends on your ChatGPT plan and workspace settings.

### Claude Code

```sh
claude mcp add --transport http --scope user applystead https://applystead.com/mcp
claude mcp login applystead   # only for account tools; job search needs no sign-in
```

### Codex

```sh
codex mcp add applystead --url https://applystead.com/mcp
codex mcp login applystead   # only for account tools; job search needs no sign-in
```

### Gemini CLI

```sh
gemini mcp add --transport http applystead https://applystead.com/mcp
```

For account tools, run `/mcp auth applystead` inside Gemini CLI; job search needs no sign-in.

### Other MCP clients

Any client that supports remote Streamable HTTP servers works. For example, in a JSON client config:

```json
{
  "mcpServers": {
    "applystead": { "type": "http", "url": "https://applystead.com/mcp" }
  }
}
```

Try it with curl:

```sh
curl -s https://applystead.com/mcp -H 'content-type: application/json' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"search_jobs","arguments":{"query":"data engineer","remote_country":"US","limit":5}}}'
```

## Job search tools

Read-only and open to anyone. Every job opens on its Applystead job page, with Apply with Applystead.

| Tool | Arguments | Returns |
| --- | --- | --- |
| `search_jobs` | `query?`, `remote_country?` (2-letter country code), `company?` (name or slug), `kind?` (`internship` or `new-grad`), `pay_listed?`, `posted_within_hours?` (1-720), `limit?` (1-20, default 10) | Matching open jobs, newest first |
| `get_company_jobs` | `company` (name or slug) | The company's name, open roles and company page, and its 10 newest jobs |
| `check_job_posting` | `url` (https job posting URL) | Whether the posting is `open`, `closed`, `unknown` or `unsupported`; the URL is never fetched |

The other tools act on a signed-in member's own account; https://applystead.com/developers lists them.

## Example prompts

- "Find remote data engineer jobs in the US posted in the last 3 days."
- "Which 2027 software engineering internships list pay?"
- "What jobs is Stripe hiring for right now?"
- "Is this job posting still open? <paste the posting URL>"
- "Show new-grad backend roles posted this week."

## Privacy

Job search requests are not stored, and the words, names and links in them are not logged. Privacy policy: https://applystead.com/privacy

## Support

Email hello@applystead.com.

## License

This README and the configuration files in this repository are released under the MIT License. The server itself is operated by Applystead at `https://applystead.com/mcp`.
