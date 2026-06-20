# 06. Common Policy

## 1. State 정책

업무 상태(State)는 공통 코드 테이블에서 관리한다.

```text
CODE_GROUP

↓

CODE_DETAIL
```

---

예)

```text
SR_STATUS

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

```text
EAPP_STATUS

SUBMITTED

IN_PROGRESS

COMPLETED

REJECTED

CANCELED
```

---

State는 화면에서 직접 비교하지 않는다.

Service 계층에서 상태값을 조회하여 업무를 처리한다.

---

## 2. Action 정책

Action은 업무 행위를 의미한다.

예)

* 상태 변경
* 승인
* 반려
* 완료
* 요청

---

Action은 Enum으로 관리한다.

공통 Enum을 사용하지 않는다.

업무별 Enum으로 분리한다.

예)

```text
SrAction

EappAction

FacilityAction

EmployeeRegistrationAction
```

---

버튼은

```text
State

+

Role

+

Actor

↓

Available Action

↓

Button 생성
```

구조를 사용한다.

화면에서 버튼 노출 여부를 직접 판단하지 않는다.

---

## 3. SAVE 정책

SAVE는 업무 시작 전 상태이다.

SAVE 상태에서는

* 수정 가능
* 삭제 가능
* 요청 가능

---

SAVE 수행 시에도

업무별 HISTORY를 생성한다.

예)

```text
SR

↓

SR_HISTORY


직원등록

↓

EMPLOYEE_REGISTRATION_HISTORY
```

---

SAVE는

* 상태 변경
* 업무 전달
* 알림 생성

을 수행하지 않는다.

---

## 4. 문서번호 정책

모든 업무 데이터는

업무별 문서번호를 가진다.

---

문서번호 형식

```text
업무코드-등록일자-순번
```

예)

```text
SR-20260620-0001

EAPP-20260620-0001

FAC-20260620-0001
```

---

문서번호 전용 테이블은 사용하지 않는다.

업무 테이블에서

당해년도 마지막 문서번호를 조회하여

신규 문서번호를 생성한다.

---

문서번호 생성은

업무 트랜잭션 내에서 수행한다.

---

문서번호 순번이 중간에 누락되는 것은 허용한다.

---

## 5. 파일 정책

파일은 공통 파일 서비스를 통해 관리한다.

---

업무 테이블은

```text
FILE_REF_NO
```

를 통해 파일과 연결한다.

---

FILE_REF_NO 형식

```text
업무코드 + 숫자
```

예)

```text
SR00000001

EAPP00000001

FAC00000001
```

---

하나의 FILE_REF_NO에는

여러 파일을 연결할 수 있다.

---

파일 사용 메뉴

* SR
* SR_UAT
* EAPP
* EMPLOYEE_REGISTRATION
* FACILITY_RESERVATION

---

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

## 6. 코드 정책

공통 코드는

```text
CODE_GROUP

↓

CODE_DETAIL
```

구조를 사용한다.

---

예)

```text
SR_STATUS

EAPP_STATUS

POSITION

FACILITY_TYPE

NOTIFICATION_TYPE
```

---

그룹코드는 수정할 수 없다.

상세코드는 수정할 수 없다.

명칭과 사용여부만 변경할 수 있다.

---

코드는 물리삭제하지 않는다.

---

## 7. 로그 정책

로그는 아래 테이블로 관리한다.

```text
SYSTEM_LOG

ERROR_LOG

DOWNLOAD_LOG

PERSONAL_INFO_ACCESS_LOG

FILE_DELETE_LOG

BATCH_LOG
```

---

처리이력과 시스템 로그는 구분한다.

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

ERROR_LOG는

업무 트랜잭션과 분리하여 저장한다.

---

## 8. 개인정보 정책

비밀번호는

```text
BCrypt
```

로 암호화한다.

---

개인정보는

```text
AES256
```

으로 암호화한다.

대상

* 휴대폰번호
* 주소
* 긴급연락처

---

이메일은

* 로그인
* 조회조건
* 중복검사

등에 사용하므로

평문으로 저장한다.

---

개인정보 조회 시

```text
PERSONAL_INFO_ACCESS_LOG
```

를 저장한다.

---

## 9. 날짜 및 시간 정책

DB는

```text
DATETIME
```

을 사용한다.

---

Java는

```text
LocalDateTime
```

을 사용한다.

---

날짜 형식

```text
yyyy-MM-dd
```

---

일시 형식

```text
yyyy-MM-dd HH:mm:ss
```

---

모든 업무 테이블은

```text
CREATED_AT

UPDATED_AT
```

를 가진다.

---

## 10. 삭제 정책

업무 데이터는

논리삭제를 기본으로 한다.

---

파일은

물리삭제를 수행한다.

---

삭제 시

관련 로그를 반드시 저장한다.

---

## 11. 공통 응답 정책

Ajax 응답은

공통 응답 객체를 사용한다.

```java
public class ApiResponse<T> {

    private boolean success;

    private String errorCode;

    private String message;

    private T data;

}
```

---

성공

```text
success = true
```

---

실패

```text
success = false

errorCode 설정

message 설정
```

---

## 12. 트랜잭션 정책

업무 처리는

```text
@Transactional
```

을 사용한다.

---

업무 실패 시

전체 롤백한다.

---

시스템 로그 저장 실패는

업무 롤백 사유가 아니다.

---

ERROR_LOG는

별도 트랜잭션으로 저장한다.

---

배치 실패 시에도

```text
BATCH_LOG
```

는 반드시 저장한다.
