# Agent Orchestration

피처 단위로 여러 에이전트가 같은 문맥을 공유하고, 이전 작업의 결과를 다음 작업의 입력으로 재사용하기 위한 오케스트레이션 규칙입니다.

## 목표

- 각 에이전트의 산출물을 다음 에이전트의 명시적 입력으로 고정합니다.
- 피처별 문서가 한 폴더 안에서 닫히도록 만들어 추적 비용을 줄입니다.
- 구현 전 설계, 구현 후 검증, 검토 후 피드백이 같은 경로에서 순환되도록 만듭니다.

## 기본 원칙

- 피처의 기준 문서는 항상 `docs/feature/<feature-slug>/task.md`입니다.
- 핸드오프 산출물은 항상 `docs/feature/<feature-slug>/artifacts/` 아래에 둡니다.
- 각 에이전트는 자신의 주 산출물 파일만 생성하거나 갱신합니다.
- 다음 에이전트는 자신의 필수 입력과 선택 입력을 먼저 읽고, 읽은 내용을 산출물의 `입력` 섹션에 남깁니다.
- 구현 에이전트는 코드만 바꾸는 것으로 끝내지 않고, 후속 에이전트가 참고할 구현 요약과 검증 결과를 반드시 산출물에 남깁니다.
- 특정 피처에 불필요한 역할은 건너뛸 수 있지만, 이미 생성된 상위 산출물과 충돌하는 방향으로 진행하면 안 됩니다.

## 권장 디렉터리 구조

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

- 모든 파일이 항상 필요한 것은 아닙니다.
- 피처 성격에 맞는 파일만 생성합니다.
- 파일명은 고정하고, 에이전트 간 참조는 상대경로보다 위 경로 규칙을 우선합니다.

## 산출물 템플릿

모든 산출물은 아래 공통 메타데이터와 섹션 구조를 따르는 것이 좋습니다.

```md
---
feature: <feature-slug>
artifact: <artifact-name>
owner: <agent-name>
status: draft
updated_at: YYYY-MM-DD
upstream:
  - ../task.md
---

# <문서 제목>

## 목적

## 입력

## 핵심 결정 또는 구현

## 검증 또는 근거

## 리스크 및 미해결

## 다음 에이전트 전달사항
```

규칙:

- `입력`에는 실제로 읽은 문서 경로와 핵심 요약을 함께 남깁니다.
- `다음 에이전트 전달사항`에는 후속 작업자가 바로 실행할 수 있는 조건, 주의사항, 차단 이슈를 적습니다.
- 구현 에이전트는 변경 파일, 검증 명령, 환경 제약을 반드시 적습니다.
- 설계 에이전트는 미정 의사결정과 선택지를 반드시 적습니다.

## 에이전트별 계약

| Agent | 필수 입력                                        | 선택 입력                                                                                      | 주 산출물 | 주요 소비자                                                                             |
| --- |----------------------------------------------|--------------------------------------------------------------------------------------------| --- |------------------------------------------------------------------------------------|
| `business-analyst` | `task.md`                                    | `docs/feature/README.md`, `docs/prd/prd_v4.md`                                             | `artifacts/business-analysis.md` | 전 에이전트                                                                             |
| `api-designer` | `task.md`, `artifacts/business-analysis.md` | `artifacts/ui-spec.md`, 기존 구현 산출물                                                          | `artifacts/api-contract.md` | backend-developer, frontend-developer, mobile-developer, qa-expert, code-reviewer  |
| `ui-designer` | `task.md`, `artifacts/business-analysis.md` | `artifacts/api-contract.md`                                                                | `artifacts/ui-spec.md` | backend-developer, frontend-developer, mobile-developer, qa-expert, code-reviewer  |
| `backend-developer` | `task.md`, `artifacts/business-analysis.md` | `artifacts/api-contract.md`, `artifacts/qa-plan.md`                                        | `artifacts/backend-implementation.md` | frontend-developer, mobile-developer, qa-expert, code-reviewer                                                 |
| `frontend-developer` | `task.md`, `artifacts/business-analysis.md` | `artifacts/api-contract.md`, `artifacts/ui-spec.md`, `artifacts/backend-implementation.md` | `artifacts/frontend-implementation.md` | backend-developer, mobile-developer, qa-expert, code-reviewer                                                                       |
| `mobile-developer` | `task.md`, `artifacts/business-analysis.md` | `artifacts/api-contract.md`, `artifacts/ui-spec.md`, `artifacts/backend-implementation.md` | `artifacts/mobile-implementation.md` | backend-developer, frontend-developer, qa-expert, code-reviewer                                                                   |
| `qa-expert` | `task.md`, `artifacts/business-analysis.md` | 설계/구현 산출물 전체                                                                               | `artifacts/qa-plan.md` | 구현 에이전트                                                                            |
| `code-reviewer` | `task.md`                                   | `artifacts/backend-implementation.md`, `artifacts/frontend-implementation.md`, `artifacts/mobile-implementation.md`            | `artifacts/code-review.md` | 구현 에이전트                                                                            |

## 권장 실행 순서

### 1. 분석 시작

1. `business-analyst`가 `task.md`를 구현 가능한 범위로 정규화합니다.
2. 결과를 `artifacts/business-analysis.md`에 저장합니다.
3. 결과물에는 각 기능 구현을 위한 세밀한 단계가 명시되어야 합니다.

이 문서는 이후 모든 에이전트의 출발점입니다. 요구사항이 흔들리면 나머지 산출물 전체가 흔들리므로 가장 먼저 고정합니다.

### 2. 설계 분기

- API 변경이 있는 피처는 `api-designer`가 `artifacts/api-contract.md`를 만듭니다.
- 사용자 인터랙션 정의가 필요한 피처는 `ui-designer`가 `artifacts/ui-spec.md`를 만듭니다.

병렬 조건:

- `business-analysis.md`가 준비되면 `api-designer`와 `ui-designer`는 병렬로 시작할 수 있습니다.
- 단, UI가 API 필드 구조에 강하게 의존하면 `ui-designer`는 `api-contract.md`를 먼저 읽고 반영합니다.

### 3. 구현 분기

- 서버 변경이 있으면 `backend-developer`가 `artifacts/backend-implementation.md`를 남깁니다.
- 웹 변경이 있으면 `frontend-developer`가 `artifacts/frontend-implementation.md`를 남깁니다.
- 앱 변경이 있으면 `mobile-developer`가 `artifacts/mobile-implementation.md`를 남깁니다.

병렬 조건:

- `api-contract.md`가 안정화되면 backend와 frontend/mobile은 병렬 진행이 가능합니다.
- 단, frontend/mobile은 병합 전 반드시 `backend-implementation.md`를 읽고 실제 구현과 계약 차이를 정리해야 합니다.

### 4. 코드 리뷰

1. `code-reviewer`가 전체 산출물과 실제 코드 변경을 기준으로 `artifacts/code-review.md`를 만듭니다.
2. 추가 수정이 더 이상 필요 없다고 판단되면 `code-review.md` 최상단 헤더에 `(완료)`를 명시적으로 표기합니다.
3. 구현 에이전트는 `code-review.md`를 다시 읽고 필요한 수정이나 잔여 리스크를 반영합니다.
4. `code-review.md` 최상단 헤더에 `(완료)`가 없으면 오케스트레이터는 `5. QA 검증`으로 넘어가지 않고 `4. 코드 리뷰` 단계의 되먹임 루프를 계속 진행합니다.
5. `4. 코드 리뷰` 단계의 되먹임 루프는 동일 피처 기준 최대 5회까지만 허용합니다.

### 5. QA 검증

1. `qa-expert`는 `code-review.md` 최상단 헤더에 `(완료)`가 명시된 경우에만 시작합니다.
2. `qa-expert`가 설계/구현 산출물과 코드 리뷰 결과를 읽고 `artifacts/qa-plan.md`를 만듭니다.
3. `qa-plan.md`에 나온 절차대로 검증을 진행하며, 각 항목별 성공과 실패를 반영합니다.
4. 구현 에이전트는 `qa-plan.md`를 다시 읽고 실패한 검증에 대해 필요한 수정이나 잔여 리스크를 반영합니다.
5. 오케스트레이터는 `qa-plan.md`의 모든 항목이 성공이 되지 않았다면, 되먹임 루프를 계속 진행합니다.
6. `code-review.md` 최상단 헤더에 한 번 `(완료)`가 명시되어 QA 검증 단계에 진입한 뒤에는, QA 되먹임 중 다시 `4. 코드 리뷰` 단계로 돌아가지 않습니다.

## 오케스트레이션 규칙

- 오케스트레이터는 에이전트 호출 시 항상 `feature-slug`를 명시합니다.
- 후속 에이전트에게는 "반드시 읽을 입력 파일 목록"과 "갱신해야 할 출력 파일 1개"를 함께 전달합니다.
- 이미 존재하는 산출물은 덮어쓰기보다 갱신을 우선하고, 이전 결정이 변경되면 변경 이유를 문서 안에 남깁니다.
- 구현이 완료되지 않은 상태라도 후속 에이전트를 돌려야 한다면 `status: draft`를 유지하고 미완 조건을 명시합니다.
- `qa-expert`는 `code-review.md` 최상단 헤더에 `(완료)`가 명시되기 전에는 호출하지 않습니다.
- `code-review.md` 최상단 헤더에 한 번 `(완료)`가 명시되어 QA 검증 단계가 시작되면, 이후 QA 되먹임은 `5. QA 검증` 단계 안에서만 반복하고 `4. 코드 리뷰` 단계로 되돌아가지 않습니다.
- `code-reviewer`, `qa-expert`의 결과는 종결 문서가 아니라 되먹임 문서입니다. 필요한 경우 구현 에이전트가 다시 자신의 산출물을 갱신합니다.

## 되먹임 루프 제한

- `4. 코드 리뷰` 단계의 되먹임 루프는 동일 피처 기준 최대 5회까지만 허용합니다.
- `5. QA 검증` 단계의 되먹임 루프는 동일 피처 기준 최대 5회까지만 허용합니다.
- `code-review.md` 최상단 헤더에 `(완료)`가 없으면 해당 사이클은 코드 리뷰 루프 안에서만 반복되며, QA 검증 루프로 넘어가거나 QA 검증 횟수로 집계하지 않습니다.
- `code-review.md` 최상단 헤더에 한 번 `(완료)`가 명시되어 QA 검증 루프가 시작된 뒤에는, 이후 실패 항목이 발생하더라도 다시 코드 리뷰 루프로 되돌리지 않습니다.
- 코드 리뷰 루프에서 1회는 `code-reviewer`의 결과를 읽고 구현 에이전트가 실제 코드 또는 자신의 구현 산출물을 다시 갱신한 뒤, 다시 코드 리뷰를 받는 한 사이클을 의미합니다.
- QA 검증 루프에서 1회는 `code-review.md` 최상단 헤더에 `(완료)`가 표기된 뒤 `qa-expert`의 결과를 읽고 구현 에이전트가 실제 코드 또는 자신의 구현 산출물을 다시 갱신한 뒤, 다시 QA 검증을 받는 한 사이클을 의미합니다.
- 각 루프가 5회 안에 종결되지 않으면 같은 루프를 계속 돌리지 않고, 오케스트레이터나 사람이 아래 항목 중 하나를 먼저 결정해야 합니다: 요구사항 재정의, 설계 수정, 범위 축소, 잔여 리스크 승인.
- 동일한 지적이 3회 연속 반복되면 남은 횟수와 무관하게 루프를 중단하고, 왜 해결되지 않았는지와 어떤 의사결정이 필요한지를 산출물의 `리스크 및 미해결`에 명시합니다.
- 되먹임 횟수는 `artifacts/code-review.md`와 `artifacts/qa-plan.md`의 `검증 또는 근거` 섹션에 각 루프 기준으로 `feedback_cycle: N` 형태로 남겨 추적합니다.

## 피처 유형별 최소 흐름

### 백엔드 중심 피처

`business-analyst` -> `api-designer` -> `backend-developer` -> `code-reviewer` -> `qa-expert`

### 웹 UI 중심 피처

`business-analyst` -> `ui-designer` -> `frontend-developer` -> `code-reviewer` -> `qa-expert`

### 모바일 UI 중심 피처

`business-analyst` -> `ui-designer` -> `mobile-developer` -> `code-reviewer` -> `qa-expert`

### 풀스택 피처

`business-analyst` -> (`api-designer` || `ui-designer`) -> (`backend-developer` || `frontend-developer` || `mobile-developer`) -> `code-reviewer` -> `qa-expert`

## 운영 팁

- `task.md`는 요구사항 원문, `artifacts/*.md`는 해석과 실행 결과라는 역할을 분리합니다.
- 사람이 읽기 쉽고 에이전트가 재사용하기 쉬운 형식은 짧은 Markdown 문서입니다. JSON보다 유지보수성이 높고, 자유 서술과 체크리스트를 함께 담기 좋습니다.
- 에이전트 간 전달 누락을 줄이려면 산출물 마지막 섹션을 항상 `다음 에이전트 전달사항`으로 고정하는 것이 가장 효과적입니다.
