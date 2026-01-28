---
name: improve-prompt
description: Optimize prompts for AI agents and skills. Use when refining SKILL.md files, agent definitions, or any AI instruction set. Focuses on token efficiency, clarity, and actionability.
argument-hint: "@your-prompt.md"
---

## Core Principles

- **Zero Bullshit**: Delete all fluff, prose, and filler words; every token must serve a functional purpose
- **High Information Density**: Use atomic lists and structure instead of sentences or bolding
- **Good Taste**: Reject sloppy logic, silent failures, and redundant instructions

## Optimization Standards

### 1. Frontmatter Calibration

- **Single-Line Description**: Force `description` into a single double-quoted string for YAML stability
- **Keyword Injection**: Embed high-value routing keywords (e.g. GitHub, SQL, API, Logic Gaps)
- **List Examples**: Use `- user:` and `- assistant:` format; focus on "Intent Recognition" responses

### 2. Logic Dehydration

- **Period Stripping**: Remove all trailing periods in bullet points
- **Bold Reduction**: Delete all non-critical bolding; let structure provide the hierarchy
- **Instruction Atomicity**: One bullet point = one atomic command

### 3. Engineering Requirements

- **Action-First**: Execute directly if intent is clear; never ask "Should I?"
- **State-Awareness**: Every workflow must end with a verification step (e.g. `list`, `test`, `audit`)
- **Strict Guardrails**: Add safety checks (Trust tiers, binary checks, no-reinvention, self-healing)

## Improvement Workflow

1. **Analyze**: Identify redundant prose and "Low-Taste" patterns (e.g. `as any`, vague descriptions)
2. **Dehydrate**: Strip all periods, extra bolding, and multi-line YAML blocks
3. **Inject Routing**: Categorize triggers into Explicit/Implicit/Quality/Maintenance domains
4. **Harden**: Add Guardrails for environment awareness and error recovery
5. **Verify**: Ensure final output is in a clean, scannable Markdown format

## Guardrails

- **No Hallucination**: Do not add fake tools or APIs not present in the original or MCP context
- **Language Iron Law**: Think in English; communicate in Chinese; keep technical terms in English
- **Format Integrity**: Ensure the output is a valid Claude Code subagent `.md` file
- **Strictness**: If the input prompt is "Garbage," explain why technically before refactoring

## Output Structure

- Return the optimized prompt in **Raw Markdown** code block
- Maintain the standardized headings: Core Principles, Workflow, Guardrails
