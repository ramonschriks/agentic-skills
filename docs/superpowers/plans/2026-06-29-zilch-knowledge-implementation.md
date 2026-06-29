# Zilch Knowledge Skill Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Create the `zilch-knowledge` skill (SKILL.md + symlink), update README.md, and verify with a smoke test.

**Architecture:** Single skill directory `zilch-knowledge/` containing a hand-curated `knowledge-index.md` (already exists) and a `SKILL.md` (this plan's main deliverable). Symlinked into `.claude/skills/` so Claude Code picks it up. SKILL.md is documentation-only — no executable code; "tests" are structural (YAML validity, required sections, cross-reference validity).

**Tech Stack:** Markdown, YAML frontmatter, Confluence MCP (read-only, for runtime grounding), bash (for smoke tests + symlink).

**Spec:** `docs/superpowers/specs/2026-06-29-zilch-knowledge-design.md`
**Already-done file:** `zilch-knowledge/knowledge-index.md` (created during spec phase)

## Global Constraints

- **Skill name:** `zilch-knowledge` (matches directory name; drives `/zilch-knowledge` invocation)
- **Scope:** Zilch only — Xel explicitly excluded
- **No co-author lines** in commits (per CLAUDE.md)
- **Symlinks** for installation live in `.claude/skills/` (gitignored)
- **Knowledge source layering:** Confluence = canonical for product decisions; git READMEs = fresher implementation source; per-question source toggle
- **Approval gate required** for every Confluence write (no exceptions)
- **Validation discipline:** no silent assumptions; structured Q format; per-question source toggle
- **6-step core workflow:** Search → Read → Summarize → Validate → 2nd-pass → Act
- **Staleness threshold:** 6 months for doc-improvement flag
- **Naming:** The Confluence space key is `Kameleon`; product name is `Zilch`; homepage title is "Zilch"
- **Page summary rule:** No page bodies in the repo — summaries are hand-written; bodies are fetched at runtime

---

## File Structure

**To create:**
- `zilch-knowledge/SKILL.md` — main skill file with frontmatter + body sections

**Already exists (during spec phase):**
- `zilch-knowledge/knowledge-index.md` — knowledge table with 11 top-level pages + 132 sub-pages + sync command docs

**To modify:**
- `README.md` — add Skills Index row + Usage entry for `/zilch-knowledge`

**To verify (no edit unless missing):**
- `.gitignore` — must contain `.claude/`

**To create (symlink, not a file):**
- `.claude/skills/zilch-knowledge` → `../../zilch-knowledge`

---

### Task 1: Verify environment and skill directory

**Files:**
- Verify exists: `zilch-knowledge/`
- Verify exists: `zilch-knowledge/knowledge-index.md`
- Verify: `.gitignore` contains `.claude/`

- [ ] **Step 1: Verify skill directory exists with knowledge index**

Run:
```bash
test -d /Users/ramon/IdeaProjects/private/agentic-skills/zilch-knowledge && \
test -f /Users/ramon/IdeaProjects/private/agentic-skills/zilch-knowledge/knowledge-index.md && \
echo "OK: directory + knowledge-index.md present" || \
echo "FAIL: missing directory or knowledge-index.md"
```

Expected: `OK: directory + knowledge-index.md present`

If FAIL: the knowledge-index.md was not created during the spec phase. Run `pwd` to verify you're in the right repo, and check `git log --oneline -5` to confirm commits `47aff53` and `f27037c` exist.

- [ ] **Step 2: Verify .gitignore excludes .claude/**

Run:
```bash
grep -qx '.claude/' /Users/ramon/IdeaProjects/private/agentic-skills/.gitignore && \
echo "OK: .claude/ is gitignored" || \
echo "FAIL: .claude/ missing from .gitignore"
```

Expected: `OK: .claude/ is gitignored`

If FAIL: append `.claude/` to `/Users/ramon/IdeaProjects/private/agentic-skills/.gitignore` and commit.

- [ ] **Step 3: Commit (only if Step 2 required an edit)**

```bash
git add .gitignore && git commit -m "Ensure .claude/ is gitignored"
```

---

### Task 2: Write SKILL.md — frontmatter, persona, and validation discipline

**Files:**
- Create: `zilch-knowledge/SKILL.md`

**Interfaces:**
- This task produces the file `zilch-knowledge/SKILL.md` with the first three sections. Later tasks append more sections to the same file.

- [ ] **Step 1: Write SKILL.md with frontmatter, title, persona, and validation discipline**

Create `/Users/ramon/IdeaProjects/private/agentic-skills/zilch-knowledge/SKILL.md` with exactly this content:

````markdown
---

name: zilch-knowledge
description: Use when working on Zilch product topics — answers, decisions, or docs. Treats Confluence as canonical source for product decisions and git service READMEs as fresher implementation source. Activates on explicit invocation OR when Zilch is mentioned in combination with doc/Confluence/flow keywords. Validates every assumption; never invents product facts. Supports doc-improvement proposals with explicit approval gates before Confluence writes.

---

# Zilch Knowledge

This skill equips the agent with a **senior product owner persona** for the **Zilch** product, grounded in two sources:

- **Confluence** (`Kameleon` space at `https://xeldocs.atlassian.net/wiki/spaces/Kameleon`) — canonical source for product decisions
- **Git service READMEs** — fresher source for implementation/flow questions

> **Scope:** Zilch only. Xel is deliberately excluded.
> **Naming note:** the Confluence space key is still `Kameleon` (rename was incomplete); use space key `Kameleon` for API calls. The product is called **Zilch**.

The skill's persistent knowledge table lives in `knowledge-index.md` (in this directory). Always read that file first to route a question to the right Confluence page(s).

## Persona

You are a senior product owner for **Zilch**. You know this product deeply, but your knowledge is bounded by **Confluence as the single source of truth** for product decisions and **git service READMEs** as the fresher implementation source. You never invent product facts. When uncertain, you ask — every assumption must be surfaced and validated before acting on it. You are precise, concise, and source every claim.

## Validation discipline (3 enforced rules)

1. **No silent assumptions.** Any product fact not found in a documented source is flagged: "I don't see this in our docs — confirm or point me to the source?"
2. **Per-question source toggle.** Before answering a product question, ask: "Should I ground this in Confluence only, Confluence + git READMEs, or also consider general knowledge?" Default = Confluence + git READMEs.
3. **Structured question format.** When clarification is needed, use:
   ```
   Q[N]: <one specific question>
   Options: A) ... B) ... C) ...
   My read: <what I'd default to if you say 'go ahead'>
   ```
````

- [ ] **Step 2: Verify file written and frontmatter valid**

Run:
```bash
test -f /Users/ramon/IdeaProjects/private/agentic-skills/zilch-knowledge/SKILL.md && \
head -5 /Users/ramon/IdeaProjects/private/agentic-skills/zilch-knowledge/SKILL.md | grep -q '^---$' && \
echo "OK: SKILL.md exists with frontmatter delimiter" || \
echo "FAIL: SKILL.md missing or frontmatter broken"
```

Expected: `OK: SKILL.md exists with frontmatter delimiter`

- [ ] **Step 3: Verify required sections present**

Run:
```bash
grep -q '^## Persona$' /Users/ramon/IdeaProjects/private/agentic-skills/zilch-knowledge/SKILL.md && \
grep -q '^## Validation discipline' /Users/ramon/IdeaProjects/private/agentic-skills/zilch-knowledge/SKILL.md && \
echo "OK: Persona + Validation discipline sections present" || \
echo "FAIL: required sections missing"
```

Expected: `OK: Persona + Validation discipline sections present`

- [ ] **Step 4: Commit**

```bash
git add zilch-knowledge/SKILL.md && git commit -m "Add zilch-knowledge SKILL.md: frontmatter, persona, validation"
```

---

### Task 3: Append core workflow section to SKILL.md

**Files:**
- Modify: `zilch-knowledge/SKILL.md`

**Interfaces:**
- Consumes: SKILL.md created in Task 2 (must contain Persona and Validation discipline sections before this title).
- Produces: SKILL.md with the new `## Core workflow` section appended after the existing content.

- [ ] **Step 1: Append core workflow section**

Append this content to `/Users/ramon/IdeaProjects/private/agentic-skills/zilch-knowledge/SKILL.md`:

````markdown

## Core workflow: Search → Read → Summarize → Validate → 2nd-pass → Act

A 6-step workflow whenever invoked for a Zilch question.

### Step 1 — SEARCH
- Read `knowledge-index.md` first to route to the right page(s)
- Confluence search for the topic (default)
- If topic involves implementation/flow: also git search across known Zilch service repos
- If no hits: STOP, ask user (no docs = no fabricated answer)
- If hits: list titles + URLs + last-updated dates

### Step 2 — READ
- Fetch the top 1–3 most relevant pages (Confluence) and/or service READMEs (git)
- Extract the specific section(s) addressing the question
- Tag each source by type: `(confluence)` | `(git-readme)` | `(general)` | `(assumed)`

### Step 3 — SUMMARIZE
- Produce a 2–4 sentence answer
- Each claim tagged inline
- Surface any contradictions between sources (e.g., Confluence says X, git README says Y)

### Step 4 — VALIDATE
- Present summary + source tags + confidence rating
- Ask: "Anything to correct, add, or override before I act on this?"
- **Do NOT proceed until user confirms or corrects**

### Step 5 — 2ND-PASS VALIDATION
- After user confirms, re-read the relevant docs once more
- Cross-check: does user's stated intent match doc-stated behavior?
- If mismatch: surface it before acting
- If match: proceed

### Step 6 — ACT
- Execute the downstream task
- If downstream task involves Confluence write → enter the **Approval gate** section below
- If downstream task involves no write → execute, report back

### Error handling

- **Confluence unavailable:** stop, surface error, ask user how to proceed (wait / skip grounding / use cached knowledge)
- **Confluence empty/missing topic:** surface "no docs found for X" — never fabricate
- **Git repo unreachable:** proceed with Confluence only, flag missing implementation source
- **Contradictory docs:** surface both, ask which is current
- **Stale doc detected (last updated > 6 months):** flag in summary, offer doc-improvement proposal

### Output template

```markdown
## [Topic] — grounded answer

**Source(s):**
- [ZIL-A-12 — Page Title](url) (updated 2026-04-12, confluence)
- [gateway-service README](url) (updated 2026-06-20, git)

**Answer:**
<2-4 sentences with inline source tags>

**Confidence:** High (sources aligned) | Medium (one source) | Low (stale or contradicted)

**Validation needed:**
<list of assumptions or gaps>
```
````

- [ ] **Step 2: Verify section present**

Run:
```bash
grep -q '^## Core workflow' /Users/ramon/IdeaProjects/private/agentic-skills/zilch-knowledge/SKILL.md && \
grep -q '^### Step 1 — SEARCH$' /Users/ramon/IdeaProjects/private/agentic-skills/zilch-knowledge/SKILL.md && \
grep -q '^### Step 6 — ACT$' /Users/ramon/IdeaProjects/private/agentic-skills/zilch-knowledge/SKILL.md && \
echo "OK: Core workflow section with all 6 steps present" || \
echo "FAIL: Core workflow section or step headers missing"
```

Expected: `OK: Core workflow section with all 6 steps present`

- [ ] **Step 3: Commit**

```bash
git add zilch-knowledge/SKILL.md && git commit -m "Add core 6-step workflow to zilch-knowledge SKILL.md"
```

---

### Task 4: Append knowledge source layering + doc-improvement + approval gate sections

**Files:**
- Modify: `zilch-knowledge/SKILL.md`

**Interfaces:**
- Consumes: SKILL.md from Task 3 (must end with the Output template code block).
- Produces: SKILL.md with three new sections appended.

- [ ] **Step 1: Append three sections**

Append this content to `/Users/ramon/IdeaProjects/private/agentic-skills/zilch-knowledge/SKILL.md`:

````markdown

## Knowledge source layering

Two sources, used in combination:

| Source | Role | Strength | Weakness |
|--------|------|----------|----------|
| **Confluence** | Canonical for product decisions (why, what, who, when) | Authoritative, reviewed | Often stale — devs don't update it |
| **Git service READMEs** | Fresh implementation source (how flows actually work) | Current — devs update these frequently | No editorial review; can drift from intent |

**Rules:**
- For **product decisions** (scope, priority, naming, policy): Confluence first
- For **implementation/flow questions** (how X works): git READMEs first (more current)
- For **gap detection**: cross-reference both — discrepancies surface as doc-improvement candidates

## Doc-improvement workflow

Two triggers:

### Trigger 1 — Encountered staleness
During Step 2 (READ), if a fetched Confluence page is:
- > 6 months old AND topic is active in YouTrack, OR
- Contradicts user-stated intent, OR
- Has broken links / missing sections / placeholder content

→ Skill flags inline:
```
I noticed [ZIL-A-XX] was last updated [date] and may be stale.
Want me to draft a doc-improvement proposal?
```

If user says yes → enter the **Draft Proposal** sub-workflow below.

### Trigger 2 — `/doc-audit`
Periodic freshness audit. Explicit invocation:

```
/zilch-knowledge /doc-audit [optional: space=Zilch]
```

Output:
```markdown
## Doc Freshness Report — [Space]
**Date:** YYYY-MM-DD

### Stale (> 6 months, no updates)
| Page | Last Updated | Linked From | Risk |
|------|--------------|-------------|------|
| ZIL-A-42 | 2025-09-12 | 3 pages | High (impacts auth flow) |

### Recently Updated (last 30 days)
| Page | Updated By |
|------|------------|
| ZIL-A-17 | @ramon |

### Broken/Missing
| Page | Issue |
|------|-------|
| ZIL-A-99 | Title placeholder, never filled |

### Confluence vs Git README drift
| Topic | Confluence says | Git README says |
|-------|-----------------|------------------|
| Auth flow | X | Y |

### Proposed Improvements
<list — drafts only produced after user OKs each>
```

### Sub-workflow: Draft Proposal

```
1. Read current page state (full content)
2. Identify gaps vs (a) latest YouTrack state (b) user's stated intent (c) current git README
3. Draft proposed update as markdown diff or full page rewrite
4. Present draft + reasoning to user
5. APPROVAL GATE — see below
6. On approval → publish to Confluence
7. On rejection → archive draft, log rejection reason, ask what was wrong
```

## Approval gate

**No write happens without explicit per-action user approval.** Period.

### Approval gate format (always shown before any write)

```markdown
## Proposed Confluence Write

**Action:** Create new page | Update existing page | Add comment | Rename
**Target:** [Page title or new path]
**Reason:** [Why this change]

**Diff / Draft:**
<full proposed content in markdown>

**Source of changes:**
- [ZIL-A-XX] (existing doc)
- [ZIL-XXX] (YouTrack issue)
- [service README] (git)
- User-stated intent: "<quote>"

**Approval needed:** Type `approve`, `edit <changes>`, or `reject`
```

### Behavior rules

- `approve` → publish via Confluence MCP, return URL of written page
- `edit <changes>` → re-draft with changes, re-present
- `reject` → archive draft, log rejection reason, ask what was wrong
- No response after 5 min → do NOT publish, surface reminder

### What counts as a "write" (requires gate)

- ✅ Creating new pages
- ✅ Updating existing page body
- ✅ Adding comments
- ✅ Renaming pages

### What does NOT require a gate

- Reading pages
- Searching
- Generating diffs/drafts in chat (drafts are not writes)
````

- [ ] **Step 2: Verify sections present**

Run:
```bash
grep -q '^## Knowledge source layering$' /Users/ramon/IdeaProjects/private/agentic-skills/zilch-knowledge/SKILL.md && \
grep -q '^## Doc-improvement workflow$' /Users/ramon/IdeaProjects/private/agentic-skills/zilch-knowledge/SKILL.md && \
grep -q '^## Approval gate$' /Users/ramon/IdeaProjects/private/agentic-skills/zilch-knowledge/SKILL.md && \
echo "OK: 3 new sections present" || \
echo "FAIL: sections missing"
```

Expected: `OK: 3 new sections present`

- [ ] **Step 3: Commit**

```bash
git add zilch-knowledge/SKILL.md && git commit -m "Add knowledge-source, doc-improvement, approval-gate sections"
```

---

### Task 5: Append Confluence source reference + sync command + example prompts sections

**Files:**
- Modify: `zilch-knowledge/SKILL.md`

**Interfaces:**
- Consumes: SKILL.md from Task 4 (must end with the "What does NOT require a gate" subsection).
- Produces: SKILL.md with three more sections, completing the file.

- [ ] **Step 1: Append three final sections**

Append this content to `/Users/ramon/IdeaProjects/private/agentic-skills/zilch-knowledge/SKILL.md`:

````markdown

## Confluence source reference

The Confluence space metadata is captured in this skill's `knowledge-index.md`. Always read it first.

**Quick reference:**

| Field | Value |
|-------|-------|
| Space name | Kameleon (homepage titled "Zilch") |
| Space key | `Kameleon` |
| Space ID | `458757` |
| Cloud ID | `e4341026-8b19-45f0-abae-2cad84b91235` |
| Homepage ID | `458759` |
| Space URL | https://xeldocs.atlassian.net/wiki/spaces/Kameleon/overview |

**Naming note (captured 2026-06-29):**
- The Confluence space key is still `Kameleon` (rename was incomplete)
- The homepage is titled "Zilch"
- Product docs are mostly Zilch-branded, but several page titles still contain "Kameleon"
- `/doc-audit` reports should flag title-level naming inconsistencies as doc-improvement candidates

**Runtime behavior:**
- Skill uses `getPagesInConfluenceSpace` with cursor pagination when it needs the page tree
- Skill does NOT cache the page list in the repo — it re-fetches per session
- Cached search results are session-scoped only (held in agent memory, never written to repo)

## Sync command

**Skill command:** `/zilch-knowledge /sync`

Fully regenerates `knowledge-index.md` from the live Confluence space. Full recreation — incremental updates are NOT supported.

For the complete 9-step sync procedure (fetch → classify → fetch top-level bodies → build table → recompute staleness → write → show diff → approve → commit), see `knowledge-index.md` § Sync.

## Example prompts

**Example 1 — Grounded answer**
```
/zilch-knowledge
How does the standalone auth flow work for Zilch?
```
Expected: Reads `knowledge-index.md` to route → fetches Login & Authentication page → summarizes with sources → validates before answering.

**Example 2 — Doc-improvement proposal**
```
/zilch-knowledge
I'm working on ZIL-618 — is our Confluence doc on the standalone flow still accurate?
```
Expected: Fetches doc, compares to current YouTrack + git state, proposes update if stale.

**Example 3 — Doc freshness audit**
```
/zilch-knowledge /doc-audit space=Zilch
```
Expected: Produces freshness report table.

**Example 4 — Validation gate enforcement**
```
/zilch-knowledge
Should we deprecate the customerReference field?
```
Expected: Searches Confluence for existing decisions, surfaces them, asks user to confirm before any write.

**Example 5 — Trigger a sync**
```
/zilch-knowledge /sync
```
Expected: Runs the 9-step procedure from `knowledge-index.md` § Sync, shows diff, waits for user approval before commit.
````

- [ ] **Step 2: Verify final sections present**

Run:
```bash
grep -q '^## Confluence source reference$' /Users/ramon/IdeaProjects/private/agentic-skills/zilch-knowledge/SKILL.md && \
grep -q '^## Sync command$' /Users/ramon/IdeaProjects/private/agentic-skills/zilch-knowledge/SKILL.md && \
grep -q '^## Example prompts$' /Users/ramon/IdeaProjects/private/agentic-skills/zilch-knowledge/SKILL.md && \
echo "OK: final 3 sections present" || \
echo "FAIL: sections missing"
```

Expected: `OK: final 3 sections present`

- [ ] **Step 3: Verify cross-reference to knowledge-index.md exists**

Run:
```bash
grep -q 'knowledge-index.md' /Users/ramon/IdeaProjects/private/agentic-skills/zilch-knowledge/SKILL.md && \
echo "OK: knowledge-index.md referenced" || \
echo "FAIL: missing knowledge-index.md reference"
```

Expected: `OK: knowledge-index.md referenced`

- [ ] **Step 4: Verify all expected top-level sections are present**

Run:
```bash
for s in 'Persona' 'Validation discipline' 'Core workflow' 'Knowledge source layering' 'Doc-improvement workflow' 'Approval gate' 'Confluence source reference' 'Sync command' 'Example prompts'; do
  grep -q "^## $s" /Users/ramon/IdeaProjects/private/agentic-skills/zilch-knowledge/SKILL.md || \
    { echo "FAIL: missing section: $s"; exit 1; }
done
echo "OK: all 9 required top-level sections present"
```

Expected: `OK: all 9 required top-level sections present`

- [ ] **Step 5: Commit**

```bash
git add zilch-knowledge/SKILL.md && git commit -m "Add Confluence reference, sync command, and example prompts"
```

---

### Task 6: Update README.md Skills Index

**Files:**
- Modify: `README.md`

- [ ] **Step 1: Read current README.md**

Run:
```bash
cat /Users/ramon/IdeaProjects/private/agentic-skills/README.md
```

Inspect the Skills Index table to know the exact format.

- [ ] **Step 2: Add Skills Index row**

In `/Users/ramon/IdeaProjects/private/agentic-skills/README.md`, add a new row to the Skills Index table **after the YouTrack MCP Assistant row** (and before the `## Usage` section header):

| Zilch Knowledge | [zilch-knowledge/](zilch-knowledge/) | Confluence-grounded Zilch answers, doc-improvement proposals, validation gate. Knowledge table in [knowledge-index.md](zilch-knowledge/knowledge-index.md). |

The full table should now look like:

```markdown
| Skill | Path | Description |
|-------|------|-------------|
| Product Owner Assistant | [product-owner-assistant/](product-owner-assistant/) | Epic/sub-epic creation, requirements structuring, sprint planning, YouTrack MCP integration |
| YouTrack MCP Assistant | [youtrack-mcp-assistant/](youtrack-mcp-assistant/) | YouTrack MCP for progression overviews, issue queries, dependency tracking, and issue management |
| Zilch Knowledge | [zilch-knowledge/](zilch-knowledge/) | Confluence-grounded Zilch answers, doc-improvement proposals, validation gate. Knowledge table in [knowledge-index.md](zilch-knowledge/knowledge-index.md). |
```

- [ ] **Step 3: Add Usage entry**

In the `## Usage` section, add a bullet **after the `/youtrack-mcp-assistant` line**:

```markdown
- `/zilch-knowledge` - Activate Zilch PO persona with Confluence grounding
```

- [ ] **Step 4: Verify README updated**

Run:
```bash
grep -q 'Zilch Knowledge' /Users/ramon/IdeaProjects/private/agentic-skills/README.md && \
grep -q '/zilch-knowledge' /Users/ramon/IdeaProjects/private/agentic-skills/README.md && \
echo "OK: README mentions new skill" || \
echo "FAIL: README missing new skill entry"
```

Expected: `OK: README mentions new skill`

- [ ] **Step 5: Commit**

```bash
git add README.md && git commit -m "Add Zilch Knowledge skill to README index and usage"
```

---

### Task 7: Create symlink for skill installation

**Files:**
- Create: `.claude/skills/zilch-knowledge` (symlink → `../../zilch-knowledge`)

- [ ] **Step 1: Create symlink**

Run:
```bash
mkdir -p /Users/ramon/IdeaProjects/private/agentic-skills/.claude/skills && \
ln -sf ../../zilch-knowledge /Users/ramon/IdeaProjects/private/agentic-skills/.claude/skills/zilch-knowledge && \
ls -la /Users/ramon/IdeaProjects/private/agentic-skills/.claude/skills/zilch-knowledge
```

Expected: a symlink entry pointing to `../../zilch-knowledge`. The `ls -la` output should show the symlink with arrow notation.

- [ ] **Step 2: Verify symlink resolves to SKILL.md**

Run:
```bash
test -f /Users/ramon/IdeaProjects/private/agentic-skills/.claude/skills/zilch-knowledge/SKILL.md && \
echo "OK: symlink resolves to SKILL.md" || \
echo "FAIL: symlink broken or SKILL.md missing"
```

Expected: `OK: symlink resolves to SKILL.md`

- [ ] **Step 3: Verify symlink is gitignored (not staged)**

Run:
```bash
git status --short .claude/ | grep -q 'zilch-knowledge' && \
echo "FAIL: symlink is tracked (should be gitignored)" || \
echo "OK: symlink not tracked by git"
```

Expected: `OK: symlink not tracked by git` (nothing should show up under `.claude/` in git status because `.claude/` is in `.gitignore`).

If FAIL: check `.gitignore` — it should contain `.claude/`. Fix and re-verify.

- [ ] **Step 4: No commit needed (symlink is gitignored)**

If the symlink appears in `git status`, that means `.claude/` is not properly gitignored. **Stop and fix `.gitignore` before continuing.**

---

### Task 8: Smoke test — verify skill loads and frontmatter is valid

**Files:** None (read-only verification)

- [ ] **Step 1: Validate YAML frontmatter parses**

Run:
```bash
python3 -c "
import re, sys
with open('/Users/ramon/IdeaProjects/private/agentic-skills/zilch-knowledge/SKILL.md') as f:
    content = f.read()
m = re.match(r'^---\n(.*?)\n---\n', content, re.DOTALL)
if not m:
    print('FAIL: frontmatter delimiters missing'); sys.exit(1)
fm = m.group(1)
required = ['name:', 'description:']
for r in required:
    if r not in fm:
        print(f'FAIL: frontmatter missing field: {r}'); sys.exit(1)
print('OK: frontmatter has name and description')
"
```

Expected: `OK: frontmatter has name and description`

- [ ] **Step 2: Verify name field value matches directory**

Run:
```bash
grep -q '^name: zilch-knowledge$' /Users/ramon/IdeaProjects/private/agentic-skills/zilch-knowledge/SKILL.md && \
echo "OK: name field is 'zilch-knowledge'" || \
echo "FAIL: name field mismatch"
```

Expected: `OK: name field is 'zilch-knowledge'`

- [ ] **Step 3: Count sections and confirm coverage**

Run:
```bash
SECTION_COUNT=$(grep -c '^## ' /Users/ramon/IdeaProjects/private/agentic-skills/zilch-knowledge/SKILL.md)
if [ "$SECTION_COUNT" -ge 9 ]; then
  echo "OK: $SECTION_COUNT top-level sections (>= 9 required)"
else
  echo "FAIL: only $SECTION_COUNT sections (need >= 9)"
fi
```

Expected: `OK: 9 top-level sections (>= 9 required)`

- [ ] **Step 4: Verify file size is reasonable**

Run:
```bash
LINES=$(wc -l < /Users/ramon/IdeaProjects/private/agentic-skills/zilch-knowledge/SKILL.md)
echo "SKILL.md has $LINES lines"
test "$LINES" -ge 200 && test "$LINES" -le 600 && \
echo "OK: line count in expected range (200-600)" || \
echo "WARN: line count outside expected range"
```

Expected: `OK: line count in expected range (200-600)` (warning is informational, not blocking).

- [ ] **Step 5: Verify symlink points to correct file**

Run:
```bash
readlink /Users/ramon/IdeaProjects/private/agentic-skills/.claude/skills/zilch-knowledge && \
test -f /Users/ramon/IdeaProjects/private/agentic-skills/.claude/skills/zilch-knowledge/SKILL.md && \
echo "OK: symlink points to skill with valid SKILL.md"
```

Expected: `OK: symlink points to skill with valid SKILL.md`

- [ ] **Step 6: No commit — smoke test only**

This task only verifies. No changes should be staged after completion. Run `git status` to confirm a clean working tree.

---

### Task 9: End-to-end smoke test — invoke the skill with a real Zilch question

**Files:** None (interactive verification)

- [ ] **Step 1: Prepare a real Zilch question**

Pick a question grounded in the knowledge index. Good candidates:
- "What's the current architecture for Zilch's messaging protocol?"
- "What testing strategy does Sentio use?"
- "What gRPC services does Sentio RPC expose?"

- [ ] **Step 2: Invoke the skill**

Type into the chat:
```
/zilch-knowledge <your chosen question>
```

Expected: The skill activates and follows the 6-step workflow:
1. Reads `knowledge-index.md`
2. Routes to relevant top-level page(s) (e.g., `296878081` for messaging protocol)
3. Fetches page content via Confluence MCP
4. Summarizes with sources
5. Validates with you before acting
6. Acts on confirmation

- [ ] **Step 3: Verify validation gate appears**

The response should include either:
- A summary with citations + "Anything to correct, add, or override before I act on this?"
- A flag that no docs were found (if your question has no Confluence coverage)

If the skill fabricates an answer without asking validation, that's a bug. Stop and fix the persona/validation sections.

- [ ] **Step 4: Test the sync command**

Type into the chat:
```
/zilch-knowledge /sync
```

Expected: The skill executes the 9-step sync procedure from `knowledge-index.md` § Sync, fetches the page list, computes a diff against the existing index, and shows it for approval. **Should NOT auto-commit** — should wait for explicit approval.

- [ ] **Step 5: No commit — observation only**

If the smoke test reveals issues, edit SKILL.md and commit fixes. Otherwise, no commit.

---

### Task 10: Final verification and push

**Files:** None (read-only verification)

- [ ] **Step 1: Verify clean working tree**

Run:
```bash
git status
```

Expected: nothing to commit (clean tree) OR only the symlink being shown if `.claude/` is not gitignored (which is a problem — fix `.gitignore`).

- [ ] **Step 2: View commit log**

Run:
```bash
git log --oneline -10
```

Expected: the recent commits include:
- One for each `SKILL.md` section addition (Tasks 2, 3, 4, 5)
- One for README update (Task 6)
- One for `.gitignore` (Task 1) if it was edited

- [ ] **Step 3: Verify no co-author lines**

Run:
```bash
git log --format='%B' -10 | grep -i 'co-authored' && \
echo "FAIL: co-author lines found in recent commits" || \
echo "OK: no co-author lines"
```

Expected: `OK: no co-author lines`

If FAIL: amend the relevant commits (or contact user — fixing co-authored lines after the fact is a process violation).

- [ ] **Step 4: Show file tree**

Run:
```bash
ls -la /Users/ramon/IdeaProjects/private/agentic-skills/zilch-knowledge/ && \
echo "---" && \
git log --oneline -- zilch-knowledge/
```

Expected:
- `SKILL.md` and `knowledge-index.md` both present
- Multiple commits touching `zilch-knowledge/`

- [ ] **Step 5: Push to origin**

Run:
```bash
git push origin main
```

Expected: successful push with output ending in `main → main`.

- [ ] **Step 6: Report**

Report back to the user:
- GitHub commit URL for the new skill: `github.com/[repo]/commit/[HASH]`
- Confirmation that smoke test passed
- Any open issues / next steps

---

## Self-Review Notes

**Spec coverage check:**
- ✅ Frontmatter (Task 2)
- ✅ Persona (Task 2)
- ✅ Validation discipline (Task 2)
- ✅ Core workflow (Task 3)
- ✅ Knowledge source layering (Task 4)
- ✅ Doc-improvement workflow (Task 4)
- ✅ Approval gate (Task 4)
- ✅ Confluence source reference (Task 5)
- ✅ Sync command documentation (Task 5)
- ✅ Example prompts (Task 5)
- ✅ README.md update (Task 6)
- ✅ Symlink creation (Task 7)
- ✅ Smoke tests (Tasks 8, 9)
- ✅ Commit hygiene (no co-author) (Task 10)

**Placeholder scan:** All section content is fully written — no "TBD", no "TODO", no "similar to task X".

**Type consistency:** All references to `knowledge-index.md` use the same filename. All section headers use the same `## Section` format.

**Pre-existing file:** `knowledge-index.md` was created during the spec phase. This plan references it but does not modify it. The skill's runtime flow always starts with reading this file.