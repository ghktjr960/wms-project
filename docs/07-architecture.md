# 07. Architecture

## 1. 전체 아키텍처 개요

본 프로젝트는 Spring Boot와 Thymeleaf를 기반으로 하는 실무형 업무관리시스템이다.

업무 도메인과 공통 프레임워크를 분리하고, 권한(Role), 업무주체(Actor), 상태(State), 업무행위(Action)를 기반으로 업무를 처리한다.

```text
Common Framework
- AOP
- Exception
- Logging
- File
- Notification
- Batch
- Security
- Response
- Util

Work
- Dashboard
- SR
- Approval
- HR
- Facility
- System
```

본 프로젝트는 Database First 방식을 따른다.

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

## 2. 패키지 구조

최상위 패키지는 `common`과 `work`로 분리한다.

```text
com.wms
├ common
└ work
```

### 2.1 common

업무와 직접 관련 없는 공통 프레임워크 영역이다.

```text
common
├ aop
├ batch
├ config
├ exception
├ file
├ log
├ notification
├ response
├ security
├ transaction
└ util
```

### 2.2 work

실제 업무 메뉴 영역이다.

```text
work
├ dashboard
├ mypage
├ sr
├ eapp
├ facility
├ hr
└ system
```

### 2.3 업무 패키지 기본 구조

각 업무 패키지는 기본적으로 다음 구조를 가진다.

```text
work/system/role
├ RoleController
├ RoleService
├ RoleRepository
├ Role
└ dto
   ├ RoleCreateDto
   ├ RoleUpdateDto
   └ RoleSearchDto
```

Entity는 `Entity` 접미사를 사용하지 않는다.

예:

```text
Role
Employee
Department
Sr
Approval
Facility
Notice
```

### 2.4 처리이력 패키지 구조

처리이력은 업무별 테이블을 사용한다.

처리이력 패키지는 각 업무 패키지 하위에 위치한다.

처리이력 패키지는 Controller를 가지지 않는다.

예: SR

```text
work/sr/history
├ SrHistoryService
├ SrHistoryRepository
├ SrHistory
└ dto
   └ SrHistoryCreateDto
```

예: 직원등록

```text
work/hr/registration/history
├ EmployeeRegistrationHistoryService
├ EmployeeRegistrationHistoryRepository
├ EmployeeRegistrationHistory
└ dto
   └ EmployeeRegistrationHistoryCreateDto
```

예: 전자결재

```text
work/eapp/line
├ EappLineService
├ EappLineRepository
├ EappLine
└ dto
   └ EappLineCreateDto
```

시설은 별도의 History 테이블을 사용하지 않는다.

처리이력은 Business Action 발생 시 업무 Service에서 저장한다.

처리이력은 시스템 로그와 별도로 관리한다.


---

## 3. Layer 역할

### Controller

Controller는 요청과 응답을 담당한다.

* 클라이언트 요청 수신
* 요청 DTO 바인딩
* 로그인 사용자 정보 확인
* Service 호출
* Model 구성
* View 반환

Controller에는 업무 로직을 작성하지 않는다.

### Service

Service는 실제 업무 로직을 담당한다.

* 파라미터 가공
* 검증
* 상태 변경
* 업무 처리
* Repository 호출
* 알림 생성
* 처리이력 저장
* 트랜잭션 관리

트랜잭션은 Service에서 관리한다.

### Repository

Repository는 DB 작업만 담당한다.

* 저장
* 수정
* 삭제
* 조회

Repository에는 업무 판단 로직을 작성하지 않는다.

### Entity

Entity는 DB 테이블과 매핑된다.

* 테이블명
* 컬럼
* 기본 상태 변경 메서드

Entity는 테이블 기준으로 작성한다.

### DTO

DTO는 화면, 요청, 응답 단위로 필요한 데이터만 가진다.

```text
CreateDto
UpdateDto
SearchDto
ResponseDto
```

---

# 4. Common Framework 구조

공통 기능은 `common` 영역에서 관리한다.

## 4.1 AOP

AOP는 공통 관심사를 처리한다.

대상

* SYSTEM_LOG
* 성능 로그
* 감사 로그
* 개인정보 접근 로그
* 파일 다운로드 로그
* 파일 삭제 로그

---

## 4.2 Exception

예외는 공통 구조로 관리한다.

구성

* BusinessException
* ValidationException
* SystemException
* ErrorCode
* GlobalExceptionHandler

예외 발생 시

```text
Exception

↓

GlobalExceptionHandler

↓

ERROR_LOG 저장

↓

에러 페이지 반환
```

순서로 처리한다.

---

## 4.3 Log

로그는 다음 테이블로 관리한다.

```text
SYSTEM_LOG

ERROR_LOG

DOWNLOAD_LOG

PERSONAL_INFO_ACCESS_LOG

FILE_DELETE_LOG

BATCH_LOG
```

REQUEST_LOG와 ACTION_LOG는 별도 관리하지 않는다.

SYSTEM_LOG에서 통합 관리한다.

---

## 4.4 File

파일은 공통 파일 서비스를 통해 관리한다.

업무 테이블은

```text
FILE_REF_NO
```

를 통해 파일과 연결한다.

사용 메뉴

* SR
* SR_UAT
* EAPP
* EMPLOYEE_REGISTRATION
* FACILITY_RESERVATION

파일 삭제 시

```text
FILE_DELETE_LOG 저장

↓

FILE 테이블 삭제

↓

물리 파일 삭제
```

순서로 처리한다.

---

## 4.5 Notification

알림은

```text
NOTIFICATION
```

테이블에서 관리한다.

다음 이벤트 발생 시 생성한다.

* SR 요청
* SR 승인
* SR 반려
* 인수테스트 요청
* 인수테스트 승인
* 인수테스트 반려
* 전자결재 상신
* 전자결재 승인
* 전자결재 반려
* 직원등록 요청
* 직원등록 승인
* 직원등록 반려
* 시설 예약
* 시설 예약 취소

최초 조회 시

```text
FIRST_READ_DT
```

를 저장한다.

---

## 4.6 Batch

배치는

```text
Spring Scheduler

+

Cron
```

으로 관리한다.

배치 마스터 테이블은 사용하지 않는다.

배치 실행 결과만

```text
BATCH_LOG
```

에 저장한다.

예)

* SR 처리기한 알림
* 미처리 SR 알림

---

## 4.7 Response

Ajax 응답은 공통 객체를 사용한다.

```java
public class ApiResponse<T> {

    private boolean success;

    private String errorCode;

    private String message;

    private T data;

}
```

---

## 5. 예외 처리 정책

예외는 GlobalExceptionHandler에서 공통 처리한다.

예외 발생 시 ERROR_LOG를 저장한다.

ERROR_LOG는 업무 트랜잭션과 분리하여 저장한다.

즉, 업무 처리 실패로 Rollback이 발생하더라도 에러 로그는 남아야 한다.

---

## 6. AOP / 로그 정책

### 6.1 SYSTEM_LOG

시스템 업무 로그를 저장한다.

저장 항목

* 업무코드
* Action
* URI
* 처리자
* 처리부서
* 처리일시
* 처리내용
* IP
* 처리시간

업무 처리 성공 시 저장한다.

---

### 6.2 ERROR_LOG

예외 발생 시 저장한다.

저장 항목

* 업무코드
* URI
* ErrorCode
* ErrorMessage
* StackTrace
* 처리자
* 발생일시
* IP

ERROR_LOG는 업무 트랜잭션과 분리하여 저장한다.

---

### 6.3 DOWNLOAD_LOG

파일 다운로드 시 저장한다.

저장 항목

* FILE_SEQ
* FILE_REF_NO
* 업무코드
* 문서번호
* 다운로드 사용자
* 다운로드 일시

---

### 6.4 PERSONAL_INFO_ACCESS_LOG

개인정보 조회 시 저장한다.

저장 항목

* 업무코드
* 접근유형
* 조회대상
* 조회자
* URI
* IP

---

### 6.5 FILE_DELETE_LOG

파일 삭제 시 저장한다.

저장 항목

* FILE_SEQ
* FILE_REF_NO
* 파일명
* 삭제자
* 삭제일시
* 삭제결과

---

처리이력과 시스템 로그는 별도로 관리한다.

```text
처리이력

↓

누가
언제
어떤 Action을 수행했는가


시스템로그

↓

시스템이
어떤 요청을
어떻게 처리했는가
```


---

## 7. 트랜잭션 정책

트랜잭션은 Service 계층에서 관리한다.

예: SR 요청

```text
SR 상태 변경
↓
SR 처리이력 저장
↓
알림 저장
```

위 처리 중 하나라도 실패하면 전체 Rollback한다.

단, ERROR_LOG 저장은 별도 트랜잭션으로 처리한다.

---

## 8. 권한 / 인증 정책

로그인은 직원번호 + 비밀번호 방식으로 처리한다.

로그인 성공 시 다음 정보를 세션에 저장한다.

* 직원번호
* 직원명
* 부서
* 권한 목록
* 접근 가능 메뉴 목록

### 8.1 Role

Role은 시스템 권한이다.

예:

```text
USER
HR_MANAGER
DEPT_MANAGER
DEV_MANAGER
SR_MANAGER
ADMIN
```

메뉴 접근 가능 여부는 Role 기준으로 판단한다.

### 8.2 Actor

Actor는 특정 업무 건에서의 사용자 역할이다.

Actor는 별도 Enum이나 코드로 관리하지 않는다.

각 업무 테이블의 담당자, 요청자, 승인자 컬럼을 기준으로 판단한다.

예)

```text
sr.requester_id

sr.assignee_id

eapp.process_id

facility.reserver_id
```

### 8.3 State

State는 업무 진행 상태이다.

각 업무별 상태값으로 관리한다.

---

## 9. State + Role + Actor + Action 정책

업무 버튼은 다음 기준으로 결정한다.

```text
State
+
Role
+
Actor
↓
Available Action
↓
Button
```

버튼은 직접 관리하지 않는다.

현재 상태에서 가능한 Action을 계산하고, 화면은 전달받은 Action 목록을 버튼으로 표시한다.

실제 처리 요청 시 서버에서 동일한 정책을 다시 검증한다.

정책 판단이 애매한 경우 버튼은 노출하지 않는다.

---

# 10. SAVE 정책

SAVE는 업무 시작 전 상태이다.

SAVE 상태에서는

* 수정 가능
* 삭제 가능
* 요청 가능

SAVE 수행 시에도 업무별 HISTORY를 생성한다.

예)

```text
SR

↓

SR_HISTORY 생성


직원등록

↓

EMPLOYEE_REGISTRATION_HISTORY 생성
```

SAVE는 상태(State)이며,

Action으로도 사용될 수 있다.

---

# 11. State / Action 정책

## 11.1 State 정책

업무 상태(State)는 공통 코드 테이블에서 관리한다.

상태값은

```text
CODE_GROUP

↓

CODE_DETAIL
```

구조를 사용한다.

상태 추가 및 변경은 코드관리 메뉴에서 수행할 수 있다.

---

예)

### SR

```text
CODE_GROUP

SR_STATUS
```

```text
CODE_DETAIL

SAVE

REQUEST

DEPT_APPROVED

DEV_APPROVED

IN_PROGRESS

DEV_COMPLETED

UAT

DEV_MANAGER_CONFIRMED

COMPLETED
```

---

### 전자결재

```text
CODE_GROUP

EAPP_STATUS
```

```text
CODE_DETAIL

SUBMITTED

IN_PROGRESS

COMPLETED

REJECTED

CANCELED
```

---

### 직원등록

```text
CODE_GROUP

EMPLOYEE_REGISTRATION_STATUS
```

```text
CODE_DETAIL

SAVE

REQUEST

APPROVED

REJECTED
```

---

### 시설예약

```text
CODE_GROUP

FACILITY_RESERVATION_STATUS
```

```text
CODE_DETAIL

RESERVED

CANCELED
```

---

State는 화면에서 직접 비교하지 않는다.

Service 계층에서 코드값을 조회하여 업무를 처리한다.

---

## 11.2 Action 정책

Action은 Enum으로 관리한다.

Action은

```text
상태 변경

업무 전달

알림 생성

처리이력 생성
```

과 같은

업무 행위를 의미한다.

---

Action은 공통 Enum 하나로 관리하지 않는다.

업무별 Enum으로 분리한다.

예)

```text
SrAction

EappAction

FacilityAction

EmployeeRegistrationAction
```

---

### SR Action

```java
public enum SrAction {

    SAVE,

    REQUEST,

    APPROVE,

    REJECT,

    DEV_COMPLETE,

    UAT_REQUEST,

    UAT_APPROVE,

    UAT_REJECT,

    DEV_MANAGER_CONFIRM,

    COMPLETE

}
```

---

### EAPP Action

```java
public enum EappAction {

    SUBMIT,

    APPROVE,

    REJECT,

    CANCEL

}
```

---

### Facility Action

```java
public enum FacilityAction {

    RESERVE,

    CANCEL

}
```

---

### EmployeeRegistration Action

```java
public enum EmployeeRegistrationAction {

    SAVE,

    REQUEST,

    APPROVE,

    REJECT

}
```

---

## 11.3 State + Role + Actor + Action

업무 버튼은 아래 규칙으로 생성한다.

```text
State

+

Role

+

Actor

↓

Available Action 계산

↓

Button 생성
```

---

State

현재 업무 상태

예)

```text
SAVE

REQUEST

IN_PROGRESS
```

---

Role

시스템 권한

예)

```text
USER

DEV_MANAGER

HR_MANAGER

ADMIN
```

---

Actor

현재 업무에서의 사용자 역할

예)

```text
요청자

담당자

승인자

참조자
```

Actor는 별도 Enum으로 관리하지 않는다.

업무 데이터의

```text
REQUEST_ID

ASSIGNEE_ID

PROCESS_ID
```

등으로 판단한다.

---

Action

현재 사용자가 수행 가능한 업무 행위

예)

```text
APPROVE

REJECT

DEV_COMPLETE
```

---

Button

화면은

```text
Available Action
```

목록을 전달받아 버튼을 생성한다.

화면에서 버튼 노출 여부를 직접 판단하지 않는다.

서버에서도 동일한 정책을 다시 검증한다.

---

## 12. Enum / Code / Master 분리 정책

### 12.1 Enum

변경 가능성이 낮고

시스템 내부 정책에 가까운 값은 Enum으로 관리한다.

예)

* 업무 Action
* ErrorCode
* RoleType

---

### 12.2 Code

운영 중 추가/수정 가능성이 있는 값은 공통코드로 관리한다.

예)

* 업무 상태(State)
* 직급
* 시설 종류
* 우선순위
* 알림 종류

코드는

```text
CODE_GROUP

↓

CODE_DETAIL
```

구조를 사용한다.

그룹코드는 수정할 수 없다.

상세코드는 수정할 수 없다.

명칭과 사용여부만 변경할 수 있다.

---

### 12.3 Master

독립 테이블로 관리해야 하는 기준 데이터는 Master로 관리한다.

예)

* 부서
* 권한
* 메뉴
* 시설

```
```

---

## 13. DB / DDL / JPA 정책

JPA 자동 DDL 생성은 사용하지 않는다.

ERD와 DDL을 먼저 작성한 뒤 Entity를 매핑한다.

물리 FK 제약은 생성하지 않는다.

논리 FK만 문서와 컬럼명으로 관리한다.

공통 컬럼은 다음을 기본으로 한다.

```text
created_at
created_by
updated_at
updated_by
```

`use_yn`은 모든 테이블 공통 컬럼으로 강제하지 않는다.

테이블 성격에 따라 개별 적용한다.

인덱스는 초기에는 PK 중심으로 구성한다.

성능 이슈 발생 시 조회 조건에 맞게 개별 추가한다.

---

## 14. 문서번호 정책

PK와 문서번호는 분리한다.

```text
SEQ
= DB 내부 식별자

업무번호
= 사용자 화면에 노출되는 업무 식별번호
```

문서번호는 각 업무 테이블에서 관리한다.

전용 문서번호 관리 테이블은 사용하지 않는다.

---

## 문서번호 사용 예

```text
SR.SR_NO

FACILITY_RESERVATION.RESERVATION_NO
```

---

## 생성 규칙

문서번호는 등록 시점에 생성한다.

기본 형식은 아래와 같다.

```text
업무코드-등록년월일-순번
```

예)

```text
SR-20260618-0001

FAC-20260618-0001
```

---

## 순번 생성 방식

문서번호 생성 시 해당 업무 테이블에서 당해년도 문서번호를 조회한다.

```text
1. 업무 테이블에서 당해년도 마지막 문서번호 조회

2. 마지막 순번 추출

3. 순번 + 1

4. 신규 문서번호 생성
```

예)

```text
SR 테이블

↓

2026년도 SR_NO 중 마지막 순번 조회

↓

SR-20260618-0008

↓

SR-20260618-0009 생성
```

---

## 정책

문서번호는 업무 테이블별로 독립 관리한다.

문서번호 생성을 위한 별도 테이블은 생성하지 않는다.

동시 요청에 의한 중복 번호가 발생하지 않도록 Service 계층에서 검증한다.

문서번호 순번이 중간에 건너뛰는 것은 허용한다.

연도 변경 시 별도 초기화 배치는 사용하지 않는다.

당해년도 데이터 조회 결과를 기준으로 새 순번을 생성한다.


---

# 15. 처리이력 정책

처리이력은 업무별 테이블로 분리한다.

예)

```text
SR

↓

SR_HISTORY


직원등록

↓

EMPLOYEE_REGISTRATION_HISTORY


전자결재

↓

EAPP_LINE


SR 승인

↓

SANC_DETAIL
```

시설은 별도의 History 테이블을 사용하지 않는다.

---

처리이력은

* 누가
* 언제
* 어떤 Action을 수행했는지

만 저장한다.

변경 전/후 데이터는 저장하지 않는다.

처리내용은 시스템 고정 문구를 저장한다.

처리이력은 시스템 로그와 별도로 관리한다.


---

## 16. 파일 업로드 정책

파일은 공통 파일 서비스를 통해 관리한다.

사용 메뉴

* SR
* SR_UAT
* EAPP
* EMPLOYEE_REGISTRATION
* FACILITY_RESERVATION

파일은

```text
FILE_REF_NO
```

를 기준으로 업무 데이터와 연결한다.

하나의 FILE_REF_NO에는 여러 파일을 연결할 수 있다.

파일 테이블에는 개별 시퀀스를 사용한다.

파일 저장 위치는 다음 순서로 확장한다.

```text
Local

↓

Docker Volume

↓

S3
```

파일 삭제 시

```text
FILE_DELETE_LOG 저장

↓

FILE 테이블 삭제

↓

물리 파일 삭제
```

순서로 처리한다.


---

# 17. 알림 / 배치 정책

알림은 업무가 다른 사용자에게 전달되거나 사용자가 인지해야 하는 이벤트가 발생한 경우 생성한다.

예)

```text
SR 요청

SR 승인

SR 반려

인수테스트 요청

인수테스트 승인

인수테스트 반려

전자결재 상신

전자결재 승인

전자결재 반려

직원등록 요청

직원등록 승인

직원등록 반려

시설 예약

시설 예약 취소
```

---

알림은

```text
NOTIFICATION
```

테이블에서 관리한다.

알림은 중복 생성을 허용한다.

알림은 물리삭제하지 않는다.

최초 조회 시

```text
FIRST_READ_DT
```

를 저장한다.

---

## 17.1 Batch

배치는

```text
Spring Scheduler

+

Cron
```

을 사용한다.

배치 마스터 테이블은 사용하지 않는다.

배치 실행 결과는

```text
BATCH_LOG
```

에 저장한다.

---

배치 예시

```text
SR 처리기한 도래 알림

미처리 SR 알림
```

---

배치 실행 시

```text
START_DT

END_DT

RESULT

SUCCESS_COUNT

FAIL_COUNT

MESSAGE
```

를 저장한다.

배치 실패 시에도

```text
BATCH_LOG
```

는 반드시 저장한다.


---

## 18. 외부 API 연동 정책

외부 API는 다음 용도로 사용한다.

```text
공휴일 API
주소 검색 API
```

공휴일 API는 SR 처리예정일 및 시설 예약일 검증에 사용한다.

주소 검색 API는 직원 주소 등록 시 사용한다.

외부 API 호출 실패 시 사용자에게 명확한 안내 메시지를 제공한다.

---

## 19. 보안 / 개인정보 정책

개인정보 대상:

* 직원명
* 생년월일
* 비밀번호
* 휴대폰번호
* 이메일
* 주소
* 긴급연락처

암호화 저장 대상:

* 비밀번호
* 휴대폰번호
* 긴급연락처

비밀번호는 BCrypt 단방향 해시로 저장한다.

주소와 상세주소는 암호화하지 않는다.

개인정보는 화면에서 마스킹한다.

휴대폰번호와 긴급연락처는 숫자만 저장한다.

주소와 상세주소는 입력 형식 그대로 저장한다.

직원 상세 조회는 개인정보 조회로 판단하고 PERSONAL_INFO_ACCESS_LOG를 저장한다.

직원 목록 조회는 개인정보 접근 로그 대상으로 보지 않는다.

개인정보 포함 첨부파일 다운로드 시 사유 입력을 필수로 한다.

---

## 20. UI 공통 구조

화면은 다음 구조를 따른다.

```text
Top

Left

Center

Right

Bottom
```

Top

* 대메뉴
* 사용자 정보
* 알림

---

Left

* 중메뉴
* 소메뉴

---

Center

* 실제 업무 화면

---

Right

향후 확장을 위해 영역만 확보한다.

현재는 별도 기능을 정의하지 않는다.

---

Bottom

* Footer
* 시스템 정보

---

메뉴 이동 시

```text
Top

Left

Right

Bottom
```

영역은 유지한다.

```text
Center
```

영역만 변경한다.

Thymeleaf Fragment와 Ajax를 활용한다.

---

### 20.1 버튼 배치

조회 버튼

* 우측 상단

예)

* 조회
* 초기화

---

업무 버튼

* 우측 하단

예)

* 저장
* 승인
* 반려
* 요청
* 취소

---

### 20.2 UI 컴포넌트

팝업

↓

Modal

단순 안내

↓

Alert

확인/취소

↓

Confirm

외부 UI 라이브러리는 Wrapper로 감싸서 사용한다.

```text
WmsGrid       -> DataTables

WmsTree       -> jsTree

WmsCalendar   -> FullCalendar

WmsDatePicker -> flatpickr
```
