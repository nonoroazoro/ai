---
name: audit-component
description: Audit a node by comparing Figma design with its implementation, report audit result
allowed-tools: mcp__figma-desktop__get_design_context, mcp__figma-desktop__get_metadata, mcp__figma-desktop__get_screenshot, mcp__playwright__*
argument-hint: [node-id] [dev-server-url]
---

## Workflow

1. **Resolve Params**:
   - `devServerURL`: from Team Lead or `$ARGUMENTS`

2. **On receiving `nodeId` from Team Lead or `$ARGUMENTS`**:

   1. **Get Node-Level Screenshots**:
      - DESIGN screenshot: `get_screenshot` with `nodeId` to get node-level screenshot
      - IMPLEMENTATION screenshot:
        1. `browser_navigate` to `devServerURL` if not already there
        2. `browser_snapshot` to get the accessibility tree
        3. Precisely search the `[data-node-id="{nodeId}"]` in the snapshot, extract the corresponding `ref`
        4. `browser_take_screenshot` with this `ref` to get node-level screenshot, save to `.playwright-mcp` directory
        5. Reuse this `ref` in subsequent checks

   2. **Visual Check**:
      - Compare design and implementation screenshots side by side
      - Check visual fidelity: layout, spacing, alignment, typography, colors, icons, images, borders, shadows
      - Check visual artifacts: missing elements, black spots, overflow bleeding, stretched or distorted icons, broken images

   3. **Style Check**:
      - `get_design_context` with `nodeId` to get design context
      - `browser_evaluate` to run `getComputedStyle()` on `data-node-id="{nodeId}"` element to get implementation context
      - Compare with tolerance:

        | Property | Tolerance |
        |----------|-----------|
        | display, flex-direction | exact |
        | font-family, font-weight | exact |
        | font-size | +-1px |
        | line-height | +-2px |
        | color, background-color | exact match preferred, RGB channel delta <= 5 for rendering noise |
        | opacity | +-0.05 |
        | padding, margin, gap | +-2px |
        | border-radius | +-1px |
        | width, height | +-2px |
        | box-shadow | visual match |

   4. **Interaction Check**:
      - `get_metadata` with `nodeId` to check for interaction variants (hover, disabled, etc.)
      - If hover variant exists:
         - `browser_hover` on the `ref`
         - `browser_take_screenshot` with `ref`
         - Check if hover state works as designed
      - If disabled variant exists:
         - `browser_evaluate` to check `disabled`/`aria-disabled` on the `ref`
         - `browser_take_screenshot` with `ref`
         - Check if disabled state works as designed

   5. **Report `auditResult`** to the Team Lead:
      - `nodeId`: the node audited
      - `score`: 1-10, overall fidelity between design and implementation
      - `pass`: true if score >= 8
      - `issues`: flat list of all issues, each includes:
        - `category`: visual | style | interaction
        - `description`: what is wrong (e.g., `font-size mismatch`, `icon missing`, `layout shift`, etc.)
        - `expected`: design value or expected behavior
        - `actual`: implementation value or actual behavior

   6. Wait for next `nodeId`

## Guardrails

- DO NOT modify any files
