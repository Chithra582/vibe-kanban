---
name: "multi-agent-dispatching"
description: "Assigns queued cards to autonomous coding agents, streaming telemetry and diffs back to the UI."
license: MIT
---

# Multi-Agent Dispatching Skill

## Overview
Coordinates execution runs across AI coding agents, streaming stdout diagnostics and diff chunks into card inspector panels.

## Operational Workflow
1. Dequeue top priority card and extract task prompt.
2. Launch agent harness via `agent-task-dispatcher` in provisioned worktree.
3. Stream incremental progress logs to frontend clients.
4. Transition card to Review column upon exit code zero.
