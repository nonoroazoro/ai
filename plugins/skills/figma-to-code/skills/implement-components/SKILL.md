---
name: implement-components
description: Implement all nodes from component spec with Figma design context, fix code based on audit results
allowed-tools: mcp__figma-desktop__get_design_context, mcp__figma-desktop__get_variable_defs, mcp__figma-desktop__get_screenshot
argument-hint: [component-spec-file-path]
---

## Workflow

1. **Resolve Params**:
   - `componentSpec`: from Team Lead or `$ARGUMENTS`
   - `projectContext`: from Team Lead

2. **Implement Nodes**:
   - Set up a minimal project if needed: based on `projectContext`, DO NOT use third-party scaffolding tools
   - Bottom-up, leaf-first, for each node in `componentSpec`:
     - `get_design_context` with `nodeId` to get design details (typically React + Tailwind code snippets)
     - `get_variable_defs` with `nodeId` to get design tokens (e.g., primary-500, primary-hover)
     - `get_screenshot` with `nodeId` to get visual reference
     - Treat Figma design context as the source of truth, follow implementation rules for code style
     - Download required assets (images, icons, SVGs) to project local files from Figma, never fabricate or reference Figma URL
     - Add `data-node-id` to the root element of every page, module, and component: use `nodeId` by default, `repeat.nodeIds` for repeated nodes
     - Nodes with `repeat`: implement once, render `repeat.count` times
     - Code directory structure MUST mirrors the `componentSpec` hierarchy

3. **Prepare for Audit**:
   - Create an example page in `./examples` directory that renders all implemented nodes
   - Start dev server by npm script, send the `devServerURL` to the Team Lead

4. **Wait for Audit Results**:
   - On receiving `auditResults` from Team Lead, apply fixes based on it and report back
   - Never change `data-node-id` during fixes

## Implementation Rules

- Follow `projectContext` (tech stack, styling, component library, reference docs, etc.)
- Reuse existing project components when possible
- Match existing project directory structure and export patterns
- Map Figma design tokens to project's token system, never hardcode token values
- Avoid inline styles unless required for dynamic values
