---
name: figma-to-code
description: Convert Figma designs to frontend code by orchestrating an Agent Team
argument-hint: [figma-url]
---

You are the Team Lead. Set up an Agent Team with specialized Teammates and orchestrate the end-to-end workflow.

## Workflow

1. **Resolve Figma URL**:
   - If `$ARGUMENTS` includes a valid Figma URL, use it as `figmaURL`
   - Otherwise, ask the user

2. **Gather Project Context**:
   - Read `package.json` to infer `Project Context`
   - Default to `Vite` and `Vitest` for build and testing if not detected
   - Ask the user for anything not inferred:
     - Framework: `React` / `Vue` / other / skip
     - Library: `Arco Design` / `Ant Design` / other / skip
     - Styling: `Tailwind` / `Less` / other / skip
     - Docs: project conventions, plan docs, etc. (optional)

3. **Setup**:
   - Set `{baseFolder}` to `.figma-to-code`
   - Create the agent team named `figma-to-code`

4. **Phase 1 - Design Components**:
   - Spawn a teammate named `design-components` with prompt:
     > Run the `/figma-to-code:design-components {figmaURL}` skill.
     > baseFolder: {baseFolder}
   - Wait for `design-components` to finish, then verify `{baseFolder}/component-spec.json` exists

5. **Phase 2 - Implement Components**:
   - Spawn a teammate named `implement-components` with prompt:
     > Run the `/figma-to-code:implement-components {baseFolder}/component-spec.json` skill.
     > projectContext: {Project Context}
   - Wait for devServerURL from `implement-components`

6. **Phase 3 - Audit and Fix**:
   - Spawn a teammate named `audit-component` with prompt:
     > Run the `/figma-to-code:audit-component` skill.
     > devServerURL: {devServerURL}
   - Read `{baseFolder}/component-spec.json` and collect all nodes as `pendingNodeIds` (bottom-up: components → modules → pages)
   - Loop up to 3 rounds of audit-fix until all nodes pass:
     1. **Audit**: for each `nodeId` in `pendingNodeIds`, send to `audit-component`, wait for `auditResult`, collect failed ones into `auditResults`
     2. If `auditResults` is empty, exit loop
     3. **Fix**: send `auditResults` to `implement-components`, wait for fix confirmation
     4. Set `pendingNodeIds` to the `nodeId`s from `auditResults` for next round

7. **Phase 4 - Done**:
   - Summarize the final result to the user:
     - List all nodes and their audit status/score
     - Highlight any nodes that didn't reach target quality within 3 rounds
   - Clean up the agent team

## Guardrails

- You are an orchestrator, DO NOT write code or call tools
- DO NOT skip any phase or audit-fix rounds
- Be patient, wait for teammates to reply. Only poll for status when absolutely necessary
- If a needed teammate is no longer running, respawn it
- If any teammate fails, report the error and stop. DO NOT retry blindly
