---
layout: post
title: SQL_11_SubQuery
date : 16 August 2023
category : SQL
comments : true
---

# 1. **하위 쿼리**
 : 하위 쿼리(sub-query)란 SQL문장에 속하는 또 다른 SQL문장으로, 
  잘 사용하면 복잡한 SQL문장을 간단히 만들며, DBMS의 데이터 처리 속도를 빠르게 향상시킬 수 있다.
  실무에서 주로 사용되는 Sub-Query를 From절에 사용시 아래 2가지 용도로 주로 사용하게 된다.
    - (1) 조건에 맞는 대상자 선정 후 요약 : 이 경우, 테이블의 별칭을 반드시 주어야 한다.(별칭이 없을 시 에러 발생)
    - (2) 테이블 조인 시

## 1) **FROM절의 하위 쿼리**
### (1) 조건에 맞는 대상자 선정 후 요약
```sql
SELECT COL_1, COL_2
FROM (
    SELECT * 
    FROM TABLE_1_NM
    WHERE CONDITIONAL
    ) AS ABBV
WHERE CONDITIONAL
-- 이 경우, 테이블의 별칭을 반드시 주어야 한다.(별칭이 없을 시 에러 발생)
```

### (2) 테이블 조인 시
```sql
SELECT TABLE_1.COL_KEY, TABLE_2.COL_B
FROM TABLE_1 LEFT OUTER JOIN (
    SELECT COL_KEY, COL_B
    FROM TABLE_2
    WHERE CONDITIONAL
    ) AS TABLE_2
ON TABLE_1.COL_KEY = TABLE_2.COL_KEY;
```




### 예제
```sql
-- 1) 조건에 맞는 데이터 추출 후 요약하기
-- RCPT_ACCT 테이블을 활용하여, 
-- 현재 400000 이상의 잔액이 있으며 & 살아있는 계좌를 보유한 고객과 미보유한 고객의 수를 나타내 보자

---- (step_1) 400000 이상의 잔액이 있으며 & 살아있는 계좌 하위 쿼리 생성
SELECT DISTINCT SSN
        FROM RCPT_ACCT
        WHERE CNCL_DT IS NULL AND 
        RCPT_AMT > 400000;
-- +---------------+
-- | SSN           |
-- +---------------+
-- | 8204073333111 |
-- | 5707121111000 |
-- | 7706302222111 |
-- +---------------+


---- (step_2) 하위 쿼리를 고객 테이블에 LEFT OUTER JOIN
SELECT *
FROM CUST_PARTY AS CUST 
LEFT OUTER JOIN (SELECT DISTINCT SSN
                FROM RCPT_ACCT
                WHERE CNCL_DT IS NULL AND 
                RCPT_AMT > 400000
                ) AS RCPT
ON CUST.SSN = RCPT.SSN;
-- +---------------+----------+---------+---------------+---------------+---------------+
-- | SSN           | PARTY_NM | CUST_ID | TEL_NO        | MOBILE_NO     | SSN           |
-- +---------------+----------+---------+---------------+---------------+---------------+
-- | 5707121111000 | AR KIM   | 5670    | 02-555-6678   | 010-1111-1111 | 5707121111000 |
-- | 6508112222333 | JH RYU   | 3574    | 02-6666-4444  | 010-6666-6666 | NULL          |
-- | 6912081111222 | SH HONG  | 2357    | 031-4456-9887 | 010-2222-2222 | NULL          |
-- | 7105252222000 | JH KIM   | 8988    | 032-333-1111  | 010-4444-4444 | NULL          |
-- | 7706302222111 | JH LEE   | 7702    | 033-111-3355  | 010-5555-5555 | 7706302222111 |
-- | 8204073333111 | YC JUNG  | 5670    | 02-2222-1111  | 010-7777-7777 | 8204073333111 |
-- | 8311221111333 | MK KANG  | 3977    | 051-999-8888  | 010-3333-3333 | NULL          |
-- | 8911293333222 | JH JUN   | 6989    | 031-224-2222  | 010-8888-8888 | NULL          |
-- | 9011034444111 | SH LEE   | 5570    | 033-333-3333  | 010-9999-9999 | NULL          |
-- +---------------+----------+---------+---------------+---------------+---------------+

---- (step_3) 조건에 맞는 유저를 카운팅하기 위한 레이블 컬럼 생성
SELECT *,  
    CASE WHEN RCPT.SSN IS NOT NULL THEN 'O'
    ELSE 'X' END AS FLAG
FROM CUST_PARTY AS CUST 
LEFT OUTER JOIN (SELECT DISTINCT SSN
                FROM RCPT_ACCT
                WHERE CNCL_DT IS NULL AND 
                RCPT_AMT > 400000
                ) AS RCPT
ON CUST.SSN = RCPT.SSN;
-- +---------------+----------+---------+---------------+---------------+---------------+------+
-- | SSN           | PARTY_NM | CUST_ID | TEL_NO        | MOBILE_NO     | SSN           | FLAG |
-- +---------------+----------+---------+---------------+---------------+---------------+------+
-- | 5707121111000 | AR KIM   | 5670    | 02-555-6678   | 010-1111-1111 | 5707121111000 | O    |
-- | 6508112222333 | JH RYU   | 3574    | 02-6666-4444  | 010-6666-6666 | NULL          | X    |
-- | 6912081111222 | SH HONG  | 2357    | 031-4456-9887 | 010-2222-2222 | NULL          | X    |
-- | 7105252222000 | JH KIM   | 8988    | 032-333-1111  | 010-4444-4444 | NULL          | X    |
-- | 7706302222111 | JH LEE   | 7702    | 033-111-3355  | 010-5555-5555 | 7706302222111 | O    |
-- | 8204073333111 | YC JUNG  | 5670    | 02-2222-1111  | 010-7777-7777 | 8204073333111 | O    |
-- | 8311221111333 | MK KANG  | 3977    | 051-999-8888  | 010-3333-3333 | NULL          | X    |
-- | 8911293333222 | JH JUN   | 6989    | 031-224-2222  | 010-8888-8888 | NULL          | X    |
-- | 9011034444111 | SH LEE   | 5570    | 033-333-3333  | 010-9999-9999 | NULL          | X    |
-- +---------------+----------+---------+---------------+---------------+---------------+------+




---- (step_4) 레이블 컬럼의 값을 그룹핑하여, 최종 카운팅
---- (4-1) 조건에 부합하는 유저만 카운팅
SELECT 
    CASE WHEN RCPT.SSN IS NOT NULL THEN 'O'
    ELSE 'X' END AS FLAG,
    SUM(
        CASE WHEN RCPT.SSN IS NOT NULL THEN 1
        ELSE 0 END
        ) AS CNT
FROM CUST_PARTY AS CUST 
LEFT OUTER JOIN (SELECT DISTINCT SSN
                FROM RCPT_ACCT
                WHERE CNCL_DT IS NULL AND 
                RCPT_AMT > 400000
                ) AS RCPT
ON CUST.SSN = RCPT.SSN
GROUP BY 1;
-- +------+------+
-- | FLAG | CNT  |
-- +------+------+
-- | O    |    3 |
-- | X    |    0 |
-- +------+------+
---- (4-2) 조건 부합 여부별 유저 수 카운팅
SELECT   
    CASE WHEN RCPT.SSN IS NOT NULL THEN 'O'
    ELSE 'X' END AS FLAG,
    COUNT(*) 
FROM CUST_PARTY AS CUST 
LEFT OUTER JOIN (SELECT DISTINCT SSN
                FROM RCPT_ACCT
                WHERE CNCL_DT IS NULL AND 
                RCPT_AMT > 400000
                ) AS RCPT
ON CUST.SSN = RCPT.SSN
GROUP BY FLAG;
-- +------+----------+
-- | FLAG | COUNT(*) |
-- +------+----------+
-- | O    |        3 |
-- | X    |        6 |
-- +------+----------+


---- (+) 하위 쿼리 생성시, DISTINCT 미적용시
---- 조건에 해당하는 동일 유저의 2개의 계좌가 모두 카운팅되어, 
---- OUTER JOIN 과정에서, 2개의 계좌를 가진 유저의 행이 하나 복사되고,
---- 그 결과, 카운팅한 결과에서 조건에 해당하는 유저가 총 3명이 아니라 4명이라고 잘못 나오게 됨(4명이나 1명이 중복된 상태)
SELECT 
    CASE WHEN RCPT.SSN IS NOT NULL THEN 'O'
    ELSE 'X' END AS FLAG,
    SUM(
        CASE WHEN RCPT.SSN IS NOT NULL THEN 1
        ELSE 0 END
        ) AS CNT
FROM CUST_PARTY AS CUST 
LEFT OUTER JOIN (SELECT SSN
                FROM RCPT_ACCT
                WHERE CNCL_DT IS NULL AND 
                RCPT_AMT > 400000
                ) AS RCPT
ON CUST.SSN = RCPT.SSN
GROUP BY FLAG;
```
### TIP
 - 데이터 분석 시 키값이 되는 열들은 중복 없이 나열할 것
 - 중복을 제거한 형태의 테이블을 만든 후 조인해야 한다. (중복 미 제거시, 오류가 발생할 확률이 높아짐) 




<br>
--- 
--- 
<br>

## 2) **WHERE 조건절의 하위 쿼리**
### (1) IN을 사용한 WHERE 조건절의 하위 쿼리
 : WHERE 조건절에서 하위 쿼리는 IN 연산자와 함게 사용된다. 
  전체 모집단에서 특정 세그먼트만 추출할 때 WHERE 조건절의 하위쿼리가 유용하게 사용된다. 
```sql
SELECT COL_1, COL_2
FROM TABLE_1_NM
WHERE COL_NM_TARGET IN (
    SELECT COL_NM_TARGET 
    FROM TABLE_2_NM
    WHERE CONDITIONAL
    );
```


### 예제
```sql
-- RCPT_ACCT 테이블과 CUST_PARTY 테이블을 이용하여,
-- 현재 사라있는 신용카드 보유 고객의,
-- 주민등록번호, 이름, 아이디, 자택번호 및 휴대폰 번호를 나타내보자.(WHERE 조건절 하위 쿼리 사용)

---- (step_1)  살아있는 계좌 하위 쿼리 생성
SELECT DISTINCT SSN
FROM RCPT_ACCT
WHERE CNCL_DT IS NULL;
-- +---------------+
-- | SSN           |
-- +---------------+
-- | 8204073333111 |
-- | 5707121111000 |
-- | 6508112222333 |
-- | 7706302222111 |
-- +---------------+

---- (step_2) CUST_PARTY 테이블의 주민등록번호, 이름, 아이디, 자택번호 및 휴대폰 번호 정보 출력
SELECT SSN, PARTY_NM, CUST_ID, TEL_NO, MOBILE_NO
FROM CUST_PARTY;
-- +---------------+----------+---------+---------------+---------------+
-- | SSN           | PARTY_NM | CUST_ID | TEL_NO        | MOBILE_NO     |
-- +---------------+----------+---------+---------------+---------------+
-- | 5707121111000 | AR KIM   | 5670    | 02-555-6678   | 010-1111-1111 |
-- | 6508112222333 | JH RYU   | 3574    | 02-6666-4444  | 010-6666-6666 |
-- | 6912081111222 | SH HONG  | 2357    | 031-4456-9887 | 010-2222-2222 |
-- | 7105252222000 | JH KIM   | 8988    | 032-333-1111  | 010-4444-4444 |
-- | 7706302222111 | JH LEE   | 7702    | 033-111-3355  | 010-5555-5555 |
-- | 8204073333111 | YC JUNG  | 5670    | 02-2222-1111  | 010-7777-7777 |
-- | 8311221111333 | MK KANG  | 3977    | 051-999-8888  | 010-3333-3333 |
-- | 8911293333222 | JH JUN   | 6989    | 031-224-2222  | 010-8888-8888 |
-- | 9011034444111 | SH LEE   | 5570    | 033-333-3333  | 010-9999-9999 |
-- +---------------+----------+---------+---------------+---------------+

---- (step_3) CUST_PARTY 테이블에서, WHERE절 하위쿼리를 활용한 조건 추가
SELECT SSN, PARTY_NM, CUST_ID, TEL_NO, MOBILE_NO
FROM CUST_PARTY
WHERE SSN IN (
    SELECT DISTINCT SSN
    FROM RCPT_ACCT
    WHERE CNCL_DT IS NULL
);
-- +---------------+----------+---------+--------------+---------------+
-- | SSN           | PARTY_NM | CUST_ID | TEL_NO       | MOBILE_NO     |
-- +---------------+----------+---------+--------------+---------------+
-- | 8204073333111 | YC JUNG  | 5670    | 02-2222-1111 | 010-7777-7777 |
-- | 5707121111000 | AR KIM   | 5670    | 02-555-6678  | 010-1111-1111 |
-- | 6508112222333 | JH RYU   | 3574    | 02-6666-4444 | 010-6666-6666 |
-- | 7706302222111 | JH LEE   | 7702    | 033-111-3355 | 010-5555-5555 |
-- +---------------+----------+---------+--------------+---------------+
```

### TIP
```sql
---- (+) CUST_PARTY 테이블에서 SSN가 5707121111000인 유저만 출력해보자
---- 이때, 조건문으로 사용된 하위쿼리의 결과물이 단일행이라면, 
---- WHRER절의 IN 대신, '='을 사용할 수도 있다.
SELECT SSN, PARTY_NM, CUST_ID, TEL_NO, MOBILE_NO
FROM CUST_PARTY
WHERE SSN = (
    SELECT DISTINCT SSN
    FROM RCPT_ACCT
    WHERE SSN = '5707121111000'
);
-- +---------------+----------+---------+-------------+---------------+
-- | SSN           | PARTY_NM | CUST_ID | TEL_NO      | MOBILE_NO     |
-- +---------------+----------+---------+-------------+---------------+
-- | 5707121111000 | AR KIM   | 5670    | 02-555-6678 | 010-1111-1111 |
-- +---------------+----------+---------+-------------+---------------+


---- (+) 그러나 아래처럼, 조건절의 결과물이 1개이상의 복수행일 때 WHERE 절에 '='을 사용하면 에러가 발생한다.
SELECT SSN, PARTY_NM, CUST_ID, TEL_NO, MOBILE_NO
FROM CUST_PARTY
WHERE SSN = (
    SELECT DISTINCT SSN
    FROM RCPT_ACCT
    WHERE SSN IN ('5707121111000', '7706302222111')
);
-- ERROR 1242 (21000): Subquery returns more than 1 row
```




## 3) **Correlated Subquery**
 : 하나의 행에 복수의 행의 정보로 집계된 값을 삽입하는 방법 
  - 활용 1) 이동 평균
    :  계산시 특정 시점을 기준으로 과거 N일치의 값을 불러와야함.
  - 활용 2) 누적 합계
    :  계산시 특정 시점을 기준으로 과거 모든 일수를 불러옴.
```sql
-- 이동 평균
SELECT 
t1.date, 
m1.value,
(select avg(value)
 from sample_table t2
 WHERE t2.date BETWEEN DATE_ADD(t1.date, INTERVAL -1 DAY) AND DATE_ADD(t1.date, INTERVAL +1 DAY))
FROM sample_table t1
```




--- 
<br>


## + 연습문제
 - CUST_PARTY 테이블
 - RCPT_ACCT 테이블




### 예제
```sql
-- 1) EX_1
-- CUSTOMERS와 ORDERS 테이블을 이용하여, [고객ID / 고객 이름 / 주문번호]를 나타내고, 
-- 주문 이력이 있는 고객은 1, 없는 고객은 0으로 표시해보자. 
SELECT CUST.CUST_ID, CUST.CUST_NM, ORD.ORDER_ID
    CASE WHEN ORD.ORDER_ID IS NOT NULL THEN 1
    ELSE 0 END AS ORDER_FLG
FROM CUSTOMERS AS CUST LEFT OUTER JOIN ORDERS AS ORD
ON CUST.CUST_ID = ORD.CUST_ID
ORDER BY CUST.CUST_ID ;

-- 2) EX_2
-- CUSTOMERS와 ORDERS 테이블을 이용하여, 
-- 주문이력이 있는 고객과 없는 고객의 숫자를 알아보자
SELECT 
    CASE WHEN ORD.ORDER_ID IS NOT NULL THEN 1
    ELSE 0 END AS ORDER_FLG,
    COUNT(*) AS CNT
FROM CUSTOMER AS CUST LEFT OUTER JOIN ORDERS AS ORD
ON CUST.CUST_ID = ORD.CUST_ID
GROUP BY ORDER_FLG

-- 3) EX_3
-- EMPLOYEE와 ORDERS 테이블을 이용하여, 
-- 직원아이디, 이름, 주문번호를 나타내고, 
-- 판매실적이 있는 직원은 1, 없는 직원은 0으로 표기후
-- 직원 아이디로 오름차순 정렬
SELECT EMP.EMP_ID, EMP.NM, ORD.ORDER_ID,
    CASE WHEN ORD.ORDER_ID IS NOT NULL THEN 1
    ELSE 0 END AS SALES_FLG 
FROM EMPLOYEE AS EMP LEFT OUTER JOIN ORDERS AS ORD
ON EMP.EMP_ID = ORD.EMP_ID
ORDER BY EMP.EMP_ID;


-- 4) EX_4
-- CUSTOMERS, EMPLOYEE, ORDERS 테이블을 이용하여, 
-- 직원아이디, 이름, 해당 직원이 맡았던 주문번호를 나타내고,
-- 해당 주문번호와 고객 아이디를 매칭시켜 고객이름을 표기하라 (outer join만 활용)
SELECT EMP.EMP_ID, EMP.NM, ORD.ORDER_ID, CUST.CUST_NM
FROM
    EMPLOYEE AS EMP 
    LEFT JOIN ORDERS AS ORD
    ON EMP.EMP_ID = ORD.EMP_ID
    LEFT JOIN CUSTOMERS AS CUST
    ON ORD.CUST_ID = CUST.CUST_ID
```
### TIP
1) 다음과 같이 WHERE 조건절을 활용하여 join을 할 수 있다.
   1) `LEFT OUTER JOIN` --> `WHERE 조건절`에 `*=`
   2) `RIGHT OUTER JOIN` --> `WHERE 조건절`에 `=*`
2) 테이블 대신, SELECT 구문을 활용하여, (서브 쿼리) 테이블을 만들 수 있다.
3) ACCESS, MY SQL 등 몇몇 DbMS에서는 FULL OUTER JOIN 키워드가 지원되지 않는다.
4) SELECT문에 출력할 열 이름을 적을 땐, 어느 테이블에서 참조되는 열인지 나타내는 좋다.

```sql
-- 1) WHERE & `*=` 를 할용한 조인
SELECT EMP.EMP_ID, EMP.NM, ORD.ORDER_ID, 
FROM EMPLOYEE AS EMP, ORDERS AS ORD
WHERE EMP.EMP_ID *= ORD.EMP_ID;

-- 2) 테이블 대신, SELECT 구문을 활용하여, (서브 쿼리) 테이블을 만들 수 있다.
-- SELECT문을 이용하여 테이블 일부만 합치기
SELECT EMP.EMP_ID, EMP.NM, ORD.ORDER_ID, 
FROM EMPLOYEE AS EMP 
    LEFT OUTER JOIN 
        (SELECT ORDER_ID, CUST_ID FROM ORDERS) AS ORD
ON EMP.EMP_ID = ORD.EMP_ID;
```


<br>

--- 
--- 

<br>



# 2. **행(Row) 합치기**
## 1) **UNION**
 : 2개 이상의 테이블의 행을 합칠 때 사용하며, 이때 중복을 제외함.
```sql
SELECT 열_1, 열_2 FROM 테이블_1 WHERE 조건절
UNION
SELECT 열_1, 열_2 FROM 테이블_2 WHERE 조건절
ORDER BY 1;
```
#### 주의사항
 - **1) UNION 연산자로 합쳐지는 SELECT문의 열의 숫자는 반드시 동일해야한다.**
 - **2) SELECT문의 각 데이터 타입은 일치해야 한다.**
 - UNION 연산자는 중복값이 있는 경우 한가지만 표기하며, 중복값을 모두 출력하고 싶은 경우 `UNION ALL`연산자를 사용해야 한다.
 - `ORDER BY`절을 사용하여 데이터를 정렬하고 싶다면, 문장의 마지막에 작성하면 된다.(2번째 SELECT문만 정렬할 것 같지만, 데이터가 합쳐진 후 전체에 ORDER BY가 적용되기 때문이다.)

### 예제
```sql
-- 1) EX_1
-- CUSTOMERS와 EMPLOYEE 테이블을 이용하여, 
-- [직원 이름과 고객이름]을 합친 후
-- 오름차순으로 정렬해보자
SELECT EMP.EMP_NM AS NM FROM EMPLOYEE AS EMP 
UNION
SELECT CUST.CUST_NM AS NM FROM CUSTOMERS AS CUST
ORDER BY 1 ;

-- 위 예제에서,합쳐지는 필드를 동일한 컬럼명으로 변경하지 않았을 경우.
SELECT EMP.EMP_NM FROM EMPLOYEE AS EMP 
UNION
SELECT CUST.CUST_NM FROM CUSTOMERS AS CUST
ORDER BY 1;
-- 첫번째 SELECT문의 "EMP_NM" 이름으로 두번째 SELECT문 컬럼에 적용함.
-- 즉, 결과의 열이름은 첫 번째 문장의 열 이름으로 출력됨.
```

### TIP
1) UNION 연산자 안의 SELECT문에는 같은 열, 식, 집계 합수를 포함해야 한다.
2) 만약 3개의 SELECT 문장을 결합한다면 두개의 UNION 연산자가 필요하다



## 2) **UNION ALL**
 : 2개 이상의 테이블의 행을 합칠 때 사용하며, 이때 중복을 제외하지 않고 모두 포함.
```sql
SELECT 열_1, 열_2 FROM 테이블_1 WHERE 조건절
UNION ALL
SELECT 열_1, 열_2 FROM 테이블_2 WHERE 조건절
ORDER BY 1;
```

### 예제
```sql
-- 1) EX_1
-- CUSTOMERS와 ORDERS 테이블을 이용하여, 
-- 중복값을 허용한채, [고객ID]을 합쳐서 오름차순으로 정렬해보자.
-- 오름차순으로 정렬해보자
SELECT CUST.CUST_ID AS NM FROM CUSTOMERS AS CUST 
UNION ALL
SELECT ORD.CUST_ID AS NM FROM ORDERS AS ORD
ORDER BY 1 ;
```


--- 

<br>


## + 연습문제
 - DLQ_PARTY 

```
SELECT * FROM DLQ_PARTY;
+---------------+---------+------------+------------+--------------+----------+
| SSN           | ACCT_NO | DLQ_ST     | DLQ_END    | DLQ_DURATION | CURR_DLQ |
+---------------+---------+------------+------------+--------------+----------+
| 6912081111222 | 32110   | 2012-07-30 | 2012-08-15 |           16 | N        |
| 8204073333111 | 88930   | 2012-09-21 | 2012-10-01 |           10 | N        |
| 8204073333111 | 35780   | 2013-01-26 | 2013-01-29 |            3 | N        |
| 7706302222111 | 78320   | 2013-11-01 | NULL       |           31 | Y        |
| 6912081111222 | 87120   | 2013-10-01 | NULL       |           62 | Y        |
| 8204073333111 | 56830   | 2013-11-18 | 2013-11-28 |           10 | N        |
| 8311221111333 | 78720   | 2013-11-14 | NULL       |           18 | Y        |
| 8311221111333 | 98730   | 2013-11-16 | NULL       |           16 | Y        |
| 6508112222333 | 57830   | 2012-12-01 | 2012-12-02 |            1 | N        |
| 6508112222333 | 78770   | 2013-09-19 | NULL       |           74 | Y        |
+---------------+---------+------------+------------+--------------+----------+
```

```sql
-- 1) 연체일이 30일이 안된 고객들에게 문자를 보내고자 할 때
-- 대상이 되는 고객의 주민등록번호, 이름, 휴대폰번호를 나열해보자.
---- (step_1) 연체일이 30일이 안된 고객 명단 추출
SELECT DISTINCT SSN 
FROM DLQ_PARTY
WHERE DLQ_DURATION < 30 AND CURR_DLQ = 'Y';

---- (step_2) 고객정보 테이블과 Merge
SELECT CUST.SSN, CUST.PARTY_NM, CUST.MOBILE_NO
FROM CUST_PARTY AS CUST INNER JOIN (
    SELECT DISTINCT SSN 
    FROM DLQ_PARTY
    WHERE DLQ_DURATION < 30 AND CURR_DLQ = 'Y'
    ) AS DLQ
ON CUST.SSN = DLQ.SSN;
```


```sql
-- 2) 현재 연체중이거나, 과거 10일 이상 연체 기록이 있는 고객들을 제외한
-- 대출 가능 고객 리스트를 뽑아보자 (외부 조인 활용)
---- (step_1) 현재 연체중이거나, 과거 10일 이상 연체 기록이 있는 유저 추출
SELECT DISTINCT SSN, 1 AS FLAG
FROM DLQ_PARTY
WHERE CURR_DLQ = 'Y' OR (DLQ_DURATION >= 10 AND CURR_DLQ = 'N');
-- +---------------+------+
-- | SSN           | FLAG |
-- +---------------+------+
-- | 6912081111222 |    1 |
-- | 8204073333111 |    1 |
-- | 7706302222111 |    1 |
-- | 8311221111333 |    1 |
-- | 6508112222333 |    1 |
-- +---------------+------+

---- (step_2) 고객 테이블에 LEFT OUTER MERGE
SELECT CUST.SSN, CUST.PARTY_NM, CUST.MOBILE_NO, DLQ.FLAG
FROM CUST_PARTY AS CUST LEFT OUTER JOIN (
    SELECT DISTINCT SSN, 1 AS FLAG
    FROM DLQ_PARTY
    WHERE CURR_DLQ = 'Y' OR (DLQ_DURATION >= 10 AND CURR_DLQ = 'N')
    ) AS DLQ
ON CUST.SSN = DLQ.SSN;
-- +---------------+----------+---------------+------+
-- | SSN           | PARTY_NM | MOBILE_NO     | FLAG |
-- +---------------+----------+---------------+------+
-- | 5707121111000 | AR KIM   | 010-1111-1111 | NULL |
-- | 6508112222333 | JH RYU   | 010-6666-6666 |    1 |
-- | 6912081111222 | SH HONG  | 010-2222-2222 |    1 |
-- | 7105252222000 | JH KIM   | 010-4444-4444 | NULL |
-- | 7706302222111 | JH LEE   | 010-5555-5555 |    1 |
-- | 8204073333111 | YC JUNG  | 010-7777-7777 |    1 |
-- | 8311221111333 | MK KANG  | 010-3333-3333 |    1 |
-- | 8911293333222 | JH JUN   | 010-8888-8888 | NULL |
-- | 9011034444111 | SH LEE   | 010-9999-9999 | NULL |
-- +---------------+----------+---------------+------+

---- (step_3) FLAG 컬럼을 기준으로, 대출 가능 고객만 필터링
SELECT CUST.SSN, CUST.PARTY_NM, CUST.MOBILE_NO
FROM CUST_PARTY AS CUST LEFT OUTER JOIN (
    SELECT DISTINCT SSN, 1 AS FLAG
    FROM DLQ_PARTY
    WHERE CURR_DLQ = 'Y' OR (DLQ_DURATION >= 10 AND CURR_DLQ = 'N')
    ) AS DLQ
ON CUST.SSN = DLQ.SSN
WHERE DLQ.FLAG IS NULL ;
-- +---------------+----------+---------------+
-- | SSN           | PARTY_NM | MOBILE_NO     |
-- +---------------+----------+---------------+
-- | 5707121111000 | AR KIM   | 010-1111-1111 |
-- | 7105252222000 | JH KIM   | 010-4444-4444 |
-- | 8911293333222 | JH JUN   | 010-8888-8888 |
-- | 9011034444111 | SH LEE   | 010-9999-9999 |
-- +---------------+----------+---------------+
```


```sql
-- 3) 위 문제를 WHERE 조건절을 활용하여 작성해보자.
SELECT DISTINCT SSN
FROM DLQ_PARTY
WHERE CURR_DLQ = 'Y' OR (DLQ_DURATION >= 10 AND CURR_DLQ = 'N');
-- +---------------+
-- | SSN           |
-- +---------------+
-- | 6912081111222 |
-- | 8204073333111 |
-- | 7706302222111 |
-- | 8311221111333 |
-- | 6508112222333 |
-- +---------------+

---- (step_2) 고객 테이블의 SSN값을 WHERE 조건절 기반으로 필터링
SELECT SSN, PARTY_NM, MOBILE_NO
FROM CUST_PARTY 
WHERE SSN NOT IN (
    SELECT DISTINCT SSN
    FROM DLQ_PARTY
    WHERE CURR_DLQ = 'Y' OR (DLQ_DURATION >= 10 AND CURR_DLQ = 'N')
    );
-- +---------------+----------+---------------+------+
-- | SSN           | PARTY_NM | MOBILE_NO     | FLAG |
-- +---------------+----------+---------------+------+
-- | 5707121111000 | AR KIM   | 010-1111-1111 | NULL |
-- | 6508112222333 | JH RYU   | 010-6666-6666 |    1 |
-- | 6912081111222 | SH HONG  | 010-2222-2222 |    1 |
-- | 7105252222000 | JH KIM   | 010-4444-4444 | NULL |
-- | 7706302222111 | JH LEE   | 010-5555-5555 |    1 |
-- | 8204073333111 | YC JUNG  | 010-7777-7777 |    1 |
-- | 8311221111333 | MK KANG  | 010-3333-3333 |    1 |
-- | 8911293333222 | JH JUN   | 010-8888-8888 | NULL |
-- | 9011034444111 | SH LEE   | 010-9999-9999 | NULL |
-- +---------------+----------+---------------+------+
```
```sql
-- 4) 현재 연체중인 고객의 최대 연체일 수를 산출한 후,
---- "XXX 고객님의 현재 연체일 수는 XX일 입니다."라는 문구를 만들고,
---- 주민등록번호, 이름, 휴대폰번호, 연체일 수와 함께 나열해보자.

---- (step_1) 현재 연체중인 고객의 최대 연체일 수 산출
SELECT SSN,
    MAX(DLQ_DURATION) AS MAX_DUR
FROM DLQ_PARTY
WHERE CURR_DLQ = 'Y' 
GROUP BY SSN;
-- +---------------+---------+
-- | SSN           | MAX_DUR |
-- +---------------+---------+
-- | 7706302222111 |      31 |
-- | 6912081111222 |      62 |
-- | 8311221111333 |      18 |
-- | 6508112222333 |      74 |
-- +---------------+---------+


---- (step_2) "XXX 고객님의 현재 연체일 수는 XX일 입니다."라는 문구 생성
SELECT CONCAT(CUST.PARTY_NM,'고객님의 현재 연체일 수는 ',DLQ.MAX_DUR,'일 입니다.') AS COMMENT
FROM CUST_PARTY AS CUST INNER JOIN (
    SELECT SSN, 
        MAX(DLQ_DURATION) AS MAX_DUR
    FROM DLQ_PARTY
    WHERE CURR_DLQ = 'Y' 
    GROUP BY SSN
    ) AS DLQ
ON CUST.SSN = DLQ.SSN;
-- +--------------------------------------------------------------+
-- | COMMENT                                                      |
-- +--------------------------------------------------------------+
-- | JH LEE고객님의 현재 연체일 수는 31일 입니다.                 |
-- | SH HONG고객님의 현재 연체일 수는 62일 입니다.                |
-- | MK KANG고객님의 현재 연체일 수는 18일 입니다.                |
-- | JH RYU고객님의 현재 연체일 수는 74일 입니다.                 |
-- +--------------------------------------------------------------+

---- (step_3)주민등록번호, 이름, 휴대폰번호, 연체일 수와 함께 나열
SELECT CUST.SSN, CUST.PARTY_NM, CUST.MOBILE_NO, DLQ.MAX_DUR,
    CONCAT(CUST.PARTY_NM,'고객님의 현재 연체일 수는 ',DLQ.MAX_DUR,'일 입니다.') AS COMMENT
FROM CUST_PARTY AS CUST INNER JOIN (
    SELECT SSN, 
        MAX(DLQ_DURATION) AS MAX_DUR
    FROM DLQ_PARTY
    WHERE CURR_DLQ = 'Y' 
    GROUP BY SSN
    ) AS DLQ
ON CUST.SSN = DLQ.SSN;

-- +---------------+----------+---------------+---------+--------------------------------------------------------------+
-- | SSN           | PARTY_NM | MOBILE_NO     | MAX_DUR | COMMENT                                                      |
-- +---------------+----------+---------------+---------+--------------------------------------------------------------+
-- | 7706302222111 | JH LEE   | 010-5555-5555 |      31 | JH LEE고객님의 현재 연체일 수는 31일 입니다.                 |
-- | 6912081111222 | SH HONG  | 010-2222-2222 |      62 | SH HONG고객님의 현재 연체일 수는 62일 입니다.                |
-- | 8311221111333 | MK KANG  | 010-3333-3333 |      18 | MK KANG고객님의 현재 연체일 수는 18일 입니다.                |
-- | 6508112222333 | JH RYU   | 010-6666-6666 |      74 | JH RYU고객님의 현재 연체일 수는 74일 입니다.                 |
-- +---------------+----------+---------------+---------+--------------------------------------------------------------+



```





#### Reference
- 칼퇴족 김대리는 알고 나만 모르는 SQL - 책밥
