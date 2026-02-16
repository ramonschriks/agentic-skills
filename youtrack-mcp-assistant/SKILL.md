---

name: youtrack-mcp-assistant
description: YouTrack MCP integration for tracking project progression, querying issues, and managing epics/sub-epics/user stories. Use when: (1) Getting progression overviews of an epic and its children, (2) Creating or updating issues (epics, sub-epics, user stories, checklists, bugs), (3) Querying issues by type, assignee, status, or custom fields, (4) Tracking blocked items, velocity, and dependencies, (5) Cross-checking epics/sub-epics for PO compliance (missing FRs, NFRs, DoD, stakeholder validation, checklists).

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

## Core Capabilities

### 1. Get Epic Progression

Query an epic and all its children (sub-epics, user stories, checklists) to get a complete progression overview.

```
Query: Get progression overview for epic [EPIC-ID]
```

Returns: Status summary, completion percentage, blocked items, sub-epics breakdown.

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

---

## Examples

**Example 1 — Get Progression**
```
What's the progression of epic PROJ-123?
```

**Example 2 — Query by Assignee**
```
Show all user stories assigned to me in epic PROJ-123
```

**Example 3 — Find Blocked Items**
```
What items are currently blocked in the authentication epic?
```

**Example 4 — Create Sub-Epic**
```
Create a sub-epic "User Profile API" under PROJ-123 with description "Implement REST endpoints for user profile management"
```

**Example 5 — Track Checklist Progress**
```
Summarize all checklists and their completion status for sub-epic PROJ-456
```

**Example 6 — PO Compliance Check**
```
Check PO compliance for epic PROJ-123. Which sub-epics are missing FRs, NFRs, DoD, or stakeholder validation?
```

**Example 7 — User Story Readiness**
```
Which user stories in sub-epic PROJ-456 are missing checklists or DoD?
```

---

## Best Practices

- Always start queries from the Epic entry point
- Use consistent naming: `Type: Sub-Epic` matches your custom types
- Query `has: blocked` to find all blocked items across the epic
- Checklist items are children of User Stories - query `Parent: {STORY-ID}`
- **For PO Compliance**: Always fetch ZIL-A-1 first to get the latest templates and rules
- Reference the PO Sub-Epic Management article when validating sub-epic/user story structure
