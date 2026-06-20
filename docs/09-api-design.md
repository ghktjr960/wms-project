# 09. API Design

## 1. 문서 목적

본 문서는

WMS에서 사용하는

외부 API 연동 정책을 정의한다.

---

내부 화면 처리는

```text id="f0zywb"
Spring MVC

+

Thymeleaf
```

구조를 사용한다.

따라서

내부 Controller URL은

본 문서의 관리 대상이 아니다.

---

본 문서는

```text id="l57ul5"
외부 API
```

만 정의한다.

---

## 2. 외부 API 목록

WMS는 아래 외부 API를 사용한다.

| API       | 목적    |
| --------- | ----- |
| 공휴일 API   | 휴일 조회 |
| 주소 검색 API | 주소 검색 |

---

## 3. 공휴일 API

### 목적

SR 처리예정일 선택 시

공휴일과 주말을 제외하기 위해 사용한다.

---

### 사용 화면

```text id="32xj4j"
SR 요청

SR 진행
```

---

### 사용 시점

처리예정일 선택 시

해당 연도의 공휴일 목록을 조회한다.

---

### 처리 방식

```text id="hbkjlwm"
사용자

↓

날짜 선택

↓

HolidayService

↓

공휴일 API 호출

↓

공휴일 여부 확인

↓

선택 가능 여부 결정
```

---

### 실패 처리

공휴일 API 호출 실패 시

```text id="slprq3"
주말

↓

선택 불가


평일

↓

선택 가능
```

으로 처리한다.

---

공휴일 API 오류는

```text id="zav6c6"
SYSTEM_LOG
```

에 저장한다.

---

## 4. 주소 검색 API

### 목적

직원 주소 입력 시

주소 검색 기능을 제공한다.

---

### 사용 화면

```text id="3egbhm"
직원등록

직원관리
```

---

### 사용 시점

주소 검색 버튼 클릭 시

주소 검색 API를 호출한다.

---

### 처리 방식

```text id="6rzg3w"
사용자

↓

주소 검색

↓

AddressService

↓

주소 검색 API 호출

↓

주소 선택

↓

주소 입력
```

---

### 저장 정책

주소 검색 API 결과는

DB에 저장하지 않는다.

---

사용자가 선택한

```text id="31bg3q"
우편번호

기본주소

상세주소
```

만 저장한다.

---

### 실패 처리

주소 검색 API 호출 실패 시

```text id="pwwi72"
주소 검색에 실패했습니다.
```

메시지를 출력한다.

---

오류는

```text id="d9t2qb"
SYSTEM_LOG
```

에 저장한다.

---

## 5. 외부 API 공통 정책

외부 API는

별도 Service로 관리한다.

예)

```text id="y0vxtx"
HolidayService

AddressService
```

---

외부 API 호출은

업무 Service에서 직접 호출하지 않는다.

---

구조

```text id="j1bxu0"
Controller

↓

Business Service

↓

External API Service

↓

External API
```

---

## 6. Timeout 정책

외부 API Timeout은

```text id="nk9yqs"
3초
```

로 설정한다.

---

3초 초과 시

호출 실패로 판단한다.

---

## 7. Retry 정책

외부 API는

자동 재시도하지 않는다.

---

사용자가

다시 요청하도록 처리한다.

---

## 8. Logging 정책

외부 API 호출 결과는

```text id="vvjkec"
SYSTEM_LOG
```

에 저장한다.

---

저장 항목

* API 명
* 요청 URI
* 응답 결과
* 응답 시간
* 성공 여부
* 오류 메시지

---

민감한 요청/응답 데이터는

로그에 저장하지 않는다.

---

## 9. Exception 정책

외부 API 예외는

업무 예외로 전파하지 않는다.

---

예)

공휴일 API 실패

↓

주말만 체크

↓

업무 진행 가능

---

주소 검색 API 실패

↓

오류 메시지 출력

↓

다시 검색 가능

---

외부 API 오류로 인해

업무 전체가 실패해서는 안 된다.
