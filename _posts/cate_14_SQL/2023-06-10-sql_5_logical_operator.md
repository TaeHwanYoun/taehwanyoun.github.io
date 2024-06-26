---
layout: post
title: SQL_5_logical operator
date : 10 Jun 2023
category : SQL
comments : true
---



# 1. AND, OR 연산자
### 1) AND
`- SELECT * FROM 테이블명 WHERE '조건절_1' AND '조건절_2';`

```sql
-- 1) 2개의 조건문
SELECT * FROM DLQ_PARTY
WHERE DLQ_DURATION <= 20
    AND DLQ_END IS NOT NULL;
```
<center> 
<img src = '/assets/14_SQL/sql_5/sql_5_1.png' width = '80%'>  
<img src = '/assets/14_SQL/sql_5/sql_5_2.png' width = '80%'> 
</center>


--- 

<br>

### 2) OR
`- SELECT * FROM 테이블명 WHERE '조건절_1' OR '조건절_2';`

```sql
SELECT * FROM DLQ_PARTY
WHERE DLQ_ST
BETWEEN '2012-09-01' AND '2013-10-31'
OR DLQ_DURATION >= 20;
```

<center> <img src = '/assets/14_SQL/sql_5/sql_5_3.png' width = '80%'> </center>

--- 

<br>

### 3) AND와 OR 우선순위 
 - **AND 연산자가 OR 연산자보다 선행한다.**
 - AND 연산자보다, OR연산자를 먼저 처리하고 싶은 경우, 해당 연산을 괄호로 묶어줘야한다.

```sql
-- 1) 괄호를 사용하지 않았을 때, 연산자 우선 순위
SELECT * FROM DLQ_PARTY
WHERE DLQ_DURATION > 15 
AND ACCT_NO < 70000
OR DLQ_ST >= '2013-01-01';
```

<center> <img src = '/assets/14_SQL/sql_5/sql_5_4.png' width = '80%'> </center>

```sql
-- 2) 괄호를 사용했을 때, 연산자 우선 순위
SELECT * FROM DLQ_PARTY
WHERE DLQ_DURATION > 15 
AND (ACCT_NO < 70000
OR DLQ_ST >= '2013-01-01');
```

<center> <img src = '/assets/14_SQL/sql_5/sql_5_5.png' width = '80%'> </center>

--- 

<br>
<br>


# 2. IN, NOT IN
### 1) IN 연산자
 - `- SELECT * FROM 테이블명 WHERE '비교할 컬럼' IN (조건_1, 조건_2);`

```sql
-- 6,7등급의 사원을 모두 선택하되, 'SS CHANG', 'JW PARK', 'SW HONG'은 반드시 포함
SELECT * FROM EMPLOYEE
WHERE NM IN ('SS CHANG', 'JW PARK', 'SW HONG') 
AND GRADE IN (6, 7);
```

<center> <img src = '/assets/14_SQL/sql_5/sql_5_6.png' width = '80%'> </center>

 - IN 연산자는 OR 연산자보다, 처리속도가 빠르다. 따라서 데이터가 클수록 IN 연산자를 잘 사용하면 좋다.
 - IN 연산자 안에 다른 SELECT 문장을 사용할 수 있다. (이는 하위 쿼리-SUB QUERY에서 자세히 언급)

--- 

<br>

### 2) NOT IN 연산자
 - `- SELECT * FROM 테이블명 WHERE '비교할 컬럼' NOT IN (조건_1, 조건_2);`

```sql
-- 6,7등급의 사원을 모두 선택하되, 'SS CHANG', 'JW PARK', 'SW HONG'은 제외
SELECT * FROM EMPLOYEE
WHERE NM NOT IN ('SS CHANG', 'JW PARK', 'SW HONG') 
AND GRADE IN (6, 7);
```

--- 

<br>

## + 연습문제

```sql
-- 1) 직전 3개월 누적 카드사용금액이 1만원 이하인 고객 추출
SELECT CMF, PARTY_NM, (
  COALESCE(TOT_AMT_1, 0) +
  COALESCE(TOT_AMT_2, 0) +
  COALESCE(TOT_AMT_3, 0)) AS TOT_AMT_1_3 
FROM PERF_MAST_201312
WHERE (
  COALESCE(TOT_AMT_1, 0) +
  COALESCE(TOT_AMT_2, 0) +
  COALESCE(TOT_AMT_3, 0)) < 10000;
```
<center> <img src = '/assets/14_SQL/sql_5/sql_5_7.png' width = '80%'> </center>


```sql
-- 2) 직전 3개월 누적 카드사용금액이 1만원 이하인 고객(이탈 고객) 추출
SELECT CMF, PARTY_NM, (
  COALESCE(TOT_AMT_1, 0) +
  COALESCE(TOT_AMT_2, 0) +
  COALESCE(TOT_AMT_3, 0)) AS TOT_AMT 
FROM PERF_MAST_201312
WHERE COALESCE(TOT_AMT_1, 0) < 5000 
AND COALESCE(TOT_AMT_2, 0) < 5000
AND COALESCE(TOT_AMT_3, 0) < 5000;
```

<center> <img src = '/assets/14_SQL/sql_5/sql_5_8.png' width = '80%'> </center>


```sql
-- 3) 이탈 고객이면서, 세그먼트가 PB 고객인 고객들 추출
SELECT CMF, PARTY_NM, SEG, (
  COALESCE(TOT_AMT_1, 0) +
  COALESCE(TOT_AMT_2, 0) +
  COALESCE(TOT_AMT_3, 0)) AS TOT_AMT 
FROM PERF_MAST_201312
WHERE COALESCE(TOT_AMT_1, 0) < 5000 
AND COALESCE(TOT_AMT_2, 0) < 5000
AND COALESCE(TOT_AMT_3, 0) < 5000
AND SEG IN ('PB');
```

<center> <img src = '/assets/14_SQL/sql_5/sql_5_9.png' width = '80%'> </center>

```sql
-- 4) 직전 3개월동안, 사용금액이 계속 줄어드는 고객중, 
-- 총 사용 금액이 7,000원 이상인 고객 추출
SELECT *, (
  COALESCE(TOT_AMT_1, 0) +
  COALESCE(TOT_AMT_2, 0) +
  COALESCE(TOT_AMT_3, 0)) AS TOT_AMT 
FROM PERF_MAST_201312
WHERE COALESCE(TOT_AMT_1, 0) <= COALESCE(TOT_AMT_2, 0)
AND COALESCE(TOT_AMT_2, 0) <= COALESCE(TOT_AMT_3, 0)
AND (
  COALESCE(TOT_AMT_1, 0) +
  COALESCE(TOT_AMT_2, 0) +
  COALESCE(TOT_AMT_3, 0)
  ) > 7000
;
```

<center> <img src = '/assets/14_SQL/sql_5/sql_5_10.png' width = '80%'> </center>





#### Reference
- 칼퇴족 김대리는 알고 나만 모르는 SQL - 책밥
