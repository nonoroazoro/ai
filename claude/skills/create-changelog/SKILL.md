---
name: create:changelog
description: Create changelogs
allowed-tools: Bash(git describe:*), Bash(git log:*), Bash(git tag:*)
---

Transform git commits into user-friendly changelogs following [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) specification.

## Workflow

1. Get commits since last release point:
   - If nothing found: use all commits

2. Categorize and transform into user-friendly changelogs:
   - DIRECTLY start with version header (e.g., `## [1.0.0] - 2024-01-01`), NO preamble, NO `[Unreleased]`
   - Order: Added → Changed → Deprecated → Removed → Fixed → Security

3. Write to existing changelog file, or `CHANGELOG.md` if none exists
