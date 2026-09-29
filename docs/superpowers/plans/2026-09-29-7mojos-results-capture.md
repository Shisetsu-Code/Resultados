# 7Mojos Results Capture Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Populate the approved Resultados format for 7Mojos, starting with Piggy Bank Bonanza and Maids Cafe Riches.

**Spec:** `docs/superpowers/specs/2026-09-29-resultados-protocol-design.md`

## Constraints
- Keep Markdown human-readable and JSON machine-readable.
- Every action records state/preconditions, exact observed request shape, response model, history links, and evidence state.
- Multi-option states require N/N coverage; otherwise mark exact missing options INCOMPLETE.
- Keep secrets redacted and dynamic.
- Promote provider-wide rules only after cross-game evidence.

### Task 1: Repository structure
Create root README, JSON schemas, `7mojos/README.md`, and `7mojos/provider.json`. Validate JSON syntax and required fields.

### Task 2: Piggy Bank Bonanza
Complete normal-action samples, stake mapping, every visible color/suit choice, any finish/collect transition, response models, indexed history, and final coverage state.

### Task 3: Maids Cafe Riches
Discover direct client/game token, capture multiple normal samples and stake mapping, enumerate all visible modes and choices, exercise every branch N/N, follow continuations to terminal state, and write README/result/index/response/history files.

### Task 4: Provider aggregation
Compare the recorded games, update provider-level common endpoints/fields/states, preserve game-specific exceptions, and ensure all index references resolve.
