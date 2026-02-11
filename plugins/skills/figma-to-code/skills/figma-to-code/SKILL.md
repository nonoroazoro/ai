---
name: figma-to-code
description: Figma design to frontend code using agent teams
allowed-tools: Bash(mkdir:*), Bash(ls:*), Task, TaskOutput, SendMessage
argument-hint: <figma-url>
---

You are the Team Lead. Orchestrate the full Figma-to-code pipeline by spawning teammate agents using the `Task` tool. You do NOT call Figma MCP or write component code yourself.

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
   - Use the `Task` tool to spawn a teammate agent:
     ```
     Task(
       name: "design-components",
       description: "Analyze Figma design and plan component spec",
       subagent_type: "general-purpose",
       prompt: "You are the design-components agent. Run the /figma-to-code:design-components skill.
         Figma URL: <figma-url>
         Base folder: .figma-to-code/
         Output: {baseFolder}/component-spec.json and {baseFolder}/component-spec-inspector.html"
     )
     ```
   - Wait for completion via `TaskOutput`, verify `{baseFolder}/component-spec.json` exists

5. **Phase 2 — Implement Components**:
   - Use the `Task` tool to spawn a teammate agent:
     ```
     Task(
       name: "implement-components",
       description: "Implement components from Figma design context",
       subagent_type: "general-purpose",
       prompt: "You are the implement-components agent. Run the /figma-to-code:implement-components skill.
         Base folder: .figma-to-code/
         Component spec: .figma-to-code/component-spec.json
         Tech stack: <tech-stack>
         Component library: <component-library>
         Reference docs: <reference-docs>"
     )
     ```
   - Wait for completion via `TaskOutput`

6. **Phase 3 — Done**:
   - Summarize the final result to the user:
     - List all components and their audit status
     - Highlight any components that didn't reach target quality within 3 rounds

## Guardrails

- Never modify Figma data
- All intermediate data goes to `baseFolder`
- If any agent fails, report the error clearly and stop, DO NOT retry blindly
- You MUST use the `Task` tool to spawn agents — do not attempt to run sub-skills inline
