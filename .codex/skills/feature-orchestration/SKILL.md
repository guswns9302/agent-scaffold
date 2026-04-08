---
name: feature-orchestration
description: Use when the work input is `docs/feature/<feature-slug>/task.md` or when coordinating multi-agent feature delivery with artifact handoffs, review loops, and final integration reporting.
---

# Feature Orchestration

Use this skill when feature work is driven by `docs/feature/<feature-slug>/task.md`.

---

## Purpose

Drive feature execution through role-specific sub-agents with explicit artifact handoffs.

Keep the feature task and artifacts as the working record.

Do not use ad hoc orchestration outside this contract unless the repository clearly cannot support it.

---

## Entry Rules

- Treat `docs/feature/<feature-slug>/task.md` as the feature source of truth.
- Read existing `artifacts/*.md` before starting new work.
- Keep all feature artifacts under `docs/feature/<feature-slug>/artifacts/`.

- Always use role-specific agents for feature execution.

- Do not silently bypass the workflow.

- If the role-based workflow cannot be followed:
  1. analyze the blocking reason
  2. document it clearly in the artifact or report
  3. proceed only with an explicit and minimal fallback execution

---

## Orchestrator Contract

- The orchestrator coordinates agents and validates integration.
- The orchestrator must not become the default implementation agent.

- The orchestrator is responsible for:
  - passing correct inputs to each agent
  - ensuring artifact ownership is respected
  - resolving blocked handoffs
  - detecting non-converging review or QA loops
  - escalating instead of continuing ineffective cycles

- The orchestrator may intervene when:
  - artifact states are inconsistent
  - downstream work is blocked
  - review or QA loops are not converging

- Pass each sub-agent:
  - the `feature-slug`
  - the exact input files it must read
  - the single primary artifact it owns

- Close completed or no-longer-needed agents before finishing.
- Clean up completed agents before spawning new ones when capacity is tight.

- The orchestrator is the only agent allowed to update `artifacts/status.md`.
- The orchestrator should not directly edit role-specific artifacts unless acting as an explicit fallback executor.

---

## Default Flow

1. `business-analyst`
2. `ui-designer` when UI definition work is needed
3. `backend-developer` when server behavior or shared data behavior changes
4. `frontend-developer` and/or `mobile-developer` after backend work is available when client surfaces depend on server behavior
5. `code-reviewer`
6. `qa-expert`

---

## Minimal Valid Paths

- Backend-centric  
  → `business-analyst` → `backend-developer` → `code-reviewer` → `qa-expert`

- Web UI-centric  
  → `business-analyst` → `ui-designer` → `backend-developer` → `frontend-developer` → `code-reviewer` → `qa-expert`

- Mobile UI-centric  
  → `business-analyst` → `ui-designer` → `backend-developer` → `mobile-developer` → `code-reviewer` → `qa-expert`

- Full-stack  
  → `business-analyst` → `ui-designer` as needed → `backend-developer` → client implementation agents → `code-reviewer` → `qa-expert`

---

## Parallel Execution Rule

- Parallelize only when upstream inputs are stable and unambiguous.

- Upstream is considered stable when:
  - required artifacts exist
  - required upstream artifacts are not `blocked`
  - no blocking decisions remain for the downstream step

- Do not start downstream work on incomplete or unstable definitions.
- When backend and client work are both required:
  - complete or stabilize backend work first
  - then start `frontend-developer` and `mobile-developer`

---

## Agent Execution Rules

- Every agent must create a short execution plan before starting work.

- The plan must:
  - define minimal execution steps
  - be concise and action-oriented
  - be recorded as the FIRST section of the artifact

- After completing the work:
  - mark each plan item as done or blocked

- Each agent must follow:
  → plan → execute → validate → record

- Validation must be based on:
  - artifact consistency
  - input-output alignment
  - observable behavior when applicable

---

## Artifact Rules

- Each agent may create or materially update only its own primary artifact.

- Do not modify another agent’s artifact.

- `business-analysis.md` is read-only after creation.

- Each agent must:
  - reflect its execution result and completion status in its own artifact
  - not rely on shared checklist mutation

- Each artifact must:
  - include standardized frontmatter
  - record actual input files used
  - include an execution plan as the first section

- Do not overwrite artifacts wholesale unless the prior content is unusable.

- If fallback execution occurs:
  - preserve intended `owner`
  - record actual executor in `created_by`

---

## Status Aggregation Rules

- `artifacts/status.md` is the single aggregated progress view for the feature.

- Only the orchestrator is allowed to create or update `status.md`.

- Role-specific agents must NOT modify `status.md`.

- The orchestrator must:
  - use `docs/status-template/status.md` as the template
  - read each artifact’s frontmatter (`status`, `updated_at`, `blocked_by`)
  - derive the overall feature status
  - update `status.md` after each major phase

- Status must use the standardized enum:
  `not_started`, `in_progress`, `blocked`, `completed`, `needs_revision`, `skipped`

- When `blocked_by` is not applicable, record `-`.

- `status.md` must not contain independent state;
  it must always reflect the current state derived from artifacts.

---

## Review and QA Gates

- Do not start `qa-expert` until `code-review.md` frontmatter is `status: completed`.

- Keep code review feedback inside the code review loop.
- Do not mix code review feedback with QA feedback.

---

## QA Execution Requirements

- QA must validate actual implemented behavior, not just documentation.

- When frontend or browser-observable changes exist:
  - QA MUST use Playwright to inspect real behavior
  - Do not finalize QA conclusions from artifacts alone if implementation is runnable

- QA must validate:
  - one normal path
  - one failure path
  - one integration edge

---

## UX Validation Responsibility

- UX perception issues must be handled in QA, not code review.

- This includes:
  - content repetition
  - CTA ambiguity
  - weak progression
  - layout comprehension issues
  - device-specific UX confusion

- Code reviewer should only flag UX issues if caused by implementation defects.

---

## Loop Control

- Limit code review loop to 3 cycles.
- Limit QA loop to 3 cycles.

- If the same issue repeats:
  - do not continue automatic cycles
  - escalate to orchestrator decision

- Detect and stop non-converging workflows early.

---

## Final Reporting

The final report must include:

- which agents were activated
- which agents actually updated each artifact (including fallback)
- execution time for each agent
- which artifact was created or updated by whom
- who performed final integration and follow-up adjustments

---

## Notes

- Agent-specific behavior, validation expectations, and artifact structure belong in `.codex/agents/*.toml`.

- Use the `brainstorming` skill for PRD or idea refinement.

- Prefer skills and agent contracts over duplicating execution rules in multiple places.
