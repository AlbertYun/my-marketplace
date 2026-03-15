# {{DOMAIN_TITLE}} 문서
# 최종수정: {{DATE}}

<!-- 
  이 파일은 agent-doc-initializer 스킬이 자동 생성한 템플릿입니다.
  실제 프로젝트 내용으로 아래 섹션을 채워주세요.
-->

---

## 개요

{{DOMAIN_DESCRIPTION}}

---

{{DOMAIN_CONTENT}}

---

## 변경 이력

| 날짜 | 변경 내용 | 변경자 |
|------|---------|--------|
| {{DATE}} | 최초 생성 (자동) | agent-doc-initializer |

---

<!-- 아래는 도메인별 섹션 가이드입니다. 불필요한 섹션은 삭제하세요 -->

<!-- ===== DB 도메인 템플릿 ===== -->
<!--
## 테이블 목록

| 테이블명 | 스키마 파일 | 용도 | 최종수정 |
|---------|-----------|------|---------|
| users | schema_auth.sql | 사용자 계정 | YYYY-MM-DD |
| stocks | schema.sql | 종목 정보 | YYYY-MM-DD |

## 테이블 상세

### [테이블명]
- 파일: schema_xxx.sql
- 용도: 한줄 설명
- 주요 컬럼: col1(타입), col2(타입) ...
- 관계: → 연결테이블명 (FK 컬럼)
- 최종수정: YYYY-MM-DD

## 관계도

```
[테이블A] ──(FK: user_id)──→ [테이블B]
[테이블B] ──(FK: stock_id)──→ [테이블C]
```

## SQL 파일 맵

| 파일명 | 포함 테이블 |
|--------|-----------|
| schema.sql | stocks, chart_daily, analysis |
| schema_auth.sql | users, login_logs |
-->

<!-- ===== API 도메인 템플릿 ===== -->
<!--
## 공통 미들웨어

| 함수명 | 위치 | 용도 |
|--------|------|------|
| requireAdmin() | src/lib/auth.ts | 관리자 권한 체크 |
| requireLogin() | src/lib/auth.ts | 로그인 체크 |

## 엔드포인트 목록

| Method | Path | 권한 | 상세 문서 |
|--------|------|------|---------|
| GET | /api/stocks | 로그인 | [stocks.md](stocks.md) |
| POST | /api/stocks/analyze | 관리자 | [stocks.md](stocks.md) |

## 도메인별 상세 문서

- [stocks.md](stocks.md) - 종목 관련 API
- [admin.md](admin.md) - 관리자 API
- [kiwoom.md](kiwoom.md) - 키움증권 API 연동
-->

<!-- ===== COMMON 도메인 템플릿 ===== -->
<!--
## 모듈 목록

| 모듈/파일 | 위치 | 주요 함수/클래스 |
|---------|------|---------------|
| kiwoom.ts | src/lib/kiwoom.ts | getKiwoomToken, callKiwoomAPI |
| auth.ts | src/lib/auth.ts | requireAdmin, requireLogin |

## 함수 상세

### [함수명]
- 위치: src/lib/xxx.ts
- 시그니처: functionName(param: Type): ReturnType
- 용도: 한줄 설명
- 주의사항: (있을 경우)
- 최종수정: YYYY-MM-DD
-->

<!-- ===== SERVER 도메인 템플릿 ===== -->
<!--
## 서버 구성

| 항목 | 값 |
|------|---|
| OS | Ubuntu 24.04 |
| Node.js | v20.x |
| PM2 프로세스 | app-name |

## 설정 파일 위치

| 파일 | 경로 | 설명 |
|------|------|------|
| nginx 메인 | /etc/nginx/nginx.conf | 메인 설정 |
| 사이트 설정 | /etc/nginx/sites-enabled/app | 도메인별 설정 |
| PM2 설정 | ecosystem.config.js | 프로세스 관리 |
| 환경변수 | .env | [env.md](env.md) 참조 |

## 상세 문서

- [nginx.md](nginx.md) - nginx 설정 상세
- [env.md](env.md) - 환경변수 목록
- [cron.md](cron.md) - Cron 스케줄 목록
-->

<!-- ===== CLIENT 도메인 템플릿 ===== -->
<!--
## 라우팅 구조

| 경로 | 파일 위치 | 권한 | 설명 |
|------|---------|------|------|
| / | app/page.tsx | 공개 | 메인 랜딩 |
| /dashboard | app/dashboard/page.tsx | 로그인 | 대시보드 |
| /admin | app/admin/page.tsx | 관리자 | 관리자 페이지 |

## 공통 컴포넌트

- [components.md](components.md) 참조

## 레이아웃

| 레이아웃 | 파일 | 적용 범위 |
|---------|------|---------|
| 루트 | app/layout.tsx | 전체 |
| 대시보드 | app/(dashboard)/layout.tsx | 로그인 사용자 |
-->

<!-- ===== EXTERNAL 도메인 템플릿 ===== -->
<!--
## 외부 API 목록

| 서비스 | 인증방식 | 상세 문서 |
|--------|---------|---------|
| 키움증권 REST | Bearer Token | [kiwoom_rest.md](kiwoom_rest.md) |
| Google Gemini | API Key | [gemini.md](gemini.md) |
| Claude API | API Key | [claude.md](claude.md) |

## 공통 인증 방식

각 서비스별 인증 방법과 토큰 관리 방법을 기술한다.
-->

<!-- ===== SOCKET 도메인 템플릿 ===== -->
<!--
## 연결 방식

- 프로토콜: WebSocket
- 엔드포인트: wss://[도메인]/ws
- 인증: 연결 시 Authorization 헤더

## 이벤트 목록

- [events.md](events.md) 참조

| 이벤트명 | 방향 | 페이로드 | 설명 |
|---------|------|---------|------|
| stock:price | Server→Client | { stockId, price } | 실시간 주가 |
| order:execute | Client→Server | { stockId, qty, price } | 주문 실행 |
-->
