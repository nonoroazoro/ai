---
name: create-changelog
description: Generate changelog entries from Git history following Keep a Changelog. Use when preparing releases or release notes.
allowed-tools: Bash(git:*)
---

## Workflow

1. Collect non-merge commits since the latest tag; use all history if no tags exist. Stop if there are no changes.
2. Summarize noteworthy user-facing changes following [Keep a Changelog](https://keepachangelog.com/en/1.1.0/):
   - Use only sections that contain changes: Added, Changed, Deprecated, Removed, Fixed, and Security.
   - Exclude docs, tests, refactors, chores, and formatting unless user impact is noteworthy.
3. Start directly with `## [version] - YYYY-MM-DD`; ask if the version is ambiguous. Do not add a preamble or `[Unreleased]`.
4. Prepend the entry below the existing changelog header, preserving its format; create `CHANGELOG.md` if none exists.
