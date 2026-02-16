# Agentic Skills

A personal collection of Claude Code skills designed to enhance your product ownership and development workflow.

## Skills Index

| Skill | Path | Description |
|-------|------|-------------|
| Product Owner Assistant | [product-owner-assistant/](product-owner-assistant/) | Epic/sub-epic creation, requirements structuring, sprint planning, YouTrack MCP integration |
| YouTrack MCP Assistant | [youtrack-mcp-assistant/](youtrack-mcp-assistant/) | YouTrack MCP for progression overviews, issue queries, dependency tracking, and issue management |

## Usage

Invoke skills directly in conversation:
- `/product-owner-assistant` - Activate PO assistant
- `/youtrack-mcp-assistant` - Activate YouTrack MCP assistant

## Required Setup

### YouTrack MCP

The YouTrack MCP is required for the YouTrack MCP Assistant and Product Owner Assistant skills to function.

**Note:** The MCP configuration is stored in `.claude/settings.json`.

1. Copy `.claude/settings.json` from this project to yours
2. Update the token with your own:

```json
{
  "mcpServers": {
    "youtrack": {
      "type": "http",
      "url": "https://liberta.youtrack.cloud/mcp",
      "headers": {
        "Authorization": "Bearer <your-token-here>"
      }
    }
  }
}
```

3. Generate a new token in YouTrack (Profile Settings > Authentication) and replace `<your-token-here>` with your token
4. Restart Claude Code or reload the project

## Adding New Skills

Add a new skill directory with a `SKILL.md` file following the product-owner-assistant template. Update the index in this README when adding new skills.

## Note

The `.claude/` directory contains local settings and is gitignored. It includes:
- `settings.json` - MCP server configuration
- `skills/` - Symlinks to installed skills
