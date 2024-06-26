---
layout: post
title: SQL_8_함수 활용하기
date : 22 Jun 2023
category : SQL
comments : true
---


# 1. 집계 데이터를 활용한 숫자 데이터

| **함수**  |  **설명**  | 
|:-: |:----- |
| **COUNT** | COUNT() : NULL값을 포함한 행의 수|
|  | COUNT(col_nm_) : NULL값을 제외한 행의 수 |
|  | COUNT(DISTINCT(col_nm_)) : NULL값을 제외한 행의 수 |
| **SUM** | SUM(col_nm_) : 전체 합계 |
| **AVG** | AVG(col_nm_) : 평균 |
| **MAX** | MAX(col_nm_) : 최댓값 |
| **MIN** | MIN(col_nm_) : 최솟값 |
| **STDENV or STD** | STDENV(col_nm_) : (Orcle or Mysql)표준편차 |
| **VARIANCE** | VARIANCE(col_nm_) : 분산 |


```sql
-- EX)
-- 1) 전체 행 카운트
SELECT COUNT(*) FROM PERF_MAST_201312;
-- +----------+
-- | COUNT(*) |
-- +----------+
-- |       11 |
-- +----------+

-- 2) TOT_AMT_1열의 NULL 제외 행 카운트
SELECT COUNT(TOT_AMT_1) FROM PERF_MAST_201312;
-- +------------------+
-- | COUNT(TOT_AMT_1) |
-- +------------------+
-- |                8 |
-- +------------------+

-- 3) SEG열의 유니크한 행값 카운트
SELECT COUNT(DISTINCT(SEG)) FROM PERF_MAST_201312;
-- +----------------------+
-- | COUNT(DISTINCT(SEG)) |
-- +----------------------+
-- |                    2 |
-- +----------------------+

-- 4) TOT_AMT_1의 평균 계산 (Null 제외)
SELECT AVG(TOT_AMT_1) FROM PERF_MAST_201312;
-- +----------------+
-- | AVG(TOT_AMT_1) |
-- +----------------+
-- |   1496144.7500 |
-- +----------------+

-- 5) TOT_AMT_1의 평균 계산 (Null은 0으로 치환)
SELECT AVG(COALESCE(TOT_AMT_1, 0)) AS AVG_TOTAL_AMT 
FROM PERF_MAST_201312;
-- +----------------------+
-- | COUNT(DISTINCT(SEG)) |
-- +----------------------+
-- |                    2 |
-- +----------------------+

-- 6) TOT_AMT_1열의 표준편차 계산
SELECT STD(TOT_AMT_1) FROM PERF_MAST_201312;
-- +--------------------+
-- | STD(TOT_AMT_1)     |
-- +--------------------+
-- | 3173918.0474547837 |
-- +--------------------+
```

 - 집계 함수 사용시, NULL은 자동으로 계산에서 무시된다.
 - 때문에, **0값을 평균에 포함하고 싶을 경우, `COALESCE`함수를 사용하여 0으로 치환**한 후 AVG계산이 필요하다.



--- 

<br>

# 2. 조건문 이해하기

### 1) CASE WHEN
 : python은 if / elif / else문과 같은 역할을 하며,
 집계함수인 `GROUP BY`절과 결합하여 자주 사용된다.
```sql
SELECT col_nm_1,
    CASE WHEN [conditioanl_1] THEN [output_1]
         WHEN [conditioanl_2] THEN [output_2]
         ELSE [output_3] END AS new_col_nm
FROM 테이블명;   
```


```sql
-- EX)
-- 1) SEG 기준 PB는 30%인상, MASS는 50%인하된 구매금액(TOT_AMT_1) 테이블 계산
SELECT PARTY_NM, SEG, TOT_AMT_1, 
    CASE WHEN SEG = 'PB' THEN TOT_AMT_1*1.3
         WHEN SEG = 'MASS' THEN TOT_AMT_1*0.5
         ELSE TOT_AMT_1 END AS TOT_AMT_1_FIX
FROM PERF_MAST_201312;
-- +-----------+------+-----------+---------------+
-- | PARTY_NM  | SEG  | TOT_AMT_1 | TOT_AMT_1_FIX |
-- +-----------+------+-----------+---------------+
-- | 임하영    | PB   |   1000400 |     1300520.0 |
-- | 김아름    | PB   |       790 |        1027.0 |
-- | 김지철    | PB   |      NULL |          NULL |
-- | 김지연    | MASS |    254860 |      127430.0 |
-- | 윤일상    | MASS |     88805 |       44402.5 |
-- | 홍지은    | MASS |      NULL |          NULL |
-- | 이선우    | MASS |     90700 |       45350.0 |
-- | 최혜연    | MASS |      NULL |          NULL |
-- | 이동건    | PB   |   9846000 |    12799800.0 |
-- | 강성범    | MASS |       540 |         270.0 |
-- | 김지현    | PB   |    687063 |      893181.9 |
-- +-----------+------+-----------+---------------+
```
```sql
-- 2) 구매금액(TOT_AMT_1)에 대하여, SEG 기준 PB는 30%인상, MASS는 50%인하하며,
--  TOT_AMT_1이 TOT_AMT_2보다 같거나 클 경우, 추가 20% 인상하며, 작을 경우 20%인하하라
SELECT PARTY_NM, SEG, TOT_AMT_2, TOT_AMT_1,
    CASE WHEN SEG = 'PB' AND TOT_AMT_1 >= TOT_AMT_2 THEN TOT_AMT_1 * 1.3 * 1.2
    WHEN SEG = 'PB' AND TOT_AMT_1 < TOT_AMT_2 THEN TOT_AMT_1 * 1.3 * 0.8
    WHEN SEG = 'MASS' AND TOT_AMT_1 >= TOT_AMT_2 THEN TOT_AMT_1 *0.5 * 1.2
    WHEN SEG = 'MASS' AND TOT_AMT_1 < TOT_AMT_2 THEN TOT_AMT_1 *0.5 * 0.8
    ELSE TOT_AMT_1 END AS TOT_AMT_1_FIX
FROM PERF_MAST_201312;
-- +-----------+------+-----------+-----------+---------------+
-- | PARTY_NM  | SEG  | TOT_AMT_2 | TOT_AMT_1 | TOT_AMT_1_FIX |
-- +-----------+------+-----------+-----------+---------------+
-- | 임하영    | PB   |    788000 |   1000400 |    1560624.00 |
-- | 김아름    | PB   |      1770 |       790 |        821.60 |
-- | 김지철    | PB   |      NULL |      NULL |          NULL |
-- | 김지연    | MASS |    578321 |    254860 |     101944.00 |
-- | 윤일상    | MASS |    659860 |     88805 |      35522.00 |
-- | 홍지은    | MASS |      NULL |      NULL |          NULL |
-- | 이선우    | MASS |      5789 |     90700 |      54420.00 |
-- | 최혜연    | MASS |      6000 |      NULL |          NULL |
-- | 이동건    | PB   |   5708900 |   9846000 |   15359760.00 |
-- | 강성범    | MASS |      NULL |       540 |           540 |
-- | 김지현    | PB   |    870000 |    687063 |     714545.52 |
-- +-----------+------+-----------+-----------+---------------+
```
```sql
--3) 2)번과 같이 매출이 발생할 때, 총 매출액을 계산하라.
SELECT SUM(CASE WHEN SEG = 'PB' AND TOT_AMT_1 >= TOT_AMT_2 THEN TOT_AMT_1 * 1.3 * 1.2
    WHEN SEG = 'PB' AND TOT_AMT_1 < TOT_AMT_2 THEN TOT_AMT_1 * 1.3 * 0.8
    WHEN SEG = 'MASS' AND TOT_AMT_1 >= TOT_AMT_2 THEN TOT_AMT_1 *0.5 * 1.2
    WHEN SEG = 'MASS' AND TOT_AMT_1 < TOT_AMT_2 THEN TOT_AMT_1 *0.5 * 0.8
    ELSE TOT_AMT_1 END) AS TOT_AMT_1_FIX_SUM
FROM PERF_MAST_201312;
-- +-------------------+
-- | TOT_AMT_1_FIX_SUM |
-- +-------------------+
-- |       17828177.12 |
-- +-------------------+

-- 이때 SELECT문 내, SUM 쿼리 앞에 다른 컬럼들을 입력하게 되면,
-- 다른 컬럼들은 Aggregation이 되지 않아 N개의 행을 갖는 반면,
-- SUM쿼리는 Aggregate되어 하나의 단일값을 갖기에,
-- 서로 나타내려는 행의 개수가 달라 에러가 발생하니 주의할 것.
```

<br>

### 2) DECODE(Oracle) 한정
 : `CASE WHEN` 함수에서 조건문이 `EQUAL` 조건만 있는 경우, `DECODE` 함수를 사용할 수 도 있다.

 ```sql
-- 함수 문법
DECODE(col_nm, conditioanl_1, output_1,
               conditioanl_2, output_2,
               conditioanl_3, output_3, default_output) new_col_nm
```
 ```sql
SELECT PARTY_NM, SEG, TOT_AMT_1 
    DECODE( SEG , 'PB' , TOT_AMT_1*1.3,
                , 'MASS' , TOT_AMT_1*0.5,
                TOT_AMT_1) AS TOT_AMT_1_FIX
FROM PERF_MAST_201312;
```

--- 

<br>


## + 연습문제
 - CASA_201312 테이블
```
+---------+----------+----------------+----------------+
| CUST_ID | CUST_SEG | BALANCE_201311 | BALANCE_201312 |
+---------+----------+----------------+----------------+
| 12344   | SILVER   |         210000 |         200000 |
| 12478   | DIAMOND  |        6015000 |        5800000 |
| 23458   | DIAMOND  |        5000000 |        6200000 |
| 32134   | GOLD     |        2354000 |        3200000 |
| 34181   | GOLD     |        4200000 |        4100000 |
| 54560   | SILVER   |        1000000 |        2000000 |
| 54789   | SILVER   |         200000 |         300000 |
| 68477   | GOLD     |         112000 |           3500 |
| 96147   | SILVER   |         300000 |        1000010 |
+---------+----------+----------------+----------------+
```

```sql
-- 1) 12월 잔액이 11월 잔액보다 10% 증가시, 오퍼 제공 (단 등급별 차등 제공)
-- DIAMOND : 5,000 / GOLD : 3,000 / SILVER : 2,000원씩
-- 이때, 오퍼를 받게 될 고객들은 1, 그 외는 0으로 표기하는 OFFER_ACCEPT 컬럼 생성
SELECT *, 
    CASE WHEN BALANCE_201312 > BALANCE_201311 *1.1 THEN 1
    ELSE 0 
    END AS OFFER_ACCEPT
FROM CASA_201312;
-- +---------+----------+----------------+----------------+--------------+
-- | CUST_ID | CUST_SEG | BALANCE_201311 | BALANCE_201312 | OFFER_ACCEPT |
-- +---------+----------+----------------+----------------+--------------+
-- | 12344   | SILVER   |         210000 |         200000 |            0 |
-- | 12478   | DIAMOND  |        6015000 |        5800000 |            0 |
-- | 23458   | DIAMOND  |        5000000 |        6200000 |            1 |
-- | 32134   | GOLD     |        2354000 |        3200000 |            1 |
-- | 34181   | GOLD     |        4200000 |        4100000 |            0 |
-- | 54560   | SILVER   |        1000000 |        2000000 |            1 |
-- | 54789   | SILVER   |         200000 |         300000 |            1 |
-- | 68477   | GOLD     |         112000 |           3500 |            0 |
-- | 96147   | SILVER   |         300000 |        1000010 |            1 |
-- +---------+----------+----------------+----------------+--------------+

-- 2) 캠페인 대상수 / 오퍼수 / 반응률 각각을 계산 (*OFFER 대상이 되는 사람의 비율 계산)
SELECT 
    COUNT(*) 
        AS TOTAL_CNT,
    SUM(
        CASE WHEN BALANCE_201312 > BALANCE_201311 *1.1 THEN 1
        ELSE 0 END
        ) AS OFFER_ACCEPT_CNT,
    AVG(
        CASE WHEN BALANCE_201312 > BALANCE_201311 *1.1 THEN 1
        ELSE 0 END
        ) AS OFFER_RATE
 FROM CASA_201312;
-- +-----------+------------------+------------+
-- | TOTAL_CNT | OFFER_ACCEPT_CNT | OFFER_RATE |
-- +-----------+------------------+------------+
-- |         9 |                5 |     0.5556 |
-- +-----------+------------------+------------+


-- 3) 수익(REVENUE) 계산 : 11, 12년도 잔액 합계, 12년도 평균수익 증가분, 평균수익(= 증가분의 0.9%)
SELECT 
    SUM(BALANCE_201311) AS BAL_1311,
    SUM(BALANCE_201312) AS BAL_1312,
    SUM(BALANCE_201312) - SUM(BALANCE_201311) AS INC_BAL,
    ROUND((SUM(BALANCE_201312) - SUM(BALANCE_201311)) * 0.009) AS REV
 FROM CASA_201312;
-- +----------+----------+---------+-------+
-- | BAL_1311 | BAL_1312 | INC_BAL | REV   |
-- +----------+----------+---------+-------+
-- | 19391000 | 22803510 | 3412510 | 30713 |
-- +----------+----------+---------+-------+


-- 4) 비용(COST) 계산 : LMS(모두에게 30원씩), OFFER 비용 (등급별)
SELECT 
    COUNT(*) * 30 AS LMS_COST,
    SUM(
        CASE WHEN CUST_SEG = 'DIAMOND' AND BALANCE_201312 > BALANCE_201311 *1.1 THEN 5000
        WHEN CUST_SEG = 'GOLD' AND BALANCE_201312 > BALANCE_201311 *1.1 THEN 3000
        WHEN CUST_SEG = 'SILVER' AND BALANCE_201312 > BALANCE_201311 *1.1 THEN 2000
        ELSE 0 END
        ) AS OFFER_COST
 FROM CASA_201312;
-- +----------+------------+
-- | LMS_COST | OFFER_COST |
-- +----------+------------+
-- |      270 |      14000 |
-- +----------+------------+

```


#### Reference
- 칼퇴족 김대리는 알고 나만 모르는 SQL - 책밥
