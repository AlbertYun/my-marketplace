# 문서 동기화 규칙 상세 (sync-rules.md)

> AGENT.md의 동기화 규칙 요약을 보완하는 상세 가이드.
> 애매한 케이스가 생기면 이 파일을 참조한다.

---

## 원칙

**"코드가 바뀌면 문서도 반드시 같이 바뀐다"**

문서 업데이트를 빠뜨리면:
- 다음 작업 시 AI가 잘못된 정보를 읽고 버그를 만든다
- 어느 파일이 최신인지 알 수 없게 된다
- 신규 팀원(또는 AI)이 온보딩할 때 혼란이 생긴다

---

## 케이스별 상세 규칙

### CASE 1: DB 테이블이 새로 생겼을 때

```
작업 내용: schema_xxx.sql 에 새 테이블 추가
↓
1. .docs/db/README.md 열기
2. "테이블 목록" 섹션에 아래 형식으로 추가:
   | 새테이블명 | schema_xxx.sql | 용도 한줄 | YYYY-MM-DD |
3. "테이블 상세" 섹션에 상세 추가:
   ### 새테이블명
   - 파일: schema_xxx.sql
   - 용도: ...
   - 주요 컬럼: id(uuid), created_at(timestamp) ...
   - 관계: → 연결테이블 (FK컬럼)
4. 관계도 있으면 "관계도" 섹션도 업데이트
5. project_plan.md의 "데이터베이스 구조" 섹션에 테이블명 추가
```

### CASE 2: 기존 테이블에 컬럼이 추가됐을 때

```
작업 내용: ALTER TABLE 또는 스키마 파일 수정
↓
1. .docs/db/README.md 열기
2. 해당 테이블 상세의 "주요 컬럼"에 새 컬럼 추가
3. 해당 .sql 파일도 새 컬럼 반영 확인 (이미 반영됐으면 skip)
4. 최종수정 날짜 업데이트
```

### CASE 3: API 엔드포인트가 새로 생겼을 때

```
작업 내용: /api/xxx/route.ts 신규 생성
↓
1. .docs/api/README.md 열기
2. "엔드포인트 목록" 테이블에 추가:
   | GET | /api/xxx | 권한 | [도메인.md](도메인.md) |
3. .docs/api/[도메인].md 열기 (없으면 신규 생성)
4. 아래 형식으로 엔드포인트 추가:
   ## GET /api/xxx
   - 권한: 공개 | 로그인 | 관리자
   - 요청: { param: string // 설명 }
   - 응답: { data: object // 설명 }
   - 변경이력: YYYY-MM-DD 최초 생성
5. project_plan.md 관련 기능 섹션에 API 엔드포인트 추가
```

### CASE 4: 공통 함수가 추가됐을 때

```
작업 내용: src/lib/xxx.ts 에 새 함수 추가
↓
1. .docs/common/README.md 열기
2. 해당 모듈 섹션에 함수 추가:
   ### 새함수명
   - 위치: src/lib/xxx.ts
   - 시그니처: newFunction(param: Type): ReturnType
   - 용도: 한줄 설명
   - 주의사항: (있으면)
   - 최종수정: YYYY-MM-DD
3. 모듈이 처음 추가되는 경우:
   "모듈 목록" 테이블에도 추가
```

### CASE 5: 함수가 삭제/변경됐을 때

```
삭제된 경우:
  .docs/common/README.md 에서 해당 항목 찾아:
  - ~~[함수명]~~ (deprecated YYYY-MM-DD, 대체: 새함수명)
  로 표시. 한달 후 제거.

시그니처가 바뀐 경우:
  .docs/common/README.md 의 시그니처 수정
  최종수정 날짜 업데이트
```

### CASE 6: 클라이언트 페이지가 추가됐을 때

```
작업 내용: app/new-page/page.tsx 생성
↓
1. .docs/client/pages.md 열기 (없으면 신규 생성)
2. 페이지 목록에 추가:
   | /new-page | app/new-page/page.tsx | 권한 | 설명 |
3. .docs/client/README.md 의 라우팅 구조 업데이트
```

### CASE 7: nginx 설정이 바뀌었을 때

```
작업 내용: /etc/nginx/sites-enabled/app 수정
↓
1. .docs/server/nginx.md 열기 (없으면 신규 생성)
2. 변경된 설정 블록을 문서에 반영:
   ## [도메인명] 설정
   변경 전: (이전 설정)
   변경 후: (새 설정)
   변경일: YYYY-MM-DD
   변경이유: ...
```

### CASE 8: 환경변수가 추가됐을 때

```
작업 내용: .env 에 새 변수 추가
↓
1. .docs/server/env.md 열기 (없으면 신규 생성)
2. 변수 목록에 추가:
   | VARIABLE_NAME | 설명 | 필수여부 | 예시값 |
3. .env.example 에도 반영 (실제 값은 제외)
```

### CASE 9: 외부 API 연동이 추가됐을 때

```
작업 내용: 새 외부 API 호출 코드 추가
↓
1. .docs/external/README.md 열기
2. 외부 API 목록에 추가:
   | 서비스명 | 인증방식 | [상세문서](서비스명.md) |
3. .docs/external/[서비스명].md 신규 생성:
   # [서비스명] API
   - 인증: Bearer Token / API Key
   - 기본 URL: https://...
   - 사용 엔드포인트: ...
   - 호출 위치: src/lib/xxx.ts
   - 주의사항: Rate Limit, 에러코드 등
```

---

## 문서 업데이트 누락 체크리스트

작업 완료 전 아래를 확인한다:

```
□ DB 변경이 있었는가?
  → .docs/db/README.md 업데이트했는가?
  → 해당 .sql 파일 업데이트했는가?

□ API 변경이 있었는가?
  → .docs/api/README.md 업데이트했는가?
  → 해당 도메인 .md 파일 업데이트했는가?

□ 공통 함수 변경이 있었는가?
  → .docs/common/README.md 업데이트했는가?

□ 서버/인프라 변경이 있었는가?
  → .docs/server/ 하위 해당 파일 업데이트했는가?

□ 클라이언트 변경이 있었는가?
  → .docs/client/pages.md 또는 components.md 업데이트했는가?

□ 기능 추가/수정이 있었는가?
  → .docs/project_plan.md 업데이트했는가?
```
