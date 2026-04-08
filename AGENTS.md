# Repository Operating Guide

## Routing Rules
- PRD/idea stage -> use `brainstorming`
- explicit `docs/feature/<feature-slug>/task.md` input -> use `feature-orchestration`
- otherwise follow repository rules without forcing feature workflow

## Brainstorming Mode
- write outputs under `docs/prd/`
- canonical PRD is `docs/prd/prd.md`
- do not implement or modify source code in this mode

## Feature Execution Mode
- source of truth is always `docs/feature/<feature-slug>/task.md`
- read existing `artifacts/*.md` before continuing
- write all artifacts under `docs/feature/<feature-slug>/artifacts/`
- follow `.codex/skills/feature-orchestration/SKILL.md`
- follow `.codex/agents/*.toml` for role contracts
- enforce role handoff when possible
- if fallback occurs, record reason and actual executor

## Execution Sources
- `AGENTS.md`
- `.codex/skills/brainstorming/SKILL.md`
- `.codex/skills/feature-orchestration/SKILL.md`
- `.codex/agents/*.toml`
- `docs/feature/<feature-slug>/task.md`

## Repository Layout
- `docs/prd/`
- `docs/feature/`
- `docs/design-system/`
- `.codex/skills/`
- `.codex/agents/`

## Working Rules
- do not expand requirements without updating PRD or `task.md`
- align design decisions with `docs/design-system/design-system.md`
- if the design system is only scaffold-level, complete it first

## Default Principle
- prefer skills and agent contracts over long human policy docs
- avoid duplicating the same rule across multiple files
- keep executable rules in one place whenever possible