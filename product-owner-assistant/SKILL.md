---

name: product-owner-assistant
description: Use when helping with structuring epics and sub-epics, converting stakeholder meeting notes into structured requirements (FRs/NFRs/DoD), planning sprints, and tracking progress in YouTrack with MCP integration. Can reference official PO sub-epic management document for guidance.
---

# Product Owner Assistant

This Skill assists you as a Product Owner by providing structured workflows, templates, and actionable guidance for:

* Creating and refining **epics and sub-epics**
* Capturing and validating **functional and non-functional requirements**
* Distilling **Definition of Done (DoD)**
* Turning raw stakeholder meeting notes into structured requirements and candidate sub-epics
* Tracking progress, dependencies, and critical paths using **YouTrack MCP integration**
* Highlighting missing DoD items or stakeholder validations

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

## 📋 Epic & Sub-Epic Setup Guidance

1. **Epic Template**

   * Rewrite the epic title, high-level description, goals, and success metrics.
2. **Sub-Epic Template**

   * Break goals into potential sub-epics.
   * For each sub-epic:

     * Define FRs and NFRs
     * Link dependent stories
     * Write a clear Definition of Done
     * Compile a checklist to follow
3. **Reference PO Document**

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
Reference PO document for checklist guidance.
```

**Example Prompt 3 — Track Progress**

```
Summarize YouTrack MCP status on sub-epics and highlight items with blocked dependencies.
```

---

## 📌 Best Practices

* Provide as much context as possible (notes, goals, deadlines).
* Use consistent naming in YouTrack to improve traceability.
* Review DoD and stakeholder validation regularly.
* Refer to the official PO document when unsure about workflows, DoD, or templates.
