# Repository Guidelines

## Core Rules

- 사용자가 명시적으로 `docs/feature/<feature-slug>/task.md` 파일을 작업 입력으로 준 경우에는 `Workflow Rules` 섹션을 적용한다.
- 사용자가 prd를 만들거나 아이디어를 구체화시킬때에는 `Brainstorming Rules` 섹션을 적용한다.

## Brainstorming Rules
- 아이디어를 구체화시킬때에는 `brainstorming` 스킬을 사용한다.
- 구체화된 산출물은 `docs/prd/`경로에서 관리하며, 최종 산출물은 `docs/prd/prd.md`로 사용한다.
- 이 단계에서 절대 구현을 하지 않는다.
  - 프로젝트 레이어와 코드베이스 등의 생성/수정은 일체 하지 않는다.

## Workflow Rules
- 작업 시작 전 반드시 `docs/workflow/agent_orchestration.md`와 `docs/workflow/agent_list.md`를 읽고 해당 피처의 오케스트레이션 규칙을 적용한다.
- 피처 작업은 가능하면 단일 에이전트가 아니라 워크플로에 정의된 역할별 에이전트로 분리 수행한다.
- 메인 오케스트레이터는 절대 코드 생성 및 변경 작업을 하지 않는다.
- 피처의 기준 문서는 항상 `docs/feature/<feature-slug>/task.md`다.
- 에이전트 산출물은 항상 `docs/feature/<feature-slug>/artifacts/` 아래에 둔다.
- 각 에이전트는 자신의 주 산출물 파일 1개만 생성하거나 갱신한다. 다른 산출물을 덮어쓰지 않는다.
- 후속 에이전트는 선행 산출물을 반드시 입력으로 읽고, 읽은 입력을 자신의 산출물 `입력` 섹션에 기록한다.
- 메인 오케스트레이터는 역할별 산출물 작성 후 최종 통합 검증과 필요한 후속 수정만 수행한다.
- 워크플로를 따르지 못한 경우 그 사유와 대체 수행 방식을 산출물 또는 최종 보고에 명시한다.
- 메인 오케스트레이터는 최종 보고에 최소 아래 4가지를 항상 포함한다.
  - 각 에이전트의 수행 시간
  - 어떤 에이전트를 활성화했는지
  - 어떤 산출물을 누가 작성하거나 갱신했는지
  - 최종 반영과 후속 수정은 누가 수행했는지

## Project Structure

- `docs/prd/`: 제품 요구사항 문서. 현재 기준 문서는 `docs/prd/prd.md`.
- `docs/feature/`: 피처별 작업 문서와 산출물.
- `docs/design-system/`: 공통 디자인 토큰과 UI 규칙.
- `docs/workflow/`: 에이전트 역할과 오케스트레이션 규칙.

## Naming Conventions

- 피처 폴더는 번호와 슬러그를 함께 쓴다. 예: `00-foundation-monorepo`, `04-store-detail-reservation`.
- 산출물 파일명은 워크플로 문서의 고정 이름을 따른다. 예: `business-analysis.md`, `api-contract.md`, `frontend-implementation.md`, `qa-plan.md`.
- 문서를 추가할 때는 짧은 Markdown 제목과 명확한 섹션명을 사용한다.

## Working Notes

- 새 작업을 만들기 전에 기존 `task.md`와 `artifacts/*.md`를 먼저 읽고 이어서 작업한다.
- 요구사항을 임의로 확장하지 말고, 변경이 필요하면 관련 PRD 또는 `task.md`를 먼저 갱신한다.
- 디자인 관련 결정은 `docs/design-system/design-system.md`를 기준으로 맞춘다.
  - `docs/design-system/design-system.md`의 내용이 기본 scaffold 수준이라면 디자인 시스템 작업을 먼저 진행한다.
