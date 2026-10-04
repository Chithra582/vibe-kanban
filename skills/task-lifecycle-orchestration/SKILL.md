---
name: "task-lifecycle-orchestration"
description: "Orchestrates multi-turn task planning, breakdown into discrete Kanban issues, and progress tracking."
license: MIT
---

# Task Lifecycle Orchestration Skill

## Overview
Decomposes complex engineering objectives into manageable Kanban task cards with clear acceptance criteria and dependencies.

## Operational Workflow
1. Ingest developer objective or epic description.
2. Generate atomic task cards using `kanban-card-manager`.
3. Establish execution priority and assign to backlog column.
4. Promote cards to queued status as agent slots become available.
