# Agent Orchestration

This document defines the orchestration rules for feature work where multiple agents share the same context and reuse outputs from earlier steps as explicit inputs for later steps.

## Goals

- Make each agent's artifact an explicit input to the next agent.
- Keep feature-specific documents self-contained in one folder to reduce tracking overhead.
- Keep design-before-implementation, verification-after-implementation, and feedback-after-review in the same execution path.

## Core Principles

- The source-of-truth document for a feature is always `docs/feature/<feature-slug>/task.md`.
- Handoff artifacts must always be stored under `docs/feature/<feature-slug>/artifacts/`.
- Each agent may create or update only its own primary artifact file.
- Exception: downstream agents may also update only the `작업 체크리스트` status and short progress notes in `artifacts/business-analysis.md`.
- Before starting work, each downstream agent must read its required inputs and any selected optional inputs, then record what it read in the artifact's `Input` section.
- Implementation agents must not stop at changing code. They must also record an implementation summary and verification results for downstream agents.
- `business-analyst` is analysis-only and must never generate code or modify code directly.
- Roles that are unnecessary for a specific feature may be skipped, but no agent may proceed in a way that conflicts with already-created upstream artifacts.

## Recommended Directory Structure

```text
docs/feature/<feature-slug>/
  task.md
  artifacts/
    business-analysis.md
    api-contract.md
    ui-spec.md
    backend-implementation.md
    frontend-implementation.md
    mobile-implementation.md
    qa-plan.md
    code-review.md
```

- Not every file is required for every feature.
- Create only the files that match the feature type.
- File names are fixed. When agents reference artifacts, prefer these path conventions over ad hoc relative paths.

## Artifact Template

All artifacts should follow this shared metadata and section structure:

```md
---
feature: <feature-slug>
artifact: <artifact-name>
owner: <agent-name>
created_by: <actual-agent-identity>
status: draft
updated_at: YYYY-MM-DD
upstream:
  - ../task.md
---

# <Document Title>

## Purpose

## Input

## Key Decisions or Implementation

## 작업 체크리스트

## Verification or Evidence

## Risks and Open Issues

## Notes for the Next Agent
```

Rules:

- `owner` is the intended workflow role responsible for the artifact.
- `created_by` is the actual agent identity that created or last materially updated the artifact in the current run.
- In a normal execution, `owner` and `created_by` may be the same. In a fallback execution, keep `owner` as the target role and record the actual writer in `created_by`, for example `orchestrator (fallback for ui-designer)`.
- In `Input`, record both the actual file paths you read and a short summary of what mattered.
- In `artifacts/business-analysis.md`, `작업 체크리스트` is the canonical shared progress tracker for the feature.
- `business-analyst` creates the initial checklist from `task.md` and normalized scope.
- Downstream agents may update only checklist status and short completion/blocker notes there. They must not silently rewrite the analysis content, `owner`, or `created_by`.
- In `Notes for the Next Agent`, record immediately actionable conditions, cautions, and blockers.
- Implementation agents must record changed files, verification commands, and environment constraints.
- Design agents must record unresolved decisions and available options.
- When an agent creates or materially updates its primary artifact, it must set or refresh `created_by` and `updated_at`.

## Agent Contracts

| Agent | Required Input | Optional Input | Primary Artifact | Primary Consumers |
| --- | --- | --- | --- | --- |
| `business-analyst` | `task.md` | `docs/feature/README.md`, `docs/prd/prd_v4.md` | `artifacts/business-analysis.md` | all downstream agents |
| `api-designer` | `task.md`, `artifacts/business-analysis.md` | `artifacts/ui-spec.md`, existing implementation artifacts | `artifacts/api-contract.md` | `backend-developer`, `frontend-developer`, `mobile-developer`, `qa-expert`, `code-reviewer` |
| `ui-designer` | `task.md`, `artifacts/business-analysis.md` | `artifacts/api-contract.md` | `artifacts/ui-spec.md` | `backend-developer`, `frontend-developer`, `mobile-developer`, `qa-expert`, `code-reviewer` |
| `backend-developer` | `task.md`, `artifacts/business-analysis.md` | `artifacts/api-contract.md`, `artifacts/qa-plan.md` | `artifacts/backend-implementation.md` | `frontend-developer`, `mobile-developer`, `qa-expert`, `code-reviewer` |
| `frontend-developer` | `task.md`, `artifacts/business-analysis.md` | `artifacts/api-contract.md`, `artifacts/ui-spec.md`, `artifacts/backend-implementation.md` | `artifacts/frontend-implementation.md` | `backend-developer`, `mobile-developer`, `qa-expert`, `code-reviewer` |
| `mobile-developer` | `task.md`, `artifacts/business-analysis.md` | `artifacts/api-contract.md`, `artifacts/ui-spec.md`, `artifacts/backend-implementation.md` | `artifacts/mobile-implementation.md` | `backend-developer`, `frontend-developer`, `qa-expert`, `code-reviewer` |
| `code-reviewer` | `task.md` | `artifacts/backend-implementation.md`, `artifacts/frontend-implementation.md`, `artifacts/mobile-implementation.md` | `artifacts/code-review.md` | implementation agents |
| `qa-expert` | `task.md`, `artifacts/business-analysis.md` | all design and implementation artifacts | `artifacts/qa-plan.md` | implementation agents |

## Recommended Execution Order

### 1. Start With Analysis

1. `business-analyst` normalizes `task.md` into an implementation-ready scope.
2. Save the result to `artifacts/business-analysis.md`.
3. The result must describe fine-grained implementation steps for each feature area.
4. The result must include a `작업 체크리스트` section that downstream agents can update as work completes.

This document is the starting point for all later agents. If requirements are unstable, every downstream artifact becomes unstable, so lock this first.

### 2. Branch Into Design

- If the feature changes APIs, `api-designer` creates `artifacts/api-contract.md`.
- If the feature needs interaction or screen-definition work, `ui-designer` creates `artifacts/ui-spec.md`.

Parallel conditions:

- Once `business-analysis.md` is ready, `api-designer` and `ui-designer` may start in parallel.
- If the UI strongly depends on API field structure, `ui-designer` must read `api-contract.md` first and reflect it.

### 3. Branch Into Implementation

- If the feature changes server behavior, `backend-developer` creates `artifacts/backend-implementation.md`.
- If the feature changes web behavior, `frontend-developer` creates `artifacts/frontend-implementation.md`.
- If the feature changes mobile app behavior, `mobile-developer` creates `artifacts/mobile-implementation.md`.

Parallel conditions:

- Once `api-contract.md` is stable, backend work and frontend/mobile work may proceed in parallel.
- Before merge or handoff completion, `frontend-developer` and `mobile-developer` must read `backend-implementation.md` and document any differences between the contract and the actual implementation.

### 4. Code Review

1. `code-reviewer` creates `artifacts/code-review.md` based on the full artifact set and the actual code changes.
2. If no further changes are required, explicitly add `(Complete)` to the top-level header of `code-review.md`.
3. Implementation agents must read `code-review.md` again and apply required fixes or document residual risk.
4. If `(Complete)` is not present in the top-level header of `code-review.md`, the orchestrator must not move to `5. QA Verification` and must continue the feedback loop in `4. Code Review`.
5. The feedback loop in `4. Code Review` is limited to a maximum of 3 cycles per feature.
6. If a code review cycle does not materially change the failing outcome, do not continue automatically. Escalate first to requirement redefinition, design revision, scope reduction, residual risk acceptance, or environment/test limitation acceptance.
7. If `code-reviewer` identifies a blocker that cannot be resolved within the current scope without a requirement or architectural change, escalate immediately instead of consuming the remaining loop budget.

### 5. QA Verification

1. `qa-expert` may start only when `(Complete)` is present in the top-level header of `code-review.md`.
2. `qa-expert` reads the design and implementation artifacts plus the code review result, then creates `artifacts/qa-plan.md`.
3. Execute verification according to `qa-plan.md`, and record success or failure for each item.
4. Implementation agents must read `qa-plan.md` again and apply required fixes or document residual risk for failed verification items.
5. If not all items in `qa-plan.md` are successful, the orchestrator must continue the QA feedback loop.
6. Once `(Complete)` has been added to `code-review.md` and the workflow has entered QA verification, later QA feedback must not send the workflow back to `4. Code Review`.
7. If a QA cycle does not materially change the failing outcome, do not continue automatically. Escalate first to requirement redefinition, design revision, scope reduction, residual risk acceptance, or environment/test limitation acceptance.
8. If `qa-expert` identifies a blocker that cannot be resolved within the current scope without a requirement or architectural change, escalate immediately instead of consuming the remaining loop budget.

## Orchestration Rules

- The orchestrator must always specify `feature-slug` when calling an agent.
- For each downstream agent, the orchestrator must pass the list of input files that must be read.
- For each downstream agent, the orchestrator must also pass the single output file that must be created or updated.
- The orchestrator must also tell downstream agents which checklist items in `artifacts/business-analysis.md` they are expected to update.
- When an artifact already exists, prefer updating it instead of overwriting it. If a prior decision changes, record the reason inside the document.
- If downstream work must begin before implementation is complete, keep `status: draft` and explicitly record the incomplete conditions.
- When updating `artifacts/business-analysis.md`, downstream agents may change only checklist status and short completion/blocker notes.
- Do not call `qa-expert` before `(Complete)` appears in the top-level header of `code-review.md`.
- Once `(Complete)` appears in the top-level header of `code-review.md` and QA begins, keep all later feedback inside `5. QA Verification` and do not return to `4. Code Review`.
- Outputs from `code-reviewer` and `qa-expert` are feedback documents, not terminal documents. If needed, implementation agents update their own artifacts again after reading them.

## Feedback Loop Limits

- The feedback loop in `4. Code Review` is limited to 3 cycles per feature.
- The feedback loop in `5. QA Verification` is limited to 3 cycles per feature.
- If `(Complete)` is not present in the top-level header of `code-review.md`, the cycle remains inside the code review loop only. It must not enter QA and must not count toward QA cycles.
- Once `(Complete)` appears in `code-review.md` and the QA loop has started, later failures must not send the workflow back to the code review loop.
- One code review loop cycle means: implementation agents read the `code-reviewer` result, update actual code or their implementation artifact, and then submit the feature for code review again.
- One QA loop cycle means: after `(Complete)` appears in the top-level header of `code-review.md`, implementation agents read the `qa-expert` result, update actual code or their implementation artifact, and then submit the feature for QA verification again.
- If a loop does not converge within 3 cycles, stop repeating that loop. The orchestrator or a human must first decide one of the following: redefine requirements, revise design, reduce scope, accept residual risk, or accept an environment/test limitation.
- If the same issue is repeated for 2 consecutive cycles, stop the loop even if cycles remain. Record why it was not resolved, classify the blocker as requirement, design, implementation, environment/test limitation, or risk acceptance, and state what decision is required in the artifact's `Risks and Open Issues` section.
- If a loop cycle does not materially change the failing outcome, do not continue the same loop automatically. Escalate before spending another cycle.
- If `code-reviewer` or `qa-expert` identifies a blocker that cannot be resolved within the current scope without a requirement or architectural change, escalate immediately instead of consuming the remaining loop budget.
- Track feedback counts in the `Verification or Evidence` section of `artifacts/code-review.md` and `artifacts/qa-plan.md` using the format `feedback_cycle: N`.

## Minimum Flows By Feature Type

### Backend-Centric Feature

`business-analyst` -> `api-designer` -> `backend-developer` -> `code-reviewer` -> `qa-expert`

### Web UI-Centric Feature

`business-analyst` -> `ui-designer` -> `frontend-developer` -> `code-reviewer` -> `qa-expert`

### Mobile UI-Centric Feature

`business-analyst` -> `ui-designer` -> `mobile-developer` -> `code-reviewer` -> `qa-expert`

### Full-Stack Feature

`business-analyst` -> (`api-designer` || `ui-designer`) -> (`backend-developer` || `frontend-developer` || `mobile-developer`) -> `code-reviewer` -> `qa-expert`

## Operating Notes

- `task.md` stores the original feature requirements. `artifacts/*.md` store interpretation and execution results.
- Short Markdown documents are the easiest format for both humans and agents to read and reuse. They are easier to maintain than JSON and work well for both freeform explanation and checklists.
- To reduce handoff loss between agents, keep the last section of every artifact fixed as `Notes for the Next Agent`.
