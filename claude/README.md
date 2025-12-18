# Claude Code Instructions

Personal Claude Code instructions, including agents, commands, and skills.

## Agents

Specialized agents for specific tasks:

- **mcp-manager** - Manage MCP servers
- **memory-builder** - Build knowledge systems
- **tester** - Comprehensive test quality assurance
- **library-researcher** - Research libraries and frameworks

## Commands

Quick commands for common workflows:

- **/commit** - Create conventional git commits

## Skills (WIP)

Coming soon.

## Usage

- Install manually: Copy `CLAUDE.md`, `agents`, `commands`, etc. into your `~/.claude` folder.

- Install via [Claude Code plugin](https://code.claude.com/docs/en/plugins) *(not recommended yet, lack of dependency management, etc.)*:

    ```bash
    /plugin marketplace add nonoroazoro/ai
    /plugin install linus@plugins
    ```

Instructions will be auto-loaded by Claude Code:

- **Commands**: Type `/command-name` to invoke
- **Agents**: Auto-selected based on tasks
- **Skills**: Auto-selected by agents
