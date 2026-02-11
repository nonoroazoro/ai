---
name: audit-component-style
description: Style audit of a component by comparing Figma design values with computed CSS
allowed-tools: mcp__figma-desktop__get_design_context, mcp__plugin_playwright_playwright__browser_navigate, mcp__plugin_playwright_playwright__browser_run_code, mcp__plugin_playwright_playwright__browser_evaluate, mcp__plugin_playwright_playwright__browser_wait_for
argument-hint: <node-id> <page-url>
---

Compare Figma design values with actual computed CSS for a single component, report style deviations.

## Workflow

1. **Resolve params** from spawn prompt context or `$ARGUMENTS`:
   - `nodeId`: the Figma node ID of the component to audit
   - `pageURL`: the dev server URL where the component is rendered
   - If missing, STOP and report which param is missing

2. **Extract design values**:
   - Call `get_design_context` with the `nodeId`
   - Parse the returned code to extract design values (font-size, color, spacing, etc.)

3. **Extract implementation values**:
   - Navigate to `pageURL`
   - Use `browser_evaluate` on `[data-node-id="{nodeId}"]` to run `getComputedStyle()` and extract CSS properties

4. **Compare with tolerance**:

   | Property | Tolerance |
   |----------|-----------|
   | font-size | ±1px |
   | font-weight | exact |
   | line-height | ±2px |
   | color, background-color | RGB channel delta ≤ 10 |
   | padding, margin | ±2px |
   | border-radius | ±1px |
   | width, height | ±2px |
   | gap | ±2px |

5. **Output result**:
   - `nodeId`: the component's Figma node ID
   - `score`: 1-10 (proportion of properties within tolerance, e.g., 8/10 properties pass → score 8)
   - `pass`: true if score ≥ 8
   - `properties`: per-property comparison, each with:
     - Property name
     - Design value vs implementation value
     - Within tolerance: yes/no
     - Suggested fix if not (e.g., "change font-size from 16px to 14px")

## Guardrails

- Read-only: do NOT modify any code or files
- If `[data-node-id="{nodeId}"]` not found on page: score 1, pass: false, report why
