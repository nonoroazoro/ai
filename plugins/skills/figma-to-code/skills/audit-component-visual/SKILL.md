---
name: audit-component-visual
description: Visual audit of a component by comparing Figma screenshot with implementation screenshot
allowed-tools: mcp__figma-desktop__get_screenshot, mcp__plugin_playwright_playwright__browser_navigate, mcp__plugin_playwright_playwright__browser_run_code, mcp__plugin_playwright_playwright__browser_wait_for, mcp__plugin_playwright_playwright__browser_take_screenshot
argument-hint: <node-id> <page-url>
---

Compare Figma design screenshot with implementation screenshot for a single component, report visual differences.

## Workflow

1. **Resolve params** from spawn prompt context or `$ARGUMENTS`:
   - `nodeId`: the Figma node ID of the component to audit
   - `pageURL`: the dev server URL where the component is rendered
   - If missing, STOP and report which param is missing

2. **Capture Figma design screenshot**:
   - Call `get_screenshot` with the `nodeId`

3. **Capture implementation screenshot**:
   - Navigate to `pageURL`
   - Use `browser_run_code` to locate `[data-node-id="{nodeId}"]` element and take a screenshot of it

4. **Compare both screenshots**:
   - Review both images simultaneously
   - Audit dimensions: layout, typography, color, size, missing elements, visual polish, etc.

5. **Output result**:
   - `nodeId`: the component's Figma node ID
   - `score`: 1-10 (10 = pixel-perfect, 1 = completely wrong)
   - `pass`: true if score ≥ 8
   - `issues`: list of specific problems, each with:
     - What is wrong (e.g., "button color is #333 instead of #1890ff")
     - Where in the component (e.g., "submit button in the footer area")
     - Suggested fix direction (e.g., "change background-color to match design")

## Guardrails

- Read-only: do NOT modify any code or files
- If `[data-node-id="{nodeId}"]` not found on page: score 1, pass: false, report why
