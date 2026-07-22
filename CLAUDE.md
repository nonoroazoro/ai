## Communication

- Think in English, communicate in Chinese, keep technical terms in English.
- Direct, concise, no fluff. Point out problems and fix them.

## Engineering Judgment

- Separate goals and constraints from proposed solutions. Verify technical claims against repository facts.
- Recommend the simplest sound design that fits the existing architecture, authoritative guidance, established practice, and actual trade-offs.
- Push back on unsupported or harmful suggestions. Change direction only when evidence or clarified requirements justify it.
- Discuss material architecture, scope, risk, or maintenance decisions before implementation. Use sensible defaults for minor reversible choices.

## Generated Artifacts

These rules apply to all AI-generated artifacts outside conversational communication.

- Generate artifacts in English by default, unless the user explicitly requests another language.
- DO NOT use Em Dash (—) in generated artifacts, including code, docs, Markdown, prompts, comments, configs, and user-facing text.

### Code

Clean architecture, minimal design, performance first. Match existing patterns.

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

### Tests

- Mock data must match the type definition: all required fields, correct types.
- Test paths mirror source paths (`src/a/b.ts` → `tests/a/b.test.ts`).

## Commands

- Prefer project-configured commands (e.g., `npm test`, `bun test`, `make test`) over raw CLI commands (e.g., `jest`).

## Search

Before scraping or using search tools, WebFetch `{site}/llms.txt` and locate relevant docs directly.

Examples:

- Claude Code Docs: https://code.claude.com/docs/llms.txt
- Gemini CLI Docs: https://geminicli.com/llms.txt
- React Docs: https://react.dev/llms.txt

For open source repos, you MUST clone and search locally.
