---
layout: post
title: SQL_programmers_quiz_2_sum_min_max
date : 04 Oct 2023
category : SQL
comments : true
---

## 2. SUM, MAX, Min
### ■ Q_1
 - 동물 보호소에 들어온 동물의 이름은 몇 개인지 조회하는 SQL 문
 - 이때 이름이 NULL인 경우는 집계하지 않으며 
 - 중복되는 이름은 하나로 칩니다.
 - [Programmers 코딩 테스트 연습 : 2_sum_min_max 중복 제거하기](https://school.programmers.co.kr/learn/courses/30/lessons/59408)  

```sql
SELECT COUNT(DISTINCT(NAME))
FROM ANIMAL_INS
WHERE NAME IS NOT NULL;
```
#### Learning
 - 행 결합
   - `DISTINCT` : (=unique)
   - `IS NOT NULL` : Null 제외


<br>

--- 

<br>

### ■ Q_2
 - FOOD_PRODUCT 테이블에서 가격이 제일 비싼 식품의 식품 ID, 식품 이름, 식품 코드, 식품분류, 식품 가격을 조회하는 SQL문을 작성
 - [Programmers 코딩 테스트 연습 : 2_sum_min_max 중복 제거하기](https://school.programmers.co.kr/learn/courses/30/lessons/131115)  

```sql
-- 1) using order by
SELECT PRODUCT_ID, PRODUCT_NAME, PRODUCT_CD, CATEGORY, PRICE
FROM FOOD_PRODUCT
ORDER BY PRICE DESC
LIMIT 1;

-- 2) using sub-query
SELECT PRODUCT_ID, PRODUCT_NAME, PRODUCT_CD, CATEGORY, PRICE
FROM FOOD_PRODUCT
WHERE PRICE = (
  SELECT MAX(PRICE) AS MAX_PRICE
  FROM FOOD_PRODUCT
  );
```
#### Learning
 - 행 결합
   - `DISTINCT` : (=unique)
   - `IS NOT NULL` : Null 제외


<br>

--- 

<br>