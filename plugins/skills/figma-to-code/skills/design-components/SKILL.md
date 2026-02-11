---
name: design-components
description: Analyze Figma design and plan component spec
allowed-tools: mcp__figma-desktop__get_metadata, mcp__figma-desktop__get_screenshot
argument-hint: <figma-url>
---

Analyze Figma design structure and output a component spec for frontend implementation.

## Workflow

1. **Parse Figma URL** from `$ARGUMENTS` or spawn prompt context:
   - Extract `nodeId` from URL query param `node-id` (e.g., `?node-id=27-16255` → `27:16255`, replace `-` with `:`)
   - If `$ARGUMENTS` is empty or not a valid Figma URL, ASK THE USER

2. **Resolve `baseFolder`**:
   - If provided by Team Lead via spawn prompt context: use it
   - Otherwise default to `.design-components/`
   - Create the directory if it doesn't exist

3. **Fetch Figma design data**:
   - Call `get_metadata` with the nodeId — returns XML node tree with IDs, types, names, positions, sizes
   - Call `get_screenshot` with the nodeId — returns design screenshot for visual reference

4. **Analyze and plan component spec**:
   - Walk the metadata node tree, identify components at three levels:
     - **page**: the root container (1 per design)
     - **module**: visually distinct sections (header, sidebar, content area, footer)
     - **component**: reusable UI units within modules (buttons, cards, avatars, inputs)
   - Naming: PascalCase, describe the visual/business purpose (e.g., `ChatHeader`, not `Frame27`)

5. **Save to file** `{baseFolder}/component-spec.json`:

   ```json
   {
     "components": [
       {
         "name": "AnalysisFailedPage",
         "nodeId": "59:3079",
         "level": "page",
         "filePath": null,
         "children": [
           {
             "name": "Header",
             "nodeId": "59:3082",
             "level": "module",
             "filePath": null,
             "children": [
               { "name": "ProductIcon", "nodeId": "59:3085", "level": "component", "filePath": null },
               { "name": "BetaBadge", "nodeId": "181:1475", "level": "component", "filePath": null }
             ]
           }
         ]
       }
     ]
   }
   ```

6. **Generate inspector HTML** `{baseFolder}/component-spec-inspector.html`:
   - Read the template at `$SKILL_DIR/templates/inspector.html`
   - Replace the placeholder `/* __COMPONENT_SPEC_JSON__ */` with the actual JSON content from step 5
   - Write the result to `{baseFolder}/component-spec-inspector.html`
   - Tell the user they can open this file in a browser to visually inspect the component tree

## Design Principles

- **Single responsibility, appropriate granularity, good reusability**
- **Metadata structure is primary**: the designer's frame grouping and nesting hierarchy is the main basis for component splitting
- **Instance nodes are components**: Figma `instance` type means the designer defined it as reusable — respect that
- **Semantic frame names matter**: names with business meaning indicate intentional module boundaries
- **Screenshots for validation**: use the visual screenshot to verify metadata grouping makes sense, and to catch visual boundaries that metadata alone can't express
- Single text/icon nodes are NOT standalone components (unless they are `instance` nodes)

## Guardrails

- Every component MUST have a valid `nodeId` from the metadata — do not invent node IDs
- `filePath` is always `null` at this stage — `implement-components` will fill it later
- Output only to `{baseFolder}/component-spec.json` and `{baseFolder}/component-spec-inspector.html`
