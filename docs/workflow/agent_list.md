# Agent List

This document lists the custom agents used during orchestration. The base directory for agent definitions is `.codex/agents`.

## Authoring Rules

- When adding a new agent, continue the numbering sequentially.
- The base directory for agent definitions is `.codex/agents`.
- Keep each entry short so the name, purpose, and core responsibility are immediately clear.

## Shared Input/Output Rules

- The source-of-truth document for every feature is `docs/feature/<feature-slug>/task.md`.
- All agent artifacts must be stored under `docs/feature/<feature-slug>/artifacts/`.
- Each agent may create or update only its own primary artifact, and must not overwrite artifacts owned by other agents.
- Each artifact should include at least these sections so the next agent can use it directly as input: `Purpose`, `Input`, `Key Decisions or Implementation`, `Risks and Open Issues`, `Notes for the Next Agent`.
- Create an artifact file only when that role is needed. For example, if the feature has no mobile work, do not create `mobile-implementation.md`.

## 01. business-analyst

Use this agent when requirements are ambiguous or the scope needs to be normalized.

- Converts business goals into implementation-ready input.
- Structures constraints and scope.
- Clarifies acceptance criteria.
- Input: `task.md`, `docs/feature/README.md`, and optionally `docs/prd/prd_v4.md`
- Output: `docs/feature/<feature-slug>/artifacts/business-analysis.md`

## 02. api-designer

Use this agent when API contracts, schemas, or authentication boundaries must be designed.

- Defines resources and endpoint structure.
- Documents request/response schemas and the error model.
- Reviews compatibility and change impact.
- Input: `task.md`, `artifacts/business-analysis.md`, and optionally existing implementation artifacts
- Output: `docs/feature/<feature-slug>/artifacts/api-contract.md`

## 03. backend-developer

Use this agent for backend implementation or server-side bug fixes.

- Implements domain logic and data flow.
- Safely applies authentication, authorization, and state transitions.
- Checks failure paths and side effects.
- Input: `task.md`, `artifacts/business-analysis.md`, and optionally `artifacts/api-contract.md`, `artifacts/qa-plan.md`
- Output: `docs/feature/<feature-slug>/artifacts/backend-implementation.md`

## 04. mobile-developer

Use this agent when mobile implementation and device-specific constraints must be handled in the user app.

- Implements screen flows and lifecycle behavior.
- Integrates APIs and organizes local state.
- Checks degraded network and empty-data scenarios.
- Input: `task.md`, `artifacts/business-analysis.md`, and optionally `artifacts/api-contract.md`, `artifacts/ui-spec.md`, `artifacts/backend-implementation.md`
- Output: `docs/feature/<feature-slug>/artifacts/mobile-implementation.md`

## 05. frontend-developer

Use this agent when admin/master web UI implementation or frontend behavior changes are required.

- Implements screens, routes, and state flow.
- Organizes async data and rendering states.
- Tunes UI behavior for operational usability.
- Input: `task.md`, `artifacts/business-analysis.md`, and optionally `artifacts/api-contract.md`, `artifacts/ui-spec.md`, `artifacts/backend-implementation.md`
- Output: `docs/feature/<feature-slug>/artifacts/frontend-implementation.md`

## 06. ui-designer

Use this agent before implementation when screen structure or interaction decisions need to be made concrete.

- Defines layout and information hierarchy.
- Specifies loading, empty, and error states.
- Provides implementation-ready interaction guidance.
- Input: `task.md`, `artifacts/business-analysis.md`, and optionally `artifacts/api-contract.md`
- Output: `docs/feature/<feature-slug>/artifacts/ui-spec.md`

## 07. qa-expert

Use this agent when defining test scope, release risk, and verification strategy.

- Identifies critical failure scenarios.
- Proposes high-priority verification items.
- Summarizes release gates and residual risk.
- Input: `task.md`, `artifacts/business-analysis.md`, and optionally all design and implementation artifacts
- Output: `docs/feature/<feature-slug>/artifacts/qa-plan.md`

## 08. code-reviewer

Use this agent when reviewing implementation quality, maintainability, and regression risk.

- Finds risk points in the code changes.
- Suggests minimal fixes that reduce risk.
- Checks for missing tests and hidden coupling.
- Input: `task.md`, and optionally all `artifacts/*.md`
- Output: `docs/feature/<feature-slug>/artifacts/code-review.md`
