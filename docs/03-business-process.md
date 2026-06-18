# 03. Business Process

# 1. 공통 정책

모든 업무는 아래 구조를 따른다.

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

버튼은 화면에서 직접 관리하지 않는다.

현재 상태(State)에 따라 가능한 Action을 계산한다.

화면은 전달받은 Action 목록을 기반으로 버튼을 생성한다.

Role과 Actor를 통해 버튼 노출 여부를 최종 결정한다.

---

## SAVE 정책

SAVE는 업무 시작 전 상태이다.

SAVE 상태에서는

* 수정 가능
* 삭제 가능
* 요청 가능

SAVE 수행 시에도 업무별 HISTORY를 생성한다.

---

## History 정책

모든 업무는 Action 발생 시 이력을 생성한다.

이력은 업무별 테이블에서 관리한다.

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

---

변경 전/후 데이터는 저장하지 않는다.

누가 언제 어떤 작업을 했는지만 저장한다.

처리내용은 시스템 고정 문구를 저장한다.

---

# 2. SR

## 개요

SR은 저장 후 요청할 수 있다.

SR 승인 프로세스와 인수테스트 프로세스를 분리하여 관리한다.

승인은

```text
SANC

SANC_DETAIL
```

에서 관리한다.

인수테스트는

```text
SR_UAT
```

에서 별도로 관리한다.

---

# 상태 흐름

```text
SAVE

↓

REQUEST

↓

DEPT_APPROVED

또는

REJECTED

↓

DEV_APPROVED

또는

REJECTED

↓

IN_PROGRESS

↓

DEV_COMPLETED

↓

UAT

↓

DEV_MANAGER_CONFIRMED

↓

COMPLETED
```

---

## 부서 반려

```text
DEPT_APPROVED

↓

REJECTED

↓

SAVE
```

---

## 개발팀 반려

```text
DEV_APPROVED

↓

REJECTED

↓

SAVE
```

---

## 인수테스트 반려

```text
UAT

↓

UAT_REJECTED

↓

DEV_COMPLETED
```

---

# SR 상태 설명

| 상태                    | 설명      |
| --------------------- | ------- |
| SAVE                  | 저장      |
| REQUEST               | 요청      |
| DEPT_APPROVED         | 부서 승인   |
| DEV_APPROVED          | 개발팀 승인  |
| IN_PROGRESS           | 개발 진행   |
| DEV_COMPLETED         | 개발 완료   |
| UAT                   | 인수테스트   |
| DEV_MANAGER_CONFIRMED | 개발팀장 확인 |
| COMPLETED             | SR 종료   |

---

# Action

| Action              | 설명       |
| ------------------- | -------- |
| SAVE                | 저장       |
| REQUEST             | 요청       |
| APPROVE             | 승인       |
| REJECT              | 반려       |
| DEV_COMPLETE        | 개발 완료    |
| UAT_REQUEST         | 인수테스트 요청 |
| UAT_APPROVE         | 인수테스트 승인 |
| UAT_REJECT          | 인수테스트 반려 |
| DEV_MANAGER_CONFIRM | 개발팀장 확인  |
| COMPLETE            | SR 종료    |

---

# 버튼 정책

## SAVE

```text
요청

삭제
```

---

## REQUEST

```text
요청취소
```

요청취소 시

```text
SAVE
```

상태로 변경한다.

---

## DEPT_APPROVED

```text
승인

반려
```

---

## DEV_APPROVED

```text
승인

반려
```

---

개발팀 승인 화면에서는

```text
담당자 선택
```

입력 항목을 제공한다.

---

승인 버튼 클릭 시 아래 조건을 검증한다.

```text
담당자 선택 여부

↓

선택 안함

↓

오류 메시지 출력

↓

승인 불가
```

---

담당자가 선택된 경우

```text
담당자 저장

↓

승인 처리

↓

DEV_APPROVED

↓

IN_PROGRESS
```

순서로 처리한다.

---

## IN_PROGRESS

```text
개발완료
```

---

## DEV_COMPLETED

```text
인수테스트 요청
```

---

## UAT

```text
인수테스트 승인

인수테스트 반려
```

---

## DEV_MANAGER_CONFIRMED

```text
확인
```

개발팀장은

```text
확인 버튼
```

만 존재한다.

---

## COMPLETED

```text
버튼 없음
```

---

# 승인 정책

SR 승인은

```text
SANC
```

에서 현재 상태를 관리한다.

```text
SANC_DETAIL
```

에서

* 요청
* 승인
* 반려

를 관리한다.

---

# 인수테스트 정책

인수테스트는

```text
SR_UAT
```

에서 관리한다.

---

개발자는

```text
테스트 방법

테스트 설명

첨부파일
```

을 등록한다.

---

요청자는

```text
테스트 결과

확인서 첨부파일
```

을 등록한다.

---

개발팀장은

```text
확인
```

버튼만 존재한다.

---

인수테스트 반려 후 재요청 시

```text
기존 Row 수정

X
```

```text
신규 Row 생성

O
```

으로 처리한다.

---

# 알림 정책

다음 업무 전달 시 알림을 생성한다.

```text
SR 요청

부서 승인

부서 반려

개발팀 승인

개발팀 반려

인수테스트 요청

인수테스트 승인

인수테스트 반려

개발팀장 확인
```

알림은

```text
NOTIFICATION
```

테이블에서 관리한다.

# 3. 전자결재(EAPP)

## 개요

전자결재는 임시저장을 사용하지 않는다.

문서 등록 즉시 상신하며,

승인자는 순차적으로 결재를 진행한다.

승인자는 최대 3명,

참조자는 최대 5명까지 등록할 수 있다.

전자결재 이력은

```text
EAPP_LINE
```

에서 관리한다.

별도의 History 테이블은 사용하지 않는다.

---

# 상태 흐름

## 승인자 1명

```text
SUBMITTED

↓

APPROVED

↓

COMPLETED
```

---

## 승인자 2명 이상

```text
SUBMITTED

↓

1차 승인

↓

IN_PROGRESS

↓

중간 승인

↓

IN_PROGRESS

↓

최종 승인

↓

COMPLETED
```

---

## 반려

```text
SUBMITTED

↓

REJECTED
```

---

## 본인취소

```text
SUBMITTED

↓

CANCELED
```

---

# 상태 설명

| 상태          | 설명     |
| ----------- | ------ |
| SUBMITTED   | 상신     |
| IN_PROGRESS | 결재 진행중 |
| COMPLETED   | 완료     |
| REJECTED    | 반려     |
| CANCELED    | 본인취소   |

---

# Action

| Action  | 설명   |
| ------- | ---- |
| SUBMIT  | 상신   |
| APPROVE | 승인   |
| REJECT  | 반려   |
| CANCEL  | 본인취소 |

---

# 버튼 정책

## SUBMITTED

### 상신자

```text
상신취소
```

승인 전까지만 가능하다.

---

### 승인자

```text
승인

반려
```

---

### 참조자

```text
버튼 없음
```

---

## IN_PROGRESS

### 현재 승인자

```text
승인

반려
```

---

### 참조자

```text
버튼 없음
```

---

## COMPLETED

```text
버튼 없음
```

---

## REJECTED

```text
버튼 없음
```

---

## CANCELED

```text
버튼 없음
```

---

# 결재선 정책

승인자는

```text
최대 3명
```

까지 등록할 수 있다.

---

참조자는

```text
최대 5명
```

까지 등록할 수 있다.

---

승인자는

```text
LINE_ORDER
```

순서대로 승인한다.

---

대직 여부는

```text
EMPLOYEE_ID

!=

PROCESS_ID
```

인 경우 대직 처리로 판단한다.

---

# 알림 정책

다음 업무 전달 시 알림을 생성한다.

```text
상신

승인

반려

본인취소
```

알림은

```text
NOTIFICATION
```

테이블에서 관리한다.

---

# 4. 직원등록(HR)

## 개요

직원등록은 저장 후 요청할 수 있다.

인사팀은 모든 부서의 신규 직원을 등록할 수 있다.

승인 완료 시

```text
EMPLOYEE
```

테이블에 실제 직원 정보를 생성한다.

직원등록 승인 프로세스는

별도 승인 테이블 없이

```text
EMPLOYEE_REGISTRATION

EMPLOYEE_REGISTRATION_HISTORY
```

로 관리한다.

---

# 상태 흐름

```text
SAVE

↓

REQUEST

↓

APPROVED

또는

REJECTED

↓

SAVE
```

---

# 상태 설명

| 상태       | 설명    |
| -------- | ----- |
| SAVE     | 저장    |
| REQUEST  | 등록 요청 |
| APPROVED | 승인    |
| REJECTED | 반려    |

---

# Action

| Action  | 설명    |
| ------- | ----- |
| SAVE    | 저장    |
| REQUEST | 등록 요청 |
| APPROVE | 승인    |
| REJECT  | 반려    |

---

# 버튼 정책

## SAVE

```text
등록요청

삭제
```

---

## REQUEST

### 인사팀

```text
승인

반려
```

---

### 일반 사용자

```text
버튼 없음
```

---

## APPROVED

```text
버튼 없음
```

---

## REJECTED

```text
수정

등록요청
```

반려 시

```text
SAVE
```

상태로 변경한다.

---

# 승인 정책

승인 완료 시

```text
EMPLOYEE
```

테이블에 데이터를 생성한다.

---

생성 항목

```text
직원번호

초기 비밀번호

기본권한(USER)

직원상태(ACTIVE)
```

은 시스템에서 자동 생성한다.

---

신청 단계에서는

```text
로그인ID

비밀번호

직급

입사일

권한
```

을 입력받지 않는다.

---

# History 정책

다음 Action 수행 시

```text
EMPLOYEE_REGISTRATION_HISTORY
```

를 생성한다.

```text
SAVE

REQUEST

APPROVE

REJECT
```

변경 전/후 데이터는 저장하지 않는다.

누가 언제 어떤 작업을 했는지만 저장한다.

---

# 알림 정책

다음 업무 전달 시 알림을 생성한다.

```text
등록요청

승인

반려
```

알림은

```text
NOTIFICATION
```

테이블에서 관리한다.

# 5. 시설(Facility)

## 개요

시설은

```text
회의실

차량

교육장
```

을 관리한다.

시설별 상세 속성은 별도로 관리하지 않는다.

시설 종류에 따라 예약 내용을 자유롭게 입력한다.

예약 정보는

```text
FACILITY

FACILITY_RESERVATION
```

에서 관리한다.

---

# 상태 흐름

```text
RESERVED

↓

CANCELED
```

또는

```text
RESERVED

↓

사용 완료
```

---

# 상태 설명

| 상태       | 설명   |
| -------- | ---- |
| RESERVED | 예약   |
| CANCELED | 예약취소 |

---

# Action

| Action  | 설명   |
| ------- | ---- |
| RESERVE | 예약   |
| CANCEL  | 예약취소 |

---

# 버튼 정책

## RESERVED

```text
예약취소
```

---

## CANCELED

```text
버튼 없음
```

---

# 예약 정책

예약 제목은 사용하지 않는다.

---

차량 예약의 경우

```text
출발지

도착지

사용목적
```

등은

```text
RESERVATION_CONTENT
```

에 자유롭게 작성한다.

---

시설 종류별

```text
차량번호

회의실 번호

교육시간
```

같은 별도 컬럼은 생성하지 않는다.

---

예약은 물리삭제하지 않는다.

예약 취소 시

```text
RESERVATION_STATUS_CD

↓

CANCELED
```

로 변경한다.

---

# 알림 정책

다음 업무 전달 시 알림을 생성한다.

```text
예약 완료

예약 취소
```

수신자

```text
예약자
```

알림은

```text
NOTIFICATION
```

테이블에서 관리한다.

---

# 6. 알림(Notification)

## 개요

알림은 업무 전달 시 생성한다.

알림은

```text
NOTIFICATION
```

테이블에서 관리한다.

---

# 생성 정책

다음 업무 전달 시 알림을 생성한다.

```text
SR

전자결재

직원등록

시설예약
```

---

예)

```text
요청자

↓

SR 요청

↓

개발팀

↓

알림 생성
```

---

```text
상신자

↓

전자결재 상신

↓

승인자

↓

알림 생성
```

---

# 읽음 정책

알림은 영구 보관한다.

---

최초 조회 시

```text
FIRST_READ_DT
```

를 저장한다.

---

이미 읽은 알림은

```text
FIRST_READ_DT
```

를 변경하지 않는다.

---

# 삭제 정책

알림은 물리삭제하지 않는다.

---

사용자는

```text
알림 삭제

알림 숨김
```

기능을 사용할 수 없다.

---

# 7. 배치(Batch)

## 개요

배치는

```text
Spring Scheduler

+

Cron
```

으로 관리한다.

배치 마스터 테이블은 사용하지 않는다.

실행 결과만

```text
BATCH_LOG
```

에 저장한다.

---

# SR 처리기한 알림

## 대상

```text
처리기한 하루 전

처리기한 당일
```

---

## 실행

```text
매일 오전 07:00
```

---

## 처리

```text
SR 조회

↓

대상 확인

↓

알림 생성

↓

BATCH_LOG 저장
```

---

# 미처리 SR 알림

## 대상

```text
개발 진행중

인수테스트 대기

장기 미처리 SR
```

---

## 실행

```text
매일 오전 07:00
```

---

## 처리

```text
미처리 SR 조회

↓

담당자 확인

↓

알림 생성

↓

BATCH_LOG 저장
```

---

# BATCH_LOG 저장 정책

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

---

배치 실패 시에도

```text
BATCH_LOG
```

는 반드시 저장한다.

---

배치는

```text
재실행 가능
```

해야 한다.
