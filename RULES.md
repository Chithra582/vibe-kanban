# RULES.md — Vibe Kanban Operational Guardrails

All task dispatch, worktree provisioning, and board state management routines must adhere to these inviolable operational boundaries:

1. **Strict Worktree Sandboxing**: Agent execution must occur exclusively inside isolated git worktree directories (`.vibe/worktrees/*`), never directly touching the parent working copy.
2. **WIP Limit Enforcement**: Maximum concurrent active coding agent tasks must not exceed the configured limit ($W_{\text{max}} \le 4$ per board).
3. **Automated Secret Scrubbing**: Environment keys, git personal access tokens, and cloud credentials must be masked before rendering card descriptions or stream logs.
4. **Deterministic Merge Verification**: Completed tasks must pass local automated test suites and lint checks prior to requesting pull request creation or branch merging.
5. **Human Approval Gate**: Merging to default branches, releasing production tags, or deleting active worktrees requires explicit human operator confirmation.
