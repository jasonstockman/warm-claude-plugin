# Warm for Claude

Connect Claude directly to your [Warm](https://warm.io) financial data through Warm's hosted remote MCP server (`https://app.warm.io/mcp`), paired with built-in skills and a check-in command so Claude follows Warm's sign conventions, privacy defaults, and approval workflow.

Works in **Claude Chat**, **Cowork**, and **Claude Code**.

## What this plugin does

This plugin contains only Markdown skills, a slash command, and a remote MCP server pointer—no local executables, shell scripts, background daemons, or embedded credentials. When you connect the `warm` MCP server, Claude communicates only with `https://app.warm.io/mcp` over HTTPS and does not send your financial data to any other external service.

### Included components

- **Remote MCP server (`warm`)**: Connects to Warm's hosted Streamable HTTP MCP endpoint at `https://app.warm.io/mcp`.
- **`financial-checkin` skill**: Guides Claude to check `get_financial_context`, `get_findings`, `get_transactions`, and `read_operation` accurately—handling Warm's per-tool amount sign conventions and respecting your personal-info filter setting.
- **`organize-finances` skill**: Guides Claude to look up exact IDs with `find_transactions` or `read_operation`, preview changes with `describe_operation`, respect Warm's approval flow (`invoke_operation`), and create human handoffs (`request_handoff`) for bank or account actions.
- **`card-benefits` skill**: Guides Claude to present confirmed credit card credits, protections, reset dates, conditions, and as-of dates from `get_card_benefits` without guessing activation or usage state.
- **`/warm:checkin` command**: Runs a structured financial check-in covering your current position, open findings, and the latest 10-day transaction window.

## Authentication and permissions

When you connect Warm from the plugin's Connectors tab (or on first use in Claude Code), you sign in to Warm with OAuth 2.1 (PKCE and Client ID Metadata Document):

- **Read-only access** (`financial_context:read`, `transactions:read`, available on all plans including free): exposes `get_financial_context`, `get_transactions`, `request_handoff`, and `get_handoff_status`.
- **Read-and-write access** (`financial_records:write`, available on paid Warm plans): adds `find_transactions`, `get_findings`, `get_card_benefits`, `read_operation`, `describe_operation`, `invoke_operation`, and `get_operation_status`.

Every record change is previewed through `describe_operation` and waits for your approval in Warm unless you explicitly enable automatic approval in your Warm Settings. You can disconnect Claude at any time in Warm under **Settings > Export & API > Connected apps**.

## Example prompts

- *"Run a financial check-in on my Warm accounts and tell me what bills or findings need attention."*
- *"Summarize my spending this month by category from Warm."*
- *"Find my recent unmarked coffee shop transactions in Warm and prepare a tag rule for them."*
- *"Check my credit card benefits in Warm and list any statement credits resetting soon."*

## Privacy and boundaries

- Warm connects to financial institutions **read-only**—neither Warm nor Claude can move money or initiate bank transfers.
- Warm respects your **Personal info filter** setting: when enabled, merchant names, descriptions, notes, and tags are omitted from `get_transactions`.
- Warm reports financial facts and catalog data from your connected accounts; it does not provide investment, tax, legal, or insurance advice.

## Links

- [Warm Hosted MCP Overview](https://warm.io/financial-mcp-server/)
- [Warm Developer Documentation](https://warm.io/developers)
- [Privacy Policy](https://warm.io/privacy/)
- [Terms of Service](https://warm.io/terms/)
- Support: [support@warm.io](mailto:support@warm.io) ([warm.io/contact](https://warm.io/contact/))
