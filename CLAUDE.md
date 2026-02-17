# Agentic Skills

**IMPORTANT: Never co-author commits. Do NOT include "Co-Authored-By: Claude" or any co-author lines in commit messages.**

This repo contains personal Claude Code skills to enhance your product ownership and development workflow.

The **Skills Index** is defined in README.md and should be interpreted from there.

## Requirement Writing Guidelines

When working with product requirements:
- **Enhance and rephrase** raw input into properly formatted requirements
- Don't take words literally - convert to professional, well-formed requirements
- Use consistent terminology and structure
- Include clear "What" and "Why" in each requirement
- Format with proper priority indicators (🔴 Must, 🟡 Should, 🟢 Could)

## Session Memory

For tracking progress across sessions:

1. **Memory Directory**: `./memory/` - contains dated session files (gitignored)
2. **File Format**: `DD-MM-YYYY.md` - one file per day
3. **Entry Format**: Each line is a brief contextual progression note
4. **Checkpoint**: When continuing work, ALWAYS read the latest memory file first

**Usage:**
- After each session, add progression lines to today's memory file
- Use brief, contextual lines that capture what was done
- When user says "continue" or starts new work, fetch latest memory file for context

## Skill Updates

When changing workflow outputs or formats:
1. **Always reason** if the skill needs updating
2. Update the skill to reflect the new correct behavior
3. Commit changes to keep skills current

**Example memory entry:**
```
- ZIL-3: Added 7 FRs for Flow/Action Schema
- ZIL-482: Indexed 4 sub-epics with MoSCoW priorities
- Updated YouTrack MCP skill with progression format
```

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
