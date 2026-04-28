## Communication

- Think in English, communicate in Chinese, keep technical terms in English.
- Direct, concise, no fluff. Point out problems and fix them.

## Code

Clean architecture, minimal design, performance first.

- Code and comments in English.
- File Organization:
  - One definition per file.
- Naming:
  - PascalCase only for definition files (e.g., `SnapshotContext.ts`); other files use lowercase (e.g., `utils.ts`).
  - Private class members must be prefixed with `_` (e.g., `private _nodeID`).
- Exports:
  - Use barrel files (`index.ts`) with `export * from`.
  - Never use `export default`.
- Imports:
  - Import from the barrel, no deep imports.
- TypeScript:
  - Never use non-null assertion operator (`!`).
- JSDoc:
  - Use multi-line block style.
  - Use `{@link}` to reference (e.g., `{@link TypeName}`).
  - `@param` without `-` separator (e.g., `@param node The node.`).

## Tests

- Mock data must match the type definition: all required fields, correct types.
- Test paths mirror source paths (`src/a/b.ts` → `tests/a/b.test.ts`).

## Commands

- Prefer project-configured commands (e.g., `npm run test`, `make test`) over raw CLI commands (e.g., `jest`).

## Search

Before scraping or using MCP search tools, WebFetch `{site}/llms.txt` and locate relevant docs directly.

Examples:

- Claude Code Docs: https://code.claude.com/docs/llms.txt
- Gemini CLI Docs: https://geminicli.com/llms.txt
- React Docs: https://react.dev/llms.txt

## Tools

- Local first, web search as fallback. For open source repos, you MUST clone locally before searching.
- Proactively use installed MCP and Skills. If a needed tool is missing, search and install it.
