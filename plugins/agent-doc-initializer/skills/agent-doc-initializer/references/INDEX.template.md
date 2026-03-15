# 📌 프로젝트 문서 인덱스
# 프로젝트: {{PROJECT_NAME}}
# 최종수정: {{DATE}}

> ⚠️ AI 에이전트는 **이 파일을 가장 먼저 읽고** 필요한 문서만 이동한다.
> 존재하지 않는 섹션 링크는 클릭/참조하지 않는다.

---

## 🗺️ 작업 유형별 빠른 탐색

| 작업 | 읽을 파일 |
|------|---------|
| DB 테이블 확인/수정 | → [`.docs/db/README.md`](.docs/db/README.md) |
| API 엔드포인트 확인/수정 | → [`.docs/api/README.md`](.docs/api/README.md) |
| 공통 함수 확인 (구현 전 필수) | → [`.docs/common/README.md`](.docs/common/README.md) |
| 서버/nginx/배포 설정 | → [`.docs/server/README.md`](.docs/server/README.md) |
| 클라이언트 페이지/컴포넌트 | → [`.docs/client/README.md`](.docs/client/README.md) |
| 외부 API 연동 스펙 | → [`.docs/external/README.md`](.docs/external/README.md) |
| 소켓/실시간 이벤트 | → [`.docs/socket/README.md`](.docs/socket/README.md) |
| 전체 기능 목록 | → [`.docs/project_plan.md`](.docs/project_plan.md) |
| AI 에이전트 행동 지침 | → [`.docs/AGENT.md`](.docs/AGENT.md) |

---

## 🏗️ 기술 스택

{{TECH_STACK_TABLE}}

---

## 📁 도메인별 문서 구조

```
.docs/
├── AGENT.md                  ← AI 에이전트 행동 지침 (모든 작업의 규칙)
├── INDEX.md                  ← 지금 이 파일 (항상 먼저 읽기)
├── project_plan.md           ← 전체 기능 명세 및 개발 계획
│
├── db/
│   ├── README.md             ← 테이블 목록 + 스키마 파일 맵 + 관계도
│   └── *.sql                 ← 실제 스키마 파일들
│
├── api/
│   ├── README.md             ← 엔드포인트 전체 목록 + 공통 미들웨어
│   └── [도메인].md           ← 도메인별 상세 스펙
│
├── common/
│   ├── README.md             ← 공통 함수/클래스 인덱스 (구현 전 필수 확인)
│   └── [모듈명].md           ← 모듈별 상세 설명
│
├── server/
│   ├── README.md             ← 서버 구성 개요
│   ├── nginx.md              ← nginx 설정 상세
│   ├── env.md                ← 환경변수 목록
│   └── cron.md               ← Cron 스케줄 목록
│
├── client/
│   ├── README.md             ← 라우팅 구조 개요
│   ├── pages.md              ← 페이지별 경로 및 설명
│   └── components.md         ← 공통 컴포넌트 목록
│
├── socket/
│   ├── README.md             ← 소켓 연결 방식
│   └── events.md             ← 이벤트 목록 및 페이로드
│
└── external/
    ├── README.md             ← 외부 API 목록
    └── [서비스명].md         ← 서비스별 상세 스펙
```

---

## 🔄 문서 동기화 원칙

코드를 변경하면 **반드시 해당 도메인 문서를 함께 업데이트**한다.

```
DB 변경      → .docs/db/README.md + 해당 .sql
API 변경     → .docs/api/README.md + .docs/api/[도메인].md
함수 변경    → .docs/common/README.md
nginx 변경   → .docs/server/nginx.md
페이지 추가  → .docs/client/pages.md
외부 API     → .docs/external/[서비스명].md
전체 기능    → .docs/project_plan.md
```

> 상세 동기화 규칙은 `.docs/AGENT.md` 참조
