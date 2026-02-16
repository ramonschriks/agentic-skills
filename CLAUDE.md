# Agentic Skills

This repo contains personal Claude Code skills to enhance your product ownership and development workflow.

## Skills

| Skill | Description |
|-------|-------------|
| Product Owner Assistant | Epic/sub-epic creation, requirements structuring, sprint planning, YouTrack MCP integration |
| YouTrack MCP Assistant | YouTrack MCP for progression overviews, issue queries, dependency tracking, and issue management |

## Skills Installation

When the directory structure changes (new skills added, removed, or modified):

1. **Update README.md** - Crawl the skills and update the skills index
2. **Install skills to project** - Create symlinks in `.claude/skills/`:
   ```bash
   mkdir -p .claude/skills
   # Link each skill directory
   ln -s $(pwd)/skill-name .claude/skills/skill-name
   ```
3. **Update .gitignore** - Ensure `.claude/` is gitignored
