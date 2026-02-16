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
