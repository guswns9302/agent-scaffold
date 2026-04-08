# Agent Scaffold

서브에이전트 기반 작업을 빠르게 시작하기 위한 프로젝트 보일러플레이트입니다. 이 저장소는 그대로 개발을 진행하는 용도보다, 저장소를 클론한 뒤 하위 파일과 폴더를 새 프로젝트 폴더로 복사해서 시작하는 기준 템플릿으로 사용하는 것을 전제로 합니다.

## 목적

- PRD, 디자인 시스템, 피처 문서, 에이전트 워크플로를 한 구조 안에서 관리하기 위한 시작점 제공
- 서브에이전트가 역할별 산출물을 나눠 작성할 수 있는 문서 구조 제공
- 기능 추가와 작업 분기를 `docs/feature/` 기준으로 일관되게 관리할 수 있도록 지원

## 사용 방법

### 1. 저장소 클론

```bash
git clone https://github.com/guswns9302/agent-scaffold.git
```

### 2. 새 프로젝트 폴더 생성

아래 예시에서 `my-new-project`는 원하는 프로젝트 이름으로 바꿔 사용합니다.

```bash
mkdir my-new-project
```

### 3. 새 프로젝트 폴더로 보일러플레이트 복사

`README.md`, `.gitignore`, `.git`을 제외하고 복사하는 예시입니다.

현재 디렉터리에서 `agent-scaffold`와 `my-new-project`가 같은 상위 경로에 있다는 기준 예시입니다.

```bash
rsync -av --exclude='README.md' --exclude='.gitignore' --exclude='.git' agent-scaffold/ my-new-project/
```

이미 `agent-scaffold` 폴더 안으로 들어온 상태라면 아래처럼 실행할 수 있습니다.

```bash
rsync -av --exclude='README.md' --exclude='.gitignore' --exclude='.git' ./ /path/to/my-new-project/
```

### 4. 새 프로젝트에서 문서 초기화

새 프로젝트 폴더로 이동한 뒤 샘플 문서를 실제 프로젝트 기준으로 채웁니다.

```bash
cd my-new-project
```

우선 아래 파일부터 프로젝트 내용에 맞게 수정하면 됩니다.

- [`docs/prd/prd.md`](/Users/okestro/work/vibe-coding/agent-scaffold/docs/prd/prd.md)
- [`docs/design-system/design-system.md`](/Users/okestro/work/vibe-coding/agent-scaffold/docs/design-system/design-system.md)
- [`docs/feature/00-project-feature/task.md`](/Users/okestro/work/vibe-coding/agent-scaffold/docs/feature/00-project-feature/task.md)

## 문서 운영 규칙

### PRD 관리

최종 PRD는 항상 [`docs/prd/prd.md`](/Users/okestro/work/vibe-coding/agent-scaffold/docs/prd/prd.md)에서 관리합니다. 제품 요구사항 변경이나 범위 조정은 먼저 이 문서에 반영한 뒤 후속 작업을 진행하는 것을 기준으로 합니다.

### 피처 등록 및 작업 분기

PRD를 바탕으로 새로운 작업을 분기하거나 새 피처를 등록할 때는 반드시 `docs/feature/` 아래에 피처 폴더를 만들고, 그 안의 `task.md`를 기준 문서로 사용합니다.

피처 폴더 이름은 [`AGENTS.md`](/Users/okestro/work/vibe-coding/agent-scaffold/AGENTS.md)에 정의된 명명 규칙을 따릅니다.

- 형식: `번호-슬러그`
- 예시: `00-foundation-monorepo`, `04-store-detail-reservation`

기본 구조 예시는 아래와 같습니다.

```text
docs/
  feature/
    00-project-feature/
      task.md
      artifacts/
```

피처 작업 시에는 `AGENTS.md`, `.codex/skills/feature-orchestration/SKILL.md`, `.codex/agents/*.toml`을 기준으로 역할별 산출물 규칙을 적용합니다.

## 포함된 샘플 파일

- [`docs/prd/prd.md`](/Users/okestro/work/vibe-coding/agent-scaffold/docs/prd/prd.md)
- [`docs/design-system/design-system.md`](/Users/okestro/work/vibe-coding/agent-scaffold/docs/design-system/design-system.md)
- [`docs/feature/00-project-feature/task.md`](/Users/okestro/work/vibe-coding/agent-scaffold/docs/feature/00-project-feature/task.md)
- `docs/feature/00-project-feature/artifacts/`

## 디렉터리 개요

- `docs/prd/`: 최종 제품 요구사항 문서
- `docs/design-system/`: 공통 디자인 토큰과 UI 규칙
- `docs/feature/`: 피처별 작업 문서와 산출물
- `.codex/skills/feature-orchestration/`: 피처 오케스트레이션 규칙
- `.codex/agents/`: 역할별 에이전트 실행 계약

## 참고

이 저장소의 운영 규칙과 명명 규칙은 [`AGENTS.md`](/Users/okestro/work/vibe-coding/agent-scaffold/AGENTS.md)를 기준으로 합니다.
