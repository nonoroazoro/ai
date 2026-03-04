# figma-to-code-subagents

Subagents version of [figma-to-code](../figma-to-code), uses [Subagents](https://docs.anthropic.com/en/docs/claude-code/sub-agents) instead of [Agent Teams](https://docs.anthropic.com/en/docs/claude-code/agent-teams).

Each phase spawns a fresh subagent that runs to completion. This avoids the Agent Teams setup and coordination overhead, but subagents have their own limitations:

> Subagents are `ephemeral`, each spawn starts with a fresh context window, so context cannot accumulate and full history is lost between phases.

This makes the `audit-fix loop` much weaker:

- With Agent Teams, `implement-components` stays alive and retains full context of what it wrote and fixed, leading to **more accurate fixes across rounds**.
- With subagents, `fix-components` is spawned fresh each round with only the `auditResults` passed in, losing all prior fix context. This leads to inaccurate fixes, or even regressing correct code into broken code.

For this reason, the `audit-fix loop` has been removed from this version. Despite this, **the implementation quality is still very good in practice**.

For the full audit-fix loop experience, use [figma-to-code](../figma-to-code) (Agent Teams version).
