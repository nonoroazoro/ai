---
allowed-tools: Bash(git add:*), Bash(git status:*), Bash(git commit:*), Bash(git diff:*)
argument-hint: [ai] (optional - add AI-Generated-By trailer)
description: Create git commits
---

## Workflow

1. Check staging: Run `git status`
   - If nothing staged: run `git add` for relevant changes

2. Analyze changes: Run `git diff --cached`
   - If multiple unrelated changes detected: split into separate commits by selectively adding files

3. Create commit message:
   - Use CONVENTIONAL COMMIT format:

      ```text
      <type>: <description>
      [optional body]
      ```

   - Type: feat, fix, docs, style, refactor, perf, test, chore
   - Description: <72 chars, use imperative mood
   - Body: When including a body, keep it precise, concise, and well-formatted
   - IMPORTANT: Do NOT add any "Generated with", "Co-Authored-By", or tool attribution text in the message body

4. Handle AI-generated trailer:
   - If $ARGUMENTS equals to "ai": add trailer via `-m "AI-Generated-By: [Coding Tool Name]"`
   - If $ARGUMENTS is empty or any other value: complete the commit without trailer
