# AI

Reusable AI agent skills and plugins for Claude Code and Codex.

## Agent Skills

General-purpose skills for compatible AI agents, following the [Agent Skills specification](https://agentskills.io/specification.md) and [skill creation best practices](https://agentskills.io/skill-creation/best-practices).

- [**code-review**](skills/code-review/SKILL.md) - Review code changes for correctness, security, architecture, maintainability, and performance
- [**create-changelog**](skills/create-changelog/SKILL.md) - Generate changelog entries from Git history following [Keep a Changelog](https://keepachangelog.com/en/1.1.0/)
- [**create-git-commit**](skills/create-git-commit/SKILL.md) - Create Git commits using the repository's existing commit style

---

## Installation

For Codex, merge [AGENTS.md](AGENTS.md) into `~/.codex/AGENTS.md` and start a new session.

### Codex

Add marketplace:

```bash
codex plugin marketplace add nonoroazoro/ai
```

Install plugins:

```bash
codex plugin add plugins@plugins
```

### Claude Code

Add marketplace:

```bash
/plugin marketplace add nonoroazoro/ai
```

Install plugins:

```bash
/plugin
```

---

## Claude Code Plugins

### Coding

- [**figma-to-code**](plugins/skills/figma-to-code) - Figma design to frontend code, `Agent Teams` version **(Recommended)**
- [**figma-to-code-subagents**](plugins/skills/figma-to-code-subagents) - Figma design to frontend code, `Subagents` version

### Git

- [**code-review**](skills/code-review/SKILL.md) - Review code changes for correctness, security, architecture, maintainability, and performance
- [**create-changelog**](skills/create-changelog/SKILL.md) - Generate changelog entries from Git history following [Keep a Changelog](https://keepachangelog.com/en/1.1.0/)
- [**create-git-commit**](skills/create-git-commit/SKILL.md) - Create Git commits using the repository's existing commit style

### Misc

- [**improve-prompt**](plugins/skills/improve-prompt) - Optimize prompts for AI agents and skills
- [**mcp-manager**](plugins/skills/mcp-manager) - MCP lifecycle management
- [**memory-builder**](plugins/skills/memory-builder) - Knowledge graph management
- [**tester**](plugins/skills/tester) - Analyze, generate, and execute test cases
