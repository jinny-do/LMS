# 학사관리 시스템 코드 컨벤션

> Spring Boot + Vue + MySQL 기반 1차 프로젝트 팀 공통 규칙

## 1. 목적

3주 프로젝트에서 코드 스타일 논쟁을 줄이고, 각자 학원과 개인 컴퓨터에서 개발한 기능을 빠르게 통합하기 위한 최소 규칙이다.

- 자동 포매터가 처리할 수 있는 영역은 도구에 맡긴다.
- DB, API, 코드에서 사용하는 상태값과 도메인 용어를 통일한다.
- 실제 충돌이나 반복 문제가 발생했을 때만 규칙을 추가한다.

## 2. 공통 규칙

| 항목 | 팀 기준 |
|---|---|
| 파일 인코딩 | UTF-8 |
| 들여쓰기 | Java 4칸, Vue·JavaScript 2칸 |
| 명명 언어 | 클래스·함수·변수는 영어 사용 |
| 설명 언어 | 주석과 문서는 한국어 허용 |
| 날짜·시간 | 날짜는 `LocalDate`, 일시는 `LocalDateTime` |
| 상태값 | 영문 대문자 Enum 사용 |
| 병합 조건 | 빌드·테스트·린트 통과 및 최소 1명 리뷰 |

- 자동 포매터 결과를 팀 표준으로 인정하고 개인 취향에 따른 재정렬을 하지 않는다.
- 사용하지 않는 코드, 디버깅 출력, 주석 처리한 코드는 병합하지 않는다.
- 상태값은 DB, API, 백엔드, 프론트에서 동일하게 사용한다.

예시:

```text
ENROLLED
SUBMITTED
CONFIRMED
```

## 3. Git 협업 규칙

### 브랜치 구성

| 브랜치 | 용도 |
|---|---|
| `main` | 발표 및 배포 가능한 안정 버전 |
| `develop` | 기능 통합 브랜치 |
| `feature/{domain}-{description}` | 개인별 기능 개발 및 오류 수정 |

브랜치 예시:

```text
feature/student-management
feature/course-lecture
feature/enrollment
feature/attendance-grade
```

### 작업 흐름

```text
develop에서 feature 브랜치 생성
→ 학원 또는 개인 컴퓨터에서 담당 기능 개발
→ 원격 저장소에 feature 브랜치 Push
→ develop 대상 Pull Request 생성
→ 최소 한 명 리뷰
→ develop에 병합
```

### 협업 규칙

- 기능 개발과 담당 기능 내부 오류 수정은 자신의 `feature` 브랜치에서 진행한다.
- `main`과 `develop` 브랜치에 직접 Push하지 않는다.
- 작업 시작 전 원격 `develop`의 최신 변경 사항을 확인한다.
- 여러 컴퓨터에서 작업할 때는 이전 작업을 원격 저장소에 Push한 뒤 다른 컴퓨터에서 이어서 작업한다.
- 동일한 파일을 여러 명이 동시에 크게 수정해야 하는 경우 먼저 팀에 공유한다.
- DB 또는 API가 변경되면 Pull Request 설명과 팀 채널에 공유한다.
- 실행되지 않거나 빌드가 실패하는 코드는 병합하지 않는다.
- 비밀번호, JWT Secret, DB 접속 정보 등 비밀 설정은 Git에 올리지 않는다.

## 4. Spring Boot 컨벤션

### 패키지 구조

패키지는 도메인 기준으로 구성하고, 각 도메인 내부에서 계층을 구분한다.

```text
com.university.lms
├─ global
│  ├─ config
│  ├─ security
│  ├─ exception
│  └─ response
├─ auth
├─ student
├─ professor
├─ academic
├─ course
├─ lecture
├─ enrollment
└─ grade
```

각 도메인의 기본 구조:

```text
student
├─ controller
├─ service
├─ repository
├─ entity
└─ dto
```

### 이름 규칙

| 대상 | 규칙 | 예시 |
|---|---|---|
| 클래스 | PascalCase | `StudentController` |
| 메서드·변수 | camelCase | `createStudent` |
| 상수·Enum 값 | UPPER_SNAKE_CASE | `ENROLLED` |
| Boolean | `is`, `has`, `can` 접두사 | `isActive`, `canEnroll` |
| 요청 DTO | `{Domain}{Action}Request` | `StudentCreateRequest` |
| 응답 DTO | `{Domain}Response` | `StudentResponse` |

### 계층별 책임

| 계층 | 책임 |
|---|---|
| Controller | 요청·응답 처리, 입력 검증, 권한 진입점 |
| Service | 업무 규칙, 권한·소유권 확인, 트랜잭션 |
| Repository | DB 접근 |
| Entity | 데이터와 최소한의 상태 변경 |
| DTO | API 요청·응답 데이터 |

다음 사항은 금지한다.

- Controller에서 Repository 직접 호출
- Entity를 API 응답으로 직접 반환
- 중요한 업무 규칙을 프론트에서만 검증

## 5. Vue 컨벤션

### 폴더 구조

```text
src
├─ api
├─ assets
├─ components
│  └─ common
├─ layouts
├─ router
├─ stores
├─ views
│  ├─ admin
│  ├─ student
│  └─ professor
└─ utils
```

### 이름 규칙

| 대상 | 규칙 | 예시 |
|---|---|---|
| 컴포넌트 | PascalCase | `StudentFormDialog.vue` |
| 변수·함수 | camelCase | `fetchStudents` |
| 이벤트 함수 | `handle` 접두사 | `handleSave` |
| 조회 함수 | `fetch` 접두사 | `fetchLectures` |
| Boolean | `is`, `has`, `can` 접두사 | `isLoading` |
| API 파일 | `{domain}Api.js` | `studentApi.js` |
| Pinia Store | `{domain}Store.js` | `authStore.js` |

- Vue 화면에서 Axios를 직접 호출하지 않고 `api` 폴더의 함수를 사용한다.
- 공통 레이아웃, 버튼, 상태 표시는 공통 컴포넌트를 우선 사용한다.
- 역할별 메뉴를 숨기는 것뿐 아니라 서버 API에서도 권한을 검증한다.
- 한 컴포넌트가 지나치게 커지면 목록, 폼, 다이얼로그 단위로 분리한다.

## 6. DB 컨벤션

| 대상 | 규칙 | 예시 |
|---|---|---|
| 테이블·컬럼 | snake_case, 테이블은 복수형 | `students`, `student_no` |
| 기본키 | `id` | `students.id` |
| 외래키 | `{entity}_id` | `department_id` |
| 시간 컬럼 | `created_at`, `updated_at` | `created_at` |
| Boolean | `is_` 접두사 | `is_active` |
| 상태값 | 코드 Enum과 동일한 영문 대문자 | `ENROLLED` |

- DB 관계는 학번이나 과목코드가 아닌 내부 PK로 연결한다.
- 학번, 교번, 과목코드 등 중복 불가 값에는 `UNIQUE` 제약조건을 적용한다.
- DB 구조를 변경하면 ERD와 마이그레이션 파일을 함께 갱신한다.

## 7. REST API 컨벤션

```text
GET    /api/admin/students
GET    /api/admin/students/{studentId}
POST   /api/admin/students
PUT    /api/admin/students/{studentId}
PATCH  /api/admin/students/{studentId}/status
```

- URL은 복수 명사를 기본으로 한다.
- 일반 CRUD에는 불필요한 동사를 넣지 않는다.
- 요청·응답 JSON은 `camelCase`를 사용한다.
- 역할별 API 경로를 명확하게 구분한다.
- API가 변경되면 API 명세 또는 Swagger를 갱신한다.

## 8. 공통 응답 및 오류

### 성공 응답

```json
{
  "data": {},
  "message": "요청이 처리되었습니다."
}
```

### 오류 응답

```json
{
  "code": "CAPACITY_EXCEEDED",
  "message": "강좌 정원이 마감되었습니다.",
  "fieldErrors": []
}
```

- 업무 오류 코드는 영문 대문자 `SNAKE_CASE`로 작성한다.
- 오류 메시지는 사용자가 해결 방법을 이해할 수 있는 한국어로 제공한다.
- 예외를 무시하거나 콘솔에만 출력하지 않고 공통 예외 처리로 전달한다.

## 9. 보안·권한 규칙

역할 권한과 데이터 소유권 검증은 별개다. URL이나 ID를 변경하여 다른 사용자의 데이터에 접근할 수 없어야 한다.

- `STUDENT`: 자신의 학적·수강·출결·성적만 조회한다.
- `PROFESSOR`: 자신이 담당하는 강좌의 수강생·출결·성적만 관리한다.
- `ADMIN`: 관리자 API에 접근하고 기준정보와 학적을 관리한다.
- 비밀번호는 평문으로 저장하지 않고 BCrypt 등 단방향 해시를 사용한다.
- 프론트 메뉴 숨김만으로 권한을 처리하지 않고 서버에서 최종 차단한다.

## 10. 자동화 및 병합 전 확인

| 영역 | 권장 도구·명령 |
|---|---|
| Java 포맷 | IntelliJ 공유 Code Style 또는 Spotless |
| Vue 포맷·검사 | ESLint + Prettier |
| 공통 편집 설정 | `.editorconfig` |
| 백엔드 검증 | `./gradlew test` |
| 프론트 검증 | `npm run lint`, `npm run build` |

### Pull Request 전 체크리스트

- [ ] 빌드와 테스트가 통과한다.
- [ ] 불필요한 파일, 로그, 비밀 설정이 포함되지 않았다.
- [ ] DB 변경 시 ERD와 마이그레이션을 갱신했다.
- [ ] API 변경 시 API 명세 또는 Swagger를 갱신했다.
- [ ] 상태값과 용어가 기존 문서·코드와 일치한다.
- [ ] 담당 기능의 성공 흐름과 주요 실패 흐름을 확인했다.

## 11. 팀 필수 합의 사항

1. 패키지는 도메인 기준으로 구성한다.
2. Entity를 API 응답으로 직접 반환하지 않는다.
3. Controller에서 Repository를 직접 호출하지 않는다.
4. 상태값은 DB·API·코드에서 동일한 Enum을 사용한다.
5. 공통 성공·오류 응답 형식을 사용한다.
6. DB 변경 시 팀에 공유하고 ERD와 마이그레이션을 갱신한다.
7. API 변경 시 명세 또는 Swagger를 갱신한다.
8. 기능 브랜치와 Pull Request를 사용한다.
9. 최소 한 명의 리뷰 후 병합한다.
10. 테스트·빌드 실패 코드는 병합하지 않는다.

---

이 문서는 프로젝트 시작 시점의 기준이다. 실제 협업 중 충돌이나 반복 문제가 발생하면 팀 논의를 거쳐 필요한 규칙만 추가한다.
