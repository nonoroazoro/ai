---
name: implement-components
description: Implement components from Figma design context and audit for quality
allowed-tools: mcp__figma-desktop__get_design_context, mcp__figma-desktop__get_variable_defs, Bash(npm:*), Bash(npx:*), Bash(pnpm:*), Bash(yarn:*), Bash(bun:*), Bash(lsof:*)
argument-hint: [component-spec-file-path]
---

Read the component spec, implement all components using Figma design context, then run per-component audit+fix loops.

## Workflow

1. **Resolve params** from spawn prompt context or `$ARGUMENTS`:
   - `baseFolder`: from Team Lead, or default to `.implement-components/`
   - `component-spec.json`: `{baseFolder}/component-spec.json`, or `$ARGUMENTS` as file path when standalone
   - Tech stack, component library, reference docs: from Team Lead, or infer from `package.json`

2. **Write all component code** (leaf-first, bottom-up):
   - For each component in the component spec:
     - Call `get_design_context` with the component's `nodeId` — get code reference
     - Call `get_variable_defs` if design tokens are needed
     - Extract layout, size, color, font, spacing values — **these are authoritative, must follow exactly**
     - Write code following the project's existing framework and conventions — **do NOT copy Figma-generated code structure**
     - Add `data-node-id="{nodeId}"` to the component's outermost DOM element
     - Backfill `filePath` in the component spec file

3. **Per-component audit + fix loop**:

   > Only execute when orchestrated by Team Lead (`figma-to-code`). Standalone `/implement-components` stops after step 2.

   - Prepare audit environment: create an example page rendering all implemented components, start dev server, get page URL
   - For each component in the component spec:
     - Loop (max 3 rounds):
       - Spawn two audit agents in parallel:
         - `audit-component-visual`: pass nodeId + page URL
         - `audit-component-style`: pass nodeId + page URL
       - Wait for both agents to return
       - Both agents return `pass: true` → next component
       - Not passing → fix code based on audit findings → continue loop
     - After 3 rounds, move on — report the component as not fully passing
   - Stop dev server when all components are done

## Guardrails

- Design values from `get_design_context` are the source of truth — never override with guesses
- Code structure follows project conventions, not Figma's auto-generated structure
- Every component's outermost DOM MUST have `data-node-id` — audit agents use this to locate elements
- Keep the component spec file in sync — update `filePath` as each file is created
