---
name: code-review
description: Review code changes or repositories for correctness, security, performance, architecture, maintainability, dead code, unnecessary complexity, and technical debt.
allowed-tools: Bash(git:*)
---

## Workflow

1. Set review scope.
   - Honor an explicit diff, commit, branch, pull request, path, or repository; establish the comparison base for changes. Clarify only material ambiguity.
   - With no scope given, use the whole repository for audits, technical debt, or over-engineering reviews; otherwise review all staged, unstaged, and untracked changes.
   - Report only introduced or worsened issues in change reviews; include existing issues in reviews of current code. Keep findings within the selected scope.
2. Understand the code. Read applicable agent instructions and the goal. Treat the diff and code as evidence; inspect affected flows, callers, tests, dependencies, history, and project checks as needed. Distinguish intended behavior from implementation.
3. Check behavior and safety.
   - Review correctness, security, trust-boundary validation, data safety, accessibility, and repository conventions. Inspect callers for symptom-level fixes where a shared root cause exists.
   - Check tests appropriate to the reviewed behavior and repeated mistakes visible in history. Run required and relevant project checks; investigate failures affecting the review.
4. Check architecture and design.
   - Trace responsibility boundaries and dependency direction for unnecessary coupling and duplicated sources of truth; assess maintainability against actual requirements.
   - Review duplicate repository functionality, custom standard-library or native-platform behavior, avoidable dependencies, speculative abstractions or configuration, and semantic-free wrappers.
5. Check dead and obsolete code. Identify unused code, configuration, dependencies, and obsolete or unreachable paths. Verify safe removal against callers, runtime registration, dynamic loading, and supported external consumers; missing static references alone are insufficient.
6. Check performance, memory, and resource usage. Follow frequent paths, growing inputs, and resource lifetimes for unnecessary work, scaling problems, blocking, excessive allocation, and leaks. Establish impact with code evidence or measurements; do not invent savings estimates.
7. Validate and consolidate findings.
   - Report all actionable, evidence-backed issues within scope, regardless of severity or correction size. Architecture, maintenance, and dead-code issues qualify even when behavior is correct and tests pass. Exclude stylistic preferences and speculation; include tool-detected failures only when material to the review.
   - Line count is not a quality metric. Do not flag necessary validation, error handling, security, accessibility, tests, or justified abstractions as bloat. Dependencies are justified when they materially improve correctness, security, interoperability, or maintenance.
   - Check `DEBT:` markers for missing `CEILING:` or objective `REVISIT_WHEN:`, reached triggers, or unjustified shortcuts. List valid markers only when requested.
   - Merge findings sharing a root cause and correction, including related `DEBT:` issues.
8. Report findings by severity with practical impact, evidence, an exact `file:line` reference, and a correction addressing the root cause. Prefer smaller changes when equally effective; recommend substantial refactoring or redesign when warranted and explain trade-offs. Do not approve change reviews with findings; if none, say so. Summarize each review area's coverage and results, distinguishing no findings from unverified checks or areas.
