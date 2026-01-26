---
name: create:changelog
description: Create changelogs
allowed-tools: Bash(git describe:*), Bash(git diff:*), Bash(git log:*), Bash(git rev-list:*), Bash(git show:*), Bash(git tag:*)
---

Transform git commits into user-friendly changelogs following [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) specification.

## Workflow

1. Find last release point (tag, version commit, etc.) as **baseline**:
   - If nothing found: use all commits

2. Get commits since **baseline**

3. Categorize and output in this order (omit empty sections):
   - Added → Changed → Deprecated → Removed → Fixed → Security
