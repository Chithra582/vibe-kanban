# EXPLAINABILITY.md

This document explains the internal mechanisms, data lineage, operational boundaries, and governance framework of **Vibe Kanban** (`vibe-kanban`) in accordance with the **OpenGAP v0.1.0** specification for the **HiDevs GitAgent Passport** clearance pipeline.

> **Agent Name:** Vibe Kanban (`vibe-kanban`)  
> **Specification:** OpenGAP v0.1.0  
> **Category / Domain:** Productivity / AI Agent Kanban Task Orchestration & Git Worktree Isolation  
> **Compliance Standard:** OpenGAP Checkpoint 2 (Explainability & Decision Governance), OWASP LLM Top 10, MITRE ATLAS  

---

## How the Agent Decides

Vibe Kanban operates an intelligent, multi-layer task orchestration and branch isolation pipeline designed to manage autonomous coding agents safely across real-time Kanban boards. The agent coordinates task decomposition, worktree provisioning, background agent dispatching, and pull request verification through a deterministic five-stage operational pipeline.

### 1. Decision Architecture

The runtime intake, state classification, evaluation, and execution tracking operate across a deterministic, five-stage pipeline:

```
[ User Developer Goal / Product Requirement Brief ]
                         │
                         ▼
[Stage 1: Requirement Decomposition & Card Triage Gate]
  - Parses developer directives, acceptance criteria, and project milestones
  - Evaluates task complexity, scope blast radius, and dependencies
  - Instantiates Kanban cards and positions them in Backlog
                         ▼
[Stage 2: Worktree Provisioning & Environment Isolation Gate]
  - Evaluates active Work-in-Progress (WIP) quota across In-Progress column
  - Creates dedicated git branch and provisions isolated Git worktree
  - Injects target codebase context and pre-flight environment checks
                         ▼
[Stage 3: Autonomous Agent Task Dispatch & Stream Gate]
  - Dispatches coding prompt to assigned agent runtime (OpenCode / Claude)
  - Captures streaming stdout/stderr diagnostics and terminal PTY buffers
  - Updates card progress meters and detects execution stalls or timeouts
                         ▼
[Stage 4: Automated Verification & Diff Inspection Gate]
  - Computes unified git diff across the isolated worktree branch
  - Dispatches test suites (`pnpm test`, `cargo test`) and lint audits
  - Evaluates card completion score S_task against acceptance predicates
                         ▼
[Stage 5: PR Synthesis & Board State Sealing Gate]
  - Generates pull request summary and branch review package
  - Moves card to Review column and notifies human operator
  - Prunes temporary worktree files upon merge approval
                         ▼
[ Verified Kanban State Persisted & Synchronized ]
```

### 2. Decision Logic & Routing Formulations

When triaging task cards and determining agent execution priority, Vibe Kanban evaluates two deterministic mathematical formulations:

1. **Card Dispatch Priority Score ($S_{\text{priority}}$)**:
   $$S_{\text{priority}}(c) = w_u \cdot U_{\text{urgency}}(c) + w_b \cdot (1 - B_{\text{blockers}}(c)) + w_e \cdot E_{\text{effort}}(c) + w_d \cdot D_{\text{dependency}}(c)$$
   Where:
   - $U_{\text{urgency}}(c) \in [0, 1]$: User-assigned urgency rating (P0 = 1.0, P1 = 0.75, P2 = 0.50, P3 = 0.25).
   - $B_{\text{blockers}}(c) \in [0, 1]$: Dependency penalty reflecting unresolved antecedent cards.
   - $E_{\text{effort}}(c) \in [0, 1]$: Normalized token and complexity estimate ($1.0$ for fast atomic tasks, $0.2$ for multi-file architectural epics).
   - $D_{\text{dependency}}(c) \in [0, 1]$: Downstream unblock potential rewarding cards that unlock dependent tasks.
   - Standard weightings: $w_u = 0.40$, $w_b = 0.30$, $w_d = 0.20$, $w_e = 0.10$ ($\sum w_i = 1.0$).
   - Promotion threshold: A card is eligible for automatic dispatch to In Progress only if $S_{\text{priority}}(c) \ge \tau_{\text{dispatch}} = 0.60$ and active WIP $< W_{\text{max}}$.

2. **Worktree Health & Isolation Index ($H_{\text{worktree}}$)**:
   $$H_{\text{worktree}} = \alpha \cdot \mathbb{I}(\text{CleanWorktree}) + \beta \cdot (1 - M_{\text{conflict}}) + \gamma \cdot \left(1 - \frac{T_{\text{elapsed}}}{T_{\text{timeout}}}\right)$$
   Where:
   - $\mathbb{I}(\text{CleanWorktree}) \in \{0, 1\}$: Binary indicator confirming no uncommitted state in parent repository.
   - $M_{\text{conflict}} \in [0, 1]$: Upstream git divergence and merge conflict likelihood.
   - $T_{\text{elapsed}} / T_{\text{timeout}}$: Fraction of task timeout budget ($30$ minutes maximum) consumed.
   - Coefficients: $\alpha = 0.50$, $\beta = 0.30$, $\gamma = 0.20$. Worktrees with $H_{\text{worktree}} < 0.40$ trigger safety quarantines.

### 3. Thresholding & Refusal Decision Criteria

Vibe Kanban enforces strict operational boundaries and deterministic refusal thresholds:
- **Refusal on ERR_WIP_LIMIT_EXCEEDED**: Active agent execution count reaching maximum WIP ceiling ($W_{\text{active}} \ge 4$) halts dispatch with code `ERR_WIP_LIMIT_EXCEEDED`.
- **Refusal on ERR_DIRTY_WORKTREE_DETECTED**: Parent repository containing uncommitted modifications halts worktree provisioning with code `ERR_DIRTY_WORKTREE_DETECTED`.
- **Refusal on ERR_AGENT_TIMEOUT_EXCEEDED**: Agent execution exceeding time limit ($> 1800$s) halts execution with code `ERR_AGENT_TIMEOUT_EXCEEDED`.
- **Refusal on ERR_MERGE_CONFLICT_UNRESOLVED**: Git branch rebase detecting conflicting files halts card promotion with code `ERR_MERGE_CONFLICT_UNRESOLVED`.
- **Refusal on ERR_UNAUTHORIZED_BRANCH_DELETION**: Request to prune non-merged production branches is refused with code `ERR_UNAUTHORIZED_BRANCH_DELETION`.

### 4. Fallback Decision Mechanism

Continuous operational stability is maintained through layered fault recovery:
- **Automatic Worktree Rollback**: If an assigned agent crashes or aborts execution, the temporary worktree is automatically cleaned without affecting main branches.
- **Sequential Queue Throttling**: When WIP limits are saturated, incoming tasks queue in the Backlog column with FIFO ordering until agent slots open.
- **Model Fallback Cascade**: High-level reasoning and synthesis default to `gemini-2.0-flash` with automatic failover to `gpt-4o` and `claude-3-5-sonnet`.

### 5. Human-in-the-Loop Governance

Human operators retain sovereign authority over the multi-agent execution lifecycle:
- **Pull Request Sign-Off**: Merging agent-generated worktree branches into master/main requires explicit code review and human sign-off.
- **Interactive Emergency Abort**: Developers can pause, cancel, or quarantine running agent tasks with a single click from the Kanban board.
- **Board Column Policy Customization**: Administrators define custom WIP limits, verification criteria, and branch naming conventions.

---

## The Data It Uses

Vibe Kanban operates under strict principles of data minimization, environment isolation, and privacy protection.

### 1. Ingested Input Data

The framework processes only operational data necessary to perform its functions:
- **Kanban Task Cards**: Task descriptions, user instructions, acceptance criteria, and priority tags.
- **Repository Git Metadata**: Branch references, commit hashes, unified diffs, and worktree path mappings.
- **Agent Execution Telemetry**: Process stdout/stderr buffers, exit statuses, and turn duration metrics.

### 2. Configuration & Reference Data

- **Board Manifests**: Column definitions, WIP limits, visual theme preferences, and label taxonomies.
- **Worktree State Registry**: SQLite database tracking active worktree directories, branch names, and PIDs.
- **Client Configuration**: Tauri window bounds, WebSocket connection tokens, and local cache paths.

### 3. Base Model & Inference Lineage

- **Backend Runtime**: High-performance Rust Axum server managing WebSocket connections and SQLite persistence.
- **Desktop Harness**: Native Tauri application with virtualized React/TypeScript frontend.
- **Model Integration**: Multi-model compatibility connecting external agent harnesses via standard REST/JSON-RPC protocols.

### 4. Data Privacy, Storage, and Retention

- **OWASP LLM & MITRE ATLAS Hardened**: Defended against indirect prompt injection in task cards, unauthorized file exfiltration, and credential leakage.
- **Local Environment Isolation**: Agent execution workspaces, intermediate scratchpads, and vector stores reside strictly within designated local project directories.
- **Automated Secret Scrubbing**: API keys, database credentials, and personal credentials are automatically redacted prior to embedding or logging.
- **Zero Commercial Monetization**: Prompts, intermediate reasoning trajectories, and task deliverables are never commercialized or shared with third parties.

---

## Limitations

Understanding the operational boundaries and technical constraints of Vibe Kanban is essential for effective deployment.

### 1. High Disk Footprint Across Parallel Worktrees
- **Limitation**: Checking out multiple concurrent worktrees for large enterprise monoliths can consume significant disk storage.
- **Mitigation**: Deploy git sparse-checkout and automatically prune inactive worktree directories upon task completion.

### 2. Upstream Merge Conflict Accumulation
- **Limitation**: Long-running agent tasks can drift from main branch updates, increasing merge conflict frequency upon completion.
- **Mitigation**: Automatically run background rebase checks against upstream main and notify developers of divergence early.

### 3. Host Process Concurrency Saturation
- **Limitation**: Running multiple concurrent compilation test suites in parallel worktrees can saturate host CPU and RAM.
- **Mitigation**: Enforce global process throttling and schedule resource-heavy builds sequentially.

### 4. Non-Standard Monorepo Lockfile Contention
- **Limitation**: Parallel dependency installations across shared package caches can encounter lockfile contention.
- **Mitigation**: Configure isolated package store caches or run dependency installation during worktree setup sequentially.

### 5. Multi-User WebSocket Synchronization Lag
- **Limitation**: High-frequency board mutations across distributed remote teams can experience occasional ordering latency.
- **Mitigation**: Implement optimistic UI updates with deterministic timestamp-based vector clocks on the Rust backend.

---

## Summary & Compliance Checklist

| Checkpoint 2 Requirement | Corresponding Section | Status |
| :--- | :--- | :---: |
| **How the agent decides** | [How the Agent Decides](#how-the-agent-decides) | **Covered** |
| - Decision architecture & 5-stage pipeline | Section 1 | Verified |
| - Decision logic & routing formulations | Section 2 | Verified |
| - Thresholding & refusal decision criteria | Section 3 | Verified |
| - Fallback decision mechanism | Section 4 | Verified |
| - Human-in-the-loop governance & oversight | Section 5 | Verified |
| **The data it uses** | [The Data It Uses](#the-data-it-uses) | **Covered** |
| - Ingested input data & query streams | Section 1 | Verified |
| - Configuration & reference schemas | Section 2 | Verified |
| - Base model lineage & deterministic engines | Section 3 | Verified |
| - Data privacy, retention lifecycle & MITRE/OWASP | Section 4 | Verified |
| **Its limitations** | [Limitations](#limitations) | **Covered** |
| - High Disk Footprint Across Parallel Worktrees | Section 1 | Verified |
| - Upstream Merge Conflict Accumulation | Section 2 | Verified |
| - Host Process Concurrency Saturation | Section 3 | Verified |
| - Non-Standard Monorepo Lockfile Contention | Section 4 | Verified |
| - Multi-User WebSocket Synchronization Lag | Section 5 | Verified |
