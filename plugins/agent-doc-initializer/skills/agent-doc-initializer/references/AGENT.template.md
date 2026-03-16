# AI Agent 개발 지침
# 프로젝트: {{PROJECT_NAME}}
# 생성일: {{DATE}}
# 기술스택: {{TECH_STACK}}

---

## ✅ 기본 원칙

- 항상 한국어로 응답한다
- 작업 전 반드시 `.docs/INDEX.md`를 먼저 읽는다
- `.docs/INDEX.md`가 안내하는 문서만 선택적으로 읽는다 (불필요한 파일 읽기 금지)
- 작업 지시가 모호하면 먼저 확인 후 진행한다
- 100줄 이상 파일 편집 시 2~3개 단위로 나누어 순차 작업한다
- 기능 구현 전 반드시 `.docs/common/README.md`를 확인하여 중복 구현을 방지한다
- `.docs/project_plan.md`파일은 개발자가 프로젝트의 전반적인 내용을 파악하기 위한 문서로서 프로젝트의 구조 및 알아둬야할 사항을 프로그램 수정 후 항상 최신으로 업데이트 및 추가 한다

---

## 📂 작업 전: 문서 탐색 규칙

작업 시작 전 `.docs/INDEX.md`를 읽고 아래 표를 기준으로 필요한 문서만 읽는다.

| 작업 유형 | 먼저 읽을 파일 | 추가로 읽을 파일 |
|-----------|--------------|----------------|
| DB 구조 확인/수정 | `.docs/db/README.md` | 해당 `.docs/db/*.sql` |
| API 추가/수정 | `.docs/api/README.md` | 해당 `.docs/api/[도메인].md` |
| 서버/nginx 설정 | `.docs/server/README.md` | `.docs/server/nginx.md` |
| 클라이언트 페이지 | `.docs/client/README.md` | `.docs/client/pages.md` |
| 공통 함수 확인 | `.docs/common/README.md` | `.docs/common/utils.md` |
| 외부 API 연동 | `.docs/external/README.md` | 해당 외부 API 문서 |
| 소켓/실시간 | `.docs/socket/README.md` | `.docs/socket/events.md` |
| 전체 기능 파악 | `.docs/project_plan.md` | - |

---

## 🔄 작업 후: 문서 자동 동기화 규칙 (필수)

작업이 완료되면 아래 매핑 표에 따라 **반드시 해당 문서를 업데이트 또는 신규 생성**한다.
문서 업데이트는 코드 작업과 **동일한 중요도**로 처리한다.

### DB 변경 시
| 변경 내용 | 업데이트할 문서 |
|-----------|--------------|
| 테이블 신규 생성 | `.docs/db/README.md` 테이블 목록 추가<br>`.docs/db/[테이블명].sql` 신규 생성 또는 기존 파일에 반영<br>`.docs/project_plan.md` DB 섹션 업데이트 |
| 컬럼 추가/수정/삭제 | `.docs/db/README.md` 해당 테이블 설명 수정<br>해당 `.docs/db/*.sql` 파일 수정 |
| 인덱스/제약조건 변경 | 해당 `.docs/db/*.sql` 파일 수정 |
| 테이블 관계 변경 | `.docs/db/README.md` 관계도 섹션 업데이트 |

### API 변경 시
| 변경 내용 | 업데이트할 문서 |
|-----------|--------------|
| 엔드포인트 신규 추가 | `.docs/api/README.md` 목록 추가<br>`.docs/api/[도메인].md` 상세 스펙 추가 (없으면 신규 생성)<br>`.docs/project_plan.md` API 섹션 업데이트 |
| 엔드포인트 수정 | `.docs/api/[도메인].md` 해당 항목 수정 |
| 엔드포인트 삭제 | README 및 도메인 문서에서 제거 또는 deprecated 표시 |
| 요청/응답 스키마 변경 | `.docs/api/[도메인].md` 해당 항목 수정 |
| 미들웨어/인증 변경 | `.docs/api/README.md` 공통 미들웨어 섹션 수정 |

### 공통 함수/유틸 변경 시
| 변경 내용 | 업데이트할 문서 |
|-----------|--------------|
| 공통 함수 신규 추가 | `.docs/common/README.md` 함수 목록 추가 (위치, 시그니처, 용도)<br>`.docs/common/utils.md` 상세 설명 및 사용 예시 추가 |
| 함수 시그니처 변경 | `.docs/common/README.md` 및 `utils.md` 수정 |
| 함수 삭제/deprecated | `.docs/common/README.md`에 ~~취소선~~ 및 대체 함수 안내 |
| 클래스/모듈 신규 추가 | `.docs/common/README.md` 모듈 목록 추가<br>`.docs/common/[모듈명].md` 신규 생성 |

### 서버/인프라 변경 시
| 변경 내용 | 업데이트할 문서 |
|-----------|--------------|
| nginx 설정 변경 | `.docs/server/nginx.md` 변경된 설정 블록 업데이트 |
| 도메인/포트 변경 | `.docs/server/nginx.md` 및 `.docs/INDEX.md` 업데이트 |
| 환경변수 추가/변경 | `.docs/server/env.md` 환경변수 목록 업데이트 (없으면 신규 생성) |
| Cron 작업 변경 | `.docs/server/cron.md` Cron 스케줄 및 설명 업데이트 |
| 패키지 의존성 변경 | `.docs/server/README.md` 주요 패키지 섹션 업데이트 |

### 클라이언트 변경 시
| 변경 내용 | 업데이트할 문서 |
|-----------|--------------|
| 페이지 신규 추가 | `.docs/client/pages.md` 페이지 경로 및 설명 추가<br>`.docs/client/README.md` 라우팅 구조 업데이트 |
| 페이지 삭제/경로 변경 | `.docs/client/pages.md` 수정 |
| 공통 컴포넌트 추가 | `.docs/client/components.md` 컴포넌트 목록 추가 (없으면 신규 생성) |
| 소켓 이벤트 변경 | `.docs/socket/events.md` 이벤트 목록 및 페이로드 업데이트 |

### 외부 API 변경 시
| 변경 내용 | 업데이트할 문서 |
|-----------|--------------|
| 외부 API 신규 연동 | `.docs/external/[서비스명].md` 신규 생성<br>`.docs/external/README.md` 목록 추가 |
| 호출 방식/파라미터 변경 | `.docs/external/[서비스명].md` 수정 |

---

## 📝 문서 표준 형식

### API 문서 (`api/[도메인].md`)
```
## [엔드포인트명]
- Method: GET | POST | PATCH | DELETE
- Path: /api/...
- 권한: 공개 | 로그인 | 관리자 | 자동매매허용
- 요청: { 필드명: 타입 // 설명 }
- 응답: { 필드명: 타입 // 설명 }
- 변경이력: YYYY-MM-DD 변경내용
```

### DB 문서 (`db/README.md`)
```
## [테이블명]
- 파일: schema_xxx.sql
- 용도: 한줄 설명
- 주요 컬럼: col1(타입), col2(타입) ...
- 관계: → 연결테이블명 (FK 컬럼)
- 최종수정: YYYY-MM-DD
```

### 공통 함수 문서 (`common/README.md`)
```
## [함수명 또는 클래스명]
- 위치: src/lib/xxx.ts
- 시그니처: functionName(param: Type): ReturnType
- 용도: 한줄 설명
- 주의사항: (있을 경우)
- 최종수정: YYYY-MM-DD
```

---

## 🚫 절대 하지 말 것

- 코드만 수정하고 관련 `.docs/` 문서를 업데이트하지 않는 것
- `.docs/INDEX.md`를 읽지 않고 바로 코드 파일부터 여는 것
- `.docs/common/README.md` 확인 없이 동일 기능을 새로 구현하는 것
- 이미 존재하는 `.docs/` 파일을 확인하지 않고 중복 문서를 생성하는 것
