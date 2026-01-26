# Claude Code Instructions

Personal Claude Code instructions, including agents and skills.

## Usage

- Install manually: Copy `CLAUDE.md`, `agents`, `skills`, etc. into your `~/.claude` folder.

- Install via [Claude Code plugin](https://code.claude.com/docs/en/plugins) *(not recommended yet, lack of dependency management, etc.)*:

    ```bash
    /plugin marketplace add nonoroazoro/ai
    /plugin install linus@plugins
    ```

## Agents

- **mcp-manager** - MCP server lifecycle management
- **memory-builder** - Knowledge graph construction

## Skills

- **/create:commit** - Create git commits
- **/create:changelog** - Create changelogs
- **/improve:prompt** - Optimize prompts
