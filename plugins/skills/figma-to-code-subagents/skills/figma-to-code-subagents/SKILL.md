---
name: figma-to-code-subagents
description: Convert Figma designs to frontend code
allowed-tools: AskUserQuestion
argument-hint: [figma-url]
---

You are the orchestrator. Spawn subagents to execute each phase.

## Workflow

1. **Resolve Figma URL**:
   - If `$ARGUMENTS` includes a valid Figma URL, use it as `figmaURL`
   - Otherwise, ask the user

2. **Gather Project Context**:
   - Read `package.json` to infer `Project Context`
   - Default to `Vite` and `Vitest` for build and testing if not detected
   - For anything not inferred, use the `AskUserQuestion` tool (if available) to ask the user to select:
     - Framework: `React` / `Vue` / other / skip
     - Library: `Arco Design` / `Ant Design` / other / skip
     - Styling: `Tailwind` / `Less` / other / skip
     - Docs: project conventions, plan docs, etc. (optional)

3. **Setup**:
   - Set `{baseFolder}` to `.figma-to-code-subagents`

4. **Phase 1 - Design Components**:
   - Spawn subagent with prompt:
     > Run the `/figma-to-code-subagents:design-components {figmaURL}` skill.
     > baseFolder: {baseFolder}
   - Verify `{baseFolder}/component-spec.json`

5. **Phase 2 - Implement Components**:
   - Spawn subagent with prompt:
     > Run the `/figma-to-code-subagents:implement-components {baseFolder}/component-spec.json` skill.
     > projectContext: {Project Context}

6. **Done**:
   - Summarize the final result to the user

## Guardrails

- You are an orchestrator, DO NOT write code
- If any subagent fails, report the error and stop. DO NOT retry blindly
