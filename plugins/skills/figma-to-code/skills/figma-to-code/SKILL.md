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
   - For anything not inferred, you MUST ask the user by providing a list of options:
     - Framework: `React` / `Vue` / other / skip
     - Library: `Arco Design` / `Ant Design` / other / skip
     - Styling: `Tailwind` / `Less` / other / skip
     - Docs: project conventions, plan docs, etc. (optional)

3. **Configure Audit-Fix**:
   - Ask the user whether to enable the audit-fix loop (default to `enabled`)
   - Description: Compare implementation against design and auto-fix issues, costs more tokens
   - Store choice as `{enableAudit}`

4. **Setup**:
   - Set `{baseFolder}` to `.figma-to-code`
   - Create the agent team named `figma-to-code`

5. **Phase 1 - Design Components**:
   - Spawn a teammate named `design-components` with prompt:
     > Run the `/figma-to-code:design-components {figmaURL}` skill.
     > baseFolder: {baseFolder}
   - Wait for `design-components` to reply, then verify `{baseFolder}/component-spec.json`

6. **Phase 2 - Implement Components**:
   - Spawn a teammate named `implement-components` with prompt:
     > Run the `/figma-to-code:implement-components {baseFolder}/component-spec.json` skill.
     > projectContext: {Project Context}
   - Wait for devServerURL from `implement-components`

7. **Phase 3 - Audit and Fix (Skip if `{enableAudit}` is false)**:
   - Spawn a teammate named `audit-component` with prompt:
     > Run the `/figma-to-code:audit-component` skill.
     > devServerURL: {devServerURL}
   - Read `{baseFolder}/component-spec.json`, collect `nodeId`s at the highest available level: `page` > `module` > `component`
   - Loop up to 3 rounds of audit-fix:
     1. For each top-level `nodeId`:
        - Send `nodeId` to `audit-component`, wait for `auditResult`
        - If failed, send `auditResult` to `implement-components`, wait for fix confirmation
        - Only proceed to the next `nodeId` after the current one is fully resolved
     2. If all passed this round, exit audit-fix loop

8. **Phase 4 - Done**:
   - Summarize the final result
     - List all nodes and their audit status/score if audit-fix was enabled
   - Clean up the agent team

## Guardrails

- You are an orchestrator, DO NOT write code or call tools
- DO NOT skip any phase or audit-fix rounds unless the user opted out of audit-fix
- Be patient, wait for teammates to reply. Only poll for status when absolutely necessary
- If a needed teammate is no longer running, respawn it
- If any teammate fails, report the error and stop. DO NOT retry blindly
