# 1-9. NULL 속성의 이해

## 1. NULL의 개념
- 데이터베이스에서 **아직 정해지지 않았거나 알 수 없는 값**을 의미한다.
- `0`(숫자 0)이나 `''`(빈 문자열)과는 전혀 다른 개념이다.
    - `0`: 수치 데이터에서의 값
    - `''`: 문자열 데이터에서의 길이가 0인 값
    - `NULL`: 값이 존재하지 않음을 의미 (미정, 불명확, 모름)
- 컬럼 설계 시 반드시 **NULL 허용 여부**(Nullable 여부)를 결정해야 한다.

---

## 2. NULL의 연산 특성
1. **NULL과의 연산 결과는 항상 NULL**
    - `NULL + 100 → NULL`
    - `SAL + COMM(NULL) → NULL`
    - 따라서 연산 시 `NVL(컬럼, 0)`(오라클) 또는 `COALESCE(컬럼, 0)`(표준 SQL) 등을 이용해 대체 후 연산 필요

2. **그룹 함수와 NULL**
    - `SUM, AVG, MIN, MAX` 함수는 **NULL을 무시하고 연산**한다.
    - `COUNT(컬럼)` → NULL이 아닌 값만 센다.
    - `COUNT(*)` → NULL 여부와 관계없이 전체 행 개수를 센다.

3. **평균 계산 시 주의**
    - `AVG(COMM)` → NULL을 제외한 데이터로 평균
    - `SUM(COMM) / 전체 행 수` → NULL 포함해 분모 계산
    - 결과가 서로 다를 수 있으므로, 목적에 맞게 선택해야 한다.

---

## 3. NULL의 ERD 표기법
- **IE 표기법**: NULL 허용 여부를 알 수 없다.
- **Barker 표기법**: 속성 이름 앞에 동그라미(O)를 붙이면 NULL 허용 속성을 의미한다.

---

## 핵심 요약
- NULL은 0이나 공백과는 다르며, "값이 정해지지 않은 상태"를 의미한다.
- NULL이 포함된 연산 결과는 항상 NULL이다.
- 그룹 함수는 NULL을 무시한다. (`COUNT(*)`만 전체 행 기준)
- ERD 표기법에 따라 NULL 허용 여부 표현이 다르다.

## 용어 설명
- **Nullable Column**: NULL 값을 허용하는 컬럼
- **NVL / COALESCE**: NULL 값을 특정 값으로 치환하는 함수
- **COUNT(*) vs COUNT(컬럼)**: NULL 포함 여부 차이

## 예시
- `SELECT NVL(COMM, 0) + SAL FROM EMP;`
- `SELECT COUNT(*) FROM EMP;  -- 전체 행 수`
- `SELECT COUNT(COMM) FROM EMP;  -- NULL 제외`
