# 07. Architecture

## 1. 전체 아키텍처 개요

본 프로젝트는 실제 업무관리시스템을 목표로 하며 다음과 같은 구조를 따른다.

* Backend : Java 21, Spring Boot
* Database : MySQL
* ORM : JPA
* Template Engine : Thymeleaf
* UI : Tabler
* Build : Gradle
* Deploy : Docker + AWS
* Test : JUnit

시스템은 다음 세 가지 영역으로 구성한다.

1. Business Action

* SR
* 전자결재
* 인사관리
* 시설관리

2. Batch

* 미처리 결재 알림
* SR 처리기한 알림
* 문서번호 연도 초기화

3. Common Framework

* Exception
* AOP
* Logging
* Notification
* File
* Security
* Code
* Document Number

## 2. 패키지 구조

패키지는 업무 단위로 구성한다.

```text
com.wms

├─ common
│ ├─ aop
│ ├─ batch
│ ├─ config
│ ├─ exception
│ ├─ file
│ ├─ notification
│ ├─ response
│ ├─ security
│ └─ util
│
├─ sr
│ ├─ dto
│ ├─ enum
│ ├─ SrController
│ ├─ SrService
│ ├─ SrRepository
│ └─ Sr
│
├─ approval
│ ├─ dto
│ ├─ enum
│ ├─ ApprovalController
│ ├─ ApprovalService
│ ├─ ApprovalRepository
│ └─ Approval
│
├─ employee
├─ facility
├─ notice
└─ system
```

Entity는 별도 접미사를 사용하지 않는다.

예)

Role
RoleRepository
RoleService
RoleController

## 3. Layer 역할

### Controller

* 요청 수신
* 로그인 사용자 확인
* DTO 검증
* Service 호출
* View 반환

비즈니스 로직을 포함하지 않는다.

---

### Service

* 업무 로직 수행
* 상태 변경
* 알림 생성
* 이력 저장
* Repository 호출

모든 트랜잭션은 Service에서 관리한다.

---

### Repository

* DB 조회
* DB 저장

업무 로직을 포함하지 않는다.

---

### DTO

* 화면 및 API 전송 객체

---

### Entity

* 테이블과 1:1 매핑
* 테이블의 모든 컬럼 보유

## 4. Common Framework 구조

```text
common

aop
batch
config
exception
file
notification
response
security
util
```

외부 라이브러리는 Wrapper를 만들어 사용한다.

```text
WmsGrid
WmsTree
WmsCalendar
WmsDatePicker
```

## 5. 예외 처리 정책

* GlobalExceptionHandler 사용
* ErrorCode Enum 관리
* 공통 에러 페이지 사용

```text
404

500

공통 ErrorCode 페이지
```

## 6. AOP / 로그 정책

자동 로그 저장

```text
SYSTEM_LOG

ERROR_LOG

DOWNLOAD_LOG

PERSONAL_INFO_ACCESS_LOG

BATCH_LOG

FILE_DELETE_LOG
```

Controller 진입 시 SYSTEM_LOG 저장

Exception 발생 시 ERROR_LOG 저장

## 7. 트랜잭션 정책

트랜잭션은 Service에서 관리한다.

예)

```text
SR 요청

요청 저장

↓

상태 이력 저장

↓

알림 저장

↓

실패

↓

전체 Rollback
```

## 8. 권한 / 인증 정책

권한은 별도 테이블로 관리한다.

예)

```text
USER

HR_MANAGER

DEPT_MANAGER

DEV_MANAGER

SR_MANAGER

ADMIN
```

권한 중복 보유를 허용한다.

## 9. State + Role + Actor + Action 정책

### Role

사용자의 시스템 권한

예)

```text
DEV_MANAGER

SR_MANAGER

USER
```

---

### Actor

업무 담당자

예)

```text
sr.assignee_id
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

업무 행위

예)

```text
SR_REQUEST

SR_REQUEST_CANCEL

APPROVE

REJECT

ASSIGN

DEV_CONFIRM

DEV_CONFIRM_CANCEL

UAT_REQUEST

UAT_APPROVE

UAT_REJECT

CLOSE
```

SAVE는 Business Action이 아니다.

SAVE는 현재 데이터를 저장하는 공통 CRUD 기능이다.

SAVE는

* 상태 변경 없음
* 알림 없음
* 업무 전달 없음

---

버튼은 직접 관리하지 않는다.

```text
State

↓

가능 Action 계산

↓

Button 생성
```

Role과 Actor를 통해 버튼 노출 여부를 최종 결정한다.

## 10. Enum / Code / Master 정책

그룹코드

```text
대문자 + 언더바

SR_STATUS

NOTICE_TYPE

DOWNLOAD_REASON
```

상세코드

```text
영문

숫자
```

그룹코드 및 상세코드는

* 코드 변경 불가
* 명칭 변경 가능
* 물리삭제 불가
* 사용여부 관리

## 11. DB / DDL / JPA 정책

Database First

```text
ERD

↓

DDL

↓

Entity

↓

Repository
```

FK는 생성하지 않는다.

논리 FK만 관리한다.

공통컬럼

```text
created_at

created_by

updated_at

updated_by
```

## 12. 파일 업로드 정책

저장위치

```text
로컬

Docker Volume

향후 S3 확장
```

파일은 file_ref_no로 관리한다.

삭제 시

```text
FILE_DELETE_LOG 저장

↓

FILE 삭제

↓

서버 물리파일 삭제
```

물리삭제는

* 요청 전
* 요청취소
* 상신취소

상태에서만 허용한다.

## 13. 알림 / 배치 정책

알림은

```text
A → B

업무 전달 시 생성
```

예)

* 담당자 지정
* 승인
* 반려
* 상신
* 예약

알림은 영구보관한다.

---

Batch는 공통 인터페이스를 사용한다.

```java
public interface BatchTask {

    String getBatchName();

    void execute();

}
```

배치관리 메뉴에서

* 실행
* 재실행
* 자동실행 사용
* 자동실행 중지

를 지원한다.

## 14. 외부 API 연동 정책

사용 API

```text
공휴일 API

주소 검색 API
```

공휴일 API는

* SR 처리예정일
* 시설 예약

검증에 사용한다.

## 15. 보안 / 개인정보 정책

개인정보

```text
직원명

생년월일

휴대폰번호

이메일

주소

긴급연락처
```

---

암호화

```text
비밀번호
→ BCrypt

휴대폰번호

긴급연락처
```

주소는 암호화하지 않는다.

---

개인정보는 화면에서 마스킹한다.

---

직원 상세 조회는

```text
PERSONAL_INFO_ACCESS_LOG
```

를 저장한다.

---

비밀번호 정책

* 8자 이상
* 영문 + 숫자 + 특수문자
* 3개월 변경
* 직원번호 초기비밀번호

## 16. UI 공통 구조

레이아웃

```text
Top

Left

Center

Bottom
```

---

Top

* 대메뉴
* 사용자정보
* 알림

---

Left

* 중메뉴
* 소메뉴

---

Center

* 업무 화면

---

조회버튼

```text
우측 상단

조회

초기화
```

---

업무버튼

```text
우측 하단

저장

승인

반려

요청

취소
```

---

UI 컴포넌트

```text
Modal

Alert

Confirm

Toast

Loading
```

---

화면 이동

```text
Top / Left 고정

Center Fragment 교체

Thymeleaf + Ajax
```
