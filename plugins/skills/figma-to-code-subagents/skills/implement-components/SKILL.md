---
name: implement-components
description: Implement all nodes from component spec with Figma design context
allowed-tools: mcp__figma-desktop__get_design_context, mcp__figma-desktop__get_screenshot, mcp__figma-desktop__get_variable_defs
argument-hint: [component-spec-file-path]
---

## Workflow

1. **Resolve Params**:
   - `componentSpec`: from `$ARGUMENTS`
   - `projectContext`: from prompt context

2. **Bootstrap**:
   - If needed, set up a minimal project based on `projectContext`
   - Write by hand, DO NOT use third-party scaffolding CLI tools

3. **Implementation**
   - Implement in strict order:
      - Components → Modules → Pages
      - Complete one node before starting the next
      - Assemble bottom-up: implement all children before their parent
   - For each node in `componentSpec`:
     - `get_design_context` with `nodeId` to get design context (typically React + Tailwind code snippets)
     - `get_variable_defs` with `nodeId` to get design tokens (e.g., primary-500, primary-hover)
     - `get_screenshot` with `nodeId` to get visual reference
     - Treat Figma design context as the source of truth: exact dimensions, spacing, font sizes, icon sizes, etc.
     - Handle Figma assets:
       - Download all Figma assets (SVG, PNG, JPG) to local assets directory, do not modify downloaded content
       - Replace all Figma-hosted URLs (e.g. `http://localhost:3845/assets/...`) with local imports
       - Preserve original Figma DOM structure to avoid rendering issues, e.g.:
         ```javascript
         {/* ✅ This structure is what Figma gives you, keep it */}
         <div className="absolute inset-[X%_Y%]">
           <img src={icon} className="absolute block max-w-none size-full" />
         </div>

         {/* ❌ DO NOT flatten into this */}
         <img src={icon} className="absolute inset-[X%_Y%] size-full" />
         ```
     - Add `data-node-id` to the root element of every node, use `nodeId` by default, `repeat.nodeIds` for repeated nodes
     - Nodes with `repeat`: implement once, render `repeat.count` times
     - Implement node's internal interactions (hover, toggles, form validation, etc.)
     - Code directory structure MUST mirror the `componentSpec` hierarchy
   - After all nodes are implemented, wire up cross-node interactions (navigation, routing, shared state, etc.)

4. **Prepare for User Review**:
   - Create an example page in `./examples` directory that renders all implemented nodes
   - Start dev server by npm script
   - Output `devServerURL`

## Implementation Rules

- Follow `projectContext` (tech stack, styling, component library, reference docs, etc.)
- Reuse existing project components when possible
- Match existing project directory structure and export patterns
- Map Figma design tokens to project's token system, never hardcode token values
- Avoid inline styles unless required for dynamic values
