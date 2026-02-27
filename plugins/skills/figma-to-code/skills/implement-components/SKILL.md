---
name: implement-components
description: Set up the project and implement all nodes from component spec, with Figma design context as reference
allowed-tools: mcp__figma-desktop__get_design_context, mcp__figma-desktop__get_variable_defs, mcp__figma-desktop__get_screenshot
argument-hint: [component-spec-file-path]
---

Implement all nodes from the component spec, with Figma design context as reference. Stay alive for `auditResult` from the Team Lead.

## Workflow

1. **Resolve Params**:
   - `componentSpec`: path to `component-spec.json`, from Team Lead message, spawn prompt context, or `$ARGUMENTS`
   - Project context: from Team Lead message or spawn prompt context

2. **Implement Nodes**:
   - If the project lacks scaffolding, set it up based on project context before proceeding
   - `get_variable_defs` to get design tokens
   - For each node in the component spec, leaf-first, bottom-up:
     - `get_design_context` with `nodeId` to get design details
     - `get_screenshot` with `nodeId` to get visual reference
     - Interpret Figma design context and following implementation rules below, do not copy verbatim
     - Add `data-node-id="{nodeId}"` to the root element of every page, module, and component, matching the spec
     - Nodes with `repeat`: implement the component once, render multiple times. Use `repeat.nodeIds` as `data-node-id` per instance
     - For Icons and Images: use assets from `get_design_context` directly. DO NOT import icon packages, fabricate markup, or use placeholders
     - Code directory structure mirrors the component spec tree

3. **Prepare for Audit**:
   - Create an example page in `examples` directory that renders all implemented nodes
   - Start dev server and send the devServerURL to the Team Lead

4. **Wait for Audit Result**:
   - Stay alive after step 2, wait for `auditResult` from the Team Lead
   - Apply fixes based on each node's issues, then notify Team Lead
   - DO NOT change `data-node-id` attributes during fixes

## Implementation Rules

- Follow project context from Team Lead (tech stack, styling, component library, reference docs, etc.)
- Reuse components from the project if possible
- Map Figma design tokens (colors, spacing, typography, etc.) to project's token system, never hardcode values that exist as tokens
- Avoid inline styles unless required for dynamic values
- Place components in the appropriate project directory, match existing export patterns
