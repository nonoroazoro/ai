---
name: design-components
description: Architecture and component design expert, design the component spec based on Figma design data and frontend best practices.
allowed-tools: mcp__figma-desktop__get_metadata, mcp__figma-desktop__get_screenshot
argument-hint: [figma-url]
---

## Workflow

1. **Resolve Params**:
   - `nodeId`: extract `node-id` query param from Figma URL in `$ARGUMENTS`, converting `-` to `:` (e.g., `27-16255` -> `27:16255`)
   - `baseFolder`: from Team Lead, defaults to `.design-components`

2. **Fetch Figma Design Data**:
   - `get_metadata` with `nodeId`, if the response is truncated, identify and call `get_metadata` on individual child nodes
   - `get_screenshot` with `nodeId`, this screenshot serves as the source of truth for visual validation

3. **Design Component Spec**:
   - Analyze screenshot and metadata to fully understand the Figma design
   - Design a sensible component tree, each node is one of these roles:
     - **page**: root container, largest granularity
     - **module**: design region split by visual boundaries (background, spacing, dividers), medium granularity, supports nesting
     - **component**: reusable UI unit, supports nesting
   - Design Rules:
     - Determine each node's role based on both visual layout and metadata nesting
     - Repeating structures MUST be extracted as components and marked with `repeat`
     - Each node MUST map to a `nodeId` from the metadata
     - Name: PascalCase, prioritize screenshot visual semantics over metadata (e.g., `ChatHeader`, not `Frame27`)
     - Description: one sentence, reflect what it does

4. **Save Component Spec** `{baseFolder}/component-spec.json`:

   ```json
   {
     "pages": [
       {
         "name": "IncidentDetailPage",
         "description": "Incident investigation page with alert summary and root cause analysis",
         "nodeId": "59:3079",
         "role": "page",
         "children": [
           { "name": "AlertSummary", "description": "Severity, service name, and triggered time", "nodeId": "59:3082", "role": "module" },
           {
             "name": "RootCauseAnalysis",
             "description": "Timeline and evidence for root cause investigation",
             "nodeId": "164:1890",
             "role": "module",
             "children": [
               {
                 "name": "EventTimeline",
                 "description": "Chronological sequence of related events",
                 "nodeId": "164:1892",
                 "role": "module",
                 "children": [
                   {
                     "name": "EventEntry",
                     "description": "Single event with timestamp, source, and impact",
                     "nodeId": "164:1893",
                     "role": "component",
                     "repeat": { "count": 3, "nodeIds": ["164:1893", "164:1912", "164:1935"] }
                   }
                 ]
               }
             ]
           }
         ]
       }
     ]
   }
   ```

5. **Generate Inspector HTML** `{baseFolder}/component-spec-inspector.html`:
   - Copy `templates/inspector.html` to `{baseFolder}/component-spec-inspector.html`
   - Replace the `/* __COMPONENT_SPEC_JSON__ */` with the JSON from step 4
   - Tell the user to open it in a browser to inspect the component spec
