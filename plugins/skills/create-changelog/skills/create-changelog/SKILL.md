---
name: create-changelog
description: "Generate or update changelogs from git history following Keep a Changelog format. Triggers: changelog, release notes, what changed, version bump, release"
allowed-tools: Bash(git describe:*), Bash(git log:*), Bash(git tag:*)
---

# Create Changelog

Generate user-friendly changelogs from git commits following [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) specification.

## Workflow

1. **Find last release point:**
   ```bash
   git describe --tags --abbrev=0 2>/dev/null
   ```
   - If no tags found: use all commits

2. **Collect commits since release:**
   ```bash
   git log <last-tag>..HEAD --pretty=format:"%s" --no-merges
   ```

3. **Categorize each commit** into Keep a Changelog sections:

   | Section | Commit patterns |
   |---------|----------------|
   | Added | `feat:`, `add:`, new functionality |
   | Changed | `refactor:`, `change:`, behavior modifications |
   | Deprecated | `deprecate:`, sunset notices |
   | Removed | `remove:`, `delete:`, dropped features |
   | Fixed | `fix:`, `bugfix:`, corrections |
   | Security | `security:`, vulnerability patches |

4. **Write output** starting DIRECTLY with version header. NO preamble, NO `[Unreleased]`:
   ```markdown
   ## [1.2.0] - 2025-03-15

   ### Added
   - Support for custom templates (#42)
   ```

5. **Write to** existing changelog file, or `CHANGELOG.md` if none exists. Prepend new entry below the file header.

## Error Recovery

- If `git log` returns empty: inform user no changes found since last release
- If commit messages lack conventional prefixes: categorize by best guess, flag uncertain entries with `<!-- review -->` comment
- If existing changelog has non-standard format: preserve it, append new section at top
