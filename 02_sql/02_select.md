# 2-2. SELECT문

## SQL 종류 분류

| 구분 | 명령어 | 기능 |
|------|--------|------|
| **DDL** (Data Definition Language) | CREATE, ALTER, DROP, TRUNCATE | 데이터 구조 정의 |
| **DML** (Data Manipulation Language) | INSERT, UPDATE, DELETE | 데이터 조작 |
| **DCL** (Data Control Language) | GRANT, REVOKE | 권한 제어 |
| **TCL** (Transaction Control Language) | COMMIT, ROLLBACK | 트랜잭션 제어 |
| **DQL** (Data Query Language) | SELECT | 데이터 조회 |

---

## SELECT문 구조

### 기본 문법
```sql
SELECT [컬럼명 | * | 표현식]
FROM 테이블명
WHERE 조건
GROUP BY 그룹핑할컬럼
HAVING 그룹조건
ORDER BY 정렬할컬럼;
```

### 절(Clause)의 작성 순서
**반드시 다음 순서로 작성해야 함:**
1. **SELECT** - 조회할 컬럼 지정
2. **FROM** - 데이터를 가져올 테이블 지정
3. **WHERE** - 행 필터링 조건
4. **GROUP BY** - 그룹핑할 컬럼
5. **HAVING** - 그룹 조건
6. **ORDER BY** - 정렬 순서

### 내부 실행(파싱) 순서
**실제 DBMS 내부에서는 다음 순서로 실행:**
1. **FROM** → 2. **WHERE** → 3. **GROUP BY** → 4. **HAVING** → 5. **SELECT** → 6. **ORDER BY**

---

## SELECT 절

### 기본 사용법
```sql
-- 전체 컬럼 조회
SELECT * FROM EMP;

-- 특정 컬럼 조회 (순서대로 출력)
SELECT EMPNO, ENAME, SAL FROM EMP;

-- 표현식 사용
SELECT EMPNO, ENAME, SAL * 1.1 FROM EMP;
```

### 특징
- `*` 사용 시 테이블의 모든 컬럼 조회
- 원하는 컬럼을 쉼표(,)로 나열하여 선택 조회 가능
- 표현식: 기존 컬럼을 이용한 연산, 함수 등 모든 계산 가능한 대상

---

## 컬럼 별칭(Alias)

### 별칭 지정 방법
```sql
-- AS 사용 (생략 가능)
SELECT EMPNO AS 사원번호, ENAME AS 사원명 FROM EMP;
SELECT EMPNO 사원번호, ENAME 사원명 FROM EMP;

-- 연산 결과에 별칭
SELECT SAL * 1.1 AS 인상급여 FROM EMP;
```

### 별칭 사용 시 주의사항
1. **사용 가능한 절**: ORDER BY 절에서만 별칭 사용 가능
    - SELECT문보다 늦게 실행되는 절은 ORDER BY뿐
2. **한글 사용**: 한글 지원 캐릭터셋 설정 시 가능
3. **예약어 금지**: SELECT, FROM, WHERE 등 SQL 예약어는 별칭으로 사용 불가
4. **쌍따옴표 필요한 경우**:
    - 공백이 포함된 별칭: `"사원 번호"`
    - 특수문자 포함: `"사원#번호"` (단, '_'는 제외)
    - 대소문자 그대로 출력: `"Employee_Name"`

### 별칭 예시
```sql
-- 올바른 사용
SELECT ENAME AS 이름, SAL 급여 FROM EMP ORDER BY 급여;

-- 잘못된 사용 (에러 발생)
SELECT ENAME AS 이름 FROM EMP WHERE 이름 = 'SMITH';  -- WHERE절에서 별칭 사용 불가

-- 쌍따옴표 필요한 경우
SELECT ENAME AS "사원 이름", SAL AS "급여#정보" FROM EMP;
```

---

## FROM 절

### 기본 사용법
```sql
-- 단일 테이블
SELECT * FROM EMP;

-- 여러 테이블 (조인 조건 없으면 카테시안 곱 발생 주의!)
SELECT * FROM EMP, DEPT WHERE EMP.DEPTNO = DEPT.DEPTNO;
```

### 테이블 별칭
```sql
-- Oracle: AS 사용 불가
SELECT E.ENAME, D.DNAME 
FROM EMP E, DEPT D 
WHERE E.DEPTNO = D.DEPTNO;

-- SQL Server: AS 사용/생략 모두 가능
SELECT E.ENAME, D.DNAME 
FROM EMP AS E, DEPT AS D 
WHERE E.DEPTNO = D.DEPTNO;
```

### 주의사항
1. **테이블 별칭 선언 후**: 반드시 별칭으로만 참조해야 함 (원래 테이블명 사용 시 에러)
2. **Oracle**: FROM절 생략 불가 (의미상 불필요한 경우 DUAL 테이블 사용)
3. **SQL Server**: FROM절 생략 가능 (오늘 날짜 조회 등)

### DUAL 테이블 (Oracle 전용)
```sql
-- Oracle에서 단순 계산이나 함수 실행 시
SELECT SYSDATE FROM DUAL;  -- 현재 날짜 조회
SELECT 2 + 3 FROM DUAL;    -- 단순 계산

-- SQL Server에서는 FROM절 생략 가능
SELECT GETDATE();  -- 현재 날짜 조회
SELECT 2 + 3;      -- 단순 계산
```

---

## 실행 순서의 중요성

### 왜 실행 순서를 알아야 하는가?
```sql
-- 별칭을 WHERE절에서 사용할 수 없는 이유
SELECT ENAME AS 이름          -- 5번째 실행
FROM EMP                      -- 1번째 실행  
WHERE 이름 = 'SMITH'          -- 2번째 실행 (별칭이 아직 정의되지 않음)
ORDER BY 이름;                -- 6번째 실행 (별칭 사용 가능)
```

### 올바른 사용 예시
```sql
-- 별칭은 ORDER BY에서만 사용
SELECT ENAME AS 이름, SAL AS 급여
FROM EMP
WHERE ENAME = 'SMITH'  -- 원본 컬럼명 사용
ORDER BY 급여 DESC;    -- 별칭 사용 가능
```

---

## 시험 출제 포인트

### 자주 나오는 문제
1. **SELECT문 절의 작성 순서** (문법 순서)
2. **내부 실행 순서** (파싱 순서)
3. **별칭 사용 가능한 절** (ORDER BY만 가능)
4. **쌍따옴표가 필요한 별칭** (공백, 특수문자, 대소문자 구분)
5. **Oracle vs SQL Server 차이점** (AS 사용 여부, FROM절 생략 여부)

### 핵심 암기 사항
- **작성 순서**: SELECT → FROM → WHERE → GROUP BY → HAVING → ORDER BY
- **실행 순서**: FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY
- **별칭 사용**: ORDER BY 절에서만 가능
- **Oracle 특징**: FROM절 필수, 테이블 별칭에 AS 사용 불가