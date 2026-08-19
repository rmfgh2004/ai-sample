# ai-dlc-sample

TODO: 프로젝트 한 줄 소개. (`aidlc-inputs/00-business-brief.md` 확정 후 채운다)

---

## AI-DLC 준수 증거

이 저장소는 AI-DLC(AI-Driven Development Life Cycle)를 따라 개발한다.
사용하는 도구(Claude Code / ChatGPT Codex / Kiro)와 무관하게 **하나의 규칙 엔진**을 공유하며,
각 단계의 산출물과 승인 기록이 아래 경로에 그대로 남는다.

### 스테이지 → 산출물 매핑

| Phase | Stage | 산출물 | 경로 |
|---|---|---|---|
| — | 진행 상태 | 단계별 진행/승인 상태 | `aidlc-docs/aidlc-state.md` |
| — | 감사 기록 | 모든 상호작용의 원문 기록 | `aidlc-docs/audit.md` |
| INCEPTION | Workspace Detection | 워크스페이스 판정(그린/브라운필드) | `aidlc-docs/audit.md` |
| INCEPTION | Reverse Engineering | 기존 코드 분석(브라운필드 한정) | `aidlc-docs/inception/reverse-engineering/` |
| INCEPTION | Requirements Analysis | 요구사항, 확인 질문 | `aidlc-docs/inception/requirements/requirements.md`<br>`aidlc-docs/inception/requirements/requirement-verification-questions.md` |
| INCEPTION | User Stories | 페르소나, 사용자 스토리 | `aidlc-docs/inception/user-stories/` |
| INCEPTION | Workflow Planning | 실행 계획 | `aidlc-docs/inception/plans/execution-plan.md` |
| INCEPTION | Application Design | 컴포넌트·서비스·의존성 설계 | `aidlc-docs/inception/application-design/` |
| INCEPTION | Units Generation | 작업 단위(unit of work) 분할 | `aidlc-docs/inception/application-design/unit-of-work.md` |
| CONSTRUCTION | Functional Design | 도메인 엔터티, 비즈니스 규칙·로직 모델 | `aidlc-docs/construction/{unit}/functional-design/` |
| CONSTRUCTION | NFR Requirements | 비기능 요구사항, 기술 스택 결정 | `aidlc-docs/construction/{unit}/nfr-requirements/` |
| CONSTRUCTION | NFR Design | 논리 컴포넌트, NFR 설계 패턴 | `aidlc-docs/construction/{unit}/nfr-design/` |
| CONSTRUCTION | Infrastructure Design | 인프라·배포 아키텍처 | `aidlc-docs/construction/{unit}/infrastructure-design/` |
| CONSTRUCTION | Code Generation | 구현 코드와 생성 계획 | `aidlc-docs/construction/plans/{unit}-code-generation-plan.md` |
| CONSTRUCTION | Build and Test | 빌드·테스트 지침과 결과 요약 | `aidlc-docs/construction/build-and-test/` |
| OPERATIONS | Operations | 운영 산출물 | `aidlc-docs/operations/` |

### 추적성 — 안정 ID

모든 요구사항은 안정 ID(`FR-XXX`)를 가지며, 그 ID는
**requirements → design → tasks → code(docstring) → test(함수명)** 까지 관통한다.
따라서 임의의 `FR-XXX` 를 grep하면 요구사항부터 그것을 검증하는 테스트까지 한 줄로 이어진다.
상세 규칙은 [aidlc-inputs/02-development-policy.md](aidlc-inputs/02-development-policy.md).

### 사람 승인(Human-in-the-Loop)

- 각 phase는 **사람의 명시적 승인 이후에만** 다음으로 진행한다.
- 승인은 결론만이 아니라 **판단 근거**와 함께 `aidlc-docs/audit.md` 에 기록한다.
- 각 기록은 5개 필드를 포함한다:
  `[타임스탬프]` / `[사용자 지시 원문]` / `[AI 제안 요약]` / `[사용자 판단·승인 근거]` / `[최종 결정]`
- 사용자 입력은 요약·바꿔쓰기 없이 **원문 그대로** 남긴다.

---

## 저장소 구조

```
.aidlc-rule-details/        AI-DLC 규칙 엔진 (공통 단일 원천)
  ├── core-workflow.md        워크플로 전문 (진입점 원본)
  ├── common/ inception/ construction/ operations/ extensions/
  └── VERSION                 규칙 엔진 버전
.kiro/
  ├── steering/aidlc.md       Kiro 진입점
  └── aws-aidlc-rule-details/ 위 엔진과 동일한 규칙 사본
CLAUDE.md                   Claude Code 진입점
AGENTS.md                   Codex / Cursor / Copilot 진입점
aidlc-inputs/               사람이 관리하는 입력 원천 (읽기 전용으로 취급)
aidlc-docs/                 AI-DLC가 생성하는 산출물 + 감사 기록
scripts/verify-aidlc-rules.sh  세 도구가 같은 엔진을 보는지 검증
```

## 규칙 로딩 순서

작업 시작 시 아래 경로를 순서대로 확인해 **처음 존재하는 것**을 규칙 디렉터리로 사용한다.

1. `.aidlc-rule-details/` — Claude Code / Codex / Cursor / Copilot
2. `.kiro/aws-aidlc-rule-details/` — Kiro

세 진입점([CLAUDE.md](CLAUDE.md) / [AGENTS.md](AGENTS.md) / [.kiro/steering/aidlc.md](.kiro/steering/aidlc.md))의
규칙 내용은 동일하며, 모두 같은 엔진을 가리킨다. 다음 명령으로 언제든 검증할 수 있다.

```bash
./scripts/verify-aidlc-rules.sh
```

## 시작하기

1. `aidlc-inputs/` 의 `TODO` 를 모두 채운다. (입력 변경은 PR 리뷰를 거친다)
2. 사용하는 AI 도구로 개발을 요청한다. 도구는 진입점 → 규칙 엔진 순으로 읽고 Inception부터 시작한다.
3. 각 단계에서 제시되는 계획을 검토하고, **판단 근거와 함께** 승인한다.
4. `aidlc-docs/aidlc-state.md` 로 진행 상태를 확인한다. 세션이 끊겨도 이어서 재개된다.

> AI-DLC 실행 중에는 `aidlc-inputs/` 를 변경하지 않는다.
> 입력에 문제가 발견되면 진행을 멈추고, 감사 기록을 남긴 뒤 PR로 입력을 고치고 재실행한다.
