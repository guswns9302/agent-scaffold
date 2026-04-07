# Agent List

오케스트레이션 시 참고할 커스텀 에이전트 목록입니다. 기준 경로는 `.codex/agents`입니다.

## 작성 규칙

- 새 에이전트를 추가할 때는 번호를 순차적으로 이어갑니다.
- 에이전트 디렉터리 기준 경로는 `.codex/agents`입니다.
- 각 항목은 이름, 용도, 핵심 역할이 바로 보이도록 짧게 작성합니다.

## 공통 입출력 규칙

- 모든 피처의 기준 문서는 `docs/feature/<feature-slug>/TASKS.md`입니다.
- 모든 에이전트 산출물은 `docs/feature/<feature-slug>/artifacts/` 아래에 저장합니다.
- 각 에이전트는 자신의 주 산출물만 생성하거나 갱신하고, 다른 에이전트 산출물은 덮어쓰지 않습니다.
- 다음 에이전트가 그대로 입력으로 쓸 수 있도록 각 산출물에는 최소 `목적`, `입력`, `핵심 결정/구현`, `리스크 및 미해결`, `다음 에이전트 전달사항` 섹션을 포함합니다.
- 산출물 파일은 필요한 경우에만 생성합니다. 예를 들어 모바일이 없는 피처는 `mobile-implementation.md`를 만들지 않습니다.

## 01. business-analyst

요구사항이 모호하거나 범위 정리가 필요할 때 사용하는 에이전트입니다.

- 비즈니스 목표를 구현 가능한 입력으로 정리합니다.
- 제약사항과 범위를 구조화합니다.
- 수용 기준을 명확히 정리합니다.
- Input: `TASKS.md`, `docs/feature/README.md`, 필요 시 `docs/prd/prd_v4.md`
- Output: `docs/feature/<feature-slug>/artifacts/business-analysis.md`

## 02. api-designer

API 계약, 스키마, 인증 경계를 설계해야 할 때 사용하는 에이전트입니다.

- 리소스와 엔드포인트 구조를 정의합니다.
- 요청/응답 스키마와 에러 모델을 정리합니다.
- 호환성 및 변경 영향을 검토합니다.
- Input: `TASKS.md`, `artifacts/business-analysis.md`, 필요 시 기존 구현 산출물
- Output: `docs/feature/<feature-slug>/artifacts/api-contract.md`

## 03. backend-developer

백엔드 구현이나 서버 측 버그 수정을 진행할 때 사용하는 에이전트입니다.

- 도메인 로직과 데이터 흐름을 구현합니다.
- 인증, 권한, 상태 전이를 안전하게 반영합니다.
- 실패 경로와 부작용을 점검합니다.
- Input: `TASKS.md`, `artifacts/business-analysis.md`, 필요 시 `artifacts/api-contract.md`, `artifacts/qa-plan.md`
- Output: `docs/feature/<feature-slug>/artifacts/backend-implementation.md`

## 04. mobile-developer

사용자 앱의 모바일 구현과 디바이스 제약 대응이 필요할 때 사용하는 에이전트입니다.

- 화면 흐름과 라이프사이클을 구현합니다.
- API 연동과 로컬 상태를 정리합니다.
- 네트워크 저하나 무데이터 상황을 점검합니다.
- Input: `TASKS.md`, `artifacts/business-analysis.md`, 필요 시 `artifacts/api-contract.md`, `artifacts/ui-spec.md`, `artifacts/backend-implementation.md`
- Output: `docs/feature/<feature-slug>/artifacts/mobile-implementation.md`

## 05. frontend-developer

관리자/마스터 웹의 UI 구현이나 프론트엔드 동작 수정이 필요할 때 사용하는 에이전트입니다.

- 화면, 라우트, 상태 흐름을 구현합니다.
- 비동기 데이터와 렌더링 상태를 정리합니다.
- 운영자가 쓰기 쉬운 UI 동작을 맞춥니다.
- Input: `TASKS.md`, `artifacts/business-analysis.md`, 필요 시 `artifacts/api-contract.md`, `artifacts/ui-spec.md`, `artifacts/backend-implementation.md`
- Output: `docs/feature/<feature-slug>/artifacts/frontend-implementation.md`

## 06. ui-designer

구현 전 화면 구조나 상호작용 결정을 구체화할 때 사용하는 에이전트입니다.

- 레이아웃과 정보 위계를 정리합니다.
- 로딩, 빈 상태, 에러 상태를 정의합니다.
- 구현 가능한 수준의 인터랙션 가이드를 제공합니다.
- Input: `TASKS.md`, `artifacts/business-analysis.md`, 필요 시 `artifacts/api-contract.md`
- Output: `docs/feature/<feature-slug>/artifacts/ui-spec.md`

## 07. qa-expert

테스트 범위, 릴리즈 리스크, 검증 전략을 세울 때 사용하는 에이전트입니다.

- 핵심 실패 시나리오를 식별합니다.
- 우선순위 높은 검증 항목을 제안합니다.
- 출시 게이트와 잔여 리스크를 정리합니다.
- Input: `TASKS.md`, `artifacts/business-analysis.md`, 필요 시 구현/설계 산출물 전체
- Output: `docs/feature/<feature-slug>/artifacts/qa-plan.md`

## 08. code-reviewer

구현 결과의 품질, 유지보수성, 회귀 위험을 점검할 때 사용하는 에이전트입니다.

- 코드 변경의 위험 지점을 찾습니다.
- 최소 수정으로 줄일 수 있는 리스크를 제안합니다.
- 테스트 누락과 숨은 결합도를 확인합니다.
- Input: `TASKS.md`, 필요 시 `artifacts/*.md` 전체
- Output: `docs/feature/<feature-slug>/artifacts/code-review.md`
