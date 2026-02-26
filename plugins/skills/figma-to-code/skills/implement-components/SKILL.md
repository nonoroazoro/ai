---
name: implement-components
description: Implement all nodes from component spec, with Figma design context as reference
allowed-tools: mcp__figma-desktop__get_design_context, mcp__figma-desktop__get_variable_defs, mcp__figma-desktop__get_screenshot
argument-hint: [component-spec-file-path]
---

Implement all nodes from the component spec, with Figma design context as reference. Stay alive for `auditResult` from the Team Lead.

## Workflow

1. **Resolve params**:
   - `componentSpec`: path to `component-spec.json`, from Team Lead message, spawn prompt context, or `$ARGUMENTS`
   - Tech stack, component library, reference docs, etc.: from Team Lead message, spawn prompt context, or infer from `package.json`

2. **Implement nodes** (leaf-first, bottom-up):
   - For each node in the component spec:
     - Call `get_design_context` with the node's `nodeId` to get design details
     - Call `get_screenshot` with the node's `nodeId` as visual reference, keep it accessible throughout implementation
     - Call `get_variable_defs` if design tokens are needed
     - Write code following the project's conventions. Design context from Figma is the source of truth
     - For Icons and Images: use assets from `get_design_context` directly. DO NOT import icon packages, fabricate markup, or use placeholders
     - Add `data-node-id="{nodeId}"` to the root element of every page, module, and component, matching the spec
     - Nodes with `repeat`: implement the component once, render it in a loop in the parent. Use each `repeat.nodeIds` entry as the `data-node-id` for each instance
     - Code directory structure mirrors the component spec tree
     - Update `filePath` in the `componentSpec` file after implementation
   - When all nodes are done:
     - Create an example page in `examples/` that renders all implemented nodes
     - Start dev server and send the devServerURL to the Team Lead

3. **Wait for audit results**:
   - Stay alive after step 2, wait for `auditResult` (nodes that need fixing) from the Team Lead
   - Apply fixes based on each node's issues, then notify Team Lead
   - DO NOT change `data-node-id` attributes during fixes
   - DO NOT exit until the Team Lead sends a shutdown request
