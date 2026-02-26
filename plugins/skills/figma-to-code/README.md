# figma-to-code

Takes a Figma URL and turns it into working frontend code, automatically. Built on [Claude Code Agent Teams](https://docs.anthropic.com/en/docs/claude-code/agent-teams), it spins up a team of specialized agents that decompose the design into a component spec, implement the code, then audit each node against the original design and fix issues, up to 3 rounds.

## How it works

1. Team Lead: **figma-to-code** collects project context, orchestrates the pipeline
2. Teammate: **design-components** decomposes the Figma design into a three-level component spec (page → module → component)
3. Teammate: **implement-components** writes code for each node, using Figma design context as reference
4. Teammate: **audit-component** compares each node's Figma design against the running implementation, scores fidelity
5. Nodes that don't pass get fixed and re-audited, up to 3 rounds

## Installation

See the [main README](../../../README.md#installation) for installation instructions.

**Requirements**:

- [Claude Code Agent Teams](https://docs.anthropic.com/en/docs/claude-code/agent-teams)
- [Figma Desktop MCP](https://www.npmjs.com/package/@anthropic-ai/claude-code-figma-mcp)
- [Playwright MCP](https://www.npmjs.com/package/@anthropic-ai/claude-code-playwright-mcp)

## Usage

One command to automatically create an Agent Team and handle everything:

```bash
/figma-to-code <figma-url> Create an Agent Team to complete the task
```

Individual skills can be run standalone for debugging or partial re-runs:

| Skill | What it does |
|-------|-------------|
| `/figma-to-code:design-components <figma-url>` | Component decomposition |
| `/figma-to-code:implement-components [spec-path]` | Code generation |
| `/figma-to-code:audit-component <node-id> <dev-server-url>` | Audit a single node against its Figma design |

## Data

Persisted files live in `.figma-to-code/`:

- `component-spec.json` - the component tree with nodeIds and file paths
- `component-spec-inspector.html` - open in browser to visually inspect the spec

Everything else (Figma data, audit results) lives in agent memory.
