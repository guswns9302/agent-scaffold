# Repository Guidelines

## Core Rules

- If the user explicitly provides `docs/feature/<feature-slug>/task.md` as the work input, apply the rules in the `Workflow Rules` section.
- If the user is creating a PRD or refining an idea, apply the rules in the `Brainstorming Rules` section.

## Brainstorming Rules

- Use the `brainstorming` skill when refining or structuring an idea.
- Store brainstorming outputs under `docs/prd/`. The canonical final document is `docs/prd/prd.md`.
- Do not implement anything during this stage.
- Do not create or modify project layers, source code, or any part of the codebase during this stage.

## Workflow Rules

- Before starting any feature work, read `docs/workflow/agent_orchestration.md` and `docs/workflow/agent_list.md`, then follow the orchestration rules defined for that feature.
- When possible, execute feature work with multiple role-specific agents defined by the workflow instead of a single agent.
- The main orchestrator must never generate or modify code directly.
- The source of truth for each feature is always `docs/feature/<feature-slug>/task.md`.
- Always place agent artifacts under `docs/feature/<feature-slug>/artifacts/`.
- Each agent must create or update only one primary artifact file. Do not overwrite artifacts owned by other agents.
- A downstream agent must read the required upstream artifacts as input and record those inputs in the `Input` section of its own artifact.
- After the role-specific artifacts are completed, the main orchestrator may only perform final integration validation and any necessary follow-up adjustments.
- After all sub-agent work is complete, the main orchestrator must close or clean up the sub-agents before finishing the task.
- If the agent thread limit is reached, the main orchestrator must first clean up completed or no-longer-needed agents and then continue the workflow.
- If the workflow cannot be followed, explicitly document the reason and the fallback execution method in the artifact or final report.
- The main orchestrator's final report must always include at least:
- Execution time for each agent
- Which agents were activated
- Which agents actually executed each artifact update, including fallback execution when applicable
- Which artifact was created or updated by whom
- Who performed the final application and any follow-up adjustments

## Project Structure

- `docs/prd/`: Product requirements documents. The current canonical PRD is `docs/prd/prd.md`.
- `docs/feature/`: Feature-specific task documents and artifacts.
- `docs/design-system/`: Shared design tokens and UI rules.
- `docs/workflow/`: Agent roles and orchestration rules.

## Naming Conventions

- Feature folders use both a numeric prefix and a slug. Examples: `00-foundation-monorepo`, `04-store-detail-reservation`.
- Artifact file names must follow the fixed names defined in the workflow documents. Examples: `business-analysis.md`, `api-contract.md`, `frontend-implementation.md`, `qa-plan.md`.
- When adding documents, use short Markdown titles and clear section names.

## Working Notes

- Before starting a new task, read the existing `task.md` and `artifacts/*.md` files first, then continue from the current state.
- Do not expand requirements on your own. If a requirement change is needed, update the related PRD or `task.md` first.
- Align all design decisions with `docs/design-system/design-system.md`.
- If `docs/design-system/design-system.md` is still only at a basic scaffold level, complete the design system work first.
