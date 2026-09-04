# NocoDB

Plugin for Cursor, Grok Build, and Claude Code that connects agents to [NocoDB](https://nocodb.com), the no-code platform for building databases, interfaces, and automations on top of your data. It bundles two components:

- **NocoDB MCP server** (hosted, OAuth): read, create, update, and delete records in a base you authorize.
- **`nocodb` skill**: a CLI over the NocoDB v3 REST API for everything the MCP server does not cover: workspaces, bases, tables, fields, views, links, attachments, filters, sorts, and team management.

## Install

| Client | How |
| --- | --- |
| Cursor | **Cursor Settings → Plugins**, search **NocoDB**, click **Install**. Or run `/add-plugin nocodb` in chat. |
| Grok Build | Install **nocodb** from the xAI plugin marketplace. |
| Claude Code | `/plugin marketplace add nocodb/agent-skills`, then `/plugin install nocodb@nocodb-agent-skills`. |
| Any agent (skill only) | `npx skills add nocodb/agent-skills` |

## MCP

```json
{
  "mcpServers": {
    "nocodb": {
      "type": "http",
      "url": "https://app.nocodb.com/mcp"
    }
  }
}
```

Auth is **OAuth**. On first use you sign in to NocoDB, choose the workspace and base to expose, and decide per tool whether the agent must ask before creating, updating, or deleting records. The token is held by your client; nothing in this plugin stores or forwards it.

### What agents can do through MCP

| Tool | Purpose |
| --- | --- |
| `getBaseInfo`, `getTablesList`, `getTableSchema` | Discover the base, its tables, fields, and views |
| `queryRecords`, `getRecord`, `countRecords`, `aggregate_single` | Filter, fetch, count, and aggregate records (NocoDB `where` syntax) |
| `readAttachment` | Read files attached to a record |
| `createRecords`, `updateRecords`, `deleteRecords` | Write records (asks for permission when configured) |

The MCP server is record-level only. It does not create or alter tables, fields, or views; use the skill for that.

### Self-hosted NocoDB

Point the URL at your own instance, `https://<your-nocodb-host>/mcp`. NocoDB can also issue a token-scoped endpoint per base under **Base settings → MCP**; that endpoint uses the form `https://<host>/mcp/<id>` with an `xc-mcp-token` header. See the [MCP server docs](https://nocodb.com/docs/product-docs/mcp).

## Skill

The `nocodb` skill (`skills/nocodb/SKILL.md`) wraps the v3 REST API in a script the agent runs locally.

- Requires `NOCODB_TOKEN`: an API token from **Team & Settings → API Tokens**.
- Optional `NOCODB_URL`, default `https://app.nocodb.com`; set it for self-hosted instances.
- Linux/macOS: `scripts/nocodb.sh` needs `curl` and `jq`. Windows: `scripts/nocodb.ps1` needs PowerShell 5.1+.

```bash
skills/nocodb/scripts/nocodb.sh base:list <workspace>
skills/nocodb/scripts/nocodb.sh table:create <base> '{"title":"Customers"}'
skills/nocodb/scripts/nocodb.sh record:list <base> <table>
```

The full command reference lives in `SKILL.md`.

## Network endpoints and credentials

- `https://app.nocodb.com/mcp` (or your self-hosted host): MCP over streamable HTTP, OAuth 2.1 with PKCE. Scoped to the workspace and base you pick at authorization.
- `https://app.nocodb.com/api/v3/*` (or `NOCODB_URL`): called by the skill scripts with your `xc-token` API token.
- No other endpoints, no telemetry, no hooks, and nothing runs at install time.

## Docs

- MCP server: https://nocodb.com/docs/product-docs/mcp
- REST API v3: https://nocodb.com/docs/api-docs
- Community: https://community.nocodb.com

## License

MIT. NocoDB and the NocoDB logo are trademarks of NocoDB Inc.
