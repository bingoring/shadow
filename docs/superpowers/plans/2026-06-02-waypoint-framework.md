# Waypoint Framework Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Waypoint 프레임워크 레포를 생성하고, Shadow 프로젝트의 전체 스테이지 문서를 작성한 뒤, Shadow 레포에 git submodule로 연결한다.

**Architecture:** Waypoint는 FRAMEWORK.md(AI 계약서) + _templates/(범용 골격) + projects/shadow/(Shadow 전용 스테이지 문서)로 구성된다. Shadow 레포는 `docs/dlc/`에 Waypoint를 서브모듈로 마운트한다.

**Tech Stack:** Markdown, Git, Git Submodules

---

## File Map

**생성할 파일 (Waypoint 레포):**
- `~/repository/waypoint/README.md`
- `~/repository/waypoint/FRAMEWORK.md`
- `~/repository/waypoint/STATUS.template.md`
- `~/repository/waypoint/_templates/stage-template.md`
- `~/repository/waypoint/_templates/gate-template.md`
- `~/repository/waypoint/projects/shadow/STATUS.md`
- `~/repository/waypoint/projects/shadow/01-inception/01-context-synthesis.md`
- `~/repository/waypoint/projects/shadow/01-inception/02-domain-model.md`
- `~/repository/waypoint/projects/shadow/01-inception/03-architecture-decision.md`
- `~/repository/waypoint/projects/shadow/02-construction/01-tauri-client.md`
- `~/repository/waypoint/projects/shadow/02-construction/02-nestjs-backend.md`
- `~/repository/waypoint/projects/shadow/02-construction/03-langgraph-agent.md`
- `~/repository/waypoint/projects/shadow/02-construction/04-integration.md`
- `~/repository/waypoint/projects/shadow/03-operations/01-deployment.md`
- `~/repository/waypoint/projects/shadow/03-operations/02-monitoring.md`

**수정할 파일 (Shadow 레포):**
- `~/repository/shadow/.gitmodules` (git submodule add로 자동 생성)

---

## Task 1: Waypoint 레포 초기화

**Files:**
- Create: `~/repository/waypoint/` (디렉토리)

- [ ] **Step 1: 로컬 레포 디렉토리 생성 및 git 초기화**

```bash
mkdir -p ~/repository/waypoint
cd ~/repository/waypoint
git init
git remote add origin https://github.com/bingoring/waypoint.git
```

Expected: `Initialized empty Git repository in .../waypoint/.git/`

- [ ] **Step 2: 디렉토리 구조 생성**

```bash
mkdir -p ~/repository/waypoint/_templates
mkdir -p ~/repository/waypoint/projects/shadow/01-inception
mkdir -p ~/repository/waypoint/projects/shadow/02-construction
mkdir -p ~/repository/waypoint/projects/shadow/03-operations
```

---

## Task 2: README.md 작성

**Files:**
- Create: `~/repository/waypoint/README.md`

- [ ] **Step 1: README.md 작성**

```markdown
# Waypoint

AI 제안 → 사람 승인 게이트 기반의 Markdown 문서 중심 개발 라이프사이클 프레임워크.

## 개념

각 개발 단계는 하나의 Markdown 문서로 표현된다. AI가 문서를 작성하면 사람이 검토·승인해야 다음 단계로 진행된다. 문서 자체가 단일 진실 공급원(Single Source of Truth)이다.

## 구조

```
waypoint/
├── FRAMEWORK.md          ← AI 규칙·컨벤션 계약서
├── STATUS.template.md    ← 프로젝트 대시보드 템플릿
├── _templates/           ← 범용 문서 골격
└── projects/
    └── {project-name}/   ← 프로젝트별 스테이지 문서
```

## 새 프로젝트에 적용하기

```bash
# 1. 프로젝트 레포에서
git submodule add https://github.com/bingoring/waypoint docs/dlc

# 2. Waypoint 레포에 프로젝트 디렉토리 생성
cd docs/dlc
mkdir -p projects/myapp/01-inception
mkdir -p projects/myapp/02-construction
mkdir -p projects/myapp/03-operations
cp STATUS.template.md projects/myapp/STATUS.md
# 이후 _templates/stage-template.md를 참고해 스테이지 문서 작성
```

## 3단계 라이프사이클

- **Phase 1 — Inception (What):** 기획, 도메인 모델, 아키텍처 결정
- **Phase 2 — Construction (How):** 레이어별 구현 계획
- **Phase 3 — Operations (Ship):** 배포 및 운영
```

- [ ] **Step 2: 커밋**

```bash
cd ~/repository/waypoint
git add README.md
git commit -m "docs: add README"
```

---

## Task 3: FRAMEWORK.md 작성 (AI 계약서)

**Files:**
- Create: `~/repository/waypoint/FRAMEWORK.md`

- [ ] **Step 1: FRAMEWORK.md 작성**

```markdown
# Waypoint Framework Rules

> 이 문서는 AI가 Waypoint 프레임워크 안에서 동작할 때 반드시 따라야 하는 계약서다.

## 세션 시작 프로토콜

AI가 새 세션을 시작할 때 반드시 아래 순서를 따른다:

1. `projects/{project-name}/STATUS.md`를 읽는다
2. `PENDING` 상태인 가장 앞 스테이지를 찾는다
3. 해당 스테이지 문서를 열고 `Inputs` 섹션에 명시된 파일을 모두 읽는다
4. `AI Proposal` 섹션을 작성하고 status를 `AI_PROPOSED`로 변경한다
5. 사람에게 리뷰를 요청하고 대기한다

## 절대 규칙

- `HUMAN_APPROVED` 없이는 다음 스테이지로 진행하지 않는다
- `AI Proposal` 섹션은 AI 전용이다. 사람이 직접 수정하면 안 된다
- 비가역 게이트(`⚠️` 표시)에서는 추가 체크리스트 항목을 반드시 확인한다
- STATUS.md의 상태 표는 항상 실제 문서 상태와 동기화되어야 한다

## 상태 생애주기

```
PENDING → IN_PROGRESS → AI_PROPOSED → HUMAN_APPROVED
```

| 상태 | 설명 |
|------|------|
| `PENDING` | 시작 전. 이전 스테이지가 APPROVED되지 않으면 진입 불가 |
| `IN_PROGRESS` | AI가 현재 작업 중 |
| `AI_PROPOSED` | AI 제안 완료. 사람의 검토 대기 |
| `HUMAN_APPROVED` | 사람이 승인. 다음 스테이지 언락 |

## 비가역 게이트 추가 체크리스트

`⚠️` 표시가 있는 스테이지는 아래 항목을 추가로 확인해야 승인 가능하다:

- [ ] 이 결정을 번복할 경우 영향받는 파일/레이어 목록을 작성했는가
- [ ] 대안을 검토하고 탈락 이유를 문서화했는가
- [ ] 외부 의존성(API, 라이브러리, 비용)을 확인했는가

## STATUS.md 업데이트 규칙

스테이지 상태가 변경될 때마다 STATUS.md의 테이블을 함께 업데이트한다.
```

- [ ] **Step 2: 커밋**

```bash
cd ~/repository/waypoint
git add FRAMEWORK.md
git commit -m "docs: add FRAMEWORK rules"
```

---

## Task 4: 템플릿 파일 작성

**Files:**
- Create: `~/repository/waypoint/STATUS.template.md`
- Create: `~/repository/waypoint/_templates/stage-template.md`
- Create: `~/repository/waypoint/_templates/gate-template.md`

- [ ] **Step 1: STATUS.template.md 작성**

```markdown
# {Project Name} — Waypoint Status

**Framework:** [Waypoint](https://github.com/bingoring/waypoint)  
**Last updated:** YYYY-MM-DD

---

## Phase 1 — Inception (What)

| 스테이지 | 문서 | 상태 |
|---------|------|------|
| 1-1 Context Synthesis | [01-context-synthesis.md](01-inception/01-context-synthesis.md) | PENDING |
| 1-2 Domain Model | [02-domain-model.md](01-inception/02-domain-model.md) | PENDING |
| 1-3 Architecture Decision ⚠️ | [03-architecture-decision.md](01-inception/03-architecture-decision.md) | PENDING |

## Phase 2 — Construction (How)

| 스테이지 | 문서 | 상태 |
|---------|------|------|
| 2-1 (컴포넌트명) | [링크] | PENDING |
| 2-2 (컴포넌트명) | [링크] | PENDING |

## Phase 3 — Operations (Ship)

| 스테이지 | 문서 | 상태 |
|---------|------|------|
| 3-1 Deployment | [01-deployment.md](03-operations/01-deployment.md) | PENDING |
| 3-2 Monitoring | [02-monitoring.md](03-operations/02-monitoring.md) | PENDING |

---

## AI 진입점

> AI는 위 테이블에서 `PENDING` 상태인 가장 앞 스테이지부터 작업을 시작하세요.
> 규칙: `FRAMEWORK.md` 참조
```

- [ ] **Step 2: stage-template.md 작성**

```markdown
---
phase: XX-phase-name
stage: XX-stage-name
status: PENDING
updated: YYYY-MM-DD
---

# [Stage X-Y] 스테이지 이름

## 목적

이 스테이지에서 AI가 달성해야 하는 목표와 최종 산출물을 한 문장으로 기술.

## 입력 (Inputs)

- 이전 스테이지 문서: `../XX-phase/XX-stage.md`
- 참조 문서: `../../prd.md`

## 체크리스트

- [ ] 항목 1
- [ ] 항목 2
- [ ] 항목 3

## AI 제안 (AI Proposal)

> ⚠️ 이 섹션은 AI가 작성합니다. 사람이 직접 수정하지 마세요.

*[AI가 분석 결과와 설계안을 여기에 작성]*

## 검토 게이트 (Human Gate)

> 아래 항목을 확인 후 frontmatter의 status를 `HUMAN_APPROVED`로 변경하세요.

- [ ] AI 제안이 PRD 요구사항과 일치하는가?
- [ ] 다음 스테이지 진행에 필요한 결정이 모두 내려졌는가?
- [ ] 산출물이 명확하게 정의되었는가?

## 다음 단계

승인 후 → `STATUS.md`의 이 스테이지 상태를 `HUMAN_APPROVED`로 업데이트 → `XX-next-stage.md`로 이동
```

- [ ] **Step 3: gate-template.md 작성 (비가역 게이트 전용)**

```markdown
# 비가역 게이트 체크리스트 ⚠️

이 스테이지는 번복 비용이 높은 결정을 포함합니다.
아래 항목을 모두 확인한 후에만 `HUMAN_APPROVED`로 변경하세요.

## 기본 게이트

- [ ] AI 제안이 PRD 요구사항과 일치하는가?
- [ ] 다음 스테이지 진행에 필요한 결정이 모두 내려졌는가?

## 비가역성 확인

- [ ] 이 결정을 번복할 경우 영향받는 파일/레이어 목록을 작성했는가?
- [ ] 대안 접근법을 검토하고 탈락 이유를 문서화했는가?
- [ ] 외부 API 의존성 및 비용 구조를 확인했는가?
- [ ] 이 결정으로 인해 잠기는(lock-in) 기술 스택이 있는가? 수용 가능한가?
```

- [ ] **Step 4: 커밋**

```bash
cd ~/repository/waypoint
git add STATUS.template.md _templates/
git commit -m "docs: add templates"
```

---

## Task 5: Shadow STATUS.md 작성

**Files:**
- Create: `~/repository/waypoint/projects/shadow/STATUS.md`

- [ ] **Step 1: Shadow STATUS.md 작성**

```markdown
# Shadow Engineer — Waypoint Status

**Framework:** [Waypoint](https://github.com/bingoring/waypoint)  
**PRD:** [prd.md](../../prd.md) | [prd-tech.md](../../prd-tech.md)  
**Last updated:** 2026-06-02

---

## Phase 1 — Inception (What)

| 스테이지 | 문서 | 상태 |
|---------|------|------|
| 1-1 Context Synthesis | [01-context-synthesis.md](01-inception/01-context-synthesis.md) | PENDING |
| 1-2 Domain Model | [02-domain-model.md](01-inception/02-domain-model.md) | PENDING |
| 1-3 Architecture Decision ⚠️ | [03-architecture-decision.md](01-inception/03-architecture-decision.md) | PENDING |

## Phase 2 — Construction (How)

| 스테이지 | 문서 | 상태 |
|---------|------|------|
| 2-1 Tauri Client | [01-tauri-client.md](02-construction/01-tauri-client.md) | PENDING |
| 2-2 NestJS Backend | [02-nestjs-backend.md](02-construction/02-nestjs-backend.md) | PENDING |
| 2-3 LangGraph Agent | [03-langgraph-agent.md](02-construction/03-langgraph-agent.md) | PENDING |
| 2-4 Integration | [04-integration.md](02-construction/04-integration.md) | PENDING |

## Phase 3 — Operations (Ship)

| 스테이지 | 문서 | 상태 |
|---------|------|------|
| 3-1 Deployment | [01-deployment.md](03-operations/01-deployment.md) | PENDING |
| 3-2 Monitoring | [02-monitoring.md](03-operations/02-monitoring.md) | PENDING |

---

## AI 진입점

> AI는 위 테이블에서 `PENDING` 상태인 가장 앞 스테이지부터 작업을 시작하세요.  
> 규칙: [`FRAMEWORK.md`](../../FRAMEWORK.md) 참조
```

- [ ] **Step 2: 커밋**

```bash
cd ~/repository/waypoint
git add projects/shadow/STATUS.md
git commit -m "feat(shadow): add STATUS dashboard"
```

---

## Task 6: Phase 1 — Inception 스테이지 문서 작성

**Files:**
- Create: `~/repository/waypoint/projects/shadow/01-inception/01-context-synthesis.md`
- Create: `~/repository/waypoint/projects/shadow/01-inception/02-domain-model.md`
- Create: `~/repository/waypoint/projects/shadow/01-inception/03-architecture-decision.md`

- [ ] **Step 1: 01-context-synthesis.md 작성**

```markdown
---
phase: 01-inception
stage: 01-context-synthesis
status: PENDING
updated: 2026-06-02
---

# [Stage 1-1] Context Synthesis

## 목적

prd.md와 prd-tech.md를 분석하여 Shadow Engineer가 통합할 4각 툴(Notion/Confluence/Jira/Slack)의 역할, 데이터 가중치, 3-Layer 병합 전략을 명세한다.

## 입력 (Inputs)

- `../../prd.md` — 제품 요구사항 (섹션 1~3 중점)
- `../../prd-tech.md` — 기술 스택 및 아키텍처

## Shadow 전용 체크리스트

- [ ] 4각 툴 각각의 역할(Skeleton/Constraint/Pivot)과 데이터 가중치 정의
- [ ] 3-Layer 병합 전략 (Base→Scope→Adjustment) 구체적 로직 기술
- [ ] Conflict Resolution 예시 시나리오 최소 2개 작성
- [ ] 각 툴의 인증 방식(OAuth 등) 및 데이터 접근 방식 목록화
- [ ] MVP 단계에서 연동할 툴 우선순위 결정

## AI 제안 (AI Proposal)

> ⚠️ 이 섹션은 AI가 작성합니다. 사람이 직접 수정하지 마세요.

*[AI가 prd.md를 분석한 뒤 4각 툴 역할 정의, 가중치 설계, 3-Layer 로직, Conflict Resolution 전략을 여기에 작성]*

## 검토 게이트 (Human Gate)

- [ ] 4각 툴의 역할과 가중치가 PRD 섹션 1의 표와 일치하는가?
- [ ] 3-Layer 병합 로직이 실제 구현 가능한 수준으로 구체화되었는가?
- [ ] Conflict Resolution 예시가 PRD 섹션 4.1의 예시를 포함하는가?
- [ ] MVP 우선순위가 PRD 섹션 6의 Week 1~2 태스크와 정렬되는가?

## 다음 단계

승인 후 → STATUS.md의 1-1을 `HUMAN_APPROVED`로 업데이트 → `02-domain-model.md`로 이동
```

- [ ] **Step 2: 02-domain-model.md 작성**

```markdown
---
phase: 01-inception
stage: 02-domain-model
status: PENDING
updated: 2026-06-02
---

# [Stage 1-2] Domain Model

## 목적

Shadow Engineer의 핵심 엔티티, 데이터 흐름, DTO 초안을 정의한다. 이 문서가 NestJS Prisma 스키마와 LangGraph 상태 스키마의 기반이 된다.

## 입력 (Inputs)

- `01-context-synthesis.md` — 4각 툴 역할 및 3-Layer 전략 (HUMAN_APPROVED 필요)
- `../../prd.md` — 섹션 4.1, 4.2, 5
- `../../prd-tech.md` — 섹션 2, 3

## Shadow 전용 체크리스트

- [ ] 핵심 엔티티 정의: User, Integration, Ticket, ContextSnapshot, ArchitecturePlan
- [ ] 엔티티 간 관계(ERD 텍스트 형식) 작성
- [ ] 3-Layer 병합 결과물인 ContextSnapshot의 필드 구조 정의
- [ ] ArchitecturePlan 엔티티 (AI가 생성하는 설계도) 구조 정의
- [ ] 웹훅 이벤트 스키마: Jira StatusChange, Slack Message, Notion Page 각각 정의
- [ ] Conflict 발생 시 사용자에게 전달하는 Elicitation 메시지 구조 정의

## AI 제안 (AI Proposal)

> ⚠️ 이 섹션은 AI가 작성합니다. 사람이 직접 수정하지 마세요.

*[AI가 엔티티 정의, ERD, DTO/스키마 초안, 웹훅 이벤트 구조를 여기에 작성]*

## 검토 게이트 (Human Gate)

- [ ] 모든 핵심 엔티티가 정의되었는가?
- [ ] ContextSnapshot 구조가 3-Layer 전략을 수용할 수 있는가?
- [ ] 웹훅 이벤트 스키마가 실제 API 문서와 대조 가능한 수준인가?
- [ ] Prisma 스키마로 바로 변환 가능한 수준으로 구체적인가?

## 다음 단계

승인 후 → STATUS.md의 1-2를 `HUMAN_APPROVED`로 업데이트 → `03-architecture-decision.md`로 이동
```

- [ ] **Step 3: 03-architecture-decision.md 작성 (비가역 게이트)**

```markdown
---
phase: 01-inception
stage: 03-architecture-decision
status: PENDING
updated: 2026-06-02
---

# [Stage 1-3] Architecture Decision ⚠️ 비가역 게이트

## 목적

Shadow Engineer의 기술 스택을 최종 확정한다. 이 결정은 이후 모든 Construction 스테이지의 기반이 되며, 번복 시 전면 재설계가 필요하다.

## 입력 (Inputs)

- `02-domain-model.md` — 엔티티 및 데이터 흐름 (HUMAN_APPROVED 필요)
- `../../prd-tech.md` — 전체 기술 스택 권장안

## Shadow 전용 체크리스트

- [ ] 로컬 클라이언트: Tauri v2 확정 (Electron 대비 메모리 이점 수치 제시)
- [ ] AI 에이전트 언어: LangGraph.js(TypeScript 통일) vs LangGraph(Python 분리) 결정
- [ ] 벡터 DB: pgvector(PostgreSQL 플러그인) vs Qdrant 독립 서버 결정
- [ ] 모노레포 구조 확정: Nx 사용 여부, 패키지 분리 전략
- [ ] AST 파싱: Tree-sitter(Rust) vs ts-morph(TypeScript) 결정
- [ ] 각 결정의 대안 탈락 이유 문서화

## AI 제안 (AI Proposal)

> ⚠️ 이 섹션은 AI가 작성합니다. 사람이 직접 수정하지 마세요.

*[AI가 각 기술 결정에 대해 대안 비교, 권장 결정, 탈락 이유를 ADR 형식으로 작성]*

## 검토 게이트 (Human Gate)

### 기본 게이트
- [ ] AI 제안이 prd-tech.md의 권장 스택과 일치하거나 탈락 이유가 명확한가?
- [ ] 모든 기술 결정이 내려졌는가?

### 비가역성 확인 ⚠️
- [ ] 이 결정을 번복할 경우 영향받는 파일/레이어 목록을 작성했는가?
- [ ] 대안 기술 스택을 검토하고 탈락 이유를 문서화했는가?
- [ ] 외부 API 의존성(Atlassian, Notion, Slack) 및 비용 구조를 확인했는가?
- [ ] LangGraph.js vs Python 선택으로 인한 장기 lock-in을 수용할 수 있는가?

## 다음 단계

승인 후 → STATUS.md의 1-3을 `HUMAN_APPROVED`로 업데이트 → Phase 2 Construction 시작
```

- [ ] **Step 4: 커밋**

```bash
cd ~/repository/waypoint
git add projects/shadow/01-inception/
git commit -m "feat(shadow): add Phase 1 inception stages"
```

---

## Task 7: Phase 2 — Construction 스테이지 문서 작성

**Files:**
- Create: `~/repository/waypoint/projects/shadow/02-construction/01-tauri-client.md`
- Create: `~/repository/waypoint/projects/shadow/02-construction/02-nestjs-backend.md`
- Create: `~/repository/waypoint/projects/shadow/02-construction/03-langgraph-agent.md`
- Create: `~/repository/waypoint/projects/shadow/02-construction/04-integration.md`

- [ ] **Step 1: 01-tauri-client.md 작성**

```markdown
---
phase: 02-construction
stage: 01-tauri-client
status: PENDING
updated: 2026-06-02
---

# [Stage 2-1] Tauri Client

## 목적

Tauri v2 로컬 앱의 컴포넌트 구조, IPC 인터페이스, AST 파싱 플로우, VS Code API 연동 방식을 설계한다.

## 입력 (Inputs)

- `../01-inception/03-architecture-decision.md` — 확정된 기술 스택 (HUMAN_APPROVED 필요)
- `../01-inception/02-domain-model.md` — ArchitecturePlan 엔티티 구조

## Shadow 전용 체크리스트

- [ ] Tauri v2 커맨드(IPC) 목록 정의: 백엔드 호출, 파일 주입, Git 브랜치 생성
- [ ] React 컴포넌트 트리 설계: 팝업 UI, ArchPlan 뷰어, Apply 버튼 플로우
- [ ] Tree-sitter(또는 ts-morph) AST 스캔 로직: NestJS 패턴(@Module, @Injectable) 인식
- [ ] 파일 주입 로직: `src/domains/{domain_name}` 하위 파일셋 생성 규칙
- [ ] 의존성 자동 업데이트: 상위 모듈 imports 배열 자동 수정 로직
- [ ] 로컬 백엔드 WebSocket 연결 관리

## AI 제안 (AI Proposal)

> ⚠️ 이 섹션은 AI가 작성합니다. 사람이 직접 수정하지 마세요.

*[AI가 Tauri IPC 커맨드 목록, React 컴포넌트 구조, AST 파싱 플로우, 파일 주입 알고리즘을 설계]*

## 검토 게이트 (Human Gate)

- [ ] IPC 커맨드가 PRD 섹션 4.2의 로컬 에이전트 역할을 모두 커버하는가?
- [ ] AST 파싱이 실제 NestJS 프로젝트 패턴을 정확히 인식하는가?
- [ ] 파일 주입 후 의존성 자동 업데이트 로직이 안전한가? (기존 코드 파괴 없음)
- [ ] 20~30MB 메모리 제약이 설계에 반영되었는가?

## 다음 단계

승인 후 → STATUS.md의 2-1을 `HUMAN_APPROVED`로 업데이트 → `02-nestjs-backend.md`로 이동
```

- [ ] **Step 2: 02-nestjs-backend.md 작성**

```markdown
---
phase: 02-construction
stage: 02-nestjs-backend
status: PENDING
updated: 2026-06-02
---

# [Stage 2-2] NestJS Backend

## 목적

NestJS 애플리케이션의 모듈 구조, BullMQ 워커 설계, Prisma 스키마, 웹훅 핸들러를 설계한다.

## 입력 (Inputs)

- `../01-inception/03-architecture-decision.md` — 확정된 기술 스택
- `../01-inception/02-domain-model.md` — 엔티티 및 웹훅 이벤트 스키마

## Shadow 전용 체크리스트

- [ ] 모듈 구조 정의: AuthModule, WebhookModule, QueueModule, ContextModule, AgentModule
- [ ] Jira 웹훅 핸들러: `In Progress` 상태 변경 이벤트 처리 로직
- [ ] Slack 웹훅 핸들러: 특정 채널 메시지 필터링 및 파싱
- [ ] Notion/Confluence API 폴링 또는 웹훅 수신 전략
- [ ] BullMQ 큐 설계: 큐 이름, 워커 수, Retry 전략, Dead Letter Queue
- [ ] Prisma 스키마: 도메인 모델 기반 테이블 정의
- [ ] OAuth 토큰 저장 및 갱신 전략

## AI 제안 (AI Proposal)

> ⚠️ 이 섹션은 AI가 작성합니다. 사람이 직접 수정하지 마세요.

*[AI가 NestJS 모듈 구조, 웹훅 핸들러 로직, BullMQ 설계, Prisma 스키마 초안을 작성]*

## 검토 게이트 (Human Gate)

- [ ] 4개 외부 툴의 웹훅/API 수신 전략이 모두 정의되었는가?
- [ ] BullMQ Retry 전략이 웹훅 폭주 시나리오를 커버하는가?
- [ ] Prisma 스키마가 도메인 모델과 1:1 대응되는가?
- [ ] OAuth 토큰 보안 저장 방식이 명시되었는가?

## 다음 단계

승인 후 → STATUS.md의 2-2를 `HUMAN_APPROVED`로 업데이트 → `03-langgraph-agent.md`로 이동
```

- [ ] **Step 3: 03-langgraph-agent.md 작성**

```markdown
---
phase: 02-construction
stage: 03-langgraph-agent
status: PENDING
updated: 2026-06-02
---

# [Stage 2-3] LangGraph Agent

## 목적

LangGraph의 그래프 노드 구조, 상태 스키마, 각 노드의 프롬프트 전략을 설계한다.

## 입력 (Inputs)

- `../01-inception/03-architecture-decision.md` — LangGraph.js vs Python 결정
- `../01-inception/01-context-synthesis.md` — 3-Layer 병합 전략
- `../01-inception/02-domain-model.md` — ContextSnapshot, ArchitecturePlan 구조

## Shadow 전용 체크리스트

- [ ] 그래프 노드 정의: Fetch, Merge, ConflictDetect, Elicit, Resolve, Generate, Validate
- [ ] 그래프 상태(State) 스키마: 각 노드 간 전달되는 데이터 구조
- [ ] Fetch 노드: 4각 툴 데이터 수집 및 가중치 적용 로직
- [ ] Merge 노드: 3-Layer 병합 알고리즘 (Base→Scope→Adjustment)
- [ ] ConflictDetect 노드: 충돌 감지 기준 정의
- [ ] Elicit 노드: 사용자에게 질문을 던지는 조건 및 메시지 포맷
- [ ] Generate 노드: 최종 ArchitecturePlan 생성 프롬프트 전략
- [ ] RAG 전략: pgvector로 과거 컨텍스트 검색하는 시점과 쿼리 방식

## AI 제안 (AI Proposal)

> ⚠️ 이 섹션은 AI가 작성합니다. 사람이 직접 수정하지 마세요.

*[AI가 LangGraph 노드 다이어그램, 상태 스키마, 각 노드 프롬프트 전략, RAG 쿼리 전략을 작성]*

## 검토 게이트 (Human Gate)

- [ ] 그래프 노드가 PRD 섹션 5의 유저 시나리오 전체 흐름을 커버하는가?
- [ ] Elicit 노드의 조건이 명확한가? (언제 사용자에게 질문하는가)
- [ ] Generate 노드의 산출물이 Tauri 클라이언트가 소비할 수 있는 형식인가?
- [ ] RAG 쿼리가 실제로 유의미한 과거 컨텍스트를 찾을 수 있는 구조인가?

## 다음 단계

승인 후 → STATUS.md의 2-3을 `HUMAN_APPROVED`로 업데이트 → `04-integration.md`로 이동
```

- [ ] **Step 4: 04-integration.md 작성**

```markdown
---
phase: 02-construction
stage: 04-integration
status: PENDING
updated: 2026-06-02
---

# [Stage 2-4] Integration

## 목적

NestJS ↔ LangGraph 통신 프로토콜, Tauri ↔ Backend WebSocket 설계, 전체 E2E 시나리오를 검증한다.

## 입력 (Inputs)

- `01-tauri-client.md` — Tauri IPC 및 WebSocket 클라이언트 (HUMAN_APPROVED 필요)
- `02-nestjs-backend.md` — NestJS 모듈 및 큐 구조 (HUMAN_APPROVED 필요)
- `03-langgraph-agent.md` — LangGraph 그래프 및 상태 스키마 (HUMAN_APPROVED 필요)

## Shadow 전용 체크리스트

- [ ] NestJS → LangGraph 호출 방식: HTTP REST vs gRPC vs 직접 함수 호출(모노레포)
- [ ] LangGraph → NestJS 결과 반환 형식: ArchitecturePlan JSON 스키마 확정
- [ ] Tauri ↔ NestJS WebSocket 이벤트 목록: plan_ready, apply_requested, apply_done
- [ ] E2E 시나리오 1: Jira 티켓 In Progress → 팝업 표시까지 전체 흐름
- [ ] E2E 시나리오 2: Conflict 발생 → Elicit → 사용자 응답 → Generate 재실행
- [ ] 오류 처리: 각 레이어 장애 시 Tauri UI에 표시되는 에러 메시지 정의
- [ ] 모노레포 패키지 간 타입 공유 전략 (DTO 공유 패키지)

## AI 제안 (AI Proposal)

> ⚠️ 이 섹션은 AI가 작성합니다. 사람이 직접 수정하지 마세요.

*[AI가 통신 프로토콜, WebSocket 이벤트 목록, E2E 시퀀스 다이어그램, 오류 처리 전략을 작성]*

## 검토 게이트 (Human Gate)

- [ ] E2E 시나리오가 PRD 섹션 5의 유저 시나리오와 정확히 일치하는가?
- [ ] 타입 공유 전략이 TypeScript 모노레포 환경에서 실제로 동작하는가?
- [ ] 오류 처리가 모든 레이어(Tauri/NestJS/LangGraph)를 커버하는가?
- [ ] WebSocket 이벤트 이름과 페이로드가 Tauri 클라이언트 설계와 일치하는가?

## 다음 단계

승인 후 → STATUS.md의 2-4를 `HUMAN_APPROVED`로 업데이트 → Phase 3 Operations 시작
```

- [ ] **Step 5: 커밋**

```bash
cd ~/repository/waypoint
git add projects/shadow/02-construction/
git commit -m "feat(shadow): add Phase 2 construction stages"
```

---

## Task 8: Phase 3 — Operations 스테이지 문서 작성

**Files:**
- Create: `~/repository/waypoint/projects/shadow/03-operations/01-deployment.md`
- Create: `~/repository/waypoint/projects/shadow/03-operations/02-monitoring.md`

- [ ] **Step 1: 01-deployment.md 작성**

```markdown
---
phase: 03-operations
stage: 01-deployment
status: PENDING
updated: 2026-06-02
---

# [Stage 3-1] Deployment

## 목적

Shadow Engineer의 배포 구성(Docker Compose, 환경변수, Tauri 앱 빌드·배포 파이프라인)을 설계한다.

## 입력 (Inputs)

- `../02-construction/04-integration.md` — 전체 통합 아키텍처 (HUMAN_APPROVED 필요)
- `../../prd-tech.md` — 기술 스택

## Shadow 전용 체크리스트

- [ ] Docker Compose 서비스 정의: nestjs, postgres, redis, qdrant(또는 pgvector만 사용)
- [ ] 환경변수 목록: 각 외부 툴 OAuth 키, DB 연결 정보, LLM API 키
- [ ] Tauri 앱 빌드 파이프라인: macOS/Windows 바이너리 생성 전략
- [ ] 개발/스테이징/프로덕션 환경 분리 전략
- [ ] 시크릿 관리: .env 파일 vs 시크릿 매니저

## AI 제안 (AI Proposal)

> ⚠️ 이 섹션은 AI가 작성합니다. 사람이 직접 수정하지 마세요.

*[AI가 Docker Compose 설정, 환경변수 목록, 배포 런북을 작성]*

## 검토 게이트 (Human Gate)

- [ ] Docker Compose가 모든 서비스를 포함하는가?
- [ ] 환경변수 목록에 누락된 키가 없는가?
- [ ] Tauri 앱 배포 방식이 실제로 실행 가능한가?

## 다음 단계

승인 후 → STATUS.md의 3-1을 `HUMAN_APPROVED`로 업데이트 → `02-monitoring.md`로 이동
```

- [ ] **Step 2: 02-monitoring.md 작성**

```markdown
---
phase: 03-operations
stage: 02-monitoring
status: PENDING
updated: 2026-06-02
---

# [Stage 3-2] Monitoring

## 목적

Shadow Engineer 운영 단계에서 모니터링할 핵심 지표와 알림 전략을 정의한다.

## 입력 (Inputs)

- `01-deployment.md` — 배포 구성 (HUMAN_APPROVED 필요)

## Shadow 전용 체크리스트

- [ ] BullMQ 큐 모니터링: 적체(backlog) 임계값, 실패율 알림 기준
- [ ] 웹훅 실패율 추적: 재시도 소진 이벤트 알림
- [ ] LLM 토큰 비용 추적: 일별/월별 사용량 대시보드
- [ ] Tauri 앱 크래시 리포팅 전략
- [ ] PostgreSQL 슬로우 쿼리 임계값 정의

## AI 제안 (AI Proposal)

> ⚠️ 이 섹션은 AI가 작성합니다. 사람이 직접 수정하지 마세요.

*[AI가 모니터링 지표 목록, 알림 임계값, 대시보드 구성 방안을 작성]*

## 검토 게이트 (Human Gate)

- [ ] 모든 핵심 지표가 정의되었는가?
- [ ] LLM 토큰 비용 추적이 실제 API 응답에서 파싱 가능한가?
- [ ] 알림 채널(Slack, 이메일 등)이 결정되었는가?

## 다음 단계

승인 후 → Waypoint의 모든 스테이지 완료. 실제 구현 단계로 전환.
```

- [ ] **Step 3: 커밋**

```bash
cd ~/repository/waypoint
git add projects/shadow/03-operations/
git commit -m "feat(shadow): add Phase 3 operations stages"
```

---

## Task 9: GitHub 푸시 및 Shadow 서브모듈 연결

**Files:**
- Modify: `~/repository/shadow/.gitmodules` (자동 생성)

- [ ] **Step 1: Waypoint 레포 GitHub에 푸시**

> ⚠️ `https://github.com/bingoring/waypoint` 레포가 GitHub에 미리 생성되어 있어야 합니다.

```bash
cd ~/repository/waypoint
git push -u origin main
```

Expected: 모든 커밋이 GitHub에 푸시됨

- [ ] **Step 2: Shadow 레포에 서브모듈 추가**

```bash
cd ~/repository/shadow
git submodule add https://github.com/bingoring/waypoint docs/dlc
```

Expected: `docs/dlc/` 디렉토리 생성, `.gitmodules` 파일 생성

- [ ] **Step 3: 서브모듈 동작 확인**

```bash
ls ~/repository/shadow/docs/dlc/projects/shadow/
```

Expected: `STATUS.md  01-inception/  02-construction/  03-operations/` 출력

- [ ] **Step 4: Shadow 레포 커밋**

```bash
cd ~/repository/shadow
git add .gitmodules docs/dlc
git commit -m "feat: add waypoint framework as submodule"
```

- [ ] **Step 5: 최종 확인**

```bash
cd ~/repository/shadow/docs/dlc
cat projects/shadow/STATUS.md
```

Expected: Shadow STATUS.md 내용 출력, 모든 스테이지가 `PENDING` 상태

---

## Self-Review

**Spec coverage:**
- [x] 섹션 2 (레포 구조) → Task 1, 2, 3
- [x] 섹션 3 (문서 구조) → Task 4 templates
- [x] 섹션 4 (게이트 메커니즘) → FRAMEWORK.md (Task 3) + 각 스테이지 문서의 게이트 섹션
- [x] 섹션 5 (Shadow 전용 체크리스트) → Task 6, 7, 8
- [x] 서브모듈 연결 → Task 9

**Placeholder 스캔:** 없음. 모든 스테이지 문서에 실제 Shadow 도메인 체크리스트 포함됨.

**Type consistency:** 문서 파일 이름이 STATUS.md 테이블 링크와 일치함.
