---
name: code-review
description: Review code changes or repositories for correctness, security, performance, maintainability, unnecessary complexity, and technical debt.
allowed-tools: Bash(git:*)
---

## Workflow

1. Infer the review scope:
   - Honor an explicit diff, commit, branch, pull request, path, or repository.
   - When no scope is given, use the whole repository for audits, technical debt, or over-engineering reviews.
   - Otherwise review all staged, unstaged, and untracked changes.
   - Ask only when the scope remains materially ambiguous.
2. Read the applicable agent instructions and understand the goal. Treat the diff and code as the source of truth; inspect affected flows, callers, tests, dependencies, history, and project checks as needed.
3. Review for:
   - Correctness, security, trust-boundary validation, data safety, and accessibility.
   - Architecture, responsibility boundaries, maintainability, and repository conventions.
   - Unnecessary work, duplicate repository functionality, custom standard-library or native-platform behavior, avoidable dependencies, speculative abstractions or configuration, semantic-free wrappers, and obsolete or unreachable code.
   - Symptom-level fixes where a shared root cause exists.
   - Evidence-based performance, memory, and resource usage.
   - Tests appropriate to changed behavior, relevant project-check failures, and repeated mistakes visible in history.
   - `DEBT:` markers missing a `CEILING:` or objective `REVISIT_WHEN:`, whose trigger has been reached, or whose shortcut is no longer justified. List valid markers only when requested.
4. Line count is not a quality metric. Do not flag necessary validation, error handling, security, accessibility, tests, or architecture-consistent abstractions as bloat. Dependencies are justified when they materially improve correctness, security, interoperability, or maintenance.
5. Report only actionable, evidence-backed issues. Exclude stylistic preferences and speculation. For change reviews, exclude pre-existing issues; for repository reviews, include existing issues within scope. Include tool-detected failures only when material to the review.
6. Report findings by severity with practical impact, evidence, the smallest sound correction, and an exact `file:line` reference. Do not invent savings estimates. Do not approve change reviews with findings; if none, say so and note unverified checks or areas.
