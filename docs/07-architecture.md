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
├ approval
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

처리이력은 업무별로 테이블을 분리한다.

처리이력 패키지는 각 업무 패키지 하위에 `history` 패키지로 둔다.

`history` 패키지는 Controller를 가지지 않는다.

예: SR

```text
work/sr/history
├ SrHistoryService
├ SrHistoryRepository
├ SrHistory
└ dto
   └ SrHistoryCreateDto
```

예: 전자결재

```text
work/approval/history
├ ApprovalHistoryService
├ ApprovalHistoryRepository
├ ApprovalHistory
└ dto
   └ ApprovalHistoryCreateDto
```

예: 시설관리

```text
work/facility/history
├ FacilityHistoryService
├ FacilityHistoryRepository
├ FacilityHistory
└ dto
   └ FacilityHistoryCreateDto
```

예: 직원등록

```text
work/hr/registration/history
├ RegistrationHistoryService
├ RegistrationHistoryRepository
├ RegistrationHistory
└ dto
   └ RegistrationHistoryCreateDto
```

처리이력은 Business Action 발생 시 해당 업무 Service에서 HistoryService를 호출하여 저장한다.

처리이력은 시스템 로그와 별개로 관리한다.

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

## 4. Common Framework 구조

공통 기능은 `common` 영역에서 관리한다.

### 4.1 AOP

* Service 실행 로그
* Action 로그
* 성능 로그
* 감사 로그

### 4.2 Exception

* BusinessException
* ValidationException
* SystemException
* ErrorCode
* GlobalExceptionHandler

### 4.3 Log

* REQUEST_LOG
* ACTION_LOG
* ERROR_LOG
* DOWNLOAD_LOG
* PERSONAL_INFO_ACCESS_LOG
* FILE_DELETE_LOG
* BATCH_LOG

### 4.4 File

* 파일 업로드
* 파일 다운로드
* 파일 삭제
* 파일 다운로드 로그
* 파일 삭제 로그

### 4.5 Notification

* 알림 생성
* 알림 목록 조회
* 읽음 처리

### 4.6 Batch

배치는 공통 인터페이스를 사용한다.

```java
public interface BatchTask {
    String getBatchName();
    void execute();
}
```

배치관리 메뉴에서는 공통 인터페이스를 통해 배치를 실행한다.

### 4.7 Response

Ajax 응답은 공통 응답 객체를 사용한다.

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

### 6.1 REQUEST_LOG

모든 HTTP 요청을 기록한다.

* URL
* HTTP Method
* 사용자
* IP
* 요청시간
* 응답시간
* 처리시간
* 결과

### 6.2 ACTION_LOG

Service 업무 실행 로그를 저장한다.

* 사용자
* 메뉴
* 메서드
* 실행시간
* 성공/실패
* 처리내용

### 6.3 ERROR_LOG

예외 발생 시 저장한다.

* 사용자
* URL
* Exception명
* ErrorCode
* StackTrace
* 발생시간

### 6.4 처리이력과 시스템 로그 구분

처리이력은 업무 Action에 대한 이력이다.

시스템 로그는 시스템 동작에 대한 로그이다.

두 로그는 별도로 관리한다.

```text
처리이력
= 누가, 언제, 어떤 업무를 어떤 Action으로 처리했는가

시스템 로그
= 어떤 요청/메서드/시스템 동작이 수행되었는가
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

예:

```text
sr.requester_id
sr.assignee_id
approval.approver_id
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

## 10. SAVE와 Business Action 구분

SAVE는 Business Action이 아니다.

SAVE는 현재 데이터를 보존하는 공통 CRUD 기능이다.

SAVE는 다음 처리를 하지 않는다.

* 상태 변경
* 업무 전달
* 알림 생성
* 처리이력 저장

Business Action은 다음 처리를 수행할 수 있다.

* 상태 변경
* 업무 전달
* 알림 생성
* 처리이력 저장

예:

```text
SAVE
= 저장

SR_REQUEST
= SR요청

APPROVAL_REQUEST
= 상신

RESERVE
= 예약
```

---

## 11. 업무별 Action 정책

Action은 공통 Enum 하나로 관리하지 않는다.

업무별 Enum으로 분리한다.

예:

```text
SrAction
ApprovalAction
FacilityAction
RegistrationAction
```

### 11.1 SR Action

```text
SR_REQUEST             SR요청
SR_REQUEST_CANCEL      SR요청취소
APPROVE                승인
REJECT                 반려
ASSIGN                 담당자 지정
DEV_CONFIRM            개발확정
DEV_CONFIRM_CANCEL     개발확정취소
UAT_REQUEST            인수테스트 요청
UAT_APPROVE            인수테스트 승인
UAT_REJECT             인수테스트 반려
CLOSE                  개발종료
EXPORT_EXCEL           엑셀 다운로드
```

### 11.2 시설 Action

```text
RESERVE                예약
CHANGE                 예약 변경
CANCEL                 예약 취소
EXPORT_EXCEL           엑셀 다운로드
```

### 11.3 전자결재 Action

```text
APPROVAL_REQUEST        상신
APPROVAL_REQUEST_CANCEL 상신취소
APPROVE                 승인
REJECT                  반려
EXPORT_EXCEL            엑셀 다운로드
```

---

## 12. Enum / Code / Master 분리 정책

### 12.1 Enum

변경 가능성이 낮고 시스템 내부 정책에 가까운 값은 Enum으로 관리한다.

예:

* SR 상태
* SR Action
* 결재 상태
* 시설 예약 상태
* 알림 유형

### 12.2 Code

운영 중 추가/수정 가능성이 있는 값은 공통코드로 관리한다.

예:

* 직급
* 결재 종류
* 시설 종류
* 공지 범위
* 다운로드 사유

그룹코드는 대문자 + 언더바 형식을 사용한다.

```text
SR_PRIORITY
NOTICE_SCOPE
DOWNLOAD_REASON
```

상세코드는 영문, 숫자만 허용한다.

그룹코드와 상세코드는 물리삭제하지 않는다.

코드값은 수정할 수 없다.

명칭과 사용여부만 수정할 수 있다.

### 12.3 Master

독립 테이블로 관리해야 하는 기준 데이터는 Master로 관리한다.

예:

* 부서
* 권한
* 메뉴
* 시설

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
seq
= DB 내부 식별자

document_no
= 사용자 화면에 노출되는 업무 식별번호
```

문서번호 형식은 다음을 기본으로 한다.

```text
메뉴코드-등록년월일-순번

예)
SR-20260616-0001
APP-20260616-0001
FAC-20260616-0001
```

문서번호는 등록 시점에 생성한다.

문서번호 순번은 DOCUMENT_NO 테이블에서 메뉴별로 관리한다.

문서번호 발급은 트랜잭션 내에서 수행한다.

동시 요청이 발생해도 동일 메뉴에서 중복 순번이 생성되지 않도록 구현한다.

문서번호 순번이 중간에 건너뛰는 것은 허용한다.

매년 1월 1일 문서번호 초기화 배치를 실행한다.

---

## 15. 처리이력 정책

시스템관리 메뉴를 제외한 모든 업무 메뉴는 처리이력을 저장한다.

처리이력은 업무별 이력 테이블로 분리한다.

예:

```text
SR_HISTORY
APPROVAL_HISTORY
FACILITY_HISTORY
EMPLOYEE_REGISTRATION_HISTORY
```

처리이력은 Business Action 발생 시 항상 저장한다.

처리이력에는 다음 내용을 저장한다.

* 누가
* 언제
* 어떤 업무를
* 어떤 Action으로
* 어떤 상태에서 어떤 상태로
* 어떤 의견으로 처리했는가

처리이력은 시스템 로그와 별개로 동작한다.

처리이력 화면 노출은 SR 메뉴만 제공한다.

SR 상세화면에는 처리이력 버튼을 제공하고, 클릭 시 처리이력 팝업을 표출한다.

그 외 메뉴는 처리이력을 저장하지만 화면에는 노출하지 않는다.

---

## 16. 파일 업로드 정책

파일은 공통 파일 서비스를 통해 관리한다.

사용 메뉴:

* SR
* 전자결재
* 공지사항
* 직원등록 요청

직원등록 요청에서는 보안서약서 등 입사 관련 파일을 첨부한다.

파일은 `file_ref_no`로 업무 데이터와 연결한다.

하나의 `file_ref_no`에 여러 파일을 연결할 수 있다.

파일 테이블에는 개별 시퀀스를 둔다.

파일 저장 위치는 다음 순서로 확장한다.

```text
로컬 디스크
↓
Docker Volume
↓
S3
```

파일 삭제 시 다음 순서로 처리한다.

```text
FILE_DELETE_LOG 저장
↓
FILE 테이블 삭제
↓
서버 물리 파일 삭제
```

파일 삭제 실패 시 실패 사유를 저장한다.

---

## 17. 알림 / 배치 정책

알림은 업무가 다른 사용자에게 전달되거나 상대방이 인지해야 하는 이벤트가 발생한 경우 생성한다.

예:

* SR요청
* 담당자 지정
* 승인
* 반려
* 인수테스트 요청
* 시설 예약
* 시설 변경
* 시설 취소

알림은 중복 생성을 허용한다.

알림은 영구 보관한다.

알림 목록에서 조회하면 읽음 처리한다.

### 17.1 배치

배치는 공통 BatchTask 인터페이스를 구현한다.

배치관리 메뉴에서 수동 실행 및 재실행을 지원한다.

배치 실행 로그를 저장한다.

배치 예:

* 미처리 결재 알림
* SR 처리기한 도래 알림
* 직원 상태 갱신
* 문서번호 연도 초기화

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
Bottom
```

Top에는 대메뉴, 사용자 정보, 알림을 표시한다.

Left에는 중메뉴와 소메뉴를 표시한다.

Center에는 실제 업무 화면을 표시한다.

메뉴 이동 시 Top / Left는 유지하고 Center 영역만 변경한다.

Thymeleaf Fragment와 Ajax를 활용한다.

### 20.1 버튼 배치

조회 관련 버튼은 우측 상단에 배치한다.

예:

* 조회
* 초기화

업무 액션 버튼은 우측 하단에 배치한다.

예:

* 저장
* 승인
* 반려
* 요청
* 취소

### 20.2 UI 컴포넌트

팝업 성격은 Modal을 사용한다.

단순 안내는 Alert를 사용한다.

확인/취소는 Confirm을 사용한다.

외부 UI 라이브러리는 Wrapper로 감싸서 사용한다.

```text
WmsGrid       DataTables
WmsTree       jsTree
WmsCalendar   FullCalendar
WmsDatePicker flatpickr
```
