# Zilch Knowledge Index

> **Purpose:** This index gives the `zilch-knowledge` skill a fast lookup table for top-level Kameleon (Zilch) Confluence pages. Each row tells the skill **what a page covers**, so when you ask a Zilch question the skill can pick the right source before doing a full search.
>
> **Source:** Confluence space `Kameleon` (`458757`) at `https://xeldocs.atlassian.net/wiki/spaces/Kameleon`. This index is hand-built by summarizing each top-level page.
>
> **Scope:** 11 top-level pages only. Child pages are reachable from these parents.

---

## Space metadata

| Field | Value |
|-------|-------|
| Space key | `Kameleon` |
| Space ID | `458757` |
| Cloud ID | `e4341026-8b19-45f0-abae-2cad84b91235` |
| Homepage | [Zilch](https://xeldocs.atlassian.net/wiki/spaces/Kameleon/pages/458759) |
| Space URL | https://xeldocs.atlassian.net/wiki/spaces/Kameleon/overview |
| Last indexed | 2026-06-29 |

---

## Top-level pages

| ID | Title | Last Updated | Covers |
|----|-------|--------------|--------|
| [`458759`](https://xeldocs.atlassian.net/wiki/spaces/Kameleon/pages/458759) | **Zilch** (homepage) | 2025-05-13 | One-line description of Zilch as "a unified story-telling website builder, designed for headless CMS integration by xel." Sparse — the landing page for the entire space. Hub for all other top-level docs. |
| [`85590034`](https://xeldocs.atlassian.net/wiki/spaces/Kameleon/pages/85590034) | **Choosing a backend framework** | 2024-08-06 | Comparative research (Aug 2024) evaluating Django, Flask, and FastAPI for the LLM-Service backend. Concludes with FastAPI as the chosen framework due to modern async support, OpenAPI integration, and low boilerplate. **Stale — pre-dates v0.9 release.** |
| [`86245396`](https://xeldocs.atlassian.net/wiki/spaces/Kameleon/pages/86245396) | **Choosing an LLM framework** | 2024-08-06 | MoSCoW-evaluated comparison of LlamaIndex, DSPy, and LangChain. Decision: use **LangChain for v0.9**, re-evaluate post-release. **Stale — pre-dates v0.9 release.** |
| [`292978689`](https://xeldocs.atlassian.net/wiki/spaces/Kameleon/pages/292978689) | **Concept: Background Agents** | 2026-03-10 | Research document on durable background execution for Sentio's voice-to-voice edit flow. Proposes a foreground/background split with `BackgroundJobDispatcher` + `JobStore` + `sentio-worker` triad built on Valkey Streams. Solves the problem that long-running build actions (5–30s) block voice responsiveness (1–2s requirement). |
| [`291373057`](https://xeldocs.atlassian.net/wiki/spaces/Kameleon/pages/291373057) | **Concept: Brand Identity Envelopes** | 2026-03-10 | Research on replacing point-value brand tokens (single hue, single font) with bounded parameter regions (e.g., "hue ∈ [200°, 240°]"). Proposes tiered mutability (Core Identity → Instance) and signal-driven envelope evolution. Enables LLM-free generation within constrained regions. |
| [`192249865`](https://xeldocs.atlassian.net/wiki/spaces/Kameleon/pages/192249865) | **Create & Edit flow** | 2025-11-26 | QA test plan for the post-onboarding "Managing menu, pages, sections and layouts" step (MM.*), page builder (PB.*), color palette (CP.*), font family/suggestions (FF/FS.*), and demo content (DC.*). Cites test owners and pass/fail status. **In-progress — many MM scenarios still UNTESTED.** |
| [`219938817`](https://xeldocs.atlassian.net/wiki/spaces/Kameleon/pages/219938817) | **Sentio RPC** | 2025-10-20 | Overview of the Python gRPC service layer for Sentio. Describes adapter → Pydantic → service architecture, dependency injection via containers, and lists services: Color Palette, Demo Content, Font Suggestions (replaces deprecated Font Family), Layout Suggestion, Page Builder. |
| [`326369283`](https://xeldocs.atlassian.net/wiki/spaces/Kameleon/pages/326369283) | **Sentio: Test Architecture & Quality Pipeline** | 2026-05-04 | Research-driven redesign of the pytest marker taxonomy (introduces `component` marker, fixes the misuse of `integration`) and the CI pipeline (splits Quality Checks into Unit+Component and Integration stages; adds `eval` gate to Nightly). Replaces LLM judge verdicts with structural end-state assertions in e2e. |
| [`294125569`](https://xeldocs.atlassian.net/wiki/spaces/Kameleon/pages/294125569) | **Strategies for Handling Large JSON Tool Responses in AI Agents** | 2026-03-11 | Catalog of 11 mitigation techniques (pre-filtering, truncation, summarization, variable references, state trimming, RAG, pagination, output schemas, direct response, lazy loading, programmatic tool calling) with explicit latency analysis. Comparative table for task-type selection. |
| [`89587713`](https://xeldocs.atlassian.net/wiki/spaces/Kameleon/pages/89587713) | **Tracing & Evaluation** | 2024-08-14 | Pre-v0.9 research comparing 10 LLM tracing/observability tools (Langfuse, LangSmith, OpenTelemetry, Datadog, etc.). Concludes with **Arize/Phoenix** as the choice for v0.9. **Stale — current production appears to use Langfuse based on Test Architecture doc references.** |
| [`296878081`](https://xeldocs.atlassian.net/wiki/spaces/Kameleon/pages/296878081) | **Zilch Unified Messaging Protocol** | 2026-03-12 | Research paper (table of contents + 9 child sections: Introduction, Related Work, Protocol Design, Migration, Schema Sharing, Discussion, Conclusions, Glossary, V2 Action Registry Appendix). Replaces single-client WebSocket architecture with multi-client protocol (JSON-RPC-inspired envelope, capability negotiation, session management, replay). |

---

## Topic clusters (skill's mental map)

When a Zilch question arrives, the skill should consult pages in this order based on topic:

### Architecture & protocol
1. **Zilch Unified Messaging Protocol** (`296878081`) — multi-client messaging, capability negotiation
2. **Concept: Background Agents** (`292978689`) — durable background execution
3. **Sentio RPC** (`219938817`) — gRPC service layer

### Brand & content generation
1. **Concept: Brand Identity Envelopes** (`291373057`) — bounded brand constraints
2. **Create & Edit flow** (`192249865`) — page/section/layout UX flow

### AI/LLM strategy
1. **Strategies for Handling Large JSON Tool Responses** (`294125569`) — agent optimization
2. **Choosing an LLM framework** (`86245396`) — LangChain decision (stale)
3. **Tracing & Evaluation** (`89587713`) — observability choice (stale)
4. **Choosing a backend framework** (`85590034`) — FastAPI decision (stale)

### Quality & testing
1. **Sentio: Test Architecture & Quality Pipeline** (`326369283`) — current CI/test strategy

---

## Stale pages (flag for `/doc-audit`)

These top-level pages have not been updated in 12+ months and should be reviewed:

| Page | Last Updated | Staleness (months) | Risk |
|------|--------------|--------------------|------|
| Choosing a backend framework | 2024-08-06 | 22 | Low — historical decision, but FastAPI confirmation still current |
| Choosing an LLM framework | 2024-08-06 | 22 | **Medium** — explicitly says "evaluate after v0.9", never followed up |
| Tracing & Evaluation | 2024-08-14 | 22 | **High** — chose Arize/Phoenix but current Test Architecture doc references Langfuse; contradiction |
| Zilch (homepage) | 2025-05-13 | 13 | Medium — sparse content; should link to current architecture pages |

---

## Sync — how to refresh this index

This index is **manually curated**. The skill does not maintain it automatically.

**Re-sync trigger:** when a top-level page is created, updated, or deprecated.

**Re-sync procedure:**

1. List top-level pages via Confluence:
   ```
   getPagesInConfluenceSpace(spaceId="458757", sort="title")
   ```
2. For each new or significantly updated top-level page, fetch full content:
   ```
   getConfluencePage(pageId=<id>, contentFormat="markdown")
   ```
3. Update the row in the **Top-level pages** table above (ID, Title, Last Updated, Covers)
4. Update the **Stale pages** table (recompute staleness from current date)
5. Update the **Topic clusters** map if a new top-level page changes how questions route
6. Commit changes to the repo (no co-author line)

**Automated aid (optional, future):** A small `/sync-zilch-index` workflow could be added that runs the listing step and produces a diff for human review. Not built today — manual curation is sufficient at 11 top-level pages.

---

## Out-of-scope for this index

- The 132 non-top-level pages (children, deep technical specs, test plans) — discoverable via search at runtime
- Cross-references to git repos (added when the user provides repo paths in implementation)
- Content snapshots — never copy page bodies here; runtime fetches always