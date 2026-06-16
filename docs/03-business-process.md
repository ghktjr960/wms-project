# 03. Business Process

## 1. 공통 정책

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

Button은 직접 관리하지 않는다.

현재 상태(State)에 따라 가능한 Action을 계산한다.

화면은 Action 목록을 전달받아 버튼을 생성한다.

Role과 Actor를 통해 버튼 노출 여부를 최종 결정한다.

---

SAVE는 Business Action이 아니다.

SAVE는

* 현재 데이터 저장
* 상태 변경 없음
* 알림 없음
* 업무 전달 없음

의 공통 기능이다.

---

모든 업무는 상태 변경 시

```text
STATE_HISTORY
```

를 저장한다.

저장 항목

```text
menu_code

document_no

before_state

after_state

action

comment

created_by

created_at
```

---

# 2. SR

## 상태 흐름

```text
저장

↓

SR요청

↓

부서승인대기

↓

개발팀승인대기

↓

개발진행

↓

개발확정

↓

인수테스트대기

↓

개발종료
```

---

반려 시

```text
부서승인대기

↓

반려

↓

저장
```

---

```text
개발팀승인대기

↓

반려

↓

부서승인대기
```

---

```text
인수테스트대기

↓

인수테스트 반려

↓

개발확정
```

---

## Action

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

---

## 버튼 정책

### 저장

```text
SR요청

삭제
```

---

### 요청완료

```text
SR요청취소
```

---

### 부서승인대기

```text
승인

반려
```

---

### 개발팀승인대기

```text
담당자 지정

반려
```

---

### 개발진행

```text
개발확정
```

---

### 개발확정

```text
개발확정취소

인수테스트 요청
```

---

### 인수테스트대기

```text
인수테스트 승인

인수테스트 반려
```

---

### 개발종료

```text
버튼 없음
```

---

## 알림

다음 업무 전달 시 알림을 생성한다.

```text
SR요청

담당자 지정

승인

반려

인수테스트 요청

인수테스트 승인

인수테스트 반려
```

---

# 3. 전자결재

## 상태

```text
저장

↓

상신

↓

승인

또는

반려
```

---

## Action

```text
APPROVAL_REQUEST

APPROVAL_REQUEST_CANCEL

APPROVE

REJECT
```

---

## 버튼

### 저장

```text
상신

삭제
```

---

### 상신

```text
상신취소
```

---

### 결재대기

```text
승인

반려
```

---

### 종결

```text
버튼 없음
```

---

## 알림

```text
상신

승인

반려
```

시 알림을 생성한다.

---

# 4. 직원등록

## 상태

```text
저장

↓

등록요청

↓

승인

또는

반려
```

---

## 버튼

### 저장

```text
등록요청

삭제
```

---

### 등록요청

```text
등록요청취소
```

---

### 승인대기

```text
승인

반려
```

---

## 알림

```text
등록요청

승인

반려
```

시 알림을 생성한다.

---

# 5. 시설관리

## 화면 상태

```text
예약완료

예약취소
```

---

## 내부 Action

```text
RESERVE

CHANGE

CANCEL
```

---

예약은 물리삭제하지 않는다.

---

예약 변경 시

```text
STATE_HISTORY
```

를 저장한다.

---

## 알림

다음 시점에 알림을 생성한다.

```text
예약 완료

예약 변경

예약 취소
```

수신자

```text
시설 담당자

예약자
```

---

# 6. 알림

알림은 업무 전달 시 생성한다.

예)

```text
A

↓

B

업무 전달

↓

알림 생성
```

---

알림은

```text
title

content

menu_code

document_no

receiver

read_at

created_at
```

를 저장한다.

---

알림은 영구 보관한다.

---

읽음 처리는

```text
알림목록 조회

↓

read_at 저장
```

으로 처리한다.

---

# 7. 배치

## 미처리 결재 알림

대상

```text
SR

전자결재

직원등록
```

---

실행

```text
영업일 오전 7시
```

---

## SR 처리기한 알림

대상

```text
처리기한 당일

처리기한 하루 전
```

---

실행

```text
영업일 오전 9시
```

---

## 문서번호 초기화

대상

```text
DOCUMENT_NO
```

---

실행

```text
매년 1월 1일
```

---

## 배치 실행 로그

저장 항목

```text
batch_type

start_at

end_at

result
```

배치는 재실행을 지원한다.
