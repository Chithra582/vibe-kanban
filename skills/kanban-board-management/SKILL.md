---
name: "kanban-board-management"
description: "Maintains visual Kanban board ergonomics, WIP limits, column sorting, and real-time state synchronization."
license: MIT
---

# Kanban Board Management Skill

## Overview
Enforces project management disciplines on the visual board, maintaining strict WIP limits and preventing task bottlenecks.

## Operational Workflow
1. Monitor active column card counts and enforce concurrency limits.
2. Stream card status updates via `board-state-synchronizer`.
3. Highlight stalled tasks or failing agent runs for operator intervention.
