# 05. Technology Stack

## 1. 기술 스택 개요

WMS는

운영 환경을 고려한

실무형 업무관리시스템 구축을 목표로 한다.

---

기술 스택은

* 생산성
* 유지보수성
* 확장성
* 운영 편의성

을 기준으로 선정한다.

---

## 2. Backend

### Java

```text
Java 21
```

---

선택 이유

* 최신 LTS 버전
* Spring Boot 최신 버전 지원
* Record 지원
* Virtual Thread 지원
* 장기 유지보수 가능

---

### Framework

```text
Spring Boot
```

---

선택 이유

* 자동 설정
* 내장 Tomcat
* 운영 편의성
* 생산성 향상

---

### Security

```text
Spring Security
```

---

사용 목적

* 로그인
* 인증
* 권한 관리
* URL 접근 제어

---

### Build Tool

```text
Gradle
```

---

선택 이유

* 빠른 빌드 속도
* Spring Boot 공식 지원
* 유지보수 편의성

---

## 3. Database

### Database

```text
MySQL
```

---

선택 이유

* 높은 범용성
* JPA 호환성
* 운영 편의성
* Docker 환경 구성 용이

---

### ORM

```text
Spring Data JPA
```

---

사용 목적

* Entity 관리
* CRUD 처리
* Repository 추상화

---

프로젝트는

```text
Database First
```

정책을 사용한다.

---

구조

```text
ERD

↓

DDL

↓

Entity

↓

Repository
```

---

JPA 자동 DDL 생성은 사용하지 않는다.

---

### Query

복잡한 조회는

```text
QueryDSL
```

을 사용한다.

---

정책

```text
단순 CRUD

↓

JPA


복잡한 조회

↓

QueryDSL
```

---

MyBatis는 사용하지 않는다.

---

## 4. Frontend

### Template Engine

```text
Thymeleaf
```

---

선택 이유

* Spring Boot 공식 지원
* 서버 사이드 렌더링
* Spring MVC와 높은 호환성

---

### UI Framework

```text
Tabler
```

---

선택 이유

* 관리자 시스템 UI 제공
* Bootstrap 기반
* 깔끔한 디자인
* 높은 확장성

---

### Script

```text
Javascript

CSS
```

---

jQuery 의존성을 최소화한다.

가능한

```text
Vanilla Javascript
```

를 사용한다.

---

## 5. UI Component

### Grid

```text
WmsGrid

↓

DataTables
```

---

사용 화면

* SR 현황
* 직원조회
* 시설사용현황
* 전자결재 목록
* 시스템 로그
* 에러 로그

---

### Tree

```text
WmsTree

↓

jsTree
```

---

사용 화면

* 조직도 조회
* 부서 관리

---

### Calendar

```text
WmsCalendar

↓

FullCalendar
```

---

사용 화면

* 시설사용현황
* 일정 조회

---

### DatePicker

```text
WmsDatePicker

↓

flatpickr
```

---

사용 화면

* SR 처리예정일
* 시설 예약일
* 검색 기간

---

## 6. Logging

로그는

```text
Slf4j

+

Logback
```

을 사용한다.

---

로그 종류

```text
SYSTEM_LOG

ERROR_LOG

DOWNLOAD_LOG

PERSONAL_INFO_ACCESS_LOG

FILE_DELETE_LOG

BATCH_LOG
```

---

시스템 로그와

업무 처리이력은 분리하여 관리한다.

---

## 7. Batch

배치는

```text
Spring Scheduler

+

Cron
```

을 사용한다.

---

별도 배치 시스템은 구축하지 않는다.

---

배치 결과는

```text
BATCH_LOG
```

에 저장한다.

---

예)

* 미처리 SR 알림
* 직원 상태 갱신

---

## 8. File

파일은

공통 파일 서비스를 사용한다.

---

파일 참조는

```text
FILE_REF_NO
```

를 사용한다.

---

하나의 FILE_REF_NO에

여러 파일을 연결할 수 있다.

---

파일 저장소는

아래 순서로 확장한다.

```text
Local

↓

Docker Volume

↓

S3
```

---

## 9. Cache

캐시 서버는 사용하지 않는다.

---

```text
Redis

↓

미사용
```

---

필요 시

추후 확장 가능하도록 설계한다.

---

## 10. Deploy

배포 환경

```text
Docker

AWS

Linux
```

---

Docker 기반으로 배포한다.

---

운영 환경은

```text
Dev

↓

Stage

↓

Prod
```

구조를 고려한다.

---

## 11. Test

테스트는

```text
JUnit
```

기반으로 작성한다.

---

테스트 범위

* 단위 테스트
* 통합 테스트
* 예외 처리 테스트
* 배치 테스트

---

운영 환경을 고려하여

```text
부하 테스트
```

를 수행한다.

---

## 12. 기술 스택 선정 원칙

기술은

최신 기술을 우선적으로 적용하기보다

---

```text
생산성

유지보수성

확장성

운영 편의성
```

을 우선 고려한다.

---

새로운 기술 도입 시

프로젝트 전체 구조와

운영 환경에 적합한지

충분히 검토 후 적용한다.
