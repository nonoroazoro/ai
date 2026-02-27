---
name: audit-component
description: Audit a node by comparing Figma design with its implementation
allowed-tools: mcp__figma-desktop__get_screenshot, mcp__figma-desktop__get_design_context, mcp__plugin_playwright_playwright__browser_navigate, mcp__plugin_playwright_playwright__browser_snapshot, mcp__plugin_playwright_playwright__browser_take_screenshot, mcp__plugin_playwright_playwright__browser_evaluate, mcp__plugin_playwright_playwright__browser_wait_for, mcp__plugin_playwright_playwright__browser_hover
argument-hint: <node-id> <dev-server-url>
---

Audit a node by comparing Figma design with its implementation.

## Workflow

1. **Resolve `devServerURL`**:
   - From Team Lead message, spawn prompt context, or `$ARGUMENTS`

2. **On receiving a `nodeId` from Team Lead message, spawn prompt context, or `$ARGUMENTS`**:

   1. **Get Screenshots**:
      - DESIGN screenshot: `get_screenshot` with `nodeId`, Figma returns only that node's region
      - IMPLEMENTATION screenshot:
        1. `browser_navigate` to devServerURL (skip if already on the page)
        2. `browser_snapshot` to get the accessibility tree
        3. Find the element with `[data-node-id="{nodeId}"]` in the snapshot, get its `ref`
        4. `browser_take_screenshot` with that `ref`, save to `.playwright-mcp` directory

   2. **Visual Check**:
      - Review both screenshots side by side
      - Check overall visual fidelity: layout, spacing, alignment, typography, color, icon, image, border, shadow, missing elements, etc.
      - Assets must render correctly, no placeholders or broken images

   3. **Style Check**:
      - `get_design_context` with `nodeId` to get design context
      - `browser_evaluate` to run `getComputedStyle()` on `[data-node-id="{nodeId}"]` to get implementation context
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
      - If Figma design includes hover variant: `browser_hover` on the element, `browser_take_screenshot` to capture hover state, compare against design
      - If the element has `disabled` or `aria-disabled` attribute: `browser_take_screenshot` to capture disabled state, compare against design

   5. **Report `auditResult`** to the Team Lead:
      - `nodeId`: the node audited
      - `score`: 1-10, overall fidelity between design and implementation
      - `pass`: true if score >= 8
      - `issues`: flat list of all issues, each includes:
        - `category`: visual | style | interaction
        - `description`: what is wrong (e.g., `font-size mismatch`, `icon missing`, `layout shift`, etc.)
        - `expected`: design value or expected behavior
        - `actual`: implementation value or actual behavior

   6. Stay alive and wait for the next `nodeId`, DO NOT exit

## Guardrails

- DO NOT modify any code or files
