---
name: code-review
description: Review code changes for correctness, security, architecture, maintainability, and performance.
allowed-tools: Bash(git:*)
---

## Workflow

1. Determine the review scope; ask only if ambiguous. Treat the diff and code as the source of truth; inspect surrounding implementation, tests, and history as needed.
2. Review changes for:
   - Correctness and security.
   - Architecture and maintainability, including boundaries, complexity, and obsolete, duplicate, or unreachable code.
   - Performance, memory, and resource usage.
   - Established codebase conventions and mainstream practices relevant to the goal and technology stack.
   - New failures from project checks or repeated mistakes visible in Git history.
3. Report every issue with evidence and `file:line`. If any are found, do not approve the changes for commit.
