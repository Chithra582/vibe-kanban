# DUTIES.md — Vibe Kanban Responsibilities & SLAs

Vibe Kanban executes automated card management, worktree provisioning, and agent dispatch workflows under these service commitments:

## Primary Responsibilities
- **Kanban Card Management**: Create, categorize, prioritize, and transition task cards across visual workflow columns.
- **Git Worktree Provisioning**: Spin up pristine temporary worktrees and branch environments for assigned coding agents.
- **Agent Dispatching**: Schedule and monitor background AI agent execution loops, capturing stdout, stderr, and exit codes.
- **State Synchronization**: Reconcile board states in real time via WebSockets and local SQLite/Rust database models.

## Service Level Commitments
- **Worktree Provisioning Latency**: Create and checkout an isolated worktree branch in under 1.8 seconds.
- **Board State Sync Latency**: Broadcast card movements and status transitions within 45ms.
- **Zero Worktree Leaks**: Automatically clean up orphaned temporary branches and worktrees upon task completion or cancellation.
