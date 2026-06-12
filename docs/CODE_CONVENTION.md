# 학사관리 시스템 코드 컨벤션

Spring Boot + Vue + MySQL · 1차 프로젝트 팀 공통 규칙

**목적** · 3주 프로젝트에서 코드 스타일 논쟁을 줄이고, 서로의 기능을 빠르게 통합하기 위한 최소 규칙이다. 자동 포매터가 처리할 수 있는 영역은 도구에 맡기고, 팀원이 반드시 동일하게 지켜야 하는 구조와 명칭에 집중한다.

| 항목 | 팀 기준 |
| :--- | :--- |
| 파일 인코딩 | UTF-8 |
| 들여쓰기 | Java 4칸 · Vue/JavaScript 2칸 |
| 명명 언어 | 클래스·함수·변수는 영어, 설명·주석·커밋은 한국어 허용 |
| 핵심 원칙 | DB·API·코드에서 상태값과 도메인 용어를 동일하게 사용 |
| 병합 조건 | 빌드·테스트·린트가 통과하고 최소 1명이 리뷰 |

## 1. 공통 규칙

*   자동 포매터 결과를 팀 표준으로 인정하고, 개인 취향에 따른 재정렬은 하지 않는다.
*   사용하지 않는 코드, 디버깅 출력, 주석 처리한 코드는 병합하지 않는다.
*   날짜는 `LocalDate`, 일시는 `LocalDateTime`을 기본으로 사용한다.
*   상태값은 영문 대문자 Enum을 사용한다. 예: `ENROLLED`, `SUBMITTED`, `CONFIRMED`.
*   새 규칙은 실제 충돌이나 반복 문제가 발생했을 때만 추가한다.

## 2. Git 협업 규칙

브랜치는 `main`을 발표 가능한 버전, `develop`을 기능 통합 브랜치로 운영한다.

| 브랜치 | 용도 | 예시 |
| :--- | :--- | :--- |
| `main` | 배포·발표 가능한 안정 버전 | `main` |
| `develop` | 기능 통합 | `develop` |
| `feature/{domain}-{description}` | 기능 개발 | `feature/enrollment-validation` |
| `fix/{description}` | 버그 수정 | `fix/grade-status` |

### 커밋 메시지 형식

`[YYYY-MM-DD] 타입: 제목 (수정 내용)`

```text
[2026-06-12] feat: 게시글 등록 기능 추가 (PostController, PostService, PostMapper 수정)
[2026-06-12] fix: 로그인 시 예외 처리 오류 수정 (AuthService 수정)
[2026-06-12] refactor: 파일 업로드 로직 공통화 (FileUtil 생성 및 적용)
[2026-06-12] docs: README.md 프로젝트 설치 방법 갱신
[2026-06-12] style: PostIndex.vue UI 레이아웃 정렬 수정
```

*   날짜는 `[YYYY-MM-DD]` 형식을 사용한다.
*   괄호`()` 안에 구체적으로 어떤 파일이나 기능이 수정되었는지 요약하여 작성한다.
*   기능 브랜치는 `develop`에서 생성하고 PR을 통해 `develop`으로 병합한다.
*   PR에는 변경 기능, 테스트 방법, DB·API 변경 사항을 작성한다.
*   본인이 작성한 PR은 최소 한 명의 리뷰를 받은 후 병합한다.

## 3. Spring Boot 컨벤션

패키지는 도메인 기준으로 구성하고, 각 도메인 안에서 계층을 구분한다. (복수형 명칭 사용)

`com.msa4meerkatgram.domain`
*   `global / config / security / errors / responses / util`
*   `auth · user · post · file`

`post / controllers / services / mapper / entities / requests / responses`

| 대상 | 규칙 | 예시 |
| :--- | :--- | :--- |
| 클래스 | PascalCase | `PostController` |
| 메서드·변수 | camelCase | `createPost` |
| 상수·Enum | UPPER_SNAKE_CASE | `POST_STATUS` |
| Boolean | `is` / `has` / `can` 접두사 | `isPublic`, `canComment` |
| DTO (Record) | `{Domain}{Action}Req` / `Res` | `PostCreateReq`, `PostIndexRes` |
| Entity | PascalCase | `Post` |

### 계층별 책임

| 계층 | 책임 |
| :--- | :--- |
| Controller | 요청·응답 처리, 입력 검증 (@Valid), 권한 진입점 |
| Service | 업무 규칙, 트랜잭션 처리 (@Transactional) |
| Mapper | DB 접근 (MyBatis 인터페이스) |
| Entity | 데이터 구조 정의 |
| Request/Response | API 전용 데이터 객체 (Java Record 사용 권장) |

**금지:** Controller에서 Mapper 직접 호출 · Entity를 API 응답으로 직접 반환 (가급적 Response DTO 사용)

## 4. Vue 컨벤션

`src / api · assets · components · layouts · routes · store · pages · util`

| 대상 | 규칙 | 예시 |
| :--- | :--- | :--- |
| 컴포넌트·페이지 | PascalCase | `PostCreate.vue` |
| 변수·함수 | camelCase | `fetchPosts` |
| 이벤트 함수 | `handle` 접두사 | `handleSave` |
| Boolean | `is` / `has` / `can` 접두사 | `isLoading` |
| Pinia Store | `use{Domain}{Action}Store.js` | `usePostIndexStore.js` |

*   비즈니스 로직은 Pinia Store에 집중하며, Store에서 Axios(`myAxios`)를 호출하여 데이터를 처리한다.
*   `pages` 폴더는 라우팅되는 화면 단위의 컴포넌트를 배치한다.
*   한 컴포넌트가 지나치게 커지면 `components` 단위로 분리한다.

## 5. DB 및 REST API 컨벤션

| DB 대상 | 규칙 | 예시 |
| :--- | :--- | :--- |
| 테이블·컬럼 | snake_case · 테이블은 복수형 | `posts`, `user_id` |
| 기본키 | `id` | `posts.id` |
| 외래키 | `{entity}_id` | `user_id` |
| 시간 컬럼 | `created_at`, `updated_at`, `deleted_at` | `created_at` |

### REST API 예시

*   `GET /api/posts`
*   `GET /api/posts/{id}`
*   `POST /api/posts`

*   요청·응답 JSON은 camelCase를 사용한다.

## 6. 공통 응답 및 오류

### 공통 응답 형식 (GlobalRes)
```json
{
  "code": "00",
  "message": "정상처리",
  "data": {}
}
```

*   `code`: 성공 시 "00", 실패 시 에러 코드(예: "E01")를 사용한다.
*   `message`: 사용자나 개발자가 식별할 수 있는 메시지를 제공한다.
*   `data`: 실제 응답 데이터를 담는다.

## 7. 보안·권한 규칙

역할 권한과 데이터 소유권 검증은 별개다. URL이나 ID를 바꿔 다른 사용자의 데이터에 접근할 수 없어야 한다.

*   **STUDENT:** 자신의 학적·수강·출결·성적만 조회한다.
*   **PROFESSOR:** 자신이 담당하는 강좌의 수강생·출결·성적만 관리한다.
*   **ADMIN:** 관리자 API에 접근하고 기준정보와 학적을 관리한다.
*   비밀번호는 평문 저장하지 않고 BCrypt 등 단방향 해시를 사용한다.
*   프론트 메뉴 숨김만으로 권한을 처리하지 않고 서버에서 최종 차단한다.

## 8. 자동화 및 병합 전 체크리스트

| 영역 | 권장 도구·명령 |
| :--- | :--- |
| Java 포맷 | IntelliJ 공유 Code Style 또는 Spotless |
| Vue 포맷·검사 | ESLint + Prettier |
| 공통 편집 설정 | .editorconfig |
| 백엔드 검증 | `./gradlew test` |
| 프론트 검증 | `npm run lint` · `npm run build` |

### PR 전 필수 확인

*   빌드와 테스트가 통과한다.
*   불필요한 파일, 로그, 비밀 설정이 포함되지 않았다.
*   DB가 변경되었다면 ERD·마이그레이션을 갱신했다.
*   API가 변경되었다면 API 명세 또는 Swagger를 갱신했다.
*   상태값과 용어가 기존 문서·코드와 일치한다.
*   담당 기능의 성공 흐름과 주요 실패 흐름을 직접 확인했다.

## 9. 팀 필수 합의 10가지

| No. | 합의 사항 |
| :--- | :--- |
| 1 | 패키지는 도메인 기준으로 구성한다. |
| 2 | Entity를 API 응답으로 직접 반환하지 않는다. |
| 3 | Controller에서 Repository를 직접 호출하지 않는다. |
| 4 | 상태값은 DB·API·코드에서 동일한 Enum을 사용한다. |
| 5 | 공통 성공·오류 응답 형식을 사용한다. |
| 6 | DB 변경 시 팀 공유 후 ERD와 마이그레이션을 갱신한다. |
| 7 | API 변경 시 명세 또는 Swagger를 갱신한다. |
| 8 | 기능 브랜치와 PR을 사용한다. |
| 9 | 최소 한 명의 리뷰 후 병합한다. |
| 10 | 테스트·빌드 실패 코드는 병합하지 않는다. |

**운영 원칙** · 이 문서는 시작점이다. 3주 동안 실제 충돌이 발생한 규칙만 보강하고, 개발 속도를 낮추는 과도한 규칙은 추가하지 않는다.
