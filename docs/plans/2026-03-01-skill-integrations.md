# Story Mapping Skill — Integrations Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Add Mermaid diagram output and MCP integration guidance (Figma, Miro, Jira, Linear) to the user-story-mapping skill.

**Architecture:** Two edits to SKILL.md — add Mermaid as Part 3 of the Output Format section, and add a new Integrations section after Guidelines. Then sync the edited file to the deployed skill location at `~/.claude/skills/user-story-mapping/SKILL.md`.

**Tech Stack:** Markdown, Claude skill system

**Design doc:** `docs/plans/2026-03-01-skill-integrations-design.md`

---

### Task 1: Add Mermaid Output (Part 3 of Output Format)

**Files:**
- Modify: `SKILL.md` — after the `### Part 2: Structured Markdown Detail` section, before `---` and `## Guidelines for Claude`

**Step 1: Add the Mermaid section**

Insert the following block in `SKILL.md` after the closing `---` of Part 2:

```markdown
### Part 3: Mermaid Diagram (always available)

An alternative to the ASCII map. Use `graph LR` (left-to-right) to mirror story map reading direction. Backbone activities are top-level nodes. Stories are child nodes. Release groupings are `subgraph` blocks.

When the user asks for Mermaid output, generate the current map in this format — replacing or alongside the ASCII, per user preference.

\`\`\`mermaid
graph LR
  subgraph R1_MVP["R1 (MVP)"]
    A1[Activity 1] --> S1_1[Story 1.1]
    A1 --> S1_2[Story 1.2]
    A2[Activity 2] --> S2_1[Story 2.1]
    A2 --> S2_2[Story 2.2]
  end
  subgraph R2
    A1 --> S1_3[Story 1.3]
    A2 --> S2_3[Story 2.3]
  end
  subgraph Future
    A1 --> S1_4[Story 1.4]
  end
\`\`\`
```

**Step 2: Verify the file looks correct**

Read `SKILL.md` and confirm Part 3 appears between Part 2 and `## Guidelines for Claude`.

---

### Task 2: Add Integrations Section

**Files:**
- Modify: `SKILL.md` — append after `## Guidelines for Claude` section

**Step 1: Add the Integrations section**

Append the following to the end of `SKILL.md`:

```markdown
---

## Integrations

### Common Pattern

When the user asks to export to an external tool, follow this pattern:

1. **Detect** — attempt to call a known tool from the relevant MCP. If it fails or isn't found, skip to step 3.
2. **Pre-flight** — ask the user for the required destination (project key, team name). If the MCP supports listing, offer to show available options.
3. **Execute** — use MCP tools to create the export.
4. **Not available** — if the MCP is not configured:
   - Say clearly: "I don't have access to [tool] — the [X] MCP isn't configured."
   - Share the link to configure it.
   - Offer a fallback: "I can export as a Mermaid diagram or save a markdown file instead."

---

### Figma / FigJam

- **Trigger:** User asks to export to Figma or FigJam
- **Detection:** Look for Figma MCP tools (e.g., `figma_create_frame`, `figma_create_sticky`)
- **Pre-flight:** Ask which FigJam file/page to export to
- **Output:**
  - Backbone activities → horizontal frames across the top
  - Stories → sticky notes below each activity
  - Release groupings → section dividers / swimlanes
- **MCP:** https://github.com/GLips/Figma-Context-MCP
- **Fallback:** Mermaid diagram or markdown file

---

### Miro

- **Trigger:** User asks to export to Miro
- **Detection:** Look for Miro MCP tools (e.g., `miro_create_board`, `miro_create_sticky_note`)
- **Pre-flight:** Ask which Miro board to export to, or offer to create a new one
- **Output:**
  - Backbone activities → top row of cards
  - Stories → sticky notes in columns below each activity
  - Release groupings → horizontal divider lines
- **MCP:** https://github.com/k-jarzyna/mcp-miro
- **Fallback:** Mermaid diagram or markdown file

---

### Jira

- **Trigger:** User asks to export to Jira or create Jira tickets
- **Detection:** Look for Jira MCP tools (e.g., `jira_create_issue`)
- **Pre-flight:** Ask for a project key, or list available projects via MCP and let the user pick
- **Output mapping:**
  - Backbone activity → Epic
  - Story → Issue under its Epic
    - Title: story short title
    - Description: user story text + acceptance criteria (Given/When/Then)
    - Sub-tasks: tasks from the Tasks list
    - Labels: risks flagged on that story
  - Release grouping → Sprint or Fix Version
- **MCP:** https://github.com/sooperset/mcp-atlassian
- **Fallback:** Mermaid diagram or markdown file

---

### Linear

- **Trigger:** User asks to export to Linear or create Linear issues
- **Detection:** Look for Linear MCP tools (e.g., `linear_create_issue`)
- **Pre-flight:** Ask for a team name; optionally ask for a project within that team. Offer to list teams via MCP.
- **Output mapping:**
  - Backbone activity → Label or Project grouping
  - Story → Issue
    - Title: story short title
    - Description: user story text + acceptance criteria (Given/When/Then)
    - Sub-issues: tasks from the Tasks list
    - Labels: risks flagged on that story
  - Release grouping → Cycle or Milestone
- **MCP:** https://github.com/jerhadf/linear-mcp-server
- **Fallback:** Mermaid diagram or markdown file
```

**Step 2: Verify the file looks correct**

Read `SKILL.md` and confirm the Integrations section appears at the end with all four tools.

---

### Task 3: Sync to Deployed Skill Location

**Files:**
- Source: `/Users/tomershahar/Documents/Projects/story_mapping/SKILL.md`
- Destination: `/Users/tomershahar/.claude/skills/user-story-mapping/SKILL.md`

**Step 1: Copy the updated file to the deployed location**

```bash
cp /Users/tomershahar/Documents/Projects/story_mapping/SKILL.md \
   /Users/tomershahar/.claude/skills/user-story-mapping/SKILL.md
```

**Step 2: Verify the deployed file matches**

Read `~/.claude/skills/user-story-mapping/SKILL.md` and confirm both Part 3 (Mermaid) and the Integrations section are present.

---

### Task 4: Smoke Test

**Step 1: Invoke the skill**

Use the Skill tool to load `user-story-mapping` and confirm it loads without errors.

**Step 2: Test Mermaid output**

With the login story map context, ask: "Give me the backbone as a Mermaid diagram."
Expected: Claude generates a valid `graph LR` Mermaid block with backbone activities and stories grouped by release.

**Step 3: Test MCP not-configured fallback**

Ask: "Export this to Miro."
Expected: Claude says Miro MCP isn't configured, shares the MCP link, and offers Mermaid or markdown as fallback.
