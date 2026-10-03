# SPACEXPLORATION for Claude

![SPACEXPLORATION](assets/logo.png)

[SPACEXPLORATION](https://spacexploration.com) is a free MLS for commercial property: industrial, office, retail, multifamily, and land, for sale or lease, across the US. This plugin connects Claude to SPACEXPLORATION's remote MCP server and adds skills that teach Claude to use it well.

## What you can do

- **Search and compare listings.** Ask in plain language, such as "warehouses near Denver under $5M with dock doors", or filter precisely on dozens of attributes. Attributes range from cap rate and clear height to flood zone and census demographics. No account needed.
- **Get alerts.** Save a search and receive an email digest when new matching listings are published.
- **List a property.** Brokers and owners can turn a flyer or offering memorandum into a draft, add photos, and publish it, all from a conversation.

## What's included

| Component | Purpose |
| --- | --- |
| `.mcp.json` | Connects to the SPACEXPLORATION MCP server at `https://spacexploration.com/mcp` (Streamable HTTP) |
| `skills/search-listings` | Finding, filtering, and reading listings |
| `skills/saved-searches` | Creating and managing email alerts |
| `skills/author-listing` | Creating, editing, and publishing listings |
| `plugin.json`, `mcp.json` | The same plugin in the Agent Plugins format, for ChatGPT and Codex |

## Accounts and sign-in

Searching and reading listings works anonymously. Saved searches and listing authoring act on your SPACEXPLORATION account. The first time one of those tools runs, Claude opens a standard OAuth 2.0 sign-in in your browser; there's no API key to configure. Creating listings also requires a verified email address, and publishing requires a verified phone number.

## Data and network access

The plugin contains only Markdown skills and an MCP server reference. It runs no local code and has no hooks or scripts. All traffic goes to one destination:

- `https://spacexploration.com`: the MCP server (`/mcp`) and its OAuth sign-in endpoints.

Tool inputs, such as search filters, listing details, and photo URLs you ask Claude to attach, are sent to SPACEXPLORATION to carry out your request. When you attach a photo by URL, SPACEXPLORATION's servers fetch that image. How SPACEXPLORATION handles your data is described in its [privacy policy](https://spacexploration.com/privacy) and [terms](https://spacexploration.com/terms).

## Without the plugin

You can connect the server directly instead:

- **claude.ai or Claude Desktop:** Settings → Connectors → Add custom connector, then enter `https://spacexploration.com/mcp`.
- **Claude Code:** `claude mcp add --transport http spacexploration https://spacexploration.com/mcp`

Full tool reference: https://spacexploration.com/docs/ai

## License

MIT. See [LICENSE](LICENSE).
