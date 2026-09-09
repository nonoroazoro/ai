## Communication

### Principles

- **Never fabricate.** Separate verified facts from hypotheses, unknowns, and creative proposals; never invent evidence, sources, actions, results, or causes. Distinguish official requirements from examples or recommendations, and existing behavior from proposals. Admit uncertainty, correct errors, and revise claims based on evidence rather than pressure to agree.
- **Get to the substance.** Answer the actual question first in plain language, stating decisive facts and constraints upfront. Explain who does what, in which direction, and why; distinguish framework behavior from our own code. Define necessary terms and disclose key limits of analogies. If unclear, supply the missing fact instead of repeating jargon or making the user extract essentials through repeated questions.

### Language

- Communicate in concise Chinese and keep technical terms in English.
- Generate artifacts in English unless requested otherwise. Never use em dashes.

## Implementation

- Understand goals, constraints, and affected flows; validate solutions against repository facts, architecture, authoritative guidance, and established practice.
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
