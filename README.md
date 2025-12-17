# AI

Personal AI tools instructions and prompts collection.

## Structure

- **/claude** - Claude Code instructions (agents, commands, skills)
    - Install via [Claude Code plugin](https://code.claude.com/docs/en/plugins) *(recommended)*:

      ```bash
      /plugin marketplace add nonoroazoro/ai
      /plugin install linus@plugins
      ```

    - Install manually: Copy `CLAUDE.md`, `agents`, `commands`, etc. into your `~/.claude` folder.

- **/prompts** - Reusable prompts and meta-prompts

## The Design Philosophy of AI Instructions

**Core Principle**: Eliminate uncertainty in *planning*, maximize automation in *execution*.

### Two-Phase Workflow

**Phase 1: Plan (Human-Led)**

- **Deep interaction** with user to clarify requirements
- Confirm design decisions and trade-offs
- Output: Clear, executable task list

**Phase 2: Execute (Agent-Led)**

- **Minimal interaction** with user
- Agents autonomously handle:
    - MCP installation
    - Code implementation
    - Documentation lookup
    - Memory storage
    - Test generation and execution
