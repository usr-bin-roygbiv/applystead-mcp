# Applystead MCP server

Search open jobs from employers' own career sites, straight from ChatGPT, Claude or any MCP client.

Applystead's MCP server is a **remote, read-only** [Model Context Protocol](https://modelcontextprotocol.io) server. It searches the open roles Applystead publishes from employers' own applicant tracking systems (Greenhouse, Lever, Ashby, Workday and more), lists a company's open jobs, and checks whether a job posting is still open. No install, no API key, no sign-in.

- **Server URL:** `https://applystead.com/mcp`
- **Transport:** Streamable HTTP (stateless JSON responses)
- **Authentication:** none (the data is public)
- **Docs:** https://applystead.com/developers

## Add it

### Claude (custom connector)

1. In Claude, open **Settings → Connectors** and choose **Add custom connector**.
2. Name it `Applystead` and enter `https://applystead.com/mcp` as the remote MCP server URL. Leave OAuth empty.
3. Enable the connector in a chat and ask a job question.

### ChatGPT (developer mode)

1. In ChatGPT, open **Settings → Apps & Connectors → Advanced settings** and turn on **Developer mode**.
2. Under **Apps & Connectors**, choose **Create**, name it `Applystead` and enter `https://applystead.com/mcp` as the MCP server URL, with no authentication.
3. Select the app in a chat. Results render in an interactive job list.

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
  -H 'accept: application/json, text/event-stream' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"search_jobs","arguments":{"query":"data engineer","limit":3}}}'
```

## Tools

All tools are read-only and idempotent. Every job opens on its Applystead job page, with Apply with Applystead.

| Tool | Arguments | Returns |
| --- | --- | --- |
| `search_jobs` | `query?` (up to 120 chars), `remote_country?` (ISO 2-letter code), `company?` (name or slug), `kind?` (`internship` or `new-grad`), `pay_listed?` (boolean), `posted_within_hours?` (1–720), `limit?` (1–20, default 10) | Matching open jobs, newest first |
| `get_company_jobs` | `company` (name or slug) | The company (name, ATS, open roles, company page) and its 10 newest jobs |
| `check_job_posting` | `url` (https job posting URL) | Whether the posting is `open`, `closed`, `unknown` or `unsupported` |

## Example prompts

- "Find remote data engineer jobs in the US posted in the last 3 days."
- "Which 2027 software engineering internships list pay?"
- "What jobs is Stripe hiring for right now?"
- "Is this job posting still open? https://boards.greenhouse.io/..."
- "Show new-grad backend roles at companies using Ashby."

## Privacy

Requests are not stored and tool arguments are not logged. Answers contain only what Applystead's public job and company pages already show; no personal data is requested. Privacy policy: https://applystead.com/privacy

## Support

Email support@mail.applystead.com.

## License

This README and the configuration files in this repository are released under the MIT License. The server itself is operated by Applystead at `https://applystead.com/mcp`.
