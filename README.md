# NocoDB Agent Skills

Agent Skills to help developers using AI agents with NocoDB. Agent Skills are folders of instructions, scripts, and resources that agents like Claude Code, Cursor, Github Copilot, etc... can discover and use to do things more accurately and efficiently.

The skills in this repo follow the [Agent Skills](https://agentskills.io) format.

## Installation

```
npx skills add nocodb/agent-skills
```

## Claude Code Plugin

You can also install the skills in this repo as Claude Code plugins:

```
/plugin marketplace add nocodb/agent-skills
/plugin install nocodb@nocodb-agent-skills
```

## Cursor Plugin

This repo is also a Cursor plugin marketplace (`.cursor-plugin/marketplace.json`). Install **NocoDB** from **Cursor Settings → Plugins**, or run `/add-plugin nocodb` in chat.

## Grok Build Plugin

The same plugin is listed on the [xAI plugin marketplace](https://github.com/xai-org/plugin-marketplace) as **nocodb**, sourced from `plugins/nocodb` in this repo.

## What the plugin contains

`plugins/nocodb/` is one plugin directory read by all three clients:

- `skills/nocodb/` - the NocoDB v3 API skill and its `nocodb.sh` / `nocodb.ps1` scripts
- `.mcp.json` and `mcp.json` - the hosted NocoDB MCP server (`https://app.nocodb.com/mcp`, OAuth)
- `.claude-plugin/`, `.cursor-plugin/`, `.grok-plugin/` - per-client manifests

See [`plugins/nocodb/README.md`](plugins/nocodb/README.md) for details.

## Usage

Skills are automatically available once installed. The agent will use them when relevant tasks are detected.

**Examples:**

```
Create a new NocoDB base for tracking inventory
```

```
Query records from my table where status is active
```

```
Set up the NocoDB API connection
```

## Skill Structure

Each skill follows the [Agent Skills Open Standard](https://agentskills.io):

- `SKILL.md` - Required skill manifest with frontmatter (name, description, metadata)
- `scripts/` - Executable shell scripts

The root `SKILL.md` and `scripts/` serve `npx skills add`; the same skill lives at `plugins/nocodb/skills/nocodb/` for the plugin clients.

## License

MIT
