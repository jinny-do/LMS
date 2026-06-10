# 학사관리 시스템 1차 MVP API 명세서 초안

## 1. 공통 규칙

- Base URL: `/api`
- Content-Type: `application/json`
- 인증: JWT Bearer Token 또는 서버 세션 중 팀 합의 방식 사용
- 날짜: `YYYY-MM-DD`
- 일시: ISO 8601 형식
- 목록 API: `page`, `size`, `sort` 파라미터 사용
- 역할 표기: `ALL`은 로그인한 전체 역할, 나머지는 `STUDENT`, `PROFESSOR`, `ADMIN`

## 2. 인증 및 공통

| Method | Endpoint | 설명 | 권한 |
|---|---|---|---|
| POST | `/auth/login` | 로그인 | ALL |
| POST | `/auth/logout` | 로그아웃 | ALL |
| GET | `/me` | 내 계정 및 역할별 기본 정보 조회 | ALL |

### `POST /auth/login`

요청:

```json
{
  "loginId": "20260001",
  "password": "password"
}
```

응답:

```json
{
  "data": {
    "accessToken": "token",
    "userId": 10,
    "name": "홍길동",
    "role": "STUDENT"
  }
}
```

## 3. 공개 강좌 조회

| Method | Endpoint | 설명 | 권한 |
|---|---|---|---|
| GET | `/lectures` | 강좌 목록 검색 | ALL |
| GET | `/lectures/{lectureId}` | 강좌 상세 조회 | ALL |

주요 검색 파라미터: `semesterId`, `departmentId`, `courseName`, `professorName`, `page`, `size`

## 4. 학생 API

| Method | Endpoint | 설명 | 권한 |
|---|---|---|---|
| GET | `/student/dashboard` | 학생 대시보드 조회 | STUDENT |
| GET | `/student/enrollments` | 내 학기별 수강 내역 조회 | STUDENT |
| POST | `/student/enrollments` | 수강 신청 | STUDENT |
| DELETE | `/student/enrollments/{enrollmentId}` | 수강 취소 | STUDENT |
| GET | `/student/timetable` | 내 시간표 조회 | STUDENT |
| GET | `/student/attendance` | 내 출결 현황 조회 | STUDENT |
| GET | `/student/grades` | 내 확정 성적 및 취득학점 조회 | STUDENT |

### `POST /student/enrollments`

요청:

```json
{
  "lectureId": 101
}
```

성공 응답:

```json
{
  "data": {
    "enrollmentId": 9001,
    "lectureId": 101,
    "enrolledCredits": 18
  },
  "message": "수강 신청이 완료되었습니다."
}
```

업무 오류: `ENROLLMENT_PERIOD_CLOSED`, `ALREADY_ENROLLED`, `CAPACITY_EXCEEDED`, `SCHEDULE_CONFLICT`, `CREDIT_LIMIT_EXCEEDED`

### `GET /student/grades?semesterId=1`

응답:

```json
{
  "data": {
    "semesterId": 1,
    "totalEarnedCredits": 18,
    "semesterGpa": 4.1,
    "grades": [
      {
        "courseCode": "CS101",
        "courseName": "프로그래밍기초",
        "credits": 3,
        "score": 95,
        "letterGrade": "A+"
      }
    ]
  }
}
```

## 5. 교수 API

| Method | Endpoint | 설명 | 권한 |
|---|---|---|---|
| GET | `/professor/dashboard` | 교수 대시보드 조회 | PROFESSOR |
| GET | `/professor/lectures` | 담당 강좌 목록 조회 | PROFESSOR |
| GET | `/professor/lectures/{lectureId}` | 담당 강좌 상세 및 수강생 조회 | PROFESSOR |
| GET | `/professor/lectures/{lectureId}/attendance-sessions` | 강좌 수업일 목록 조회 | PROFESSOR |
| POST | `/professor/lectures/{lectureId}/attendance-sessions` | 수업일 생성 | PROFESSOR |
| PUT | `/professor/lectures/{lectureId}/attendance-sessions/{sessionId}/records` | 출결 일괄 저장 | PROFESSOR |
| GET | `/professor/lectures/{lectureId}/grades` | 강좌 성적 목록 조회 | PROFESSOR |
| PUT | `/professor/lectures/{lectureId}/grades` | 성적 일괄 임시 저장 | PROFESSOR |
| POST | `/professor/lectures/{lectureId}/grades/submit` | 성적 제출 | PROFESSOR |

### `PUT /professor/lectures/{lectureId}/attendance-sessions/{sessionId}/records`

요청:

```json
{
  "records": [
    {
      "enrollmentId": 9001,
      "status": "PRESENT"
    },
    {
      "enrollmentId": 9002,
      "status": "LATE"
    }
  ]
}
```

### `PUT /professor/lectures/{lectureId}/grades`

요청:

```json
{
  "grades": [
    {
      "enrollmentId": 9001,
      "score": 95,
      "letterGrade": "A+"
    },
    {
      "enrollmentId": 9002,
      "score": 82,
      "letterGrade": "B0"
    }
  ]
}
```

업무 오류: `NOT_LECTURE_PROFESSOR`, `GRADE_NOT_EDITABLE`, `INVALID_GRADE`

## 6. 관리자 기준정보·사용자 API

| Method | Endpoint | 설명 | 권한 |
|---|---|---|---|
| GET, POST | `/admin/semesters` | 학기 목록 조회·등록 | ADMIN |
| GET, PUT | `/admin/semesters/{semesterId}` | 학기 상세 조회·수정 | ADMIN |
| GET, POST | `/admin/departments` | 학과 목록 조회·등록 | ADMIN |
| GET, PUT | `/admin/departments/{departmentId}` | 학과 상세 조회·수정 | ADMIN |
| GET, POST | `/admin/majors` | 전공 목록 조회·등록 | ADMIN |
| GET, PUT | `/admin/majors/{majorId}` | 전공 상세 조회·수정 | ADMIN |
| GET, POST | `/admin/students` | 학생 목록 조회·등록 | ADMIN |
| GET, PUT | `/admin/students/{studentId}` | 학생 상세 조회·수정 | ADMIN |
| GET, POST | `/admin/professors` | 교수 목록 조회·등록 | ADMIN |
| GET, PUT | `/admin/professors/{professorId}` | 교수 상세 조회·수정 | ADMIN |
| GET | `/admin/users` | 계정 목록 조회 | ADMIN |
| PATCH | `/admin/users/{userId}/status` | 계정 활성 상태 변경 | ADMIN |
| PATCH | `/admin/users/{userId}/roles` | 계정 역할 변경 | ADMIN |

### `POST /admin/students`

요청:

```json
{
  "loginId": "20260001",
  "initialPassword": "temporary-password",
  "name": "홍길동",
  "email": "student@example.edu",
  "studentNo": "20260001",
  "departmentId": 1,
  "majorId": 1,
  "gradeLevel": 1,
  "admissionYear": 2026,
  "academicStatus": "ENROLLED"
}
```

## 7. 관리자 과목·강좌 API

| Method | Endpoint | 설명 | 권한 |
|---|---|---|---|
| GET, POST | `/admin/courses` | 과목 목록 조회·등록 | ADMIN |
| GET, PUT | `/admin/courses/{courseId}` | 과목 상세 조회·수정 | ADMIN |
| GET, POST | `/admin/lectures` | 강좌 목록 조회·개설 | ADMIN |
| GET, PUT | `/admin/lectures/{lectureId}` | 강좌 상세 조회·수정 | ADMIN |
| PATCH | `/admin/lectures/{lectureId}/status` | 강좌 상태 변경 | ADMIN |

### `POST /admin/lectures`

요청:

```json
{
  "semesterId": 1,
  "courseId": 11,
  "professorId": 21,
  "sectionNo": "01",
  "capacity": 30,
  "classroom": "공학관 101",
  "schedules": [
    {
      "dayOfWeek": "MON",
      "startPeriod": 2,
      "endPeriod": 4
    }
  ]
}
```

## 8. 관리자 수강·성적·감사 API

| Method | Endpoint | 설명 | 권한 |
|---|---|---|---|
| GET | `/admin/enrollments` | 학생·강좌별 수강 내역 조회 | ADMIN |
| POST | `/admin/enrollments/adjustments` | 관리자 수강 추가 | ADMIN |
| POST | `/admin/enrollments/{enrollmentId}/cancel` | 관리자 수강 취소 | ADMIN |
| GET | `/admin/grade-submissions` | 제출·확정 대상 강좌 조회 | ADMIN |
| GET | `/admin/grade-submissions/{lectureId}` | 제출 성적 상세 조회 | ADMIN |
| POST | `/admin/grade-submissions/{lectureId}/confirm` | 성적 확정 | ADMIN |
| POST | `/admin/grade-submissions/{lectureId}/reject` | 성적 반려 | ADMIN |
| GET | `/admin/audit-logs` | 주요 변경 이력 조회 | ADMIN |

### `POST /admin/enrollments/adjustments`

요청:

```json
{
  "studentId": 31,
  "lectureId": 101,
  "reason": "관리자 승인에 따른 수강 추가"
}
```

### `POST /admin/enrollments/{enrollmentId}/cancel`

요청:

```json
{
  "reason": "관리자 승인에 따른 수강 취소"
}
```

### `POST /admin/grade-submissions/{lectureId}/reject`

요청:

```json
{
  "reason": "일부 학생의 점수와 등급을 다시 확인해 주세요."
}
```

## 9. 주요 HTTP 상태 코드

| 상태 코드 | 사용 기준 |
|---|---|
| 200 | 조회·수정·상태 변경 성공 |
| 201 | 등록 성공 |
| 204 | 삭제·취소 성공, 응답 본문 없음 |
| 400 | 입력값 오류 또는 업무 규칙 위반 |
| 401 | 인증되지 않은 요청 |
| 403 | 역할 또는 데이터 접근 권한 없음 |
| 404 | 대상 데이터 없음 |
| 409 | 중복 데이터, 정원 초과 등 충돌 |

## 10. OpenAPI 작성 기준

- Springdoc OpenAPI를 사용해 실제 구현 API를 문서화한다.
- 요청·응답 DTO에 예시와 필드 설명을 작성한다.
- 업무 오류 코드를 각 API 응답에 명시한다.
- 본 초안과 구현 API가 달라질 경우 실제 OpenAPI를 기준으로 본 문서를 갱신한다.
