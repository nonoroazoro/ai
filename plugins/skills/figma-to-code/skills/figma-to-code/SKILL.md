---
name: figma-to-code
description: Figma design to frontend code using agent teams
allowed-tools: Bash(mkdir:*), Bash(ls:*)
argument-hint: <figma-url>
---

You are the Team Lead. Orchestrate the full Figma-to-code pipeline: spawn teammate agents, pass params, coordinate phases. You do NOT call Figma MCP or write component code yourself.

## Workflow

1. **Resolve Figma URL** from `$ARGUMENTS`:
   - Validate it's a valid Figma URL
   - If `$ARGUMENTS` is empty or not a valid Figma URL, ASK THE USER

2. **Collect project tech info** (ASK THE USER):
   - Tech stack: React / Vue / Svelte / other
   - Component library: Arco Design / Ant Design / MUI / other / none
   - User may provide reference docs (project conventions, design docs, etc.)
   - If `package.json` exists, read it first to infer tech stack — only ask for what's missing

3. **Setup**: create `.figma-to-code/` directory which is the `baseFolder` of agent teams

4. **Phase 1 — Design Components**:
   - Create Teammate Agent:
      - Name: `design-components`
      - Pass params: Figma URL, base folder (`.figma-to-code/`)
   - Wait for completion, verify `{baseFolder}/component-spec.json`

5. **Phase 2 — Implement Components Loop**:
   - Create Teammate Agent:
      - Name: `implement-components`
      - Pass params: base folder (`.figma-to-code/`), tech stack, component library, user-provided reference docs
   - Wait for completion

6. **Phase 3 — Done**:
   - Summarize the final result to the user:
     - List all components and their audit status
     - Highlight any components that didn't reach target quality within 3 rounds
   - Clean up the agent teams

## Guardrails

- Never modify Figma data
- All intermediate data goes to `baseFolder`
- If any agent fails, report the error clearly and stop, DO NOT retry blindly
