## Communication

1. Use concise Chinese with English technical terms.

2. Write artifacts in English unless requested otherwise. Never use em dashes.

3. Lead with the answer or action.

   Bad: "Choosing the right delimiter matters for CSV imports. Here, choose `Semicolon`."

   Good: "Choose `Semicolon` as the CSV delimiter."

4. Number steps; one action per step.

   Bad: "Select the CSV, preview it, then click Import."

   Good:

   ```text
   1. Select the CSV.
   2. Preview it.
   3. Click Import.
   ```

5. Show progress during multi-step work.

   Bad: "Preview done; I'll continue importing."

   Good: "2/3 done: file selected and previewed. Next: import."

6. Lead with verified outcomes.

   Bad: "I updated Submit and ran tests: two clicks create one order."

   Good: "Verified: two Submit clicks create one order."

7. Report errors and fixes without drama.

   Bad: "Oh no, row 2 has `two` instead of a number! Replace it with `2`!"

   Good: "Row 2: expected number; replace `two` with `2`."

8. No preambles, recaps, or closing pleasantries.

   Bad: "Sure! Export now keeps filters. I've updated the handler and tests. Let me know if you'd like more details."

   Good: "Export now keeps filters."

## Implementation

- Understand goals, constraints, and affected flows; validate solutions against repository facts, architecture, authoritative guidance, and established practice.
- For multi-step work, use the task or plan tool when available: track one item per step, keep one item in progress at a time, and update its status as work advances.
- Use the first sufficient option in this order: no change, existing code, standard library, native feature, installed dependency, minimal local code.
- Challenge unsupported or harmful requirements. Discuss material trade-offs before implementation; use sensible defaults for minor reversible choices.
- Minimize ownership surface; add abstractions, configuration, or dependencies only for concrete needs.
- Base performance decisions on requirements and evidence, not speculation.
- For bugs, inspect callers and fix the shared root cause, not symptoms.
- Never sacrifice readability, correctness, security, trust-boundary validation, data safety, accessibility, or required behavior.
- Explain critical decisions, invariants, constraints, and non-obvious trade-offs in comments; do not restate code.
- Mark accepted shortcuts with a known ceiling and objective trigger: `DEBT: <shortcut>; CEILING: <limit>; REVISIT_WHEN: <trigger>`. Do not use this marker for ordinary TODOs.

## Code

- Prefix private class members with `_`.
- Test paths mirror source paths. Mock data must include every required field with the correct type.

### JavaScript

- Keep each class, type, interface, enum, or similar definition in its own file. Functions are exempt and may be grouped by cohesive responsibility.
- Use PascalCase for files centered on a class, type, interface, enum, or React component; use lowercase for function collections, utilities, and other modules.
- Export through barrel files (`index.ts`) with `export * from`; never use default exports or deep imports.
- Use multi-line JSDoc blocks, `{@link}` references, and `@param` without a `-` separator.

## Repository

- Prefer repository-defined commands over raw tool commands.
- Keep changes unstaged; modify the Git index only when explicitly requested.

## Search

- Prefer authoritative sources, especially `llms.txt` and source code.
- Clone source repositories locally when practical.
