# 04-1. 공통(Common)

## 1. 공통 설계 규칙

### 1.1 설계 원칙

* Database First 방식으로 설계한다.
* JPA 자동 DDL 생성 기능은 사용하지 않는다.
* 물리 FK는 생성하지 않고 논리 FK로 관리한다.
* 테이블명 및 컬럼명은 대문자 + 언더바 형식을 사용한다.
* PK는 `테이블명_SEQ` 형식을 사용한다.
* 물리삭제는 기본적으로 금지한다.
* 첨부파일은 FILE_REF_NO를 기준으로 관리한다.
* 코드성 데이터는 CODE_GROUP, CODE_DETAIL에서 관리한다.

---

### 1.2 공통 컬럼

모든 테이블은 아래 공통 컬럼을 사용한다.

| 컬럼명       | 정의       |
| --------- | -------- |
| CREATE_DT | 등록일시     |
| CREATE_ID | 등록자 직원번호 |
| UPDATE_DT | 수정일시     |
| UPDATE_ID | 수정자 직원번호 |

---

# 2. EMPLOYEE

직원 마스터 정보를 관리한다.

### 정책

* PK는 EMPLOYEE_SEQ를 사용한다.
* EMPLOYEE_ID는 직원번호이자 로그인 ID이다.
* EMPLOYEE_ID는 UNIQUE로 관리한다.
* 비밀번호는 암호화하여 저장한다.

### 컬럼 정의

| 컬럼명                | 정의           |
| ------------------ | ------------ |
| EMPLOYEE_SEQ       | 직원 PK        |
| EMPLOYEE_ID        | 직원번호 및 로그인ID |
| PASSWORD           | 비밀번호         |
| EMPLOYEE_NM        | 직원명          |
| DEPT_CD            | 부서코드         |
| POSITION_CD        | 직급코드         |
| BIRTH_DT           | 생년월일         |
| PHONE_NO           | 휴대폰번호        |
| EMERGENCY_PHONE_NO | 긴급연락처        |
| EMAIL              | 이메일          |
| ZIP_CODE           | 우편번호         |
| ADDRESS            | 주소           |
| DETAIL_ADDRESS     | 상세주소         |
| HIRE_DT            | 입사일          |
| RETIRE_DT          | 퇴사일          |
| EMPLOYEE_STATUS_CD | 직원상태         |
| LOGIN_FAIL_CNT     | 로그인 실패횟수     |
| LOGIN_LOCK_YN      | 로그인 잠금여부     |
| LOGIN_LOCK_DT      | 로그인 잠금일시     |
| PASSWORD_CHANGE_DT | 비밀번호 변경일     |
| CREATE_DT          | 등록일시         |
| CREATE_ID          | 등록자 직원번호     |
| UPDATE_DT          | 수정일시         |
| UPDATE_ID          | 수정자 직원번호     |

---

# 3. EMPLOYEE_HISTORY

직원정보 변경 이력을 관리한다.

### 정책

* 변경 전/후 데이터는 저장하지 않는다.
* 누가 언제 어떤 작업을 했는지만 저장한다.
* 처리 내용은 시스템 고정 문구를 저장한다.

### 컬럼 정의

| 컬럼명                  | 정의         |
| -------------------- | ---------- |
| EMPLOYEE_HISTORY_SEQ | 직원이력 PK    |
| EMPLOYEE_ID          | 대상 직원번호    |
| ACTION_CD            | 수행한 작업코드   |
| PROCESS_ID           | 처리자 직원번호   |
| PROCESS_DEPT_CD      | 처리 당시 부서코드 |
| PROCESS_DT           | 처리일시       |
| PROCESS_COMMENT      | 처리내용       |
| CREATE_DT            | 등록일시       |
| CREATE_ID            | 등록자 직원번호   |

---

# 4. DEPARTMENT

부서 정보를 관리한다.

### 정책

* DEPT_CD는 UNIQUE로 관리한다.
* 부서 삭제는 하지 않는다.
* 부서 폐지일과 사용여부로 관리한다.

### 컬럼 정의

| 컬럼명            | 정의       |
| -------------- | -------- |
| DEPT_SEQ       | 부서 PK    |
| DEPT_CD        | 부서코드     |
| DEPT_NM        | 부서명      |
| PARENT_DEPT_CD | 상위 부서코드  |
| DEPT_LEVEL     | 부서 레벨    |
| SORT_ORDER     | 정렬순서     |
| DEPT_CREATE_DT | 부서 생성일   |
| DEPT_CLOSE_DT  | 부서 폐지일   |
| USE_YN         | 사용여부     |
| CREATE_DT      | 등록일시     |
| CREATE_ID      | 등록자 직원번호 |
| UPDATE_DT      | 수정일시     |
| UPDATE_ID      | 수정자 직원번호 |

---

# 5. ROLE

권한 정보를 관리한다.

### 정책

* ROLE_CD는 UNIQUE로 관리한다.
* 권한 삭제는 사용여부로 관리한다.

### 컬럼 정의

| 컬럼명       | 정의       |
| --------- | -------- |
| ROLE_SEQ  | 권한 PK    |
| ROLE_CD   | 권한코드     |
| ROLE_NM   | 권한명      |
| ROLE_DESC | 권한 설명    |
| USE_YN    | 사용여부     |
| CREATE_DT | 등록일시     |
| CREATE_ID | 등록자 직원번호 |
| UPDATE_DT | 수정일시     |
| UPDATE_ID | 수정자 직원번호 |

---

# 6. EMPLOYEE_ROLE

직원과 권한을 매핑한다.

### 컬럼 정의

| 컬럼명               | 정의       |
| ----------------- | -------- |
| EMPLOYEE_ROLE_SEQ | 직원권한 PK  |
| ROLE_CD           | 권한코드     |
| EMPLOYEE_ID       | 직원번호     |
| CREATE_DT         | 등록일시     |
| CREATE_ID         | 등록자 직원번호 |

---

# 7. MENU

메뉴 정보를 관리한다.

### 정책

* MENU_CD는 UNIQUE로 관리한다.
* MENU_CD는 최초 등록 후 수정할 수 없다.
* 상위 메뉴는 PARENT_MENU_CD로 관리한다.

### 컬럼 정의

| 컬럼명            | 정의       |
| -------------- | -------- |
| MENU_SEQ       | 메뉴 PK    |
| MENU_CD        | 메뉴코드     |
| MENU_NM        | 메뉴명      |
| PARENT_MENU_CD | 상위 메뉴코드  |
| MENU_LEVEL     | 메뉴 레벨    |
| MENU_URL       | 메뉴 URL   |
| SORT_ORDER     | 정렬순서     |
| USE_YN         | 사용여부     |
| CREATE_DT      | 등록일시     |
| CREATE_ID      | 등록자 직원번호 |
| UPDATE_DT      | 수정일시     |
| UPDATE_ID      | 수정자 직원번호 |

---

# 8. MENU_ROLE

메뉴별 접근 권한을 관리한다.

### 컬럼 정의

| 컬럼명           | 정의       |
| ------------- | -------- |
| MENU_ROLE_SEQ | 메뉴권한 PK  |
| MENU_CD       | 메뉴코드     |
| ROLE_CD       | 권한코드     |
| CREATE_DT     | 등록일시     |
| CREATE_ID     | 등록자 직원번호 |

---

# 9. CODE_GROUP

코드 그룹을 관리한다.

### 정책

* CODE_GROUP_CD는 UNIQUE로 관리한다.
* CODE_GROUP_CD는 수정할 수 없다.

### 컬럼 정의

| 컬럼명            | 정의       |
| -------------- | -------- |
| CODE_GROUP_SEQ | 코드그룹 PK  |
| CODE_GROUP_CD  | 그룹코드     |
| CODE_GROUP_NM  | 그룹코드명    |
| USE_YN         | 사용여부     |
| CREATE_DT      | 등록일시     |
| CREATE_ID      | 등록자 직원번호 |
| UPDATE_DT      | 수정일시     |
| UPDATE_ID      | 수정자 직원번호 |

---

# 10. CODE_DETAIL

상세 코드를 관리한다.

### 정책

* CODE_DETAIL_CD는 그룹 내 UNIQUE로 관리한다.
* CODE_DETAIL_CD는 수정할 수 없다.

### 컬럼 정의

| 컬럼명             | 정의       |
| --------------- | -------- |
| CODE_DETAIL_SEQ | 상세코드 PK  |
| CODE_GROUP_CD   | 그룹코드     |
| CODE_DETAIL_CD  | 상세코드     |
| CODE_DETAIL_NM  | 상세코드명    |
| SORT_ORDER      | 정렬순서     |
| USE_YN          | 사용여부     |
| CREATE_DT       | 등록일시     |
| CREATE_ID       | 등록자 직원번호 |
| UPDATE_DT       | 수정일시     |
| UPDATE_ID       | 수정자 직원번호 |

---

# 11. FILE

첨부파일 정보를 관리한다.

### 정책

* FILE_REF_NO를 기준으로 관리한다.
* FILE_REF_NO는 업무코드 + 숫자(8자리 내외) 형식으로 생성한다.

예)

* SR00000001
* UAT00000001
* EAPP00000001

### 컬럼 정의

| 컬럼명              | 정의         |
| ---------------- | ---------- |
| FILE_SEQ         | 파일 PK      |
| FILE_REF_NO      | 파일 참조번호    |
| ORIGINAL_FILE_NM | 원본 파일명     |
| STORED_FILE_NM   | 저장 파일명     |
| FILE_PATH        | 파일 저장 경로   |
| FILE_EXT         | 파일 확장자     |
| FILE_SIZE        | 파일 크기      |
| PRIVACY_YN       | 개인정보 포함 여부 |
| CREATE_DT        | 등록일시       |
| CREATE_ID        | 등록자 직원번호   |

---

# 12. NOTIFICATION

사용자 알림을 관리한다.

### 정책

* 알림은 물리삭제하지 않는다.
* 알림 링크는 별도 컬럼 없이 BIZ_CD와 업무 데이터로 이동한다.
* 최초 조회 시 FIRST_READ_DT를 저장한다.

### 컬럼 정의

| 컬럼명              | 정의       |
| ---------------- | -------- |
| NOTIFICATION_SEQ | 알림 PK    |
| NOTIFICATION_CD  | 알림 코드    |
| BIZ_CD           | 업무 코드    |
| SENDER_ID        | 발신자 직원번호 |
| RECEIVER_ID      | 수신자 직원번호 |
| TITLE            | 알림 제목    |
| CONTENT          | 알림 내용    |
| FIRST_READ_DT    | 최초 읽은 일시 |
| CREATE_DT        | 등록일시     |
| CREATE_ID        | 등록자 직원번호 |

# 04-2. SR

# 1. SR

SR 요청 및 개발 진행 정보를 관리한다.

### 정책

* SR은 저장 후 요청할 수 있다.
* 저장 상태에서는 수정 및 삭제가 가능하다.
* 요청 상태 이후에는 진행 상태에 따라 버튼을 제어한다.
* 요청 취소는 별도 상태를 사용하지 않는다.
* 요청 취소 시 SAVE 상태로 되돌린다.
* 요청 당시 부서를 별도로 저장한다.
* 개발 담당자를 지정하여 관리한다.
* 우선순위 및 분류는 코드관리에서 관리한다.

### 컬럼 정의

| 컬럼명             | 정의          |
| --------------- | ----------- |
| SR_SEQ          | SR PK       |
| SR_NO           | SR 문서번호     |
| SR_TITLE        | SR 제목       |
| SR_CONTENT      | SR 내용       |
| SR_CATEGORY_CD  | SR 분류 코드    |
| SR_PRIORITY_CD  | SR 우선순위 코드  |
| REQUEST_ID      | 요청자 직원번호    |
| REQUEST_DEPT_CD | 요청 당시 부서코드  |
| ASSIGNEE_ID     | 개발 담당자 직원번호 |
| SR_STATUS_CD    | 현재 SR 상태 코드 |
| DUE_DT          | 처리 예정일      |
| COMPLETE_DT     | 개발 완료일시     |
| FILE_REF_NO     | 첨부파일 참조번호   |
| CREATE_DT       | 등록일시        |
| CREATE_ID       | 등록자 직원번호    |
| UPDATE_DT       | 수정일시        |
| UPDATE_ID       | 수정자 직원번호    |

---

### SR_STATUS_CD

| 코드            | 설명     |
| ------------- | ------ |
| SAVE          | 저장     |
| REQUEST       | 요청     |
| DEPT_APPROVED | 부서 승인  |
| DEV_APPROVED  | 개발팀 승인 |
| IN_PROGRESS   | 개발 진행  |
| UAT           | 인수테스트  |
| COMPLETED     | 개발 완료  |

---

# 2. SR_HISTORY

SR 처리 이력을 관리한다.

### 정책

* 모든 Action은 이력을 남긴다.
* 저장 버튼도 이력을 생성한다.
* 변경 전/후 데이터는 저장하지 않는다.
* 누가 언제 어떤 작업을 했는지만 저장한다.
* 처리내용은 시스템 고정 문구를 저장한다.

### 컬럼 정의

| 컬럼명             | 정의            |
| --------------- | ------------- |
| SR_HISTORY_SEQ  | SR 이력 PK      |
| SR_SEQ          | SR PK         |
| SR_NO           | SR 문서번호       |
| ACTION_CD       | 수행한 Action 코드 |
| PROCESS_ID      | 처리자 직원번호      |
| PROCESS_DEPT_CD | 처리 당시 부서코드    |
| PROCESS_DT      | 처리일시          |
| PROCESS_COMMENT | 처리내용          |
| CREATE_DT       | 등록일시          |
| CREATE_ID       | 등록자 직원번호      |

---

# 3. SR_UAT

인수테스트 요청 및 결과를 관리한다.

### 정책

* 개발 완료 후 인수테스트를 요청한다.
* 개발자는 테스트 방법을 작성한다.
* 요청자는 테스트 결과를 작성한다.
* 개발팀장은 확인 버튼만 존재한다.
* 인수테스트 반려 후 재요청 시 새로운 Row를 생성한다.
* 이력을 위해 기존 Row는 수정하지 않는다.
* 첨부파일은 2개 그룹으로 관리한다.

### 첨부파일

#### REQUEST_FILE_REF_NO

개발자가 테스트 방법을 설명하는 파일

예)

* 테스트 시나리오
* 테스트 방법서
* 화면 캡처

#### RESULT_FILE_REF_NO

요청자가 인수테스트 완료 후 첨부하는 파일

예)

* 인수테스트 확인서
* 결과 문서
* 확인 서명 파일

---

### 컬럼 정의

| 컬럼명                    | 정의          |
| ---------------------- | ----------- |
| SR_UAT_SEQ             | 인수테스트 PK    |
| SR_SEQ                 | SR PK       |
| UAT_ORDER              | 인수테스트 회차    |
| REQUEST_ID             | 인수테스트 요청자   |
| REQUEST_DT             | 인수테스트 요청일시  |
| TEST_METHOD            | 테스트 방법      |
| TEST_CONTENT           | 테스트 내용      |
| REQUEST_FILE_REF_NO    | 개발자 테스트 자료  |
| RESULT_ID              | 테스트 수행자     |
| RESULT_DT              | 테스트 결과 등록일시 |
| TEST_RESULT            | 테스트 결과      |
| RESULT_FILE_REF_NO     | 요청자 확인서 파일  |
| UAT_STATUS_CD          | 인수테스트 상태    |
| REJECT_REASON          | 반려 사유       |
| DEV_MANAGER_CONFIRM_ID | 개발팀장 확인자    |
| DEV_MANAGER_CONFIRM_DT | 개발팀장 확인일시   |
| CREATE_DT              | 등록일시        |
| CREATE_ID              | 등록자 직원번호    |
| UPDATE_DT              | 수정일시        |
| UPDATE_ID              | 수정자 직원번호    |

---

### UAT_STATUS_CD

| 코드        | 설명       |
| --------- | -------- |
| REQUEST   | 인수테스트 요청 |
| APPROVED  | 인수테스트 완료 |
| REJECTED  | 인수테스트 반려 |
| CONFIRMED | 개발팀장 확인  |

---

# 4. SANC

SR 승인 현재 상태를 관리한다.

### 정책

* SANC는 SR 전용으로 사용한다.
* 현재 승인 단계와 승인 상태만 관리한다.
* 승인 상세 이력은 SANC_DETAIL에서 관리한다.

### 컬럼 정의

| 컬럼명             | 정의       |
| --------------- | -------- |
| SANC_SEQ        | 승인 PK    |
| BIZ_CD          | 업무 코드    |
| TARGET_SEQ      | 대상 업무 PK |
| CURRENT_STEP_CD | 현재 승인 단계 |
| SANC_STATUS_CD  | 현재 승인 상태 |
| CREATE_DT       | 등록일시     |
| CREATE_ID       | 등록자 직원번호 |
| UPDATE_DT       | 수정일시     |
| UPDATE_ID       | 수정자 직원번호 |

---

### CURRENT_STEP_CD

| 코드   | 설명     |
| ---- | ------ |
| DEPT | 부서 승인  |
| DEV  | 개발팀 승인 |

---

### SANC_STATUS_CD

| 코드       | 설명 |
| -------- | -- |
| REQUEST  | 요청 |
| APPROVED | 승인 |
| REJECTED | 반려 |

---

# 5. SANC_DETAIL

SR 승인 상세 정보를 관리한다.

### 정책

* 요청, 승인, 반려 모두 저장한다.
* 처리내용은 시스템 고정 문구를 저장한다.
* 반려 시 PROCESS_COMMENT는 필수이다.

### 컬럼 정의

| 컬럼명             | 정의        |
| --------------- | --------- |
| SANC_DETAIL_SEQ | 승인 상세 PK  |
| SANC_SEQ        | 승인 PK     |
| SANC_STEP_CD    | 승인 단계     |
| SANC_ACTION_CD  | 처리 Action |
| PROCESS_ID      | 처리자 직원번호  |
| PROCESS_DEPT_CD | 처리 당시 부서  |
| PROCESS_DT      | 처리일시      |
| PROCESS_COMMENT | 처리내용      |
| CREATE_DT       | 등록일시      |
| CREATE_ID       | 등록자 직원번호  |

---

### SANC_ACTION_CD

| 코드       | 설명    |
| -------- | ----- |
| REQUEST  | 승인 요청 |
| APPROVED | 승인    |
| REJECTED | 반려    |

# 04-3. 인사(HR)

# 1. EMPLOYEE_REGISTRATION

직원 등록 요청 정보를 관리한다.

### 정책

* 직원 등록은 저장 후 요청할 수 있다.
* 저장 상태에서는 수정 및 삭제가 가능하다.
* 요청 상태 이후에는 승인 또는 반려가 가능하다.
* 반려 시 저장(SAVE) 상태로 변경한다.
* 모든 Action은 이력을 남긴다.
* 신청 단계에서는 로그인 ID를 입력받지 않는다.
* 신청 단계에서는 비밀번호를 입력받지 않는다.
* 신청 단계에서는 권한을 입력받지 않는다.
* 신청 단계에서는 직급을 입력받지 않는다.
* 신청 단계에서는 입사일을 입력받지 않는다.
* 인사팀은 모든 부서의 직원을 등록할 수 있다.
* 승인 완료 후 EMPLOYEE 테이블에 실제 직원 데이터를 생성한다.
* 생성된 직원번호를 로그인 ID로 사용한다.
* 최초 비밀번호는 시스템 정책에 따라 생성하며 최초 로그인 후 변경한다.

---

### REGISTRATION_STATUS_CD

| 코드       | 설명    |
| -------- | ----- |
| SAVE     | 저장    |
| REQUEST  | 등록 요청 |
| APPROVED | 승인    |
| REJECTED | 반려    |

---

### 컬럼 정의

| 컬럼명                       | 정의         |
| ------------------------- | ---------- |
| EMPLOYEE_REGISTRATION_SEQ | 직원등록 PK    |
| EMPLOYEE_NM               | 직원명        |
| DEPT_CD                   | 등록 대상 부서코드 |
| BIRTH_DT                  | 생년월일       |
| PHONE_NO                  | 휴대폰번호      |
| EMERGENCY_PHONE_NO        | 긴급연락처      |
| EMAIL                     | 이메일        |
| ZIP_CODE                  | 우편번호       |
| ADDRESS                   | 주소         |
| DETAIL_ADDRESS            | 상세주소       |
| REGISTRATION_STATUS_CD    | 직원등록 상태    |
| FILE_REF_NO               | 첨부파일 참조번호  |
| CREATE_DT                 | 등록일시       |
| CREATE_ID                 | 등록자 직원번호   |
| UPDATE_DT                 | 수정일시       |
| UPDATE_ID                 | 수정자 직원번호   |

---

# 2. EMPLOYEE_REGISTRATION_HISTORY

직원등록 처리 이력을 관리한다.

### 정책

* 저장 버튼도 이력을 남긴다.
* 요청 버튼도 이력을 남긴다.
* 승인 버튼도 이력을 남긴다.
* 반려 버튼도 이력을 남긴다.
* 변경 전/후 데이터는 저장하지 않는다.
* 누가 언제 어떤 작업을 했는지만 저장한다.
* 처리내용은 시스템 고정 문구를 저장한다.

---

### ACTION_CD

| 코드       | 설명    |
| -------- | ----- |
| SAVE     | 저장    |
| REQUEST  | 등록 요청 |
| APPROVED | 승인    |
| REJECTED | 반려    |

---

### 컬럼 정의

| 컬럼명                               | 정의            |
| --------------------------------- | ------------- |
| EMPLOYEE_REGISTRATION_HISTORY_SEQ | 직원등록 이력 PK    |
| EMPLOYEE_REGISTRATION_SEQ         | 직원등록 PK       |
| ACTION_CD                         | 수행한 Action 코드 |
| PROCESS_ID                        | 처리자 직원번호      |
| PROCESS_DEPT_CD                   | 처리 당시 부서      |
| PROCESS_DT                        | 처리일시          |
| PROCESS_COMMENT                   | 처리내용          |
| CREATE_DT                         | 등록일시          |
| CREATE_ID                         | 등록자 직원번호      |

---

# 3. 직원 승인 프로세스

### 일반 부서

```text
저장

↓

등록 요청

↓

인사팀 승인

↓

EMPLOYEE 생성

↓

완료
```

또는

```text
저장

↓

등록 요청

↓

인사팀 반려

↓

저장
```

---

### 인사팀

인사팀은 모든 부서의 신규 직원을 등록할 수 있다.

```text
인사팀 저장

↓

등록 요청

↓

인사팀 승인

↓

EMPLOYEE 생성

↓

완료
```

---

# 4. EMPLOYEE 생성 정책

직원등록 승인 완료 시 EMPLOYEE 테이블에 데이터를 생성한다.

### 생성 대상

| EMPLOYEE           | EMPLOYEE_REGISTRATION |
| ------------------ | --------------------- |
| EMPLOYEE_NM        | EMPLOYEE_NM           |
| DEPT_CD            | DEPT_CD               |
| BIRTH_DT           | BIRTH_DT              |
| PHONE_NO           | PHONE_NO              |
| EMERGENCY_PHONE_NO | EMERGENCY_PHONE_NO    |
| EMAIL              | EMAIL                 |
| ZIP_CODE           | ZIP_CODE              |
| ADDRESS            | ADDRESS               |
| DETAIL_ADDRESS     | DETAIL_ADDRESS        |

---

### 시스템 생성 항목

| 컬럼명                | 생성 방식         |
| ------------------ | ------------- |
| EMPLOYEE_ID        | 직원번호 자동 생성    |
| PASSWORD           | 초기 비밀번호 자동 생성 |
| EMPLOYEE_STATUS_CD | ACTIVE        |
| LOGIN_FAIL_CNT     | 0             |
| LOGIN_LOCK_YN      | N             |

---

### 권한 정책

직원 생성 시

```text
USER
```

권한을 기본으로 부여한다.

추가 권한은

```text
ROLE

EMPLOYEE_ROLE
```

메뉴에서 별도로 관리한다.

# 04-4. 시설(Facility)

# 1. FACILITY

시설 마스터 정보를 관리한다.

회의실, 차량, 교육장 등 예약 가능한 시설 정보를 관리한다.

### 정책

* 시설 삭제는 하지 않는다.
* 사용여부(USE_YN)로 관리한다.
* 시설 종류는 코드관리에서 관리한다.
* 시설별 상세 속성은 별도로 관리하지 않는다.
* 차량의 출발지/도착지 등은 예약 내용에 자유롭게 작성한다.

---

### FACILITY_TYPE_CD

| 코드             | 설명  |
| -------------- | --- |
| MEETING_ROOM   | 회의실 |
| VEHICLE        | 차량  |
| EDUCATION_ROOM | 교육장 |

※ 실제 데이터는 CODE_GROUP, CODE_DETAIL에서 관리한다.

---

### 컬럼 정의

| 컬럼명              | 정의       |
| ---------------- | -------- |
| FACILITY_SEQ     | 시설 PK    |
| FACILITY_CD      | 시설코드     |
| FACILITY_TYPE_CD | 시설 종류 코드 |
| FACILITY_NM      | 시설명      |
| LOCATION         | 시설 위치    |
| CAPACITY         | 수용 인원    |
| DESCRIPTION      | 시설 설명    |
| USE_YN           | 사용여부     |
| CREATE_DT        | 등록일시     |
| CREATE_ID        | 등록자 직원번호 |
| UPDATE_DT        | 수정일시     |
| UPDATE_ID        | 수정자 직원번호 |

---

# 2. FACILITY_RESERVATION

시설 예약 정보를 관리한다.

### 정책

* 예약번호(RESERVATION_NO)를 별도로 생성한다.
* 예약 제목은 사용하지 않는다.
* 차량 출발지, 도착지 등은 예약 내용에 자유롭게 작성한다.
* 시설 종류별 별도 컬럼은 추가하지 않는다.
* 예약 취소는 상태값으로 관리한다.
* 예약 당시 부서를 별도로 저장한다.
* 첨부파일은 FILE_REF_NO를 사용한다.

---

### RESERVATION_STATUS_CD

| 코드       | 설명   |
| -------- | ---- |
| RESERVED | 예약   |
| CANCELED | 예약취소 |

---

### 컬럼 정의

| 컬럼명                      | 정의         |
| ------------------------ | ---------- |
| FACILITY_RESERVATION_SEQ | 시설예약 PK    |
| FACILITY_SEQ             | 시설 PK      |
| RESERVATION_NO           | 예약번호       |
| RESERVATION_CONTENT      | 예약 내용      |
| REQUEST_ID               | 예약자 직원번호   |
| REQUEST_DEPT_CD          | 예약 당시 부서코드 |
| START_DT                 | 예약 시작일시    |
| END_DT                   | 예약 종료일시    |
| RESERVATION_STATUS_CD    | 예약 상태      |
| FILE_REF_NO              | 첨부파일 참조번호  |
| CREATE_DT                | 등록일시       |
| CREATE_ID                | 등록자 직원번호   |
| UPDATE_DT                | 수정일시       |
| UPDATE_ID                | 수정자 직원번호   |

---

# 3. 시설 예약 프로세스

### 회의실

```text
예약

↓

사용

↓

종료
```

또는

```text
예약

↓

취소
```

---

### 차량

```text
예약

↓

예약내용 작성

예)
출발 : 본사
도착 : 판교
사용목적 : 고객사 방문

↓

사용

↓

종료
```

또는

```text
예약

↓

취소
```

---

### 교육장

```text
예약

↓

사용

↓

종료
```

또는

```text
예약

↓

취소
```

---

# 4. 첨부파일 정책

시설 예약 첨부파일은 FILE_REF_NO를 사용한다.

예)

```text
FACILITY_RESERVATION

FILE_REF_NO
↓

FILE
```

FILE_REF_NO 생성 규칙

```text
FAC00000001

FAC00000002
```

업무코드 + 숫자 8자리 내외 형식으로 생성한다.

# 04-5. 전자결재(EAPP)

# 1. 개요

전자결재 문서와 결재선을 관리한다.

전자결재는

* 임시저장을 사용하지 않는다.
* 등록 즉시 상신한다.
* 승인자는 최대 3명까지 등록할 수 있다.
* 참조자는 최대 5명까지 등록할 수 있다.
* 승인자는 순차적으로 승인한다.
* 결재 이력은 EAPP_LINE에서 관리한다.
* 별도의 EAPP_HISTORY 테이블은 사용하지 않는다.

---

# 2. EAPP_DOCUMENT

전자결재 문서를 관리한다.

### 정책

* 임시저장을 사용하지 않는다.
* 등록 즉시 상신한다.
* 상신 전 수정은 존재하지 않는다.
* 승인 전까지 본인 취소가 가능하다.
* 취소 후 재상신은 새로운 문서를 생성한다.
* 첨부파일은 FILE_REF_NO 하나만 사용한다.
* 상신 당시 부서를 별도로 저장한다.

---

### EAPP_STATUS_CD

| 코드          | 설명     |
| ----------- | ------ |
| SUBMITTED   | 상신     |
| IN_PROGRESS | 결재 진행중 |
| COMPLETED   | 완료     |
| REJECTED    | 반려     |
| CANCELED    | 본인취소   |

---

### 상태 변경 규칙

#### 승인자 1명

```text
상신

↓

SUBMITTED

↓

승인

↓

COMPLETED
```

---

#### 승인자 2~3명

```text
상신

↓

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

#### 반려

```text
상신

↓

SUBMITTED

↓

반려

↓

REJECTED
```

---

#### 본인 취소

```text
상신

↓

SUBMITTED

↓

본인취소

↓

CANCELED
```

---

### 컬럼 정의

| 컬럼명               | 정의         |
| ----------------- | ---------- |
| EAPP_DOCUMENT_SEQ | 전자결재 문서 PK |
| EAPP_TITLE        | 전자결재 제목    |
| EAPP_CONTENT      | 전자결재 내용    |
| REQUEST_ID        | 상신자 직원번호   |
| REQUEST_DEPT_CD   | 상신 당시 부서코드 |
| EAPP_STATUS_CD    | 전자결재 상태    |
| FILE_REF_NO       | 첨부파일 참조번호  |
| CREATE_DT         | 등록일시       |
| CREATE_ID         | 등록자 직원번호   |
| UPDATE_DT         | 수정일시       |
| UPDATE_ID         | 수정자 직원번호   |

---

# 3. EAPP_LINE

전자결재 결재선을 관리한다.

### 정책

* 승인자는 최대 3명까지 등록할 수 있다.
* 참조자는 최대 5명까지 등록할 수 있다.
* 승인자는 순차 승인한다.
* 참조자는 승인권한이 없다.
* 참조자는 읽음 여부를 관리하지 않는다.
* 별도 이력 테이블은 사용하지 않는다.
* 대직 여부는 EMPLOYEE_ID와 PROCESS_ID 비교로 판단한다.

---

### LINE_TYPE_CD

| 코드        | 설명  |
| --------- | --- |
| APPROVER  | 승인자 |
| REFERENCE | 참조자 |

---

### LINE_STATUS_CD

#### APPROVER

| 코드       | 설명    |
| -------- | ----- |
| WAIT     | 승인 대기 |
| APPROVED | 승인    |
| REJECTED | 반려    |

---

#### REFERENCE

| 코드   | 설명 |
| ---- | -- |
| WAIT | 참조 |

---

### 컬럼 정의

| 컬럼명               | 정의           |
| ----------------- | ------------ |
| EAPP_LINE_SEQ     | 전자결재 결재선 PK  |
| EAPP_DOCUMENT_SEQ | 전자결재 문서 PK   |
| LINE_TYPE_CD      | 결재선 유형       |
| LINE_ORDER        | 승인 순서        |
| EMPLOYEE_ID       | 원 결재자 또는 참조자 |
| LINE_STATUS_CD    | 결재선 상태       |
| PROCESS_DT        | 처리일시         |
| PROCESS_ID        | 실제 처리자       |
| CREATE_DT         | 등록일시         |
| CREATE_ID         | 등록자 직원번호     |
| UPDATE_DT         | 수정일시         |
| UPDATE_ID         | 수정자 직원번호     |

---

# 4. 대직 처리 정책

대직 여부는 별도 컬럼으로 관리하지 않는다.

다음 조건으로 판단한다.

```text
EMPLOYEE_ID

!=

PROCESS_ID
```

예)

```text
원 결재자

EMPLOYEE_ID

260001


실제 처리자

PROCESS_ID

260010
```

위와 같은 경우

```text
대직 처리
```

로 판단한다.

---

# 5. 첨부파일 정책

전자결재 첨부파일은 FILE_REF_NO를 사용한다.

예)

```text
EAPP_DOCUMENT

FILE_REF_NO

↓

FILE
```

생성 규칙

```text
EAPP00000001

EAPP00000002
```

업무코드 + 숫자 8자리 내외 형식으로 생성한다.

# 04-6. 로그(Log)

# 1. 개요

시스템 운영 과정에서 발생하는 로그를 관리한다.

로그는 다음 종류로 구분한다.

* SYSTEM_LOG
* ERROR_LOG
* DOWNLOAD_LOG
* PERSONAL_INFO_ACCESS_LOG
* FILE_DELETE_LOG
* BATCH_LOG

공통 정책

* 로그 데이터는 물리삭제하지 않는다.
* MENU_CD는 저장하지 않는다.
* 업무 구분은 BIZ_CD로 관리한다.
* 처리자 기준 조회가 가능해야 한다.
* 처리 당시 부서를 저장한다.

---

# 2. SYSTEM_LOG

시스템 액션 로그를 관리한다.

### 정책

* 업무별 처리 이력을 저장한다.
* BIZ_CD로 업무를 구분한다.
* Action 단위로 로그를 생성한다.
* 처리 내용은 시스템 고정 문구를 저장한다.

---

### 컬럼 정의

| 컬럼명             | 정의            |
| --------------- | ------------- |
| SYSTEM_LOG_SEQ  | 시스템로그 PK      |
| BIZ_CD          | 업무 코드         |
| ACTION_CD       | 수행한 Action 코드 |
| URI             | 호출 URI        |
| PROCESS_ID      | 처리자 직원번호      |
| PROCESS_DEPT_CD | 처리 당시 부서      |
| PROCESS_DT      | 처리일시          |
| PROCESS_COMMENT | 처리 내용         |
| IP              | 요청 IP         |
| EXECUTE_TIME    | 처리 시간(ms)     |
| CREATE_DT       | 등록일시          |
| CREATE_ID       | 등록자 직원번호      |

---

# 3. ERROR_LOG

에러 로그를 관리한다.

### 정책

* 예외 발생 시 자동 저장한다.
* GlobalExceptionHandler와 연동한다.
* StackTrace는 전문을 저장한다.
* 업무별 조회가 가능해야 한다.

---

### 컬럼 정의

| 컬럼명           | 정의       |
| ------------- | -------- |
| ERROR_LOG_SEQ | 에러로그 PK  |
| BIZ_CD        | 업무 코드    |
| URI           | 호출 URI   |
| ERROR_CODE    | 에러 코드    |
| ERROR_MESSAGE | 에러 메시지   |
| STACK_TRACE   | 에러 상세 정보 |
| PROCESS_ID    | 처리자 직원번호 |
| PROCESS_DT    | 에러 발생일시  |
| IP            | 요청 IP    |
| CREATE_DT     | 등록일시     |
| CREATE_ID     | 등록자 직원번호 |

---

# 4. DOWNLOAD_LOG

파일 다운로드 로그를 관리한다.

### 정책

* 파일 다운로드 시 자동 저장한다.
* 다운로드 사유는 저장하지 않는다.
* 개인정보 파일 여부와 관계없이 모두 저장한다.

---

### 컬럼 정의

| 컬럼명              | 정의         |
| ---------------- | ---------- |
| DOWNLOAD_LOG_SEQ | 다운로드로그 PK  |
| FILE_SEQ         | 파일 PK      |
| FILE_REF_NO      | 파일 참조번호    |
| BIZ_CD           | 업무 코드      |
| DOCUMENT_NO      | 업무 문서번호    |
| DOWNLOAD_ID      | 다운로드한 직원번호 |
| DOWNLOAD_DT      | 다운로드 일시    |
| CREATE_DT        | 등록일시       |
| CREATE_ID        | 등록자 직원번호   |

---

# 5. PERSONAL_INFO_ACCESS_LOG

개인정보 접근 로그를 관리한다.

### 정책

* 개인정보 조회 시 자동 저장한다.
* 누가 누구의 정보를 조회했는지 저장한다.
* 개인정보 수정 로그는 저장하지 않는다.
* 조회 이력만 관리한다.

---

### 컬럼 정의

| 컬럼명                          | 정의          |
| ---------------------------- | ----------- |
| PERSONAL_INFO_ACCESS_LOG_SEQ | 개인정보접근로그 PK |
| BIZ_CD                       | 업무 코드       |
| ACCESS_TYPE                  | 접근 유형       |
| TARGET_ID                    | 조회 대상 직원번호  |
| TARGET_NM                    | 조회 대상 직원명   |
| ACCESS_ID                    | 조회한 직원번호    |
| REQUEST_URI                  | 호출 URI      |
| IP                           | 요청 IP       |
| CREATE_DT                    | 등록일시        |
| CREATE_ID                    | 등록자 직원번호    |

---

# 6. FILE_DELETE_LOG

파일 삭제 로그를 관리한다.

### 정책

* 파일 삭제 시 자동 저장한다.
* 삭제 사유는 저장하지 않는다.
* 삭제 성공/실패 여부를 저장한다.

---

### 컬럼 정의

| 컬럼명                 | 정의        |
| ------------------- | --------- |
| FILE_DELETE_LOG_SEQ | 파일삭제로그 PK |
| FILE_SEQ            | 파일 PK     |
| FILE_REF_NO         | 파일 참조번호   |
| ORIGINAL_FILE_NM    | 원본 파일명    |
| STORED_FILE_NM      | 저장 파일명    |
| FILE_PATH           | 파일 경로     |
| DELETE_DT           | 삭제 일시     |
| DELETE_ID           | 삭제한 직원번호  |
| DELETE_RESULT       | 삭제 결과     |
| ERROR_MESSAGE       | 삭제 실패 메시지 |
| CREATE_DT           | 등록일시      |
| CREATE_ID           | 등록자 직원번호  |

---

# 7. BATCH_LOG

배치 실행 로그를 관리한다.

### 정책

* Spring Scheduler와 Cron을 사용한다.
* 배치 정의 테이블은 사용하지 않는다.
* 실행 결과만 로그로 저장한다.
* 성공/실패 건수를 저장한다.

---

### 컬럼 정의

| 컬럼명           | 정의       |
| ------------- | -------- |
| BATCH_LOG_SEQ | 배치로그 PK  |
| BATCH_CD      | 배치 코드    |
| START_DT      | 배치 시작일시  |
| END_DT        | 배치 종료일시  |
| RESULT        | 실행 결과    |
| SUCCESS_COUNT | 성공 건수    |
| FAIL_COUNT    | 실패 건수    |
| MESSAGE       | 실행 메시지   |
| CREATE_DT     | 등록일시     |
| CREATE_ID     | 등록자 직원번호 |

---

# 8. 로그 생성 정책

### SYSTEM_LOG

```text
화면 요청

↓

업무 처리

↓

SYSTEM_LOG 생성
```

---

### ERROR_LOG

```text
예외 발생

↓

GlobalExceptionHandler

↓

ERROR_LOG 생성
```

---

### DOWNLOAD_LOG

```text
파일 다운로드

↓

DOWNLOAD_LOG 생성
```

---

### PERSONAL_INFO_ACCESS_LOG

```text
개인정보 조회

↓

PERSONAL_INFO_ACCESS_LOG 생성
```

---

### FILE_DELETE_LOG

```text
파일 삭제

↓

FILE_DELETE_LOG 생성
```

---

### BATCH_LOG

```text
Spring Scheduler 실행

↓

배치 수행

↓

BATCH_LOG 생성
```

# 04-7. 테이블 관계도

## 1. 공통

```text
EMPLOYEE
  └─ EMPLOYEE_ROLE
        └─ ROLE
```

```text
MENU
  └─ MENU_ROLE
        └─ ROLE
```

```text
CODE_GROUP
  └─ CODE_DETAIL
```

```text
EMPLOYEE
  └─ EMPLOYEE_HISTORY
```

```text
FILE
- FILE_REF_NO 기준으로 각 업무 테이블과 논리 연결
```

```text
NOTIFICATION
- BIZ_CD 기준으로 각 업무와 논리 연결
- SENDER_ID / RECEIVER_ID는 EMPLOYEE_ID와 논리 연결
```

---

## 2. SR

```text
SR
  ├─ SR_HISTORY
  ├─ SR_UAT
  └─ SANC
        └─ SANC_DETAIL
```

### 관계 설명

| 기준 테이블 | 대상 테이블      | 관계                   |
| ------ | ----------- | -------------------- |
| SR     | SR_HISTORY  | SR 1건에 여러 처리이력       |
| SR     | SR_UAT      | SR 1건에 여러 인수테스트 요청   |
| SR     | SANC        | SR 승인 상태             |
| SANC   | SANC_DETAIL | 승인 1건에 여러 승인 상세      |
| SR     | FILE        | FILE_REF_NO 기준 논리 연결 |

---

## 3. 인사

```text
EMPLOYEE_REGISTRATION
  └─ EMPLOYEE_REGISTRATION_HISTORY
```

```text
EMPLOYEE_REGISTRATION
  └─ 승인 완료 후 EMPLOYEE 생성
```

### 관계 설명

| 기준 테이블                | 대상 테이블                        | 관계                   |
| --------------------- | ----------------------------- | -------------------- |
| EMPLOYEE_REGISTRATION | EMPLOYEE_REGISTRATION_HISTORY | 직원등록 요청 1건에 여러 처리이력  |
| EMPLOYEE_REGISTRATION | EMPLOYEE                      | 승인 완료 후 직원 마스터 생성    |
| EMPLOYEE_REGISTRATION | FILE                          | FILE_REF_NO 기준 논리 연결 |

---

## 4. 시설

```text
FACILITY
  └─ FACILITY_RESERVATION
```

### 관계 설명

| 기준 테이블               | 대상 테이블               | 관계                   |
| -------------------- | -------------------- | -------------------- |
| FACILITY             | FACILITY_RESERVATION | 시설 1건에 여러 예약         |
| FACILITY_RESERVATION | FILE                 | FILE_REF_NO 기준 논리 연결 |

---

## 5. 전자결재

```text
EAPP_DOCUMENT
  └─ EAPP_LINE
```

### 관계 설명

| 기준 테이블        | 대상 테이블    | 관계                                |
| ------------- | --------- | --------------------------------- |
| EAPP_DOCUMENT | EAPP_LINE | 전자결재 문서 1건에 여러 결재선                |
| EAPP_DOCUMENT | FILE      | FILE_REF_NO 기준 논리 연결              |
| EAPP_LINE     | EMPLOYEE  | EMPLOYEE_ID / PROCESS_ID 기준 논리 연결 |

---

## 6. 로그

```text
SYSTEM_LOG
ERROR_LOG
DOWNLOAD_LOG
PERSONAL_INFO_ACCESS_LOG
FILE_DELETE_LOG
BATCH_LOG
```

### 관계 설명

| 로그 테이블                   | 관계                                          |
| ------------------------ | ------------------------------------------- |
| SYSTEM_LOG               | BIZ_CD, PROCESS_ID 기준 논리 연결                 |
| ERROR_LOG                | BIZ_CD, PROCESS_ID 기준 논리 연결                 |
| DOWNLOAD_LOG             | FILE_SEQ, FILE_REF_NO, DOWNLOAD_ID 기준 논리 연결 |
| PERSONAL_INFO_ACCESS_LOG | TARGET_ID, ACCESS_ID 기준 EMPLOYEE와 논리 연결     |
| FILE_DELETE_LOG          | FILE_SEQ, FILE_REF_NO, DELETE_ID 기준 논리 연결   |
| BATCH_LOG                | BATCH_CD 기준 배치 코드와 논리 연결                    |

---

## 7. 전체 관계 요약

```text
EMPLOYEE
  ├─ EMPLOYEE_ROLE
  ├─ EMPLOYEE_HISTORY
  ├─ SR.REQUEST_ID
  ├─ SR.ASSIGNEE_ID
  ├─ FACILITY_RESERVATION.REQUEST_ID
  ├─ EAPP_DOCUMENT.REQUEST_ID
  └─ EAPP_LINE.EMPLOYEE_ID / PROCESS_ID
```

```text
ROLE
  ├─ EMPLOYEE_ROLE
  └─ MENU_ROLE
```

```text
FILE
  ├─ SR.FILE_REF_NO
  ├─ SR_UAT.REQUEST_FILE_REF_NO
  ├─ SR_UAT.RESULT_FILE_REF_NO
  ├─ EMPLOYEE_REGISTRATION.FILE_REF_NO
  ├─ FACILITY_RESERVATION.FILE_REF_NO
  └─ EAPP_DOCUMENT.FILE_REF_NO
```

```text
CODE_GROUP
  └─ CODE_DETAIL
      └─ 각 업무 코드 컬럼에서 사용
```

---

## 8. FK 정책

* 물리 FK는 생성하지 않는다.
* ERD 및 문서상 논리 FK만 관리한다.
* 업무 테이블에서는 코드값, 직원번호, 파일참조번호를 기준으로 논리 연결한다.
* 삭제 정책은 업무별 상태값과 사용여부 컬럼으로 관리한다.
