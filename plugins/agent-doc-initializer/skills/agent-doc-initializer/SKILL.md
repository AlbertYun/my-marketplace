---
name: agent-doc-initializer
description: >
  풀스택 프로젝트(DB/서버/엔진/클라이언트)를 분석하여 AI 에이전트가 "색인처럼 골라 읽을 수 있는"
  트리형 문서 구조(.docs/AGENT.md, INDEX.md, 도메인별 README.md)를 자동 생성/갱신하는 스킬.
  목적은 에이전트의 컨텍스트 엔지니어링: 전체 코드를 읽지 않고 INDEX → 필요한 도메인 문서만
  선택적으로 읽게 만들어 토큰과 시간을 절약한다.
  다음 상황에서 반드시 이 스킬을 사용하라:
  - "agent.md 만들어줘", "docs 구조 잡아줘", "커서 지침/커서 룰 만들어줘", "CLAUDE.md 만들어줘" 라고 할 때
  - "이 프로젝트 문서화해줘", "AI가 읽기 좋게 정리해줘", "코드베이스 파악용 문서", "온보딩 문서" 를 요청할 때
  - 프로젝트에 `.docs/AGENT.md` 또는 `.docs/INDEX.md`가 없다고 언급되거나, 없음을 발견했을 때
  - 새 프로젝트 시작 시 AI 개발 지침 문서가 필요할 때
  - 기존 project_plan.md는 있지만 트리형 문서 구조가 없을 때
  - "문서 자동화", "LLM 문서 구조", "문서 동기화", "문서 갱신/최신화" 등을 언급할 때
  - 기존 `.docs/`가 있어도 코드와 문서가 어긋났다(드리프트)고 의심될 때 (갱신 모드로 실행)
---

# Agent Doc Initializer

프로젝트를 분석하여 AI 에이전트(Cursor, Claude Code 등)가 효율적으로 읽을 수 있는
트리형 문서 구조를 자동 생성·갱신한다.

## 이 스킬의 의도 (판단 기준의 뿌리)

이 스킬의 사용자는 DB, 서버, 엔진(배치/워커), 클라이언트를 혼자 개발하는 1인 풀스택 개발자다.
사람 팀원을 위한 문서가 아니라 **AI 에이전트를 위한 색인**을 만드는 것이 목적이다.

애매한 상황에서는 항상 아래 원칙으로 판단하라:

1. **선택적 읽기**: 에이전트가 작업 시 INDEX.md만 읽고 → 필요한 도메인 README 1~2개만 열도록 설계한다.
   한 파일에 모든 걸 넣지 말고, 도메인별로 쪼개고 INDEX에서 가리킨다.
2. **밀도 우선**: 문서는 LLM이 소비한다. 산문·수사·중복 설명 금지. 테이블, 목록, 파일 경로,
   시그니처 중심으로 압축한다. 하나의 README는 200줄 이내를 목표로 하고,
   넘치면 하위 상세 문서(`[도메인]/[주제].md`)로 분리하고 README에서 링크한다.
3. **실제 내용**: 빈 템플릿을 채워넣는 것은 실패다. 반드시 실제 코드(라우트 파일, 스키마,
   lib exports 등)를 읽고 추출한 내용으로 채운다. 확인 못한 항목은 `<!-- TODO: 확인 필요 -->`로
   명시적으로 표시하고 완료 보고에 나열한다.
4. **진실은 코드**: 문서와 코드가 충돌하면 코드가 맞다. 갱신 모드에서는 코드 기준으로 문서를 고친다.

---

## 0. 사전 확인 → 실행 모드 결정 (항상 먼저 실행)

```
1. .docs/AGENT.md 존재 여부 확인
2. .docs/INDEX.md 존재 여부 확인
3. 루트의 CLAUDE.md / AGENTS.md / .cursor/rules/ / .cursorrules 존재 여부 확인
4. 기존 .docs/project_plan.md 또는 README.md 존재 시 내용 파악
```

**모드 결정:**

| 상태 | 모드 | 실행 범위 |
|------|------|---------|
| `.docs/AGENT.md` 없음 | **초기화 모드** | Step 1~7 전체 |
| AGENT.md 있고 INDEX.md 없음 | **부분 생성** | Step 1 분석 후 Step 3~7 |
| 둘 다 있음 | **갱신 모드** | Step 8 (드리프트 감지 → 선택 갱신) |

둘 다 있는데 사용자가 명시적으로 "다시 만들어줘"라고 하면 초기화 모드로 진행하되,
기존 문서의 수기 작성 내용(변경이력, 주의사항 등)은 보존·병합한다.

---

## 1. 프로젝트 구조 분석

추측하지 말고 아래 명령을 실제 실행하여 파악한다. (Windows 환경이면 동등한 명령으로 대체)

### 1-1. 전체 구조 스냅샷
```bash
# 디렉토리 트리 (3단계, 노이즈 제외)
find . -maxdepth 3 -type d | grep -vE 'node_modules|\.git|\.next|dist|build|__pycache__|\.venv'

# 파일 수가 많은 폴더 파악 (코드 무게중심)
find ./src ./app ./lib 2>/dev/null -name '*.ts' -o -name '*.tsx' -o -name '*.js' -o -name '*.py' | \
  sed 's|/[^/]*$||' | sort | uniq -c | sort -rn | head -20
```

### 1-2. 기술 스택 감지
```
확인 파일: package.json, requirements.txt, go.mod, pom.xml, Cargo.toml, pyproject.toml
감지 항목:
  - Frontend (Next.js / React / Vue / Remix / Expo 등)
  - Backend (Express / FastAPI / Spring / Next API Routes 등)
  - DB (PostgreSQL / MySQL / MongoDB / MSSQL / pgvector 등)
  - ORM/쿼리 (Prisma / TypeORM / MyBatis / raw SQL 등)
  - 인프라 (Docker / nginx / PM2 / Vercel / cron 등)
  - 외부 API (dependencies + grep으로 확인: anthropic, openai, kiwoom, stripe, telegram 등)
```
```bash
# 외부 API 연동 흔적 탐색
grep -rEl 'api\.anthropic|openai|kiwoom|api\.telegram|stripe|slack' src lib app 2>/dev/null | head
```

### 1-3. 도메인별 실체 확인
```bash
# API 라우트 (Next.js App Router / Express 등 프로젝트에 맞게)
find . -path ./node_modules -prune -o -name 'route.ts' -print 2>/dev/null
grep -rn "router\.\(get\|post\|put\|patch\|delete\)" src 2>/dev/null | head -30

# DB 스키마
find . -name '*.sql' -not -path '*/node_modules/*' | head -20
ls prisma/schema.prisma migrations 2>/dev/null

# 공통 함수 (lib/utils의 export 목록)
grep -rn "^export " src/lib src/utils lib utils 2>/dev/null | head -40

# 엔진/배치/워커 (스케줄러, 큐, 데몬성 프로세스)
grep -rln 'cron|node-cron|BullMQ|worker|schedule|setInterval' src scripts 2>/dev/null | head
ls scripts/ batch/ worker/ engine/ 2>/dev/null

# 소켓/실시간
grep -rln 'WebSocket|socket\.io|wss://' src 2>/dev/null | head

# 인프라
ls nginx.conf nginx/ ecosystem.config.js Dockerfile docker-compose.yml .env.example 2>/dev/null
```

### 1-4. 기존 문서 파악
```
우선순위:
  1. .docs/project_plan.md → 기능 목록, DB 구조, API 목록 추출
  2. README.md → 프로젝트 개요, 실행방법
  3. .docs/*.md, .docs/*.sql → 기존 문서/스키마 목록
```

---

## 2. 생성할 파일 목록 결정

분석 **결과에 실체가 있는 도메인만** 생성한다. 없는 도메인의 빈 문서를 만들지 않는다.

```
항상 생성:
  .docs/AGENT.md          ← AI 에이전트 행동 지침
  .docs/INDEX.md          ← 문서 탐색 진입점

조건부 생성 (해당 실체가 감지됐을 때만):
  .docs/db/README.md       ← .sql / prisma / migrations 존재 시
  .docs/api/README.md      ← API 라우트 존재 시
  .docs/common/README.md   ← /lib, /utils 존재 시
  .docs/engine/README.md   ← 배치/워커/스케줄러/파이프라인 코드 존재 시
  .docs/server/README.md   ← nginx/PM2/Docker/.env/cron 존재 시
  .docs/client/README.md   ← 클라이언트 페이지 존재 시
  .docs/socket/README.md   ← WebSocket/실시간 코드 존재 시
  .docs/external/README.md ← 외부 API 연동 존재 시
```

사용자에게 **감지 근거와 함께** 생성 목록을 보여주고 확인받은 뒤 생성한다.
(예: "engine: scripts/daily-batch.ts에서 node-cron 감지 → .docs/engine/README.md 생성 예정")

---

## 3. AGENT.md 생성

`references/AGENT.template.md`를 기반으로 분석된 프로젝트 정보를 채워 생성한다.

채워야 할 항목:
- `{{PROJECT_NAME}}` → package.json의 name 또는 루트 폴더명
- `{{TECH_STACK}}` → Step 1-2에서 감지한 스택
- `{{DB_TYPE}}` → 감지된 DB 종류
- `{{DOMAIN_LIST}}` → Step 2에서 결정된 도메인 목록
- `{{DATE}}` → 오늘 날짜 (YYYY-MM-DD)

템플릿의 "작업 전 문서 탐색 규칙" 표에서 **존재하지 않는 도메인 행은 삭제**한다.

---

## 4. INDEX.md 생성

`references/INDEX.template.md`를 기반으로 실제 존재하는 도메인만 포함하여 생성한다.

**규칙:**
- 실제로 생성된 폴더/파일만 인덱스에 포함한다 (깨진 링크 = 에이전트 오작동)
- 기술 스택 요약을 상단에 명시한다
- 각 링크에 "언제 읽어야 하는지" 한 줄 트리거를 붙인다 (에이전트의 라우팅 정확도가 올라간다)

---

## 5. 도메인별 README.md 생성

각 README는 `references/domain-readme.template.md`의 해당 도메인 섹션을 참조하되,
**반드시 Step 1에서 실제 코드를 읽고 추출한 내용으로 채운다.**

| 도메인 | 핵심 내용 | 채우기 위해 읽을 실제 소스 |
|--------|---------|------------------------|
| db | 테이블 목록, 스키마 파일 맵, 관계도 | *.sql, prisma/schema.prisma, migrations |
| api | 엔드포인트 목록, 미들웨어, 인증 방식 | route.ts / router 정의 파일 (각 파일 상단 30줄) |
| common | 함수/클래스 목록, 위치, 시그니처 | lib/utils의 export 문 |
| engine | 배치/워커 목록, 트리거(cron/큐), 입출력, 실행 방법 | scripts/, worker/, cron 설정 |
| server | nginx/PM2/Docker 설정 위치, 환경변수, cron | nginx.conf, ecosystem.config.js, .env.example |
| client | 페이지 라우팅, 권한, 공통 컴포넌트 | app/ 또는 pages/ 디렉토리 구조 |
| socket | 연결 방식, 이벤트 목록, 페이로드 | 소켓 핸들러 파일 |
| external | 외부 API 목록, 인증 방식, 호출 위치 | 외부 API 호출 코드, .env.example의 키 목록 |

**품질 기준:**
- 항목 하나당 "이름 / 위치(파일 경로) / 한 줄 용도"는 필수. 경로가 없는 항목은 색인 가치가 없다.
- 코드에서 확인 못한 값은 `<!-- TODO: 확인 필요 -->`로 표시 (추측으로 채우지 않는다)
- 기존 `project_plan.md`에 관련 내용이 있으면 추출·병합한다

---

## 6. 에이전트 하네스 연결 (필수 — 이 단계 없이는 문서가 읽히지 않는다)

`.docs/AGENT.md`를 만들어도 에이전트가 자동으로 읽지 않는다.
에이전트 도구의 **진입 파일에 포인터를 심어야** 색인 구조가 실제로 작동한다.

```
루트에 CLAUDE.md 있음        → 상단에 포인터 블록 추가 (기존 내용 보존)
루트에 AGENTS.md 있음        → 동일하게 포인터 블록 추가
.cursor/rules/ 또는 .cursorrules 있음 → 포인터 룰 추가
아무것도 없음                → 사용자가 쓰는 도구를 확인하고 해당 진입 파일 신규 생성
                              (모르면 CLAUDE.md + .cursor/rules/docs.mdc 둘 다 생성)
```

**포인터 블록 (진입 파일에 삽입할 내용):**
```markdown
## 문서 시스템 (필수)
- 모든 작업 시작 전 `.docs/INDEX.md`를 먼저 읽고, 안내된 도메인 문서만 선택적으로 읽는다.
- 행동 지침 상세: `.docs/AGENT.md`
- 코드 변경 후 `.docs/AGENT.md`의 동기화 규칙에 따라 해당 도메인 문서를 반드시 갱신한다.
```

---

## 7. 검증 및 완료 보고

생성 후 반드시 검증한다:

```
□ INDEX.md의 모든 링크가 실제 존재하는 파일을 가리키는가?
□ AGENT.md의 탐색 표에 존재하지 않는 도메인이 남아있지 않은가?
□ 각 도메인 README에 실제 파일 경로가 최소 1개 이상 들어있는가? (빈 껍데기 검출)
□ 진입 파일(CLAUDE.md 등)에 포인터가 들어갔는가?
```

보고 형식:

```
✅ 생성 완료:
  .docs/AGENT.md
  .docs/INDEX.md
  .docs/db/README.md (테이블 12개 색인)
  .docs/api/README.md (엔드포인트 23개 색인)
  ... (생성 파일 + 색인된 항목 수)
  CLAUDE.md ← 포인터 삽입

⚠️ TODO로 표시한 항목 (사람 확인 필요):
  - .docs/db/README.md: users ↔ orders 관계 방향
  - .docs/server/README.md: 운영 서버 cron 스케줄
  ...

📌 자동 분석 기반이므로 실제 코드와 대조하여 보완을 권장합니다.
```

---

## 8. 갱신 모드 (AGENT.md + INDEX.md가 이미 존재할 때)

전체 재생성하지 말고 **드리프트 감지 → 선택 갱신**으로 진행한다.

```
1. Step 1의 분석 명령을 다시 실행하여 현재 코드 실체를 파악한다
2. 각 도메인 README와 대조하여 드리프트 목록을 만든다:
   - 코드에 있는데 문서에 없음 (누락)      → 문서에 추가
   - 문서에 있는데 코드에 없음 (유령 항목)  → deprecated 표시 또는 제거
   - 시그니처/경로/스키마 불일치            → 코드 기준으로 수정
3. 드리프트 목록을 사용자에게 보여주고 확인 후 갱신한다
4. 사람이 수기로 쓴 주의사항·변경이력은 절대 삭제하지 않는다
5. 새 도메인이 생겼으면 (예: socket 코드 신규 등장) 해당 README를 신규 생성하고 INDEX에 추가한다
```

---

## 문서 동기화 규칙 (생성 이후 지속 적용)

생성 이후 코드 변경이 발생할 때마다 동기화 규칙을 적용한다.
이 규칙은 AGENT.md에도 명시되어 있으며, 모든 작업 완료 시 체크해야 한다.

> 상세 규칙: `references/sync-rules.md` 참조

**요약 매핑:**
```
DB 테이블 추가/수정    → .docs/db/README.md + 해당 .sql 파일
API 엔드포인트 추가    → .docs/api/README.md + .docs/api/[도메인].md
공통 함수 추가/수정    → .docs/common/README.md
엔진/배치 추가/수정    → .docs/engine/README.md
nginx 설정 변경        → .docs/server/nginx.md
페이지 추가            → .docs/client/pages.md
소켓 이벤트 변경       → .docs/socket/events.md
외부 API 연동 추가     → .docs/external/[서비스명].md
기능 추가/수정 전반    → .docs/project_plan.md
```
