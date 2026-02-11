# figma-to-code Plugin Design

## Context

End-to-end pipeline from Figma design to frontend code: Figma URL → component decomposition → code implementation → per-component visual/style audit → fix → iterate until passing. Automates frontend fidelity checking, replacing manual visual comparison.

## Figma MCP Capabilities (Verified)

| Tool | Returns | Usage |
|------|---------|-------|
| `get_metadata` | XML node tree: ID, type, name, position, size | Structure analysis, component decomposition |
| `get_design_context` | React + Tailwind code with precise style values and `data-node-id` | Code reference (values are authoritative, code structure is reference only) |
| `get_screenshot` | Node screenshot PNG | Visual comparison |
| `get_variable_defs` | Design token map (color, font variables) | Supplementary reference |

Key constraints:

- About `get_design_context` return values:
    - **Layout, size, color, font, spacing values are authoritative** — must follow exactly
    - Do not copy the code verbatim — write code following project conventions and best practices

## Key Decisions

| Decision | Conclusion |
|----------|-----------|
| Architecture | 5 skills: figma-to-code, design-components, implement-components, audit-component-visual, audit-component-style |
| Data flow | `component-spec.json` is the only persisted file; Team Lead passes `baseFolder` to downstream agents; Figma data is not persisted; audit results flow in implement-components agent memory |
| Component identifier | `data-node-id` (consistent with Figma MCP output) |
| Iteration strategy | Per-component audit+fix loop, audit outputs score (1-10 scale, ≥8 = pass) |
| Frontend framework | Framework-agnostic, LLM infers from project context |

## Directory Structure

```
plugins/skills/figma-to-code/
├── .claude-plugin/
│   └── plugin.json
└── skills/
    ├── figma-to-code/              ← Team Lead: orchestrates the full pipeline
    │   └── SKILL.md
    ├── design-components/          ← Calls Figma MCP, decomposes design, outputs component-spec.json
    │   └── SKILL.md
    ├── implement-components/       ← Reads component-spec.json, calls Figma MCP, writes/fixes code
    │   └── SKILL.md
    ├── audit-component-visual/     ← Figma screenshot vs Playwright screenshot
    │   └── SKILL.md
    └── audit-component-style/      ← Figma design context style vs Playwright computed style
        └── SKILL.md
```

## Data Directory

```
.figma-to-code/
└── component-spec.json       ← Written by design-components, filePath backfilled by implement-components
```

### component-spec.json Format

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

- `design-components` writes name, nodeId, level; filePath is null
- `implement-components` backfills filePath after implementation (e.g., `src/components/Header.tsx`)
- `implement-components` iterates this file in Phase 3, spawning audit agents per component

## Full Pipeline (Agent Teams)

```
User: /figma-to-code <figma-url>

Phase 1: Component Decomposition
  design-components
    → Calls get_metadata + get_screenshot to analyze design
    → Outputs component-spec.json

Phase 2: Code Implementation
  implement-components
    → Reads component-spec.json
    → Calls get_design_context per component for code reference
    → Writes all component code following project conventions
    → Backfills filePath to component-spec.json

Phase 3: Per-component audit + fix loop (executed inside implement-components)
  implement-components creates example page, starts dev server, gets page URL
  for each component in component-spec.json:
    loop (max 3 rounds):
      implement-components spawns audit-component-visual agent  ┐ parallel
      implement-components spawns audit-component-style agent   ┘
      → implement-components reads agent results (score + pass + issues)
      → Both agents return pass: true → break, move to next component
      → Not passing → implement-components fixes code based on issues → continue loop

Phase 4: Done
  implement-components exits → Team Lead outputs final report, cleans up team
```

### Task Dependency

```
Task 1: design-components          → blockedBy: none
Task 2: implement-components       → blockedBy: [1]
  Internal spawns:
    audit-component-visual(componentA)  ┐ parallel
    audit-component-style(componentA)   ┘
    → Not passing → implement-components fixes → re-spawn audit
    → Passing → next component
```

### Teammate Assignment

| Teammate | Lifecycle | subagent_type | Tools |
|----------|-----------|--------------|-------|
| design-components | Short-lived | general-purpose | Figma MCP (get_metadata, get_screenshot), Read, Write |
| implement-components | Long-lived | general-purpose | Figma MCP (get_design_context, get_variable_defs), Read, Write, Edit, Bash, Task (spawn sub-agents) |
| audit-component-visual | Short-lived (spawned by implement-components) | general-purpose | Figma MCP (get_screenshot), Playwright MCP (browser_*) |
| audit-component-style | Short-lived (spawned by implement-components) | general-purpose | Figma MCP (get_design_context), Playwright MCP (browser_*) |

## Skill Details

### 1. `figma-to-code` (Team Lead)

- **Trigger**: `/figma-to-code <figma-url>`
- **Tools**: TeamCreate, TaskCreate, TaskUpdate, TaskList, TeamDelete, Bash(mkdir), Read, Task (spawn agent)
- **Responsibilities**:
  1. Validate Figma URL
  2. Collect project tech info (ask the user):
     - Tech stack: React / Vue / Svelte / other
     - Component library: Arco Design / Ant Design / MUI / custom / none
     - User may provide reference docs (project conventions, design system docs, etc.)
     - If `package.json` exists, read it first to infer — only ask for what's missing
  3. Create `.figma-to-code/` directory (the `baseFolder`)
  4. Spawn design-components agent with Figma URL + baseFolder, wait for completion
  5. Spawn implement-components agent with baseFolder + tech stack info + user-provided reference docs, wait for completion
     - implement-components handles Phase 2 (code) and Phase 3 (audit+fix loop) internally
  6. Output final report, clean up team

### 2. `design-components`

- **Trigger**: `/design-components <figma-url>`
- **Tools**: Figma MCP (get_metadata, get_screenshot), Read, Write
- **Input**: Figma URL (from user or Team Lead spawn prompt)
- **Output**: `{baseFolder}/component-spec.json` (standalone default baseFolder: `.design-components/`)
- **Responsibilities**:
  1. Parse nodeId from Figma URL
  2. Call `get_metadata` for node tree structure (frame hierarchy, instance nodes, names, positions, sizes)
  3. Call `get_screenshot` for visual reference
  4. Analyze metadata structure, plan component spec
  5. Output component-spec.json (name, nodeId, level, filePath=null)
- **Decomposition principles**:
    - **Follow frontend component best practices**: single responsibility, appropriate granularity, good reusability
    - **Metadata structure is primary**: designer's frame grouping and nesting hierarchy is the main basis
    - **Instance nodes are components**: Figma `instance` type means the designer defined it as reusable — respect that
    - **Semantic frame names matter**: names with business meaning indicate intentional module boundaries
    - **Screenshots for validation**: verify metadata grouping makes sense, catch visual boundaries metadata can't express
    - Single text/icon nodes are NOT standalone components (unless they are `instance` nodes)

### 3. `implement-components`

- **Trigger**: `/implement-components [component-spec-file-path]` (standalone only does Phase 2)
- **Tools**: Figma MCP (get_design_context, get_variable_defs), Read, Write, Edit, Bash, Task (spawn sub-agents)
- **Input**: `{baseFolder}/component-spec.json` (standalone default baseFolder: `.implement-components/`)
- **Output**: Project source code files, backfills filePath in component-spec.json
- **Phase 2 — Write all code**:
  1. Read component-spec.json for component list
  2. Call `get_design_context` per component for code reference (leaf-first, bottom-up)
  3. Extract layout, size, color, font, spacing values (authoritative, must follow exactly)
  4. Write code following project conventions (do NOT copy Figma-generated code structure)
  5. Add `data-node-id` to each component's outermost DOM element
  6. Backfill filePath to component-spec.json
- **Phase 3 — Per-component audit+fix loop** (only when orchestrated by Team Lead):
  1. Create example page rendering all implemented components, start dev server, get page URL
  2. For each component in component-spec.json:
     - Spawn audit-component-visual + audit-component-style in parallel, pass nodeId + page URL
     - Both agents return `pass: true` → next component
     - Not passing → fix code based on issues → re-spawn audit agents → max 3 rounds
  3. Stop dev server when done

### 4. `audit-component-visual`

- **Trigger**: `/audit-component-visual <node-id> <page-url>`
- **Tools**: Figma MCP (get_screenshot), Playwright MCP (browser_navigate, browser_run_code, browser_wait_for, browser_take_screenshot)
- **Input**: nodeId + page URL from implement-components spawn prompt
- **Output**: Returns result to implement-components agent; standalone outputs directly to user
- **Responsibilities**:
  1. Call Figma MCP `get_screenshot` for the design screenshot of the specified component
  2. Playwright opens page, uses `browser_run_code` to locate `[data-node-id="xxx"]` element and screenshot it
  3. LLM reviews both screenshots simultaneously, audits visual fidelity
  4. Output: nodeId, score (1-10, 10=pixel-perfect), pass (score ≥ 8), issues (with specific problem, location, suggested fix)
- **Audit dimensions**: layout, typography, color, size, missing elements, visual polish
- **Pass/fail**: score ≥ 8 = pass
- **Missing params**: STOP and report reason, do NOT ask user

### 5. `audit-component-style`

- **Trigger**: `/audit-component-style <node-id> <page-url>`
- **Tools**: Figma MCP (get_design_context), Playwright MCP (browser_navigate, browser_run_code, browser_evaluate, browser_wait_for)
- **Input**: nodeId + page URL from implement-components spawn prompt
- **Output**: Returns result to implement-components agent; standalone outputs directly to user
- **Responsibilities**:
  1. Call Figma MCP `get_design_context` for the component code, extract design values
  2. Playwright opens page, uses `browser_evaluate` on `[data-node-id="xxx"]` to run `getComputedStyle()` and extract CSS properties
  3. Compare each property against design values with tolerance
  4. Output: nodeId, score (1-10, by proportion of properties within tolerance), pass (score ≥ 8), per-property comparison (with suggested fix)
- **Properties and tolerances**:

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

- **Pass/fail**: score ≥ 8 = pass
- **Missing params**: STOP and report reason, do NOT ask user

## Files

1. `plugins/skills/figma-to-code/.claude-plugin/plugin.json`
2. `plugins/skills/figma-to-code/skills/figma-to-code/SKILL.md`
3. `plugins/skills/figma-to-code/skills/design-components/SKILL.md`
4. `plugins/skills/figma-to-code/skills/implement-components/SKILL.md`
5. `plugins/skills/figma-to-code/skills/audit-component-visual/SKILL.md`
6. `plugins/skills/figma-to-code/skills/audit-component-style/SKILL.md`

## Verification

1. Install plugin, confirm 5 `/skill` commands are available
2. `/design-components <figma-url>` — verify component decomposition
3. `/implement-components` — verify code generation
4. `/audit-component-visual <node-id> <page-url>` — verify screenshot comparison
5. `/audit-component-style <node-id> <page-url>` — verify style comparison
6. `/figma-to-code <figma-url>` — run full pipeline
7. Verify per-component audit+fix loop works correctly
8. Verify single component exits after max 3 rounds
