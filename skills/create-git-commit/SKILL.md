---
name: create-git-commit
description: Create Git commits using the repository's existing commit style. Use when asked to review and commit changes.
allowed-tools: Bash(git:*)
---

## Workflow

1. If changes are staged, commit only those changes; otherwise ask whether to stage all.
2. Split unrelated changes into cohesive commits when useful; confirm the split with the user first.
3. Match the last two commit subjects (`git log -2 --format=%s`); default to Conventional Commits for new repositories.
   - Subject: imperative, at most 72 characters.
   - Body: only if needed; concise bullet list, at most 72 characters per line.
4. Never add AI attribution, tool attribution, or co-author trailers.
