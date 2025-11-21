# Commands: Create git commit

This command helps you create well-formatted commits with conventional commit messages.

## The processing flow of this command

1. Runs `git status` to check staged files:
    - If 0 files are staged, runs `git add` to stage changes
    - Otherwise, continue with the process
2. Runs `git diff --cached` to analyze **staged changes** and determine if multiple distinct logical changes are present
    - If multiple distinct changes are detected, suggests `splitting the commit` into multiple commits
    - Otherwise, continue with the process
3. Creates a commit message using conventional commit format for each commit

## Guidelines for splitting the commit

When analyzing the diff, consider splitting commits based on these criteria:

1. **Different concerns**: Changes to unrelated parts of the codebase
2. **Different types of changes**: Mixing features, fixes, refactoring, etc.
3. **File patterns**: Changes to different types of files, e.g., source code vs documentation
4. **Logical grouping**: Changes that would be easier to understand or review separately
5. **Size**: Very large changes that would be clearer if broken down

## Best practices for commits

- **Atomic commits**: Each commit should contain related changes that serve a single purpose
- **Split large changes**: If changes touch multiple concerns, split them into separate commits
- **Conventional commit format**: Use the format `<type>: <description>` where type is one of:
    - `feat`: Add features
    - `fix`: Fix bugs
    - `docs`: Change documentation
    - `style`: Change code style
    - `refactor`: Refactoring code
    - `perf`: Improve performance
    - `test`: Add or fix tests
    - `chore`: Change build process, publish process, etc.
- **Present tense, imperative mood**: Write commit messages as commands, e.g., "add feature" not "added feature".
- **Concise commit message**: Only allows one line message, and must be less than 72 characters. Never generate detailed information.

## Examples

Good commit messages:

- feat: add user authentication system
- fix: resolve memory leak in rendering process
- docs: update API documentation with new endpoints
- refactor: simplify error handling logic in parser
- fix: resolve linter warnings in component files
- chore: improve developer tooling setup process
- feat: implement business logic for transaction validation
- fix: address minor styling inconsistency in header
- fix: patch critical security vulnerability in auth flow
- style: reorganize component structure for better readability
- fix: remove deprecated legacy code
- feat: add input validation for user registration form
- fix: resolve failing CI pipeline tests
- feat: implement analytics tracking for user engagement
- fix: strengthen authentication password requirements
- feat: improve form accessibility for screen readers

## Important Notes

- Prohibit additions like 'Generated with Claude' or 'Co-Authored-By: Claude' in commit messages
- If encounter lint issues, you should try to fix the issues
- The commit message will be constructed based on the changes detected
- Before committing, the command will review the diff to identify if multiple commits would be more appropriate
- If suggesting multiple commits, it will help you stage and commit the changes separately
- Always reviews the commit diff to ensure the message matches the changes
