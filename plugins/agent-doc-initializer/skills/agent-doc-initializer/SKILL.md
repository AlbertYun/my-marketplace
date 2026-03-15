---
name: agent-doc-initializer
description: >
  Cursor AI 또는 AI 에이전트 기반 풀스택 프로젝트에서 `.docs/AGENT.md`가 없을 때,
  프로젝트 구조를 자동으로 분석하여 AGENT.md, INDEX.md, 도메인별 README.md를 생성하는 스킬.
  다음 상황에서 반드시 이 스킬을 사용하라:
  - "agent.md 만들어줘", "docs 구조 잡아줘", "커서 지침 만들어줘" 라고 할 때
  - 프로젝트에 `.docs/AGENT.md` 또는 `.docs/INDEX.md`가 없다고 언급될 때
  - 새 프로젝트를 시작하면서 AI 개발 지침 문서가 필요할 때
  - 기존 project_plan.md는 있지만 트리형 문서 구조가 없을 때
  - "문서 자동화", "LLM 문서 구조", "커서 룰" 등을 언급할 때
---

# Agent Doc Initializer

프로젝트에 `.docs/AGENT.md`가 없을 때, 프로젝트를 분석하고
Cursor AI / AI 에이전트가 효율적으로 읽을 수 있는 트리형 문서 구조를 자동 생성한다.

---

## 0. 사전 확인 (항상 먼저 실행)

작업 시작 전 아래 순서로 반드시 확인한다.

```
1. .docs/AGENT.md 존재 여부 확인
2. .docs/INDEX.md 존재 여부 확인
3. 프로젝트 루트 구조 파악 (디렉토리 트리)
4. 기존 project_plan.md 또는 README.md 존재 시 내용 파악
```

**판단 기준:**
- `.docs/AGENT.md` 없음 → 전체 초기화 프로세스 실행 (Step 1~5)
- `.docs/AGENT.md` 있고 `.docs/INDEX.md` 없음 → Step 3~5만 실행
- 둘 다 있음 → 사용자에게 "이미 존재합니다. 갱신할까요?" 확인 후 진행

---

## 1. 프로젝트 구조 분석

아래 항목을 순서대로 파악한다. 파악된 내용은 이후 템플릿 생성에 사용한다.

### 1-1. 기술 스택 감지
```
확인 파일: package.json, requirements.txt, go.mod, pom.xml, Cargo.toml 등
감지 항목:
  - Frontend 프레임워크 (Next.js / React / Vue / Nuxt 등)
  - Backend 프레임워크 (Express / FastAPI / Spring 등)
  - DB (PostgreSQL / MySQL / MongoDB / MSSQL 등)
  - ORM/쿼리빌더 (Prisma / TypeORM / MyBatis 등)
  - 인프라 (Docker / Nginx / PM2 / Vercel 등)
  - 외부 API (키움 / Slack / Gmail / Stripe 등)
```

### 1-2. 폴더 구조 감지
```
확인 대상:
  - /src 또는 /app 내 주요 폴더
  - /api 또는 /routes 경로
  - /lib, /utils, /helpers (공통 함수 위치)
  - /components (클라이언트 컴포넌트)
  - /sql, /.docs, /scripts (문서/DB 관련)
  - nginx.conf 또는 /nginx 폴더
  - .env.example (환경변수 목록)
```

### 1-3. 기존 문서 파악
```
우선순위:
  1. .docs/project_plan.md → 기능 목록, DB 구조, API 목록 추출
  2. README.md → 프로젝트 개요, 실행방법
  3. .docs/*.md → 기존 문서 목록
  4. .docs/*.sql → DB 스키마 파일 목록
```

---

## 2. 생성할 파일 목록 결정

분석 결과를 바탕으로 아래 기준에 따라 생성할 파일을 결정한다.

```
항상 생성:
  .docs/AGENT.md          ← AI 에이전트 행동 지침
  .docs/INDEX.md          ← 문서 탐색 진입점

DB 관련 파일이 있을 때:
  .docs/db/README.md      ← 테이블 목록 및 파일 맵

API 라우트가 있을 때:
  .docs/api/README.md     ← 엔드포인트 목록

/lib, /utils 폴더가 있을 때:
  .docs/common/README.md  ← 공통 함수 인덱스

nginx.conf가 있을 때:
  .docs/server/README.md  ← 서버/인프라 설정

클라이언트 페이지가 있을 때:
  .docs/client/README.md  ← 페이지 라우팅 구조

외부 API 연동이 있을 때:
  .docs/external/README.md ← 외부 API 목록
```

사용자에게 생성 목록을 보여주고 확인을 받은 뒤 생성한다.

---

## 3. AGENT.md 생성

`references/AGENT.template.md`를 기반으로 분석된 프로젝트 정보를 채워 생성한다.

> 참조: `references/AGENT.template.md`

채워야 할 항목:
- `{{PROJECT_NAME}}` → package.json의 name 또는 루트 폴더명
- `{{TECH_STACK}}` → Step 1-1에서 감지한 스택
- `{{DB_TYPE}}` → 감지된 DB 종류
- `{{DOMAIN_LIST}}` → Step 2에서 결정된 도메인 목록
- `{{DATE}}` → 오늘 날짜 (YYYY-MM-DD)

---

## 4. INDEX.md 생성

`references/INDEX.template.md`를 기반으로 실제 존재하는 도메인만 포함하여 생성한다.

> 참조: `references/INDEX.template.md`

**규칙:**
- 실제로 생성된 폴더/파일만 인덱스에 포함한다
- 존재하지 않는 섹션은 인덱스에서 제외한다
- 기술 스택 요약을 상단에 명시한다

---

## 5. 도메인별 README.md 생성

Step 2에서 결정된 각 도메인 폴더의 README.md를 생성한다.
각 README.md는 `references/domain-readme.template.md`를 참조한다.

> 참조: `references/domain-readme.template.md`

**도메인별 채워야 할 내용:**

| 도메인 | README 핵심 내용 |
|--------|----------------|
| db | 테이블 목록, 스키마 파일 맵, 테이블 관계도 |
| api | 엔드포인트 목록, 공통 미들웨어, 인증 방식 |
| common | 함수/클래스 목록, 위치, 시그니처 |
| server | nginx 설정 파일 위치, 환경변수 목록, Cron 스케줄 |
| client | 페이지 라우팅 목록, 공통 컴포넌트 위치 |
| external | 외부 API 목록, 인증 방식, 관련 문서 파일 |

기존 `project_plan.md`에서 관련 내용을 추출하여 채운다.

---

## 6. 작업 완료 보고

모든 파일 생성 후 아래 형식으로 보고한다.

```
✅ 생성 완료:
  .docs/AGENT.md
  .docs/INDEX.md
  .docs/db/README.md
  .docs/api/README.md
  ... (생성된 파일 목록)

📌 다음 작업 권장:
  1. .docs/db/README.md에서 테이블 목록이 누락된 항목 확인
  2. .docs/api/README.md에서 엔드포인트 권한 정보 보완
  3. .docs/common/README.md에 주요 유틸 함수 추가
  
⚠️ 주의: 자동 분석으로 생성된 문서입니다.
   실제 코드와 대조하여 누락/오류 항목을 보완해주세요.
```

---

## 문서 동기화 규칙 (생성 이후 지속 적용)

생성 이후 코드 변경이 발생할 때마다 아래 규칙을 적용한다.
이 규칙은 AGENT.md에도 명시되어 있으며, 모든 작업 완료 시 체크해야 한다.

> 상세 규칙: `references/sync-rules.md` 참조

**요약 매핑:**
```
DB 테이블 추가/수정    → .docs/db/README.md + 해당 .sql 파일
API 엔드포인트 추가    → .docs/api/README.md + .docs/api/[도메인].md
공통 함수 추가/수정    → .docs/common/README.md
nginx 설정 변경        → .docs/server/nginx.md
페이지 추가            → .docs/client/pages.md
외부 API 연동 추가     → .docs/external/[서비스명].md
기능 추가/수정 전반    → .docs/project_plan.md
```
