# 01. Project Overview

## 1. 프로젝트 개요

본 프로젝트는 실제 운영 환경을 고려한 업무관리시스템(WMS, Work Management System)을 구축하는 것을 목표로 한다.

단순 CRUD 프로젝트가 아닌,

* 업무 프로세스(State)
* 권한(Role)
* 담당자(Actor)
* 업무 행위(Action)
* 공통 프레임워크
* 보안 및 개인정보
* 로그 및 배치

등을 포함한 실무형 프로젝트를 목표로 한다.

---

## 2. 개발 목적

### 2.1 실무형 업무관리시스템 구축

다음 기능을 포함한 통합 업무관리시스템을 구축한다.

* SR 관리
* 전자결재
* 인사관리
* 시설관리
* 공지사항
* 시스템관리

---

### 2.2 운영 환경 고려

다음 운영 요소를 포함한다.

* 문서번호 자동 발급
* 공통 코드관리
* 권한관리
* 메뉴별 권한관리
* 알림 시스템
* 배치 시스템
* 공통 파일관리
* 시스템 로그
* 에러 로그
* 개인정보 접근 로그
* 개인정보 암호화

---

### 2.3 공통 프레임워크 구축

공통 기능을 Framework 형태로 구현한다.

* AOP
* Exception
* Logging
* Notification
* File
* Security
* Code
* Document Number
* Batch

업무 로직과 공통 기능을 분리하여 유지보수성과 확장성을 높인다.

---

## 3. 기술 스택

### Backend

```text
Java 21

Spring Boot

Spring Data JPA

Spring Security

Gradle
```

---

### Database

```text
MySQL
```

Database First 정책을 적용한다.

```text
ERD

↓

DDL

↓

Entity

↓

Repository
```

JPA 자동 DDL 생성은 사용하지 않는다.

---

### Frontend

```text
Thymeleaf

Tabler UI

Javascript

CSS
```

---

### UI Library

```text
DataTables

jsTree

FullCalendar

flatpickr
```

외부 라이브러리는 직접 사용하지 않고

```text
WmsGrid

WmsTree

WmsCalendar

WmsDatePicker
```

형태의 Wrapper를 생성하여 사용한다.

---

### Deploy

```text
Docker

AWS

Linux
```

---

### Test

```text
JUnit

통합 테스트

부하 테스트
```

---

## 4. 시스템 구성

시스템은 다음과 같이 구성한다.

```text
Business

↓

Common Framework

↓

Database
```

---

### Business

업무 메뉴

```text
SR

전자결재

인사관리

시설관리

공지사항

시스템관리
```

---

### Common Framework

공통 기능

```text
AOP

Exception

Security

Notification

File

Code

Document Number

Batch

Logging
```

---

### Database

```text
MySQL
```

모든 업무 데이터는 문서번호를 가진다.

---

## 5. 문서번호 정책

모든 업무 데이터는 문서번호를 사용한다.

문서번호 형식

```text
메뉴코드-등록일자-순번

예)

SR-20260616-0001

APP-20260616-0001

FAC-20260616-0001
```

---

문서번호는 등록 시점에 생성한다.

---

문서번호 발급은 트랜잭션 내에서 수행한다.

동일 메뉴에 대해 동시에 문서번호가 생성되더라도

중복된 번호가 생성되지 않아야 한다.

---

문서번호가 중간에 누락되는 것은 허용한다.

예)

```text
SR-20260616-0001

SR-20260616-0002

(오류 발생)

SR-20260616-0004
```

---

## 6. 업무 처리 방식

업무는 다음 구조를 따른다.

```text
Role

+

Actor

+

State

↓

가능한 Action 계산

↓

Button 생성

↓

업무 처리
```

---

### Role

사용자의 시스템 권한

예)

```text
USER

SR_MANAGER

DEV_MANAGER

HR_MANAGER

DEPT_MANAGER

ADMIN
```

---

### Actor

업무 담당자

예)

```text
SR 담당자

시설 예약자

결재자
```

---

### State

업무 진행 상태

예)

```text
WAIT_APPROVAL

IN_PROGRESS

COMPLETED
```

---

### Action

상태를 변경하는 업무 행위

예)

```text
SR_REQUEST

APPROVE

REJECT

DEV_CONFIRM

UAT_REQUEST

CLOSE
```

---

Button은 직접 관리하지 않는다.

현재 상태(State)에 따라 가능한 Action을 계산한다.

화면은 Action 목록을 전달받아 버튼을 생성한다.

---

## 7. 저장(SAVE) 정책

SAVE는 Business Action이 아니다.

SAVE는 현재 데이터를 보존하는 공통 CRUD 기능이다.

SAVE는

* 상태를 변경하지 않는다.
* 업무 전달을 하지 않는다.
* 알림을 생성하지 않는다.
* 이력을 생성하지 않는다.

---

업무 Action은

* 상태 변경
* 업무 전달
* 알림 생성
* 상태 이력 저장

을 수행할 수 있다.

---

## 8. 프로젝트 목표

본 프로젝트는

단순 게시판 프로젝트가 아닌,

실제 운영 가능한 업무관리시스템 구축을 목표로 한다.

다음 항목을 경험하는 것을 목표로 한다.

* 업무 프로세스 설계
* ERD 설계
* DDL 작성
* 공통 프레임워크 구축
* 권한 관리
* 상태 기반 업무 처리
* 파일 업로드
* 알림 및 배치
* 보안 및 개인정보 처리
* Docker 배포
* AWS 운영
* 테스트 및 부하 테스트
