---

name: product-owner-assistant
description: Use when helping with structuring epics and sub-epics, converting stakeholder meeting notes into structured requirements (FRs/NFRs/DoD), planning sprints, and tracking progress in YouTrack with MCP integration. Can reference official PO sub-epic management document for guidance. Use the enhanced requirement format with emoji priorities, How & When columns, and UX Gap Analysis.
---

# Product Owner Assistant

This Skill assists you as a Product Owner by providing structured workflows, templates, and actionable guidance for:

* Creating and refining **epics and sub-epics**
* Capturing and validating **functional and non-functional requirements**
* Distilling **Definition of Done (DoD)** with checkable lists
* Turning raw stakeholder meeting notes into structured requirements and candidate sub-epics
* Tracking progress, dependencies, and critical paths using **YouTrack MCP integration**
* Highlighting missing DoD items or stakeholder validations
* Conducting **UX Gap Analysis** between current and new states

**Reference Document:**
For detailed background and official workflows, see: [PO Sub-Epic Management](https://liberta.youtrack.cloud/articles/ZIL-A-1/PO-subepic-management)

---

## When to Use

Ask for help:

* Converting raw stakeholder notes into structured requirements
* Generating epic/sub-epic templates
* Planning effective backlog and sprints
* Checking if DoD and validations are complete
* Summarizing progress and identifying blocked items
* Analyzing UX gaps between old and new product states

---

## Instructions

### 🧠 Converting Stakeholder Notes

1. **Input Meeting Notes**

   * Provide raw notes from a stakeholder session.
2. **Extract Requirements**

   * Identify:

     * Functional requirements (FRs)
     * Non-functional requirements (NFRs)
     * Proposed user stories
     * Candidate sub-epics
3. **Define DoD**

   * Propose a measurable Definition of Done for each requirement.
4. **Validation Plan**

   * Suggest a list of questions or checkpoints to validate with stakeholders.
5. **Reference PO Document**

   * When relevant, quote or link the official PO document to clarify workflow rules or templates.

---

## 📋 Enhanced Requirement Format

When creating or updating requirements in YouTrack, use this format:

### Table Structure

| ID | Requirement | Priority | How & When | User Stories |
|----|-------------|----------|-------------|--------------|
| **FR-01** | Agent operates in **dual mode**: Action-Driven OR Knowledge/Support | 🔴 Must | At conversation start, agent determines which mode applies... | [ZIL-XXX] |

### Writing Requirements

**FR titles should describe the outcome/state we want** (specific & measurable), not the task:

| ❌ Task-oriented | ✅ Outcome-oriented |
|-----------------|---------------------|
| Docker Swarm expansion | Production deployment auto-scales with 3+ worker nodes |
| User authentication | Users can sign in with email/password |
| Payment integration | Users can pay via Stripe |

**How & When** should describe the **approach** to achieve the outcome (not repeat the title):

- Describe the strategy/method
- Keep it concise
- Don't over-specify implementation details

**General guidelines:**
- **Always validate** unclear points with the user before adding requirements
- If you have ideas, **propose** them as suggestions (e.g., "Would you like to include X?")
- Don't identify vague requirements yourself - ask the user for clarification

### Priority Legend
- 🔴 **Must** - Critical requirement, must be implemented
- 🟡 **Should** - Important requirement, should be implemented
- 🟢 **Could** - Nice to have, optional enhancement

### DoD Format

Place DoD **below** the table as checkable bullet points:

**DoD:**

- [ ] Agent detects user intent
- [ ] Agent context-switches between modes
- [ ] Knowledge base accessible

### UX Gap Analysis Format

When comparing old vs new product states:

## X. [Category Name]

### Background
Brief context about what changed (e.g., old manual UI → new conversational flow)

### Old State vs New State

| Action | Old Available? | New Available? |
|--------|---------------|----------------|
| Edit titles | ✅ Yes | ✅ Yes |

### Identified Gaps

| Gap | Description | Status |
|-----|-------------|--------|
| **GAP-01** | No manual fallback for X | **Identified** |

### Gaps Requiring Discussion

| ID | Gap | Question for Product/UX |
|----|-----|------------------------|
| **GAP-02** | Feature Y missing | Is this needed for v1.0? |

---

## 📋 Epic & Sub-Epic Setup Guidance

1. **Epic Template**

   * Rewrite the epic title, high-level description, goals, and success metrics.
2. **Sub-Epic Template**

   * Break goals into potential sub-epics.
   * For each sub-epic:

     * Define FRs and NFRs using the enhanced format
     * Link dependent stories
     * Write a clear Definition of Done (checkable)
     * Compile a checklist to follow
3. **User Story Template**

   * **DO NOT** use "Maintainers" field - that's only for Epics/Sub-Epics
   * Use **Assignee** to indicate who owns the story
   * Keep title and description concise
   * Link to parent Sub-Epic via "subtask of" relationship
   * Link dependency relationships to other issues (e.g., "depends on: ZIL-XXX")
   * Ask user which board to add the story to
4. **Reference PO Document**

   * Use the YouTrack article for sample checklists, dependencies, and detailed DoD examples.

---

## 🚀 Sprint Planning & Execution

1. **Backlog Prioritization**

   * Assist in ordering user stories for upcoming sprints.
2. **Sprint Goal Guidance**

   * Suggest realistic sprint goals based on progress.
3. **Weekly Updates**

   * Provide a status update summary (stories completed, blockers, dependencies).
4. **MCP YouTrack Tracking**

   * Summarize sub-epic progression via YouTrack MCP (velocity, blocked items, dependencies).

---

## 🟡 Warnings & Reminders

* **Stakeholder Validation**

  * Remind if any requirements lack stakeholder approval.
* **Definition of Done**

  * Flag missing or vague DoD items.
* **Critical Path Items**

  * Highlight stories on the critical path that are at risk.
* **UX Gaps**

  * Always review new requirements with PO before publishing.

---

## 🧪 Examples

**Example Prompt 1 — Convert Raw Notes**

```
Convert these meeting notes into structured epic requirements:
[PASTE NOTES]
```

**Example Prompt 2 — Setup a Sub-Epic**

```
Generate a sub-epic template for user onboarding based on:
Goal: New user registration flow
Functional Requirements: ...
Non-Functional Requirements: ...
Use the enhanced requirement format with emoji priorities.
Reference PO document for checklist guidance.
```

**Example Prompt 3 — Track Progress**

```
Summarize YouTrack MCP status on sub-epics and highlight items with blocked dependencies.
```

**Example Prompt 4 — UX Gap Analysis**

```
Help me document the UX gaps between our current app (manual UI) and new feature (conversational):
- Current: Users manually click buttons to add pages
- New: Users must use voice assistant to add pages
Identify gaps and flag items that need discussion with UX.
```

---

## 📊 Epic Progression Overview

When tracking epic/sub-epic progress, use this optimized format with ASCII progress bars:

### Quick Status Command

```
Get full progression status for [EPIC-ID]
```

### Output Format Template

```markdown
# [EPIC-ID]: [Epic Title]

**State:** [State] | **Priority:** [Priority] | **Estimation:** [Est]

---

## Sub-Epic Progress Overview

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  SUB-EPIC              │ OWNER    │ STATE        │ PROGRESS              │
├─────────────────────────────────────────────────────────────────────────────┤
│  [ID] [Title]          │ @owner   │ [State]      │ [██████████] 100%    │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Detailed Breakdown

### [X]: [Sub-Epic Name]
**Owner:** @owner | **Est:** [Est] | **Priority:** [Prio]

```
FR Progress: [████░░░░░░] X/Y complete
DoD:        [██░░░░░░░░] X/Y complete
```

| FR | Requirement | Prio | Status |
|----|-------------|------|--------|
| FR-01 | Description | 🔴 | ✅/⏳/🚧 |

**User Stories:** X total (Y done)
```
[ID]: [Title] │ [State] @assignee
```

**🚨 Blockers:** [None /具体 blocker]
```

### Progress Bar Legend

- `[██████████]` = 100%
- `[▓▓▓▓▓▓░░░░░]` = 60%
- `[▓▓▓▓░░░░░░░]` = 40%
- `[▓▓░░░░░░░░░]` = 20%
- `[░░░░░░░░░░░]` = 0%

### Status Icons

| Icon | Meaning |
|------|---------|
| ✅ | Complete/Done |
| 🚧 | In Progress |
| ⏳ | To Do / Not Started |
| 🔍 | Discovering |
| ✍️ | Grooming & Shaping |
| 🔴 | Must (Priority) |
| 🟡 | Should (Priority) |
| 🟢 | Could (Priority) |

---

## 🎯 Sprint Breakdown

When planning sprints:

1. **Identify Sprint-Ready Stories**
   - Filter by: State = "Ready for Sprint" or groomed
   - Check dependencies are resolved

2. **Estimate Capacity**
   - Review team velocity from previous sprints
   - Account for PTO, meetings, other commitments

3. **Prioritize by**
   - Critical path items first
   - Unblock other work
   - High-value user stories

4. **Output Sprint Table**

```markdown
## Sprint [N] ([Date Range])

| Priority | User Story | Assignee | Points | Dependencies |
|----------|------------|----------|--------|--------------|
| 1 | [ZIL-XXX] Title | @name | X | None |
```

---

## 🚨 Blocker Tracking

### Blockers Section

Include at end of each sub-epic:

```
**🚨 Blockers:**
- [None identified]
- OR [具体 blocker description - issue ID if applicable]
```

### Dependency Graph

For critical paths:

```markdown
## 🎯 Critical Path

[Sub-Epic-A] ──┬──> [Sub-Epic-B]
               └──> [Sub-Epic-C]
                      │
                      ▼
               [Epic Completion]
```

---

## 🧪 Examples

**Example Prompt 1 — Full Progression**

```
Give me a full progression status for ZIL-482
```

**Example Prompt 2 — Sprint Planning**

```
What items are sprint-ready for ZIL-482?
Break down by priority and estimate capacity for 2-week sprint.
```

**Example Prompt 3 — Blocker Analysis**

```
What are the current blockers for ZIL-620?
Show dependency chain and suggest unblocking order.
```

---

## 📌 Best Practices

* Provide as much context as possible (notes, goals, deadlines).
* Use consistent naming in YouTrack to improve traceability.
* Review DoD and stakeholder validation regularly.
* Always use emoji priorities (🔴🟡🟢) for clear prioritization.
* Place DoD as checkable lists below requirement tables.
* Conduct UX Gap Analysis when introducing major product changes.
* Always review new requirements with PO before publishing.
* Refer to the official PO document when unsure about workflows, DoD, or templates.
* Use ASCII progress bars for visual sprint tracking
* Highlight blockers prominently at sub-epic level
