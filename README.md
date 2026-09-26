# Shoppex for Claude

Run your [Shoppex](https://shoppex.io) store from Claude: products, orders,
customers, coupons, payment links, support tickets, automations and
storefront themes.

The plugin connects Claude to the Shoppex MCP server at
`https://api.shoppex.io/mcp` and adds skills that teach Claude how to change
a live store safely.

## Install

In claude.ai, the desktop app or Cowork: **Customize → Connectors → Shoppex**.

In Claude Code:

```bash
claude plugin marketplace add ShoppexIO/claude-plugin
claude plugin install shoppex@shoppex
```

On first use Claude opens the Shoppex consent page. Sign in, pick the store
and click **Allow Access**.

## What is inside

| Part | Purpose |
|---|---|
| `.mcp.json` | The remote Shoppex MCP server (OAuth, one store per connection) |
| `skills/shoppex-store` | Products, orders, customers, coupons, tickets, analytics |
| `skills/shoppex-automations` | Designing, testing and activating workflows |
| `skills/shoppex-themes` | Editing hosted storefront themes |
| `skills/shoppex-code-storefront` | Building and deploying code storefronts with the CLI |

## Privacy and access

The connection is limited to the store the merchant approves and to the
scopes shown on the consent page. Merchants disconnect it under
**Settings → Developer → API Keys → Connected Apps**. Privacy policy:
https://shoppex.io/privacy.

Documentation: https://docs.shoppex.io/developers/claude

This folder is maintained in the Shoppex monorepo
(`integrations/claude-plugin/`) and mirrored to this repository.
