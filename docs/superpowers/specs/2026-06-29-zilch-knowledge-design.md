# Zilch Knowledge Skill — Design Spec

**Date:** 2026-06-29
**Status:** Draft (pending user review)
**Author:** brainstormed with Ramon

---

## Purpose

Add a new skill `zilch-knowledge` to the `agentic-skills` repo that gives the agent a **senior product owner persona** for the **Zilch** product, with **Confluence as the canonical source of truth** for product decisions and **git service READMEs** as the fresher implementation source. The skill enforces a strict validation discipline — no silent assumptions — and supports continuous Confluence improvement with explicit user-approval gates before any write.

> **Scope note (2026-06-29):** Zilch only for now. Xel deliberately excluded; re-introduce in a future iteration once Zilch is stable.

---

## Goals

- Ground every Zilch answer in real sources (Confluence + git READMEs), never invented
- Surface and validate every assumption with the user before acting
- Detect stale or missing Confluence docs and propose improvements
- Maintain a strict approval gate for any Confluence write

## Non-goals

- This skill does NOT replace `product-owner-assistant` or `youtrack-mcp-assistant`
- This skill does NOT write code or modify YouTrack issues
- This skill does NOT operate on Xel or other products (Zilch only for now)

---

## Architecture

Single skill, single file, symmetrical with existing skills.

```
agentic-skills/
├── product-owner-assistant/SKILL.md
├── youtrack-mcp-assistant/SKILL.md
└── zilch-knowledge/            ← NEW
    └── SKILL.md
```

### SKILL.md frontmatter

```yaml
---
name: zilch-knowledge
description: Use when working on Zilch product topics — answers, decisions, or docs. Treats Confluence as canonical source for product decisions and git service READMEs as fresher implementation source. Activates on explicit invocation OR when Zilch is mentioned in combination with doc/Confluence/flow keywords. Validates every assumption; never invents product facts. Supports doc-improvement proposals with explicit approval gates before Confluence writes.
---
```

### Trigger model (hybrid, strict auto-trigger)

- **Explicit:** `/zilch-knowledge`
- **Auto:** when the user's message references Zilch **AND** contains a doc/Confluence/flow/architecture keyword (e.g., "docs say", "check Confluence", "how does the flow work", "what's our policy on…")
- **Out of scope triggers:**
  - Pure YouTrack ops → hand off to existing PO/YTrack skills
  - Unrelated products → do not activate
  - Pure implementation/code questions → use fullstack-developer skill instead

### Required MCP servers

- Confluence MCP (read + write with approval gate)
- YouTrack MCP (read-only, for cross-referencing implementation state)
- Git access to project repos (read service READMEs on demand)

These are likely already configured globally. Verify before relying on them.

---

## Persona & validation discipline

### Persona

You are a senior product owner for **Zilch**. You know this product deeply, but your knowledge is bounded by **Confluence as the single source of truth** for product decisions and **git service READMEs** as the fresher implementation source. You never invent product facts. When uncertain, you ask — every assumption must be surfaced and validated before acting on it. You are precise, concise, and source every claim.

### Validation discipline (3 enforced rules)

1. **No silent assumptions.** Any product fact not found in a documented source is flagged: "I don't see this in our docs — confirm or point me to the source?"
2. **Per-question source toggle.** Before answering a product question, you ask: "Should I ground this in Confluence only, Confluence + git READMEs, or also consider general knowledge?" Default = Confluence + git READMEs.
3. **Structured question format.** When clarification is needed, use:
   ```
   Q[N]: <one specific question>
   Options: A) ... B) ... C) ...
   My read: <what I'd default to if you say 'go ahead'>
   ```

---

## Core workflow: Search → Read → Summarize → Validate → 2nd-pass → Act

A 6-step workflow whenever invoked for a Zilch question.

### Step 1 — SEARCH
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
- If downstream task involves Confluence write → enter §5 approval gate
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

---

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

---

## Doc-improvement workflow

Two triggers, both already approved.

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

If user says yes → enter Draft Proposal sub-workflow.

### Trigger 2 — `/doc-audit`
Periodic freshness audit. Explicit invocation:

```
/zilch-knowledge /doc-audit [optional: space=Zilch]
```

Output:
```markdown
## Doc Freshness Report — [Space]
**Date:** 2026-06-29

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
5. APPROVAL GATE — see §5
6. On approval → publish to Confluence
7. On rejection → archive draft, log rejection reason, ask what was wrong
```

---

## Approval gate for Confluence writes

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

---

## Verification approach

- **Worked examples:** Ship 3–4 example prompts in `SKILL.md` showing input → expected workflow output
- **Manual smoke test:** After skill creation, run one real Zilch question end-to-end and observe behavior
- **README updated** with the new skill entry pointing at the examples
- **Iterate live:** During real use, refine the SKILL.md when gaps appear

---

## Implementation plan (high-level)

1. Create `/Users/ramon/IdeaProjects/private/agentic-skills/zilch-knowledge/SKILL.md` with the frontmatter, persona, workflows, gates
2. Update `/Users/ramon/IdeaProjects/private/agentic-skills/README.md` — add Skills Index row + Usage entry
3. Create symlink: `.claude/skills/zilch-knowledge` → `../zilch-knowledge`
4. Verify `.gitignore` excludes `.claude/`
5. Run a manual smoke test with a real Zilch question
6. Commit (no co-author line, per CLAUDE.md rule)

---

## Open questions for implementation phase

- List of known Zilch git repos for README fetching — to be collected from user during implementation
- ~~Confluence space key for Zilch — to be confirmed~~ **Resolved:** Confluence space is **`Kameleon`** (formerly known as Zilch; homepage title = "Zilch") at `https://xeldocs.atlassian.net/wiki/spaces/Kameleon`. See Appendix B for full page index.
- Default staleness threshold (currently 6 months) — confirm acceptable

---

## Appendix B — Kameleon (Zilch) Confluence Space — Page Index

> **Note:** The Confluence space is named **`Kameleon`** but the homepage is titled "Zilch" and Zilch is the product name used everywhere in the docs. The rename is partial — the space URL/key retains the Kameleon name. Use space key `Kameleon` for all API calls.

**Space metadata:**
- Cloud ID: `e4341026-8b19-45f0-abae-2cad84b91235`
- Space Key: `Kameleon`
- Space ID: `458757`
- Homepage ID: `458759` ([Zilch](https://xeldocs.atlassian.net/wiki/spaces/Kameleon/pages/458759))
- URL: https://xeldocs.atlassian.net/wiki/spaces/Kameleon/overview
- Total pages: **143**
- Last refreshed: 2026-06-29

### Top-level pages

| ID | Title | Last Updated | Children |
|----|-------|--------------|----------|
| `85590034` | [Choosing a backend framework](https://xeldocs.atlassian.net/wiki/spaces/Kameleon/pages/85590034) | 2024-08-06 | 0 |
| `86245396` | [Choosing an LLM framework](https://xeldocs.atlassian.net/wiki/spaces/Kameleon/pages/86245396) | 2024-08-06 | 0 |
| `292978689` | [Concept: Background Agents](https://xeldocs.atlassian.net/wiki/spaces/Kameleon/pages/292978689) | 2026-03-10 | 0 |
| `291373057` | [Concept: Brand Identity Envelopes](https://xeldocs.atlassian.net/wiki/spaces/Kameleon/pages/291373057) | 2026-03-10 | 0 |
| `192249865` | [Create & Edit flow](https://xeldocs.atlassian.net/wiki/spaces/Kameleon/pages/192249865) | 2025-11-26 | 0 |
| `219938817` | [Sentio RPC](https://xeldocs.atlassian.net/wiki/spaces/Kameleon/pages/219938817) | 2025-10-20 | 1 |
| `326369283` | [Sentio: Test Architecture & Quality Pipeline](https://xeldocs.atlassian.net/wiki/spaces/Kameleon/pages/326369283) | 2026-05-04 | 0 |
| `294125569` | [Strategies for Handling Large JSON Tool Responses in AI Agents](https://xeldocs.atlassian.net/wiki/spaces/Kameleon/pages/294125569) | 2026-03-11 | 0 |
| `89587713` | [Tracing & Evaluation](https://xeldocs.atlassian.net/wiki/spaces/Kameleon/pages/89587713) | 2024-08-14 | 0 |
| `458759` | [Zilch](https://xeldocs.atlassian.net/wiki/spaces/Kameleon/pages/458759) | 2025-05-13 | 20 |
| `296878081` | [Zilch Unified Messaging Protocol](https://xeldocs.atlassian.net/wiki/spaces/Kameleon/pages/296878081) | 2026-03-12 | 9 |

### Full page list (143 pages, sorted by title)

| ID | Title | Parent ID | Last Updated |
|----|-------|-----------|--------------|
| `34701314` | (Disabled) functionality | `34701339` | 2024-01-19 |
| `296976385` | 1. Introduction | `296878081` | 2026-03-12 |
| `297140225` | 2. Related Work and Research Method | `296878081` | 2026-03-12 |
| `296976404` | 3. Protocol Design | `296878081` | 2026-03-12 |
| `297172993` | 4. Migration Strategy | `296878081` | 2026-03-12 |
| `296714242` | 5. Schema Sharing Strategy | `296878081` | 2026-03-12 |
| `297205761` | 6. Discussion | `296878081` | 2026-03-12 |
| `296845328` | 7. Conclusions | `296878081` | 2026-03-12 |
| `297041921` | 8. Glossary & References | `296878081` | 2026-03-12 |
| `297238529` | 9. Appendix — V2 Action Registry | `296878081` | 2026-03-12 |
| `280395780` | Agent behaviour | `275873797` | 2026-02-16 |
| `167641091` | Anatomy of Dialogue | `168853505` | 2025-04-28 |
| `31686661` | Authentication | `458759` | 2025-01-22 |
| `458867` | Block Definition | `458953` | 2025-02-07 |
| `458953` | Block Info | `458900` | 2023-03-13 |
| `139165698` | Block Mapping Definition | `458953` | 2025-02-07 |
| `167641124` | Brand Key Model Evaluation Proof of Concept | `147783687` | 2025-04-25 |
| `458797` | Builder | `458759` | 2023-10-09 |
| `46071810` | Building static content (GatsbyJS) | `46694404` | 2025-01-23 |
| `458959` | CI/CD | `458759` | 2023-10-11 |
| `33030147` | CMS | `458759` | 2025-01-22 |
| `85590034` | Choosing a backend framework | `277118977` | 2024-08-06 |
| `86245396` | Choosing an LLM framework | `277118977` | 2024-08-06 |
| `12845088` | Code reviews | `9535489` | 2023-08-30 |
| `281477122` | Commerce | `458759` | 2026-02-17 |
| `278495243` | Common Example Scenarios | `275873797` | 2026-02-16 |
| `458846` | Compiler | `13303833` | 2023-03-14 |
| `292978689` | Concept: Background Agents | `277118977` | 2026-03-10 |
| `291373057` | Concept: Brand Identity Envelopes | `277118977` | 2026-03-10 |
| `458770` | Configuration | `458759` | 2023-03-14 |
| `168853505` | Conversation Orchestrator | `146767874` | 2025-04-28 |
| `147783687` | Conversation System Architecture | `147619853` | 2025-03-27 |
| `275873797` | Conversation: Edit Flow | `277053441` | 2026-02-16 |
| `146767874` | Conversation: Onboarding | `277053441` | 2026-02-16 |
| `168788057` | Conversational Architecture | `168853505` | 2025-04-28 |
| `162201637` | Conversational Engine and Evaluation | `147783687` | 2025-04-25 |
| `192249865` | Create & Edit flow | `244482049` | 2025-11-26 |
| `27262978` | Create Home page | `7438349` | 2023-12-05 |
| `459017` | Creating Elements | `458912` | 2025-04-23 |
| `458861` | Creating Section/Block component | `459017` | 2023-08-30 |
| `458843` | Defaults | `458900` | 2023-10-24 |
| `147685418` | Drawbacks and alternative architecture flow | `147783687` | 2025-03-11 |
| `236388353` | Edit Flow | `12353537` | 2025-11-26 |
| `230424577` | Edit flow (assistant) | `12353537` | 2025-11-20 |
| `458912` | Element Library | `458759` | 2023-08-30 |
| `319062017` | Endpoints | `318832641` | 2026-04-17 |
| `168787971` | Evaluation | `168853505` | 2025-04-28 |
| `168787990` | Evaluation Frameworks | `168787971` | 2025-04-28 |
| `277053441` | Features | `85557273` | 2026-02-16 |
| `12877883` | Font Icons | `12943437` | 2023-08-30 |
| `219840535` | Font Suggestions Service | `219938817` | 2025-10-20 |
| `319291393` | Form semantics | `318832641` | 2026-04-17 |
| `318832641` | Forms | `458759` | 2026-04-17 |
| `458794` | Game Service | `458759` | 2023-03-14 |
| `458879` | Gateway | `458759` | 2025-01-22 |
| `46694404` | GatsbyJS (compiler/renderer) | `458759` | 2024-03-22 |
| `458767` | Getting Started | `458759` | 2023-08-28 |
| `458972` | Home | `458759` | 2026-02-19 |
| `277086211` | Infrastructure | `85557273` | 2026-02-13 |
| `319127554` | Integration handling | `318832641` | 2026-05-11 |
| `168722434` | LLM-as-a-Judge | `168787971` | 2025-04-28 |
| `192413700` | Login & Authentication | `12353537` | 2025-08-11 |
| `458837` | Manifest | `458759` | 2023-08-29 |
| `12943437` | Manifest Theming | `458837` | 2023-08-29 |
| `12910980` | Manifest page structure | `458837` | 2023-08-29 |
| `139427842` | Mapping: blog_archive | `139165698` | 2025-02-11 |
| `139427853` | Mapping: blog_categories | `139165698` | 2025-02-07 |
| `141328385` | Mapping: blog_post | `139165698` | 2025-02-11 |
| `140017665` | Mapping: blog_post_meta | `139165698` | 2025-02-07 |
| `139558914` | Mapping: blog_posts | `139165698` | 2025-02-11 |
| `458764` | Milestones | `13303833` | 2023-03-13 |
| `22609921` | Neo4j Meta database | `7438349` | 2023-12-05 |
| `126550017` | Notification events | `111083523` | 2024-12-18 |
| `192348161` | Onboarding - Existing Project | `12353537` | 2025-11-26 |
| `192184324` | Onboarding - New Project | `12353537` | 2025-11-26 |
| `146833419` | Onboarding Process Explanation and key concepts | `146767874` | 2025-03-05 |
| `13303833` | Outdated | `458759` | 2023-08-30 |
| `458855` | Output Theme | `13303833` | 2023-03-14 |
| `18874387` | Pass by review | `458959` | 2023-10-11 |
| `458873` | Preview (Second Screen) (GatsbyJS) | `46694404` | 2024-03-21 |
| `7438349` | Profile | `458759` | 2023-08-30 |
| `192184379` | Publish Project | `12353537` | 2025-11-26 |
| `69992451` | Publish and deploy | `458879` | 2024-06-14 |
| `458987` | React Component | `458900` | 2023-10-24 |
| `107544577` | Refreshing User Token and Information in Zilch App | `31686661` | 2024-10-04 |
| `166428690` | Relume Layout Integratie in de Kameleon Library | `459017` | 2025-04-23 |
| `318799874` | Rendering | `318832641` | 2026-04-17 |
| `13205680` | Research | `458759` | 2023-08-30 |
| `308248577` | Research: Form Submission Spam Pipeline & Gibberish Classifier PoC | `13205680` | 2026-04-02 |
| `268435457` | Research: Implementing External Connections to the Forms Service | `13205680` | 2026-02-23 |
| `1605633` | Research: Session management of logged in users | `13205680` | 2023-08-30 |
| `147587075` | Research: client/server communication | `147619853` | 2025-03-06 |
| `1638452` | Research: oauth login in xel-sso | `13205680` | 2023-08-30 |
| `458933` | Scripts | `458900` | 2023-08-30 |
| `85557273` | Sentio (LLM Service) | `458759` | 2024-08-06 |
| `219938817` | Sentio RPC | `277118977` | 2025-10-20 |
| `326369283` | Sentio: Test Architecture & Quality Pipeline | `277118977` | 2026-05-04 |
| `158695426` | Speech To Text | `146767874` | 2025-04-10 |
| `280068099` | State | `278167556` | 2026-02-16 |
| `458816` | Stories | `458900` | 2023-08-30 |
| `294125569` | Strategies for Handling Large JSON Tool Responses in AI Agents | `277118977` | 2026-03-11 |
| `275382275` | Stripe API Rate Limiting | `13205680` | 2026-02-13 |
| `281673729` | Stripe Infrastructure | `281477122` | 2026-06-11 |
| `270565377` | Stripe integration within Zilch | `13205680` | 2026-02-17 |
| `458900` | Structure of an Element | `458912` | 2023-08-30 |
| `458996` | Styles | `458900` | 2023-08-30 |
| `319324163` | Submission semantics | `318832641` | 2026-04-17 |
| `147619853` | System architecture | `146767874` | 2025-03-06 |
| `148406278` | Technical: Sequence diagram | `147783687` | 2025-03-27 |
| `148471854` | Technical: Transmitted data & handling | `147783687` | 2025-03-27 |
| `11993089` | Test plan - Game | `458794` | 2024-06-10 |
| `12353537` | Test plan - Kameleon Builder App | `458797` | 2025-07-09 |
| `306348033` | Test plan - Onboarding + Edit Flow | `458797` | 2026-06-08 |
| `68714497` | Test plan - WordPress | `34701339` | 2024-06-19 |
| `111542273` | Test plan - Zilch Connect | `111083523` | 2025-01-23 |
| `458825` | Tests | `458900` | 2023-07-04 |
| `161742875` | Text To Speech | `146767874` | 2025-04-11 |
| `278167556` | The Edit Flow Agent Graph | `275873797` | 2026-02-16 |
| `89587713` | Tracing & Evaluation | `277118977` | 2024-08-14 |
| `458947` | Understanding the Build Process | `458912` | 2023-08-30 |
| `13205902` | Use and render blocks | `458912` | 2023-08-30 |
| `34701339` | WordPress | `33030147` | 2025-01-22 |
| `458759` | Zilch | `—` | 2025-05-13 |
| `111083523` | Zilch Connect | `458759` | 2025-01-13 |
| `295305217` | Zilch Onboarding Summary (High-level docs) | `146767874` | 2026-03-17 |
| `296878081` | Zilch Unified Messaging Protocol | `277118977` | 2026-03-12 |
| `316932097` | [Albert] - The Zilch Experience | `306348033` | 2026-04-14 |
| `345473027` | [Albert] - The Zilch Experience 2.0 | `306348033` | 2026-06-15 |
| `458849` | [DEPRECATED] From UX to dev (Webflow exports) | `459017` | 2025-04-23 |
| `341114900` | [Karim] -  The Zilch Experience | `306348033` | 2026-06-08 |
| `313327617` | [Nizar] - The Zilch Experience | `306348033` | 2026-04-13 |
| `340918275` | [Nizar] - The Zilch Experience 2.0 | `306348033` | 2026-06-15 |
| `313393153` | [Ramon] - The Zilch Experience | `306348033` | 2026-04-09 |
| `341934081` | [Robin] Test plan - The Zilch Experience | `306348033` | 2026-06-17 |
| `458791` | v1 - Client Interaction | `458764` | 2023-03-14 |
| `458782` | v2 - Client profile analytics | `458764` | 2023-03-13 |
| `458903` | v3 - SSO & SaaS | `458764` | 2023-03-13 |
| `458761` | v4 - Library completion | `458764` | 2023-03-13 |
| `458779` | v5 - Compiling and further | `458764` | 2023-03-13 |
| `9535489` | 💅 Code styling & conventions | `458759` | 2023-08-17 |
| `112590849` | 📚Library Update and Deployment Synchronization | `13205680` | 2024-10-24 |
| `200245250` | 📸 Wireframe Screenshots | `458912` | 2025-08-04 |
| `17006593` | 🔍 Research: Visual Regression | `13205680` | 2023-10-04 |

### Notes on the index

- **Recently active (2026):** Zilch Unified Messaging Protocol, Forms-related pages, Stripe Infrastructure, Test plans for Onboarding+Edit Flow
- **Stale (>1 year no update):** Choosing a backend framework, Choosing an LLM framework, Tracing & Evaluation, several element library pages — flag in `/doc-audit` reports
- **Tree shape:** Zilch homepage (`458759`) is the central hub with 20 children. Sub-sections include: Authentication, Builder, CI/CD, CMS, Commerce, Element Library, Forms, Game Service, Gateway, GatsbyJS, Getting Started, Profile, Research, Sentio (LLM), Zilch Connect, plus housekeeping ("Outdated", "Code styling")
- **Deprecated/Outdated bucket:** `13303833` ("Outdated") houses deprecated milestones + themes
- **Naming:** Some pages still reference "Kameleon" in titles (e.g., "Test plan - Kameleon Builder App", "[DEPRECATED] Relume Layout Integratie in de Kameleon Library") — the rename was not fully applied to content titles

---

## Appendix — Example prompts

**Example 1 — Grounded answer**
```
/zilch-knowledge
How does the standalone auth flow work for Zilch?
```
Expected: Search Confluence + git READMEs, summarize with sources, validate before answering.

**Example 2 — Doc-improvement proposal**
```
/zilch-knowledge
I'm working on ZIL-618 — is our Confluence doc on the standalone flow still accurate?
```
Expected: Fetch doc, compare to current YouTrack + git state, propose update if stale.

**Example 3 — Doc freshness audit**
```
/zilch-knowledge /doc-audit space=Zilch
```
Expected: Produce freshness report table.

**Example 4 — Validation gate enforcement**
```
/zilch-knowledge
Should we deprecate the customerReference field?
```
Expected: Search Confluence for existing decisions, surface them, ask user to confirm before any write.