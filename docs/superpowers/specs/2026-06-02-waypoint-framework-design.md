# Waypoint Framework — Design Spec
**Date:** 2026-06-02  
**Status:** APPROVED  
**Project:** Shadow Engineer (first instantiation)

---

## 1. 개요

Waypoint는 AI 제안 → 사람 승인 게이트 기반의 Markdown 문서 중심 개발 라이프사이클 프레임워크다.  
AWS aidlc-workflows에서 영감을 받았으나, 단일 레포에 종속되지 않는 독립 프레임워크로 설계된다.

**핵심 원칙:**
- 문서가 단일 진실 공급원(Single Source of Truth)
- AI는 제안만 하고, 사람이 승인해야 다음 단계로 진행
- 범용 골격(waypoint 레포) + 프로젝트 전용 인스턴스(projects/{name}/)

---

## 2. 레포지토리 구조

### Waypoint 레포 (`github.com/bingoring/waypoint`)

```
waypoint/
├── README.md                       ← 프레임워크 소개 및 사용법
├── FRAMEWORK.md                    ← AI가 반드시 읽는 규칙·컨벤션 계약서
├── STATUS.template.md              ← 프로젝트별 대시보드 템플릿
├── _templates/
│   ├── stage-template.md           ← 범용 스테이지 문서 골격
│   └── gate-template.md            ← 게이트 체크리스트 골격
└── projects/
    └── shadow/                     ← Shadow Engineer 전용 스테이지 문서
        ├── STATUS.md
        ├── 01-inception/
        │   ├── 01-context-synthesis.md
        │   ├── 02-domain-model.md
        │   └── 03-architecture-decision.md   ← 비가역 게이트
        ├── 02-construction/
        │   ├── 01-tauri-client.md
        │   ├── 02-nestjs-backend.md
        │   ├── 03-langgraph-agent.md
        │   └── 04-integration.md
        └── 03-operations/
            ├── 01-deployment.md
            └── 02-monitoring.md
```

### Shadow 레포 (`github.com/bingoring/shadow`)

```
shadow/
├── prd.md
├── prd-tech.md
└── docs/
    └── dlc/ → (git submodule: github.com/bingoring/waypoint)
```

Shadow의 모든 스테이지 문서는 waypoint 레포의 `projects/shadow/` 안에 위치하며,  
Shadow 레포에서 `docs/dlc/`를 열면 submodule로 전체 Waypoint 레포를 마운트한다.

---

## 3. 스테이지 문서 구조

모든 스테이지 문서는 아래 골격을 따른다.

```markdown
---
phase: 01-inception
stage: 01-context-synthesis
status: PENDING | IN_PROGRESS | AI_PROPOSED | HUMAN_APPROVED
updated: YYYY-MM-DD
---

# [Stage X-Y] 스테이지 이름

## 목적
이 스테이지에서 AI가 해야 할 일과 산출물 정의.

## 입력 (Inputs)
- 이전 스테이지 문서 링크 또는 PRD 링크
- AI가 읽어야 할 파일 목록

## Shadow 전용 체크리스트
도메인에 특화된 확인 항목.

## AI 제안 (AI Proposal)
> ⚠️ 이 섹션은 AI가 작성합니다. 사람이 직접 수정하지 마세요.

[AI가 분석 결과와 설계안을 작성]

## 검토 게이트 (Human Gate)
> 아래 항목 확인 후 status를 HUMAN_APPROVED로 변경하세요.

- [ ] AI 제안이 PRD 요구사항과 일치하는가?
- [ ] 다음 스테이지 진행에 필요한 결정이 모두 내려졌는가?

## 다음 단계
승인 후 → 다음 스테이지 문서로 이동
```

---

## 4. 게이트 메커니즘

### 상태 생애주기

```
PENDING → IN_PROGRESS → AI_PROPOSED → HUMAN_APPROVED
```

| 상태 | 주체 | 행동 |
|------|------|------|
| `PENDING` | — | 시작 전. 앞 스테이지 APPROVED 전까지 진입 불가 |
| `IN_PROGRESS` | AI | STATUS.md 확인 후 문서 작성 시작 |
| `AI_PROPOSED` | AI | AI Proposal 섹션 완성, 사람에게 리뷰 요청 |
| `HUMAN_APPROVED` | 사람 | 게이트 체크리스트 확인 후 status 직접 변경 |

### AI 세션 시작 규칙 (FRAMEWORK.md에 명시)

1. `projects/shadow/STATUS.md`를 읽는다
2. `PENDING` 상태인 가장 앞 스테이지를 찾는다
3. 해당 스테이지 문서를 열어 작업한다
4. `AI_PROPOSED`로 상태 변경 후 사람에게 리뷰 요청
5. `HUMAN_APPROVED` 없이는 다음 스테이지로 절대 진행하지 않는다

### 비가역 게이트 (`03-architecture-decision.md`)

일반 게이트에 아래 3개 항목 추가:
- `[ ] 이 결정을 번복할 경우 영향받는 파일/레이어 목록을 작성했는가`
- `[ ] 대안 기술 스택을 검토하고 탈락 이유를 문서화했는가`
- `[ ] 외부 API 의존성 및 비용 구조를 확인했는가`

---

## 5. Shadow 전용 스테이지 정의

### Phase 1 — Inception (What)

| 스테이지 | AI 분석 대상 | 게이트 산출물 |
|---------|------------|-------------|
| `01-context-synthesis` | prd.md·prd-tech.md 분석, 4각 툴 데이터 가중치 정의, 3-Layer 병합 전략 명세 | 컨텍스트 소스별 역할 정의서 |
| `02-domain-model` | 엔티티(User, Ticket, Context, ArchPlan) 정의, 데이터 흐름도, Conflict Resolution 로직 | 도메인 모델 다이어그램 + DTO 초안 |
| `03-architecture-decision` ⚠️ | Tauri vs Electron, LangGraph.js vs Python, pgvector vs Qdrant, 모노레포 구조 확정 | ADR(Architecture Decision Record) |

### Phase 2 — Construction (How)

| 스테이지 | AI 분석 대상 | 게이트 산출물 |
|---------|------------|-------------|
| `01-tauri-client` | Tauri v2 IPC 구조, Tree-sitter AST 파싱 플로우, VS Code API 연동 인터페이스 | Tauri 컴포넌트 설계서 |
| `02-nestjs-backend` | 모듈 구조(Webhook/Queue/Auth), BullMQ 워커 설계, Prisma 스키마 초안 | NestJS 아키텍처 맵 |
| `03-langgraph-agent` | 그래프 노드 정의(Fetch→Merge→Resolve→Generate), 상태 스키마, LLM 프롬프트 전략 | LangGraph 플로우 다이어그램 |
| `04-integration` | NestJS↔LangGraph 통신 프로토콜, Tauri↔Backend WebSocket 설계, E2E 시나리오 | 통합 시퀀스 다이어그램 |

### Phase 3 — Operations (Ship)

| 스테이지 | AI 분석 대상 | 게이트 산출물 |
|---------|------------|-------------|
| `01-deployment` | Docker Compose 구성, 환경변수 관리, Tauri 앱 배포 파이프라인 | 배포 런북 |
| `02-monitoring` | BullMQ 큐 모니터링, 웹훅 실패율 알림, LLM 토큰 비용 추적 | 모니터링 체크리스트 |

---

## 6. 다른 프로젝트에서 Waypoint 사용하기

```bash
# 새 프로젝트에서
git submodule add https://github.com/bingoring/waypoint docs/dlc

# Waypoint 레포에 프로젝트 디렉토리 생성
mkdir -p waypoint/projects/myapp
cp waypoint/_templates/stage-template.md waypoint/projects/myapp/STATUS.md
# 이후 프로젝트 전용 스테이지 문서 작성
```

---

## 7. 미결 사항

없음. 모든 설계 결정이 완료되었다.
