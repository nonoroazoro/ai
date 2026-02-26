---
name: figma-to-code
description: Figma design to frontend code using Claude Code Agent Teams. When the user asks to convert a Figma design to code, follow this workflow to create an agent team and orchestrate the pipeline.
argument-hint: <figma-url>
---

You are the Team Lead, responsible for creating the **Agent Team**, spawning Teammates, and orchestrating the workflow.

## Workflow

1. **Resolve Figma URL**:
   - If `$ARGUMENTS` contains a valid Figma URL, use it as `figmaURL`
   - Otherwise, ask the user to provide one

2. **Gather project context**:
   - If `package.json` exists, read it first to infer what you can
   - Ask the user to confirm or fill in what's missing, using selection prompts:
     - Tech stack: `React` / `Vue` / `Svelte` / other
     - Build tool: `Vite` / `Webpack` / `Rspack` / other
     - Component library: `Arco Design` / `Ant Design` / `MUI` / other / none
     - Testing tool: `Vitest` / `Jest` / other / none
     - Reference docs: project conventions, design docs, etc. (optional)

3. **Setup**:
   - Create `.figma-to-code` directory as the `{baseFolder}`
   - Create the agent team named `figma-to-code`

4. **Phase 1 - Design Components**:
   - Spawn a teammate named `design-components` with prompt:
     > Run the `/figma-to-code:design-components` skill.
     > figmaURL: {figmaURL}
     > baseFolder: {baseFolder}
   - Wait for `design-components` to finish, then verify `{baseFolder}/component-spec.json` exists

5. **Phase 2 - Implement Components**:
   - Spawn a teammate named `implement-components` with prompt:
     > Run the `/figma-to-code:implement-components {baseFolder}/component-spec.json` skill.
     > techStack: {techStack}
     > buildTool: {buildTool}
     > componentLibrary: {componentLibrary}
     > testingTool: {testingTool}
     > referenceDocs: {referenceDocs}
   - Wait for devServerURL from `implement-components`

6. **Phase 3 - Audit and Fix**:
   - Spawn a teammate named `audit-component` with prompt:
     > Run the `/figma-to-code:audit-component` skill.
     > devServerURL: {devServerURL}
   - Read `{baseFolder}/component-spec.json` to get all nodes from the spec
   - Loop (max 3 rounds):
     1. **Audit**:
        - For each node (bottom-up: components → modules → pages), send `nodeId` to `audit-component`
        - Wait for `auditResult` before proceeding
        - If `auditResult` does not pass, add to `results` array
        - Otherwise, continue to the next node
     2. **Fix**:
        - If `results` is not empty, send it as `auditResult` to `implement-components`, wait for fix confirmation
        - Otherwise exit loop
   - After 3 rounds, move on with remaining failures

7. **Phase 4 - Done**:
   - Summarize the final result to the user:
     - List all nodes and their audit status
     - Highlight any nodes that didn't reach target quality within 3 rounds
   - Clean up the agent team

## Guardrails

- If any teammate fails, report the error and stop. DO NOT retry blindly.
