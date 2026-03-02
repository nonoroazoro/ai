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

2. **Bootstrap**:
   - If needed, set up a minimal project based on `projectContext`
   - DO NOT use third-party scaffolding tools

3. **Implementation**
   - Implement in strict order:
      - Components → Modules → Pages
      - Complete one node before starting the next
      - Assemble bottom-up: implement all children before their parent
   - For each node:
     - `get_design_context` with `nodeId` to get design details (typically React + Tailwind code snippets)
     - `get_variable_defs` with `nodeId` to get design tokens (e.g., primary-500, primary-hover)
     - `get_screenshot` with `nodeId` to get visual reference
     - Treat Figma design context as the source of truth: exact dimensions, spacing, font sizes, icon sizes, etc.
     - Icons and Images: use assets from `get_design_context` directly, prefer inline SVG. DO NOT reference Figma URL or fabricate markup
     - Add `data-node-id` to the root element of every node, use `nodeId` by default, `repeat.nodeIds` for repeated nodes
     - Nodes with `repeat`: implement once, render `repeat.count` times
     - Implement node-local interactions (toggles, form validation, etc.)
   - After all nodes are implemented, wire up cross-node interactions (navigation, routing, shared state, etc.)

4. **Prepare for Audit**:
   - Create an example page in `./examples` directory that renders all implemented nodes
   - Start dev server by npm script, send the `devServerURL` to the Team Lead

5. **Wait for Audit Results**:
   - On receiving `auditResults` from Team Lead, apply fixes based on it and report back
   - Never change `data-node-id` during fixes

## Implementation Rules

- Follow `projectContext` (tech stack, styling, component library, reference docs, etc.)
- Code directory structure MUST mirror the `componentSpec` hierarchy
- Reuse existing project components when possible
- Match existing project directory structure and export patterns
- Map Figma design tokens to project's token system, never hardcode token values
- Avoid inline styles unless required for dynamic values
