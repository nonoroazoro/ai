---
name: audit-component
description: Audit a node by comparing Figma design with its implementation
allowed-tools: mcp__figma-desktop__get_screenshot, mcp__figma-desktop__get_design_context, mcp__plugin_playwright_playwright__browser_navigate, mcp__plugin_playwright_playwright__browser_snapshot, mcp__plugin_playwright_playwright__browser_take_screenshot, mcp__plugin_playwright_playwright__browser_evaluate, mcp__plugin_playwright_playwright__browser_wait_for
argument-hint: <node-id> <dev-server-url>
---

Audit a node by comparing Figma design with its implementation. Stay alive to process each nodeId from the Team Lead, report the `auditResult`, then wait for the next one.

## Workflow

1. **Resolve `devServerURL`**:
   - From Team Lead message, spawn prompt context, or `$ARGUMENTS`

2. **On receiving a `nodeId` from Team Lead message, spawn prompt context, or `$ARGUMENTS`**:

   1. **Get screenshots** (both scoped to the exact node, not the full page):
      - DESIGN screenshot: `get_screenshot` with `nodeId`, Figma returns only that node's region
      - IMPLEMENTATION screenshot:
        1. `browser_navigate` to devServerURL (skip if already on the page)
        2. `browser_snapshot` to get the accessibility tree
        3. Find the element with `[data-node-id="{nodeId}"]` in the snapshot, get its `ref`
        4. `browser_take_screenshot` with that `ref` and a relative `filename` to keep screenshots in the Playwright output directory

   2. **Compare design vs implementation**:
      - Review both screenshots, check overall visual fidelity: layout, spacing, alignment, typography, color, icon, missing elements, image, border, shadow, etc.
      - If visual differences are found, drill into style details:
        - `get_design_context` with `nodeId` to extract design values
        - `browser_evaluate` to run `getComputedStyle()` on `[data-node-id="{nodeId}"]` to extract implementation values
        - Compare with tolerance:

          | Property | Tolerance |
          |----------|-----------|
          | display, flex-direction | exact |
          | font-size | +-1px |
          | font-weight | exact |
          | line-height | +-2px |
          | color, background-color | RGB channel delta <= 10 |
          | opacity | +-0.05 |
          | padding, margin, gap | +-2px |
          | border-radius | +-1px |
          | width, height | +-2px |
          | box-shadow | visual match |
          | icon, image | present and visually matching, no placeholders |

   3. **Report `auditResult`** to the Team Lead:
      - `nodeId`: the node audited
      - `score`: 1-10, overall fidelity between design and implementation
      - `pass`: true if score >= 8
      - `issues`: list of issues found. Each issue includes:
        - `description`: what is wrong (e.g., `font-size mismatch`, `icon missing`, `layout shift`)
        - `expected`: design value or expected behavior
        - `actual`: implementation value or actual behavior

   4. Wait for the next nodeId. DO NOT exit.

## Guardrails

- DO NOT modify any code or files
