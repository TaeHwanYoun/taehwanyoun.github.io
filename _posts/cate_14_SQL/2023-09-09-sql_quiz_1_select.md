---
layout: post
title: SQL_programmers_quiz_1_select
date : 30 August 2023
category : SQL
comments : true
---

## 1. SELECT
### 오프라인/온라인 판매 데이터 통합하기
### ■ Q_1
 - ONLINE_SALE 테이블과 OFFLINE_SALE 테이블에서 2022년 3월의 오프라인/온라인 상품 판매 데이터의 판매 날짜, 상품ID, 유저ID, 판매량을 출력하는 SQL문을 작성해주세요.  
 - OFFLINE_SALE 테이블의 판매 데이터의 USER_ID 값은 NULL 로 표시해주세요.  
 - 결과는 판매일을 기준으로 오름차순 정렬해주시고 판매일이 같다면 상품 ID를 기준으로 오름차순, 상품ID까지 같다면 유저 ID를 기준으로 오름차순 정렬해주세요.  
 - [Programmers 코딩 테스트 연습 : 1_SELECT_오프라인/온라인 판매 데이터 통합하기](https://school.programmers.co.kr/learn/courses/30/lessons/131537)  

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

### ■ Q_2
 - 동물 보호소에 가장 먼저 들어온 동물의 이름을 조회하는 SQL 문을 작성
 - [Programmers 코딩 테스트 연습 : 1_SELECT  상위 n개 레코드](https://school.programmers.co.kr/learn/courses/30/lessons/59405)  


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


<br>

--- 

<br>

### ■ Q_3
 : USER_INFO 테이블에서 2021년에 가입한 회원 중 나이가 20세 이상 29세 이하인 회원이 몇 명인지 출력하는 SQL문을 작성해주세요.
  - [Programmers 코딩 테스트 연습 : 1_SELECT 조건에 맞는 회원수 구하기](https://school.programmers.co.kr/learn/courses/30/lessons/131535) 

```sql
-- 1) MY ANSWER
-- SELECT COUNT(DISTINCT(USER_ID)) AS USER_ID 
-- FROM USER_INFO
-- WHERE JOINED >= '2021-01-01' AND JOINED <= '2021-12-31'
-- AND AGE >= 20 AND AGE < 30;

-- 2) BETTER ANSWER
SELECT COUNT(DISTINCT(USER_ID)) AS OUTPUT
FROM USER_INFO
WHERE 
    YEAR(JOINED) = 2021 AND
    AGE BETWEEN 20 AND 29;

```


<br>

--- 

<br>

### ■ Q_4
 : USER_INFO 테이블에서 2021년에 가입한 회원 중 나이가 20세 이상 29세 이하인 회원이 몇 명인지 출력하는 SQL문을 작성해주세요.
  - [Programmers 코딩 테스트 연습 : 1_SELECT 조건에 맞는 회원수 구하기](https://school.programmers.co.kr/learn/courses/30/lessons/131535) 

```sql
-- SUB QUERY
SELECT FLAVOR
FROM FIRST_HALF 
WHERE 
    TOTAL_ORDER > 3000 AND 
    FLAVOR IN (
        SELECT FLAVOR
        FROM ICECREAM_INFO 
        WHERE INGREDIENT_TYPE = 'FRUIT_BASED'
    )
ORDER BY TOTAL_ORDER DESC;

-- LEFT JOIN
SELECT ORD.FLAVOR 
FROM FIRST_HALF AS ORD
LEFT JOIN ICECREAM_INFO AS INFO
ON ORD.FLAVOR = INFO.FLAVOR
WHERE
    ORD.TOTAL_ORDER > 3000 AND 
    INFO.INGREDIENT_TYPE = 'FRUIT_BASED'
ORDER BY TOTAL_ORDER DESC;
```

<br>

--- 

<br>

### ■ Q_5
 - REST_INFO와 REST_REVIEW 테이블에서 서울에 위치한 식당들의 
 - 식당 ID, 식당 이름, 음식 종류, 즐겨찾기수, 주소, 리뷰 평균 점수를 조회하는 SQL문을 작성
 - 이때 리뷰 평균점수는 소수점 세 번째 자리에서 반올림, 평균점수를 기준으로 내림차순 정렬, 평균점수가 같다면 즐겨찾기수를 기준 내림차순 
 - [Programmers 코딩 테스트 연습 : 1_SELECT 서울에 위치한 식당 목록 출력하기](https://school.programmers.co.kr/learn/courses/30/lessons/131118) 

```sql
SELECT INFO.REST_ID, INFO.REST_NAME, INFO.FOOD_TYPE, INFO.FAVORITES, INFO.ADDRESS,
        REVIEW.SCORE
FROM
    REST_INFO AS INFO
    INNER JOIN (
        SELECT REST_ID, ROUND(AVG(REVIEW_SCORE), 2) AS SCORE
        FROM REST_REVIEW
        GROUP BY REST_ID
        ) AS REVIEW
    ON INFO.REST_ID = REVIEW.REST_ID
WHERE  INFO.ADDRESS LIKE '서울%'
ORDER BY SCORE DESC, INFO.FAVORITES DESC;
```


<br>

--- 

<br>

### ■ Q_6
 - USED_GOODS_BOARD와 USED_GOODS_REPLY 테이블에서 2022년 10월에 작성된 게시글 제목, 게시글 ID, 댓글 ID, 댓글 작성자 ID, 댓글 내용, 댓글 작성일을 조회하는 SQL문을 작성해주세요. 
 - 댓글 작성일을 기준 오름차순 정렬, 댓글 작성일이 같다면 게시글 제목을 기준 오름차순 정렬.
 - [Programmers 코딩 테스트 연습 : 1_SELECT 조건에 부합하는 중고거래 댓글 조회](https://school.programmers.co.kr/learn/courses/30/lessons/164673) 

```sql
-- 1) subquery로 연/월 필드 생성 후 일치 여부로 조건 부여
SELECT 
    board.title, board.board_id, 
    reply.reply_id, reply.writer_id, 
    reply.contents, DATE_FORMAT(reply.created_date,'%Y-%m-%d')
FROM
    (
        SELECT title, board_id, 
        MONTH(created_date) AS created_month,
        YEAR(created_date) AS created_YEAR
        FROM USED_GOODS_BOARD
        ) AS board
    INNER JOIN 
    USED_GOODS_REPLY AS reply
    ON board.board_id = reply.board_id
WHERE 
    board.created_month = 10 AND
    board.created_year = 2022
ORDER BY reply.created_date ASC, board.title asc 
;


-- 2) between으로 조건 부여
SELECT 
    board.title, board.board_id, 
    reply.reply_id, reply.writer_id, 
    reply.contents, DATE_FORMAT(reply.created_date,'%Y-%m-%d')
FROM
    USED_GOODS_BOARD AS board
    INNER JOIN 
    USED_GOODS_REPLY AS reply
    ON board.board_id = reply.board_id
WHERE 
    board.created_date BETWEEN '2022-10-01' AND '2022-10-31'
ORDER BY reply.created_date ASC, board.title asc 
;

```