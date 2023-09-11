---
layout: post
title: SQL_programmers_quiz_1_select
date : 30 August 2023
category : SQL
comments : true
---

## 1. SELECT
### 오프라인/온라인 판매 데이터 통합하기
#### Quiz
 - ONLINE_SALE 테이블과 OFFLINE_SALE 테이블에서 2022년 3월의 오프라인/온라인 상품 판매 데이터의 판매 날짜, 상품ID, 유저ID, 판매량을 출력하는 SQL문을 작성해주세요.  
 - OFFLINE_SALE 테이블의 판매 데이터의 USER_ID 값은 NULL 로 표시해주세요.  
 - 결과는 판매일을 기준으로 오름차순 정렬해주시고 판매일이 같다면 상품 ID를 기준으로 오름차순, 상품ID까지 같다면 유저 ID를 기준으로 오름차순 정렬해주세요.  
 - [Programmers 코딩 테스트 연습 : 1_SELECT_3_오프라인/온라인 판매 데이터 통합하기](https://school.programmers.co.kr/learn/courses/30/lessons/131537)  

```sql
SELECT
    DATE_FORMAT(SALES_DATE, '%Y-%m-%d') AS SALES_DATE,
    PRODUCT_ID, USER_ID, SALES_AMOUNT
FROM ONLINE_SALE 
WHERE SALES_DATE BETWEEN '2022-03-01' AND '2022-03-31'
UNION ALL
SELECT
    DATE_FORMAT(SALES_DATE, '%Y-%m-%d') AS SALES_DATE,
    PRODUCT_ID, NULL AS USER_ID, SALES_AMOUNT
FROM OFFLINE_SALE
WHERE SALES_DATE BETWEEN '2022-03-01' AND '2022-03-31'
ORDER BY SALES_DATE, PRODUCT_ID, USER_ID;
```
#### Learning
 - 행 결합
   - `UNION ALL` : 행 결합 과정에서 중복을 제외하지 않고 모두 포함하여 결합할시 `ALL`을 써주자.
   - `NULL AS USER_ID` : 행결합시 특정 테이블에 공통 컬럼이 부재할 때, NULL값으로 컬럼을 생성하고 컬럼명을 생성해주자
 - 날짜 
   - `DATE_FORMAT(SALES_DATE, '%Y-%m-%d')` : 날짜 형태를 변환하기  
   - `WHERE col BETWEEN A AND B` : 날짜 범위 지정하기  


<br>

--- 

<br>

#### Quiz
 - 동물 보호소에 가장 먼저 들어온 동물의 이름을 조회하는 SQL 문을 작성
 - [Programmers 코딩 테스트 연습 : 1_SELECT_3_상위 n개 레코드](https://school.programmers.co.kr/learn/courses/30/lessons/59405)  


```sql
-- 1) using subquery
SELECT NAME
FROM ANIMAL_INS
WHERE DATETIME = (
    SELECT MIN(DATETIME)FROM ANIMAL_INS
    );

-- 2) using order by
SELECT NAME
FROM ANIMAL_INS
ORDER BY DATETIME ASC
LIMIT 1;
```
#### Learning
 - 행 결합
   - `LIMIT` : head()와 동일하게, 상위 N 개의 값을 출력



