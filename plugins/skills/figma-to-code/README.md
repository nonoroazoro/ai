# figma-to-code

Takes a Figma URL and turns it into working frontend code — automatically. Built on [Claude Code Agent Teams](https://docs.anthropic.com/en/docs/claude-code/agent-teams), it spins up a team of specialized agents that break down the design into components, generate code, then audit each component visually and stylistically, fixing issues until everything looks right.

## How it works

1. Run `/figma-to-code <figma-url>`
2. **design-components** analyzes the Figma design and splits it into component spec (`component-spec.json`)
3. **implement-components** reads the spec, pulls style values from Figma, and writes the actual code
4. For each component, it runs **visual audit** (screenshot comparison) and **style audit** (style comparison) in parallel
5. If a component doesn't pass, it fixes the code and re-audits — up to 3 rounds
6. Done. You get the code and a report.

## Usage

```
/figma-to-code <figma-url>
```

That's it. The pipeline handles everything from there.

You can also run individual steps standalone if you need to debug or re-run part of the pipeline:

| Skill | What it does |
|-------|-------------|
| `/design-components <figma-url>` | Just the component decomposition step |
| `/implement-components [spec-path]` | Just the code generation step (no audit loop) |
| `/audit-component-visual <node-id> <page-url>` | Screenshot comparison for a single component |
| `/audit-component-style <node-id> <page-url>` | Computed style comparison for a single component |

## Requirements

- Claude Code Agent Teams
- Figma Desktop MCP
- Playwright MCP

## Data

The only file that gets persisted is `.figma-to-code/component-spec.json`. Everything else — Figma data, audit results — lives in agent memory and doesn't touch disk.
