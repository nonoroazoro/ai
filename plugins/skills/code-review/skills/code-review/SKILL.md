---
name: code-review
description: Code review
allowed-tools: Bash(git diff:*), Bash(git log:*), Bash(git blame:*), Bash(git rev-parse:*), Bash(git branch:*), Bash(git show:*), Bash(git status:*)
---

Code review for local code changes with confidence scoring and false positive filtering.

## Workflow

1. Auto-detect diff scope from `git status`. Ask the user if ambiguous.

2. Gather context (3 parallel Haiku agents): CLAUDE.md file paths, change summary, modified file list.

3. Deep review (5 parallel Sonnet agents). Each returns `{file, line, description, evidence, category}`:
   - CLAUDE.md compliance: only flag what CLAUDE.md explicitly requires.
   - Bugs: focus on the diff. Ignore linter/typechecker-catchable issues.
   - Security: only flag issues introduced by this change.
   - Git history: flag repeated past mistakes or convention violations.
   - Performance: only flag measurable impact.

4. Score each finding (parallel Haiku agents, one per finding) with diff context and CLAUDE.md paths. For CLAUDE.md-flagged issues, verify CLAUDE.md actually calls it out. Rubric (provide verbatim):
   - 0: False positive, pre-existing, or does not hold up to scrutiny.
   - 25: Unverifiable. Stylistic issue not explicitly in CLAUDE.md.
   - 50: Real but minor. Unlikely in practice.
   - 75: Verified. Will impact functionality, or directly violates CLAUDE.md.
   - 100: Confirmed critical. Happens frequently. Evidence proves it.

5. Filter and classify findings scored >= 75. Report clean if none remain.
   - P0 (Blocker): score 100 with security or data loss risk.
   - P1 (Must fix): score >= 85, correctness or CLAUDE.md violation.
   - P2 (Should fix): score >= 75, performance or maintainability.

6. Print results.

## False Positives

Provide to scoring agents:

- Pre-existing issues or issues on unmodified lines
- Linter/typechecker/compiler-catchable issues
- General quality concerns unless CLAUDE.md requires them
- CLAUDE.md violations silenced by inline ignore comments
- Intentional functionality changes related to the broader change

## Output Format

```
### Code Review

Found {n} issues:

1. [P0] {description} ({source: CLAUDE.md quote or code evidence})

   {file}:{line}

2. [P1] {description} ({source})

   {file}:{line}
```

If no issues survive filtering:

```
### Code Review

No issues found. Checked for bugs, security, performance, and CLAUDE.md compliance.
```
