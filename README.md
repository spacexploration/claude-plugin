# SPACEXPLORATION plugin

![SPACEXPLORATION](assets/logo.png)

[SPACEXPLORATION](https://spacexploration.com) is a free MLS for commercial property: industrial, office, retail, multifamily, and land, for sale or lease, across the US. This plugin connects Claude, ChatGPT, Codex, Grok Bot, and Cursor to SPACEXPLORATION's remote MCP server and adds skills that teach them to use it well.

## What you can do

- **Search and compare listings.** Ask in plain language, such as "warehouses near Denver under $5M with dock doors", or filter precisely on dozens of attributes. Attributes range from cap rate and clear height to flood zone and census demographics. No account needed.
- **Get alerts.** Save a search and receive an email digest when new matching listings are published.
- **List a property.** Brokers and owners can turn a flyer or offering memorandum into a draft, add photos, and publish it, all from a conversation.

## What's included

Every platform shares the same skills and the same MCP server, `https://spacexploration.com/mcp` (Streamable HTTP). Each reads its own manifest.

| Component | Purpose |
| --- | --- |
| `skills/search-listings` | Finding, filtering, and reading listings |
| `skills/saved-searches` | Creating and managing email alerts |
| `skills/author-listing` | Creating, editing, and publishing listings |
| `.claude-plugin/plugin.json`, `.mcp.json` | The plugin for Claude Code and claude.ai |
| `plugin.json`, `mcp.json` | The plugin for ChatGPT and Codex, in the Agent Plugins format |
| `.cursor-plugin/plugin.json` | The plugin for Grok Bot and Cursor, using `.mcp.json` |

## Accounts and sign-in

Searching and reading listings works anonymously. Saved searches and listing authoring act on your SPACEXPLORATION account. The first time one of those tools runs, your assistant opens a standard OAuth 2.0 sign-in in your browser; there's no API key to configure. Creating listings also requires a verified email address, and publishing requires a verified phone number.

## Data and network access

The plugin contains only Markdown skills and an MCP server reference. It runs no local code and has no hooks or scripts. All traffic goes to one destination:

- `https://spacexploration.com`: the MCP server (`/mcp`) and its OAuth sign-in endpoints.

Tool inputs, such as search filters, listing details, and photo URLs you ask your assistant to attach, are sent to SPACEXPLORATION to carry out your request. When you attach a photo by URL, SPACEXPLORATION's servers fetch that image. How SPACEXPLORATION handles your data is described in its [privacy policy](https://spacexploration.com/privacy) and [terms](https://spacexploration.com/terms).

## Without the plugin

You can connect the server directly instead:

- **claude.ai or Claude Desktop:** Settings → Connectors → Add custom connector, then enter `https://spacexploration.com/mcp`.
- **Claude Code:** `claude mcp add --transport http spacexploration https://spacexploration.com/mcp`
- **ChatGPT:** turn on developer mode (Settings → Security and login), then add a plugin at chatgpt.com/plugins with the URL `https://spacexploration.com/mcp` and OAuth sign-in.
- **Grok Bot or Cursor:** add a custom MCP server with the URL `https://spacexploration.com/mcp` and no headers.

Full tool reference: https://spacexploration.com/docs/ai

## License

MIT. See [LICENSE](LICENSE).
