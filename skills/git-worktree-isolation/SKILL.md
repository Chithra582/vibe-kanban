---
name: "git-worktree-isolation"
description: "Isolates coding agent execution environments using git worktrees to prevent workspace pollution."
license: MIT
---

# Git Worktree Isolation Skill

## Overview
Automates the lifecycle of isolated Git worktrees, allowing parallel agent development on independent feature branches.

## Operational Workflow
1. Provision pristine worktree directory via `worktree-isolator`.
2. Bind target card ID to dedicated git branch ref.
3. Validate clean working copy before launching agent execution.
4. Reconcile and prune worktree after pull request creation.
