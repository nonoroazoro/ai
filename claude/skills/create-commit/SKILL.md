---
name: create:commit
description: Create git commits
allowed-tools: Bash(git add:*), Bash(git commit:*), Bash(git diff:*), Bash(git status:*)
argument-hint: [ai] (optional - add AI-Generated-By trailer)
---

## Workflow

1. Check staging: Run `git status`
   - If nothing staged: use `git add <file>` to stage specific relevant files

2. Analyze changes: Run `git diff --cached`
   - If multiple unrelated changes detected: split into separate commits by selectively adding files

3. Create commit message:
   - Use CONVENTIONAL COMMIT format:

      ```text
      <type>[!]: <description>

      [optional body]
      ```

   - Type: feat, fix, docs, style, refactor, perf, test, build, chore
   - Description: <72 chars, use imperative mood
   - Body:
      - Include only if necessary and format the body line-by-line using bullet points
      - CONSTRAINT: Do NOT add any "Generated with", "Co-Authored-By", or tool attribution text in the message body

4. Handle AI-generated trailer:
   - If `$ARGUMENTS` equals "ai": add trailer via `-m "AI-Generated-By: [Coding Tool Name]"`
   - If `$ARGUMENTS` is empty or any other value: complete the commit without trailer
