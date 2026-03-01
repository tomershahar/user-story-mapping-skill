# Design: Story Mapping Skill — Integrations & Mermaid Output

**Date:** 2026-03-01
**Status:** Approved

---

## Overview

Add four new capabilities to the user-story-mapping skill:
1. **Mermaid diagram output** — always available alternative to ASCII map
2. **Figma/FigJam export** — via MCP, on request
3. **Miro export** — via MCP, on request
4. **Jira/Linear backlog creation** — via MCP, on request, full detail

---

## Decision Log

| Question | Decision |
|----------|----------|
| When to trigger MCP integrations? | On explicit user request only |
| Mermaid availability? | Always available, no MCP needed |
| When MCP not configured? | Say not available + link to MCP + offer fallback |
| Jira/Linear export detail level? | Full: stories, acceptance criteria, tasks as sub-tickets, risks as labels |
| Destination (project/team)? | One destination per export, user selects via pre-flight question |

---

## Section 1: Mermaid Output (Part 3 of Output Format)

Added as a third output format alongside ASCII map and Structured Markdown. Always available — no MCP or condition required.

**Format:** `graph LR` (left-to-right to mirror story map reading direction)
- Backbone activities → top-level nodes
- Stories → child nodes below each activity
- Release grouping → `subgraph` blocks

**Trigger:** User asks for Mermaid output at any point ("give me this as a Mermaid diagram")

**Behavior:** Claude regenerates the current map in Mermaid format. User can choose to replace or add alongside the ASCII.

**Example structure:**
```
graph LR
  subgraph R1_MVP
    A1[Open App] --> S1_1[Straight to login]
    A1 --> S1_2[Session handling]
    A2[View Login] --> S2_1[Google + OTP]
  end
  subgraph R2
    A1 --> S1_3[Brute force lockout]
  end
```

---

## Section 2: Common MCP Integration Pattern

All MCP integrations follow this 4-step pattern:

1. **Detect** — attempt to call a known tool from the relevant MCP. If unavailable, move to step 3.
2. **Pre-flight** — ask user for the required destination (project key, team name). If MCP supports listing, offer to list options.
3. **Execute** — use MCP tools to create the export.
4. **Not available** — if MCP not configured:
   - "I don't have access to [tool] — the [X] MCP isn't configured."
   - Share link to configure it.
   - Offer fallback: "I can export as a Mermaid diagram or save a markdown file instead."

---

## Section 3: Per-Tool Specs

### Figma / FigJam
- **Trigger:** User asks to export to Figma or FigJam
- **Detection:** Check for Figma MCP tools (e.g., `figma_create_frame`, `figma_create_sticky`)
- **Pre-flight:** Ask which FigJam file/page to export to
- **Output:**
  - Backbone activities → horizontal frames across the top
  - Stories → sticky notes below each activity
  - Release groupings → section dividers / swimlanes
- **MCP reference:** https://github.com/GLips/Figma-Context-MCP
- **Fallback:** Mermaid or markdown file

---

### Miro
- **Trigger:** User asks to export to Miro
- **Detection:** Check for Miro MCP tools (e.g., `miro_create_board`, `miro_create_sticky_note`)
- **Pre-flight:** Ask which Miro board to export to (or offer to create a new one)
- **Output:**
  - Backbone activities → top row of cards
  - Stories → sticky notes in columns below each activity
  - Release groupings → horizontal divider lines
- **MCP reference:** https://github.com/k-jarzyna/mcp-miro
- **Fallback:** Mermaid or markdown file

---

### Jira
- **Trigger:** User asks to export to Jira or create Jira tickets
- **Detection:** Check for Jira MCP tools (e.g., `jira_create_issue`)
- **Pre-flight:** Ask for project key (or list available projects via MCP)
- **Output mapping:**
  - Backbone activity → Epic
  - Story → Issue under its Epic
    - Title: story short title
    - Description: user story + acceptance criteria (Given/When/Then)
    - Sub-tasks: tasks from the Tasks list
    - Labels: risks flagged on that story
  - Release grouping → Sprint or Fix Version
- **MCP reference:** https://github.com/sooperset/mcp-atlassian
- **Fallback:** Mermaid or markdown file

---

### Linear
- **Trigger:** User asks to export to Linear or create Linear issues
- **Detection:** Check for Linear MCP tools (e.g., `linear_create_issue`)
- **Pre-flight:** Ask for team (and optionally project within that team); offer to list teams via MCP
- **Output mapping:**
  - Backbone activity → Label or Project grouping
  - Story → Issue
    - Title: story short title
    - Description: user story + acceptance criteria (Given/When/Then)
    - Sub-issues: tasks from the Tasks list
    - Labels: risks flagged on that story
  - Release grouping → Cycle or Milestone
- **MCP reference:** https://github.com/jerhadf/linear-mcp-server
- **Fallback:** Mermaid or markdown file

---

## Implementation Plan (for writing-plans)

Changes to `SKILL.md`:
1. Add Part 3 (Mermaid) to the Output Format section
2. Add a new "Integrations" section after Guidelines with:
   - Common pattern description
   - Per-tool entries (Figma, Miro, Jira, Linear)

Changes to `~/.claude/skills/user-story-mapping/SKILL.md`:
- Mirror the same changes (this is the deployed copy)
