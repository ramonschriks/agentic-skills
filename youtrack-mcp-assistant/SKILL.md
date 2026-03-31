---
name: youtrack-mcp-assistant
description: "YouTrack MCP integration for tracking project progression, querying issues, and managing epics/sub-epics/user stories. Use when: (1) Getting progression overviews of an epic and its children, (2) Creating or updating issues (epics, sub-epics, user stories, checklists, bugs), (3) Querying issues by type, assignee, status, or custom fields, (4) Tracking blocked items, velocity, and dependencies, (5) Cross-checking epics/sub-epics for PO compliance (missing FRs, NFRs, DoD, stakeholder validation, checklists)."
---

# YouTrack MCP Assistant

## Overview

This skill provides workflows for interacting with YouTrack via MCP to track project progression, query issues, and manage the full epic hierarchy. Your YouTrack uses: **Epic → Sub-Epic → User Story → Checklist** hierarchy with custom types: Epic, Sub-Epic, User Story, Checklist (subtask of User Story), Bug.

## Project Context

- **Entry Point**: Always start from an Epic - this determines the project
- **Two Projects**: Multiple projects exist, but all tracking starts from an Epic's entry point
- **Issue Types**:
  - `Epic` - Quarterly planning container
  - `Sub-Epic` - Parallel work streams assignable to different people
  - `User Story` - Requirements to be implemented
  - `Checklist` - Subtask of User Story: implementation details, acceptance criteria, deploy services, DoD
  - `Bug` - Defect tracking

## Epic vs Sub-Epic: Two Different Views

Understanding the distinction between Epic and Sub-Epic determines how to present progression:

### Epic Overview
- **Purpose**: Summarizes and prioritizes child sub-epics
- **Content**: Index of sub-epics with MoSCoW priorities, owners, and brief descriptions
- **When to use**: High-level planning, quarterly reviews, resource allocation

```
## Sub-Epics Index

| Sub-Epic | Priority | Owner |
|----------|----------|-------|
| [ID] Sub-Epic Name | 🔴 Must | person |
```

### Sub-Epic with Requirements
- **Purpose**: Contains detailed requirements (FRs), DoDs, and linked user stories
- **Content**: FR tables, DoD checkboxes, UX Gap Analysis, related issues
- **When to use**: Implementation planning, tracking MUST completion, PO compliance

```
## Requirements & DoDs

| ID | Requirement | Priority | How & When | User Stories |
|----|-------------|----------|-------------|--------------|
| FR-01 | [Requirement text] | 🔴 Must | [Implementation guidance] | [ZIL-XXX] |

**DoD:**
- [ ] [Checklist item]
```

### Key Difference
- **Epic**: "What sub-epics do we need?" → Sub-epic index with MoSCoW
- **Sub-Epic**: "What must be built?" → FRs, DoDs, user stories

**Rule**: Always determine whether the user is asking about an Epic or Sub-Epic first, then apply the appropriate format.

## Core Capabilities

### 1. Get Epic/Sub-Epic Progression

Query an epic or sub-epic and all its children to get a complete progression overview. Use this when the user asks for progression, status, or progress of an epic or sub-epic.

```
Query: Get progression overview for [EPIC-ID] or [SUB-EPIC-ID]
```

**When user asks for "progression", "progress", "status", or "how is [epic/sub-epic] doing", ALWAYS use this format:**

```
## [EPIC/SUB-EPIC-ID] Progress Analysis

### Overview

| Metric | Value |
|--------|-------|
| Total Issues | X |
| Completed | X |
| In Progress | X |
| To Do | X |
| Completion | X% |

---

### [For Sub-Epics] User Stories Overview

| Status | Count | Stories |
|--------|-------|----------|
| ✅ Done | X | List IDs |
| 🏃‍♀️ In Progress | X | List IDs |
| 📋 To Do | X | List IDs |

---

### [For Sub-Epics] MUST-Priority Requirements Mapping

Map each requirement from the description to its linked user stories:

| Section | FRs | Status | Linked User Stories |
|---------|-----|--------|-------------------|
| [Section 1] | FR-01 to FR-XX | ✅/🏃/📋 | [Story-IDs] |
| [Section 2] | FR-XX to FR-XX | ✅/🏃/📋 | [Story-IDs] |

**For each section:**
- Extract all FRs (Functional Requirements) from the issue description
- User stories are automatically linked via subtask relationship (query `subtask of: [SUB-EPIC-ID]`)
- Show status per FR based on linked story status

### Completed MUST Requirements

| FR | Requirement | Story | Status |
|----|-------------|-------|--------|
| FR-XX | [Requirement text] | [Story-ID] | ✅ Done |

### Remaining for MUST Completion

List what's still needed:

| FR | Requirement | Story | Status |
|----|-------------|-------|--------|
| FR-XX | [Requirement text] | [Story-ID] | 📋 To Do |

---

### Summary

**Total MUST requirements: X**
- Completed: X
- Remaining: X

**User Stories needed to complete all MUST requirements:**
1. [Story-ID] - [Story title] - [Status]
2. [Story-ID] - [Story title] - [Status]

---

### Key Findings

- What are the blockers (if any)?
- What's at risk?
- Any dependencies?
```

**Steps to generate this:**

1. **Fetch the issue** (epic or sub-epic) to get its description with requirements/FRs
2. **Query children** - Get all user stories linked as `subtask of` or `parent for`
3. **For each user story** - Get its status from custom fields
4. **Parse requirements** - Extract FRs from the description (look for tables with FR-XX)
5. **Map FRs to stories** - FRs are linked to user stories via subtask relationships (automatically linked in YouTrack)
6. **Calculate progress** - Count completed vs remaining
7. **Format output** - Present in the format above

---

### 2. Query Issues

Use YouTrack's search syntax to find issues by various criteria:

| Query Type | YouTrack Syntax |
|------------|------------------|
| By Epic | `Epic: {EPIC-ID}` |
| By Type | `Type: {Epic\|Sub-Epic\|User Story\|Checklist\|Bug}` |
| By Assignee | `Assignee: {username}` |
| By Status | `State: {In Progress\|Open\|Fixed\|Verified}` |
| By Sub-Epic | `Parent: {SUB-EPIC-ID}` |
| Blocked | `has: blocked` |
| Blocked By | `depends on: {ISSUE-ID}` |

### 3. Create Issues

Create new issues with proper type and hierarchy:

**Create Sub-Epic under Epic:**
```
Create sub-epic under [EPIC-ID]:
- Title: [title]
- Description: [description]
- Assignee: [optional]
```

**Create User Story under Sub-Epic:**
```
Create user story under [SUB-EPIC-ID]:
- Title: [title]
- Description: [FRs/NFRs]
- Assignee: [optional]
```

**Create Checklist under User Story:**
```
Create checklist under [USER-STORY-ID]:
- Implementation details: [...]
- Acceptance criteria: [...]
- Deploy services: [service1, service2]
- DoD: [...]
```

### 4. Update & Manage

- Update status, assignee, description
- Link dependencies (blocks/blocked by)
- Add tags or custom fields
- Close/resolve issues

### 5. PO Compliance Check

Cross-check epics/sub-epics against the official PO Sub-Epic Management rules. **Always fetch the reference document first:**

1. **Fetch PO Reference** - Get issue `ZIL-A-1` (PO Sub-Epic Management article) for the latest templates and rules
2. **Apply Templates** - Use the templates from ZIL-A-1 to validate structure

**Check for these elements:**

| Rule | What to Check |
|------|---------------|
| **Functional Requirements (FRs)** | Description contains FRs per ZIL-A-1 template |
| **Non-Functional Requirements (NFRs)** | Description contains NFRs per ZIL-A-1 template |
| **Definition of Done (DoD)** | Description contains DoD, or Checklist exists with DoD items |
| **Stakeholder Validation** | Mentioned in description or checklist item |
| **User Stories** | Sub-epic has child User Stories |
| **Checklists** | User Stories have child Checklists with implementation details |

**Compliance Query Pattern:**
```
Check PO compliance for epic [EPIC-ID]
```

This will: (1) Fetch ZIL-A-1 for current templates, (2) Query all sub-epics/user stories under the epic, (3) Compare each against the templates, (4) Report gaps.

Returns: Which sub-epics and user stories are missing FRs, NFRs, DoD, checklists, or stakeholder validation.

---

## Workflows

### Progression Overview Workflow

1. **Identify Epic** - Get the Epic ID from user
2. **Query Children** - Get all sub-epics under the epic
3. **Query Stories** - Get all user stories under each sub-epic
4. **Calculate Progress** - Count completed vs total
5. **Identify Blockers** - Find issues with blocked dependencies
6. **Summarize** - Present progression with critical path items

### Dependency Tracking Workflow

1. **Find Blocked Items** - Query `has: blocked`
2. **Trace Dependencies** - Follow `depends on` links
3. **Identify Critical Path** - Find longest dependency chain
4. **Highlight Risks** - Flag at-risk items

### PO Compliance Check Workflow

1. **Fetch PO Reference** - Get issue `ZIL-A-1` (PO Sub-Epic Management) to get current templates and rules
2. **Query Sub-Epics** - Get all sub-epics under the epic
3. **Query User Stories** - Get all user stories under each sub-epic
4. **Check Each Item** - For each item, verify against ZIL-A-1 templates:
   - Has FRs (in description)
   - Has NFRs (in description)
   - Has DoD (in description or has child Checklist)
   - Has Stakeholder Validation (mentioned)
5. **Query Checklists** - For each user story, check for child Checklist
6. **Summarize Gaps** - List items missing required PO elements

### Gap Analysis Workflow

Identify MUST requirements without linked user stories.

1. **Fetch Sub-Epic** - Get the sub-epic description to extract FRs
2. **Query User Stories** - Get all stories under the sub-epic (`subtask of: [SUB-EPIC-ID]`)
3. **Parse FRs** - Extract all FRs with 🔴 Must priority from description
4. **Check Coverage** - Match each MUST FR to a user story
5. **Report Gaps** - List uncovered MUST requirements

**Output Format:**
```
## Gap Analysis: [SUB-EPIC-ID]

| FR | Requirement | Priority | User Stories | Status |
|----|-------------|----------|--------------|--------|
| FR-01 | [Requirement text] | 🔴 Must | [ZIL-XXX] | ✅ |

**Missing User Stories:**
- None! All MUST requirements covered.

**OR:**

- FR-02: [Requirement text] - needs user story
```

### Epic Progress Overview Workflow

Show overall progress of an Epic with progress bars.

1. **Fetch Epic** - Get sub-epics index from description
2. **Query Sub-Epics** - Get all sub-epics under the epic
3. **For Each Sub-Epic:**
   - Query all user stories (`subtask of: [SUB-EPIC-ID]`)
   - Count total US vs completed US (based on resolved/State field)
   - Calculate completion %
4. **Generate Visual Output** - Progress bars + tables

**IMPORTANT:** Track **User Story completion**, not FR completion. FRs define requirements, but US completion shows actual progress.

**Output Format:**
```
## [EPIC-ID] Progress Overview

### Overall: ██████░░░░░ 30% (14/47 US)

### By Sub-Epic

| Sub-Epic | State | Owner | US Total | Done | Progress |
|----------|-------|-------|----------|------|----------|
| ZIL-XXX | 🚧 In Progress | @person | 10 | 2 | 20% |
```

---

## Examples

**Example 1 — Get Progression (Sub-Epic)**
```
What's the progression of [SUB-EPIC-ID]?
```

This will return the full progression analysis including:
- Overview metrics
- User stories status table
- MUST requirements mapping
- Completed vs remaining requirements
- Summary with next steps

**Example 2 — Get Epic Progression**
```
How is [EPIC-ID] doing?
```

**Example 3 — Query by Assignee**
```
Show all user stories assigned to me in epic PROJ-123
```

**Example 4 — Find Blocked Items**
```
What items are currently blocked in the authentication epic?
```

**Example 5 — Create Sub-Epic**
```
Create a sub-epic "User Profile API" under PROJ-123 with description "Implement REST endpoints for user profile management"
```

**Example 6 — Get Status Summary**
```
Show me the status of [SUB-EPIC-ID] - what user stories are done, what's in progress?
```

**Example 7 — Track Checklist Progress**
```
Summarize all checklists and their completion status for sub-epic PROJ-456
```

**Example 8 — PO Compliance Check**
```
Check PO compliance for epic PROJ-123. Which sub-epics are missing FRs, NFRs, DoD, or stakeholder validation?
```

**Example 9 — User Story Readiness**
```
Which user stories in sub-epic PROJ-456 are missing checklists or DoD?
```

**Example 10 — Gap Analysis**
```
Show me gaps in ZIL-626. Which MUST requirements don't have user stories?
```

**Example 11 — Epic Progress Overview**
```
Show me the overall progress of ZIL-482 with progress bars
```

---

## UX User Stories

UX User Stories follow a specific template structure with iteration checklists. Use this when creating UX design work under a UX Sub-Epic.

### UX Story Hierarchy

```
UX Sub-Epic (e.g., ZIL-935)
└── UX User Story (from Template)
    ├── Checklist: Iteration 1 - Wireframing
    ├── Checklist: Iteration 2 - Initial Design
    └── Checklist: Iteration 3 - Final Design
```

### Creating UX Stories Workflow

**Step 1: Find Templates**
Fetch the UX Sub-Epic to find existing UX story templates:
```
Query: Get issues under [UX-SUB-EPIC-ID] with Type: Template
```
Look for UX Template user stories and their child Iteration Checklist templates.

**Step 2: Duplicate Template**
Duplicate the UX Template user story under the appropriate UX Sub-Epic:
- Rename to specific screen/section name
- Clear any placeholder/example content

**Step 3: Create Iteration Checklists**
Create 3 Iteration Checklist subtasks under the new UX story:
- Type: Story Checklist
- Link as `subtask of` parent UX story
- Link dependencies: Iter 2 → Iter 1, Iter 3 → Iter 2
- Set State: Parked (backlog), Estimation: 3d

**Step 4: Extract & Populate**
Interview stakeholder/user OR review existing brief to fill:
- Problem Statement: What problem does this solve?
- Success Criteria: Measurable KPIs
- Constraints: Technical + Brand
- Scope: In scope / Out of scope
- Agreed Deliverables: What UX will produce
- Stakeholders: Who approves, who gives feedback

**Step 5: Review with PO**
Present populated UX story:
- Confirm scope boundaries
- Align on success criteria
- Get stakeholder sign-off (record in Brief Alignment section)

### Key Differences: Dev vs UX Stories

| Aspect | Dev User Story | UX User Story |
|--------|---------------|---------------|
| DoD scope | Implementation + testing | Iteration review + sign-off |
| Structure | Single checklist | 3 iteration checklists |
| Estimation | Story points | 3d per iteration (~1 week total) |
| Review | PR/QA | Stakeholder review per iteration |
| Handoff | Code to CI | Designs to dev via Figma |

### UX Story States

Use `State - Backlog` field:
- 💤 Ungroomed: Not yet refined
- ⏸️ Parked: Ready but not started
- 🛠️ In Preparation: Being worked on

---

## Best Practices

- Always start queries from the Epic entry point
- Use consistent naming: `Type: Sub-Epic` matches your custom types
- Query `has: blocked` to find all blocked items across the epic
- Checklist items are children of User Stories - query `Parent: {STORY-ID}`
- **For PO Compliance**: Always fetch ZIL-A-1 first to get the latest templates and rules
- Reference the PO Sub-Epic Management article when validating sub-epic/user story structure
