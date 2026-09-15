## Code

1. Follow framework contracts and existing conventions before these defaults.

2. Prefix private class members with `_`.

3. Mirror source paths in tests; include required fields and types in mocks.

## JavaScript

1. Keep each class, type, interface, or enum in its own file.

2. Group functions by cohesive responsibility.

3. Use PascalCase for type and component files; lowercase for utilities.

4. Use named exports and `export * from` in `index.ts` barrels.

5. Import through barrels instead of internal module paths.

6. Use multi-line JSDoc, `{@link}` references, and `@param` without a `-` separator.

## Validation

1. Prefer repository-defined commands; find them in scripts and CI configuration.

2. Use configured linters, formatters, and type checkers for mechanical rules.

3. Run behavior, security, and accessibility checks relevant to the change.

4. Report checks that could not run and their blockers.
