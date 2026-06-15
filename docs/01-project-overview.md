# 01. Project Overview

## 1. 프로젝트 개요

### 프로젝트명

WMS (Work Management System)

업무관리시스템

---

### 프로젝트 목적

본 프로젝트는 Spring Boot 기반의 실무형 업무관리시스템을 설계하고 구현하기 위한 토이 프로젝트이다.

기존 Spring MVC + MyBatis 기반의 레거시 프로젝트 경험을 기반으로 최신 Spring Boot 환경에서 실무에 가까운 시스템을 직접 설계하고 구축하는 것을 목표로 한다.

단순 CRUD 프로젝트가 아닌 실제 기업에서 사용할 수 있는 수준의 업무관리시스템을 목표로 하며 다음과 같은 기능을 포함한다.

* SR 관리
* 전자결재
* 인사관리
* 시설예약
* 권한관리
* 공지사항
* 알림
* 첨부파일
* 시스템 로그

---

## 2. 프로젝트 목표

### 기술 역량 강화

* Spring Boot 4 기반 개발 경험
* JPA 기반 Entity 설계 경험
* 실무형 ERD 설계 경험
* Spring Security 기반 권한 처리 경험
* AOP 기반 공통 기능 구현 경험
* Docker 및 AWS 배포 경험

---

### 실무형 프로젝트 경험

* 요구사항 분석
* 메뉴 설계
* 업무 프로세스 정의
* ERD 설계
* API 설계
* 화면 설계
* 구현
* 테스트
* 배포
* 성능 테스트

---

## 3. 기술 스택

### Backend

* Java 21
* Spring Boot 4.0.6
* Spring Security
* Spring Data JPA
* QueryDSL
* Validation
* Lombok

---

### Build Tool

* Gradle

Gradle Wrapper(gradlew)를 Git에 포함하여 운영체제에 관계없이 동일한 빌드 환경을 유지한다.

---

### Database

* MySQL

JPA는 객체와 테이블 매핑 용도로 사용한다.

테이블 생성은 JPA 자동 생성 기능을 사용하지 않는다.

ERD 설계 후 DDL을 직접 작성하여 테이블을 생성한다.

DDL은 Git으로 관리한다.

---

### Frontend

* Thymeleaf
* Bootstrap
* Tabler
* Vanilla Javascript

---

### UI Library

* DataTables
* jsTree
* FullCalendar
* flatpickr

---

### Infra

* Git
* GitHub
* Docker
* Docker Compose
* AWS EC2

---

## 4. 개발 철학

본 프로젝트는 단순 기능 구현보다 실무 환경과 유사한 구조를 만드는 것을 목표로 한다.

다음 원칙을 따른다.

### 1) Domain 중심 설계

업무를 기준으로 도메인을 분리한다.

예시

* SR
* 전자결재
* 인사관리
* 시설관리
* 공통영역

---

### 2) 공통 기능은 프레임워크 형태로 분리

반복적으로 사용하는 기능은 Common 영역으로 분리한다.

예시

* 로그
* 예외처리
* 파일 업로드
* 코드 조회
* 알림
* 공통 Response
* 공통 JS
* 공통 Modal
* 공통 Validation

---

### 3) Enum / Code / Master 분리

변경 가능성이 있는 데이터는 코드 테이블로 관리한다.

변경되지 않는 시스템 정책은 Enum으로 관리한다.

예시

Code 관리

* 직급
* 시설 종류
* 전자결재 종류
* 공지 범위
* SR 우선순위

Enum 관리

* SR 상태
* 버튼 정책
* Action 정책
* 알림 유형

Master 관리

* 부서
* 권한
* 메뉴

---

## 5. 공통 프레임워크(Common Framework)

프로젝트 내에 자체적인 미니 프레임워크 구조를 구성한다.

예상 구조

```text
common

├ config

├ exception

├ aop

├ log

├ response

├ security

├ file

├ notification

├ code

├ util

└ ui
```

---

### 공통 UI

공통 Layout 제공

```text
TOP

LEFT

CENTER

BOTTOM(Optional)
```

---

공통 기능

* Modal
* Toast
* Alert
* Confirm
* Loading
* Ajax
* Validation

---

### 공통 JS

예시

```javascript
Common.ajax()

Common.alert()

Common.confirm()

Modal.open()

Toast.success()
```

---

## 6. 권한 정책

권한(Role)과 업무주체(Actor)를 분리한다.

---

### Role

시스템 권한

예시

* USER
* SR_MANAGER
* DEPT_MANAGER
* DEV_MANAGER
* HR_MANAGER
* FACILITY_MANAGER
* ADMIN

---

### Actor

업무 주체

예시

SR

* 요청자
* 담당자
* 개발팀장

전자결재

* 기안자
* 승인자
* 참조자
* 대직자

직원등록

* 요청자
* 승인자

---

사용자는 여러 권한을 동시에 가질 수 있다.

권한 충돌 시 우선순위를 적용한다.

---

## 7. 데이터베이스 정책

ERD를 먼저 설계한다.

ERD 기준으로 DDL을 직접 작성한다.

DDL을 통해 테이블을 생성한다.

JPA는 Mapping 및 Query 용도로 사용한다.

---

DDL 파일은 Git으로 관리한다.

예시

```text
database

01_department.sql

02_role.sql

03_employee.sql

04_employee_role.sql

05_sr.sql

06_sr_history.sql
```

---

## 8. 보안 정책

개인정보는 암호화 저장한다.

대상

* 휴대폰번호
* 이메일
* 주소
* 상세주소

---

비밀번호는 BCrypt 기반 단방향 해시로 저장한다.

---

개인정보는 권한에 따라 마스킹하여 표시한다.

예시

```text
010-1234-5678

↓

010-****-5678
```

---

개인정보 원본 조회 시 조회 이력을 저장한다.

---

## 9. 예외 처리 및 로그 정책

예외는 GlobalExceptionHandler를 통해 공통 처리한다.

---

에러 페이지

* 400
* 403
* 404
* 500

---

에러 발생 시

* ERROR_LOG 저장
* 에러 코드 생성
* 사용자 친화적 메시지 제공

---

AOP를 이용해 공통 로그를 처리한다.

예시

* 실행 로그
* 사용자 액션 로그
* 예외 로그
* 성능 로그

---

실행시간이 일정 기준 이상일 경우 WARN 또는 ERROR 로그를 남긴다.

---

## 10. 외부 API

외부 API를 연동한다.

---

### 휴일 API

공공데이터포털 휴일 API 사용

사용 목적

* 공휴일 조회
* 휴가 계산
* SR 처리 예정일 검증
* 시설 예약 검증

휴일 정보는 배치를 통해 DB에 저장하여 사용한다.

---

### 주소 API

다음(카카오) 우편번호 API 사용

사용 목적

* 주소 검색
* 우편번호 자동 입력

외부 API 장애 시 사용자가 직접 입력할 수 있도록 한다.

---

## 11. 테스트 정책

테스트 코드를 작성한다.

대상

* Repository
* Service
* Controller

---

주요 업무 프로세스에 대한 통합 테스트를 작성한다.

예시

* SR 등록 ~ 종결
* 전자결재 상신 ~ 승인
* 직원등록 요청 ~ 승인
* 시설예약 등록 ~ 취소

---

## 12. 배포 정책

Docker 기반으로 서비스를 구성한다.

예상 구성

```text
Nginx

↓

Spring Boot

↓

MySQL
```

---

Docker Compose를 이용하여 로컬 및 운영 환경을 구성한다.

---

AWS EC2에 배포한다.

개발 환경과 운영 환경 설정은 분리하여 관리한다.

---

## 13. 문서 작성 순서

본 프로젝트는 아래 순서로 진행한다.

1. Project Overview
2. Menu Structure
3. Business Process
4. ERD Design
5. Screen Design
6. API Design
7. Architecture
8. Implementation
9. Test
10. Deployment
11. Performance Test
