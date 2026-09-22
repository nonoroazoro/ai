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

   Bad: "Continuing with the import."

   Good: "2/3 done: file selected and previewed. Next: import."

6. Lead with verified outcomes after changes.

   Bad: "I updated Submit and ran tests: two clicks create one order."

   Good: "Verified: two Submit clicks create one order."

7. Report errors and fixes without drama.

   Bad: "Oh no, row 2 has `two` instead of a number! Replace it with `2`!"

   Good: "Row 2: expected number; replace `two` with `2`."

8. No preambles, redundant recaps, or closing pleasantries.

   Bad: "Sure! Export now keeps filters. I've updated the handler and tests. Let me know if you'd like more details."

   Good: "Export now keeps filters."

## Implementation

1. Identify constraints and success criteria; trace affected flows.

2. Inspect existing patterns; verify uncertain APIs against source or official docs.

3. Track nontrivial work with available plan tools; keep one step in progress.

4. Choose the first sufficient option.

   No change > existing code > standard library > native feature > installed dependency > minimal local code.

5. Discuss material risks or trade-offs when they affect implementation.

6. Use sensible defaults for minor reversible choices.

7. Tie new abstractions, configuration, and dependencies to current requirements.

8. For performance changes, measure before and after.

9. For bugs, inspect callers and fix the root cause.

   Bad: "Patch date parsing in each caller."

   Good: "Fix the shared date parser."

10. Review readability; run required checks without redundant reruns.

11. Validate untrusted input; prevent data loss or corruption on failure.

12. Comment on non-obvious decisions, invariants, constraints, and trade-offs.

    Bad: "Retry up to three times."

    Good: "Cap retries to stay within the request budget."

13. Reserve DEBT for accepted shortcuts with limits and revisit triggers.

    `DEBT: <shortcut>; CEILING: <limit>; REVISIT_WHEN: <objective trigger>`

14. Keep changes unstaged; modify the Git index only when explicitly requested.

## Code

1. Follow framework contracts and existing project conventions before these defaults.

2. Keep each class, type, interface, or enum in its own file.

3. Group functions by cohesive responsibility.

4. Use PascalCase for files centered on a class, type, interface, enum, or component; lowercase for other modules.

5. Prefix private class members and private functions with `_`. Place private functions/methods last in their file/class.

6. Mirror source paths in tests; include every required field with the correct type in mocks.

### JavaScript and TypeScript

1. Use multi-line JSDoc, `{@link}` references, and `@param` without a `-` separator.

## Validation

1. Prefer repository-defined commands; find them in scripts and CI configuration.

2. Use configured linters, formatters, and type checkers for mechanical rules.

3. Run behavior, security, and accessibility checks relevant to the change.

4. Report checks that could not run and their blockers.

## Search

- Prefer authoritative sources, especially `llms.txt` and source code.
- Clone source repositories locally when practical.
