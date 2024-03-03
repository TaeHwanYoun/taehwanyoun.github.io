---
layout: post
title: datarian_sql_study
date : 20 Jan 2024
category : SQL
comments : true
---

## 1. Retention
### ■ 1) Class Retention

```sql
-- classic retention
with RC AS (
  SELECT 
    R.customer_id
    , R.order_id
    , R.order_date
    , C.first_order_date
    , DATE_FORMAT(R.order_date, '%Y-%m-01') AS ORDER_MONTH
    , DATE_FORMAT(C.first_order_date, '%Y-%m-01') AS FIRST_ORDER_MONTH
  FROM log_data R
  LEFT JOIN customer_stats C ON R.customer_id = C.customer_id
),

RT AS (
  SELECT
    FIRST_ORDER_MONTH
    , COUNT(DISTINCT(ORDER_ID)) as M0
    , COUNT(DISTINCT((CASE WHEN ORDER_MONTH = DATE_ADD(FIRST_ORDER_MONTH, INTERVAL +1 MONTH) THEN ORDER_ID END))) as M1
    , COUNT(DISTINCT((CASE WHEN ORDER_MONTH = DATE_ADD(FIRST_ORDER_MONTH, INTERVAL +2 MONTH) THEN ORDER_ID END))) as M2
    , COUNT(DISTINCT((CASE WHEN ORDER_MONTH = DATE_ADD(FIRST_ORDER_MONTH, INTERVAL +3 MONTH) THEN ORDER_ID END))) as M3
    , COUNT(DISTINCT((CASE WHEN ORDER_MONTH = DATE_ADD(FIRST_ORDER_MONTH, INTERVAL +4 MONTH) THEN ORDER_ID END))) as M4
  FROM RC
  GROUP BY FIRST_ORDER_MONTH
)

SELECT
 RT.FIRST_ORDER_MONTH
 , M0
 , ROUND(M1 / M0 * 100,2) AS M1_RATE
 , ROUND(M2 / M0 * 100,2) AS M2_RATE
 , ROUND(M3 / M0 * 100,2) AS M3_RATE
 , ROUND(M4 / M0 * 100,2) AS M4_RATE
FROM RT
```



### ■ 2) ROLLING Retention
 - 롤링 리텐션은, 수치가 향후에 결제/방문이 발생하면 과거 수치가 모두 채워지기에 과거 수치가 모두 수정됨.
 - 때문에 보고용으로 적합하지 않을 수 있음.
```SQL
-- rolling retention
with RC AS (
  SELECT 
    R.customer_id
    , R.order_id
    , C.first_order_date
    , C.last_order_date
    , DATE_FORMAT(C.first_order_date, '%Y-%m-01') AS FIRST_ORDER_MONTH
    , DATE_FORMAT(C.last_order_date, '%Y-%m-01') AS LAST_ORDER_MONTH
  FROM log_data R
  LEFT JOIN customer_stats C ON R.customer_id = C.customer_id
)

SELECT
  FIRST_ORDER_MONTH
  , COUNT(DISTINCT(ORDER_ID)) as M0
  , COUNT(DISTINCT((CASE WHEN DATE_ADD(FIRST_ORDER_MONTH, INTERVAL +1 MONTH) <= LAST_ORDER_MONTH THEN ORDER_ID END)))  as M1
  , COUNT(DISTINCT((CASE WHEN DATE_ADD(FIRST_ORDER_MONTH, INTERVAL +2 MONTH) <= LAST_ORDER_MONTH THEN ORDER_ID END)))  as M2
  , COUNT(DISTINCT((CASE WHEN DATE_ADD(FIRST_ORDER_MONTH, INTERVAL +3 MONTH) <= LAST_ORDER_MONTH THEN ORDER_ID END)))  as M3
  , COUNT(DISTINCT((CASE WHEN DATE_ADD(FIRST_ORDER_MONTH, INTERVAL +4 MONTH) <= LAST_ORDER_MONTH THEN ORDER_ID END)))  as M4
  , COUNT(DISTINCT((CASE WHEN DATE_ADD(FIRST_ORDER_MONTH, INTERVAL +5 MONTH) <= LAST_ORDER_MONTH THEN ORDER_ID END)))  as M5
  , COUNT(DISTINCT((CASE WHEN DATE_ADD(FIRST_ORDER_MONTH, INTERVAL +6 MONTH) <= LAST_ORDER_MONTH THEN ORDER_ID END)))  as M6
  , COUNT(DISTINCT((CASE WHEN DATE_ADD(FIRST_ORDER_MONTH, INTERVAL +7 MONTH) <= LAST_ORDER_MONTH THEN ORDER_ID END)))  as M7
  , COUNT(DISTINCT((CASE WHEN DATE_ADD(FIRST_ORDER_MONTH, INTERVAL +8 MONTH) <= LAST_ORDER_MONTH THEN ORDER_ID END)))  as M8
  , COUNT(DISTINCT((CASE WHEN DATE_ADD(FIRST_ORDER_MONTH, INTERVAL +9 MONTH) <= LAST_ORDER_MONTH THEN ORDER_ID END)))  as M9
  , COUNT(DISTINCT((CASE WHEN DATE_ADD(FIRST_ORDER_MONTH, INTERVAL +10 MONTH) <= LAST_ORDER_MONTH THEN ORDER_ID END)))  as M10
  , COUNT(DISTINCT((CASE WHEN DATE_ADD(FIRST_ORDER_MONTH, INTERVAL +11 MONTH) <= LAST_ORDER_MONTH THEN ORDER_ID END)))  as M11
FROM RC
GROUP BY FIRST_ORDER_MONTH
```


<br>

--- 

<br>


## 2. DAU / WAU / MAU
 - **WAU & MAU 생성시, outer 쿼리의 값을 sub 쿼리에서 사용 가능하다는 점을 잘 활용하기!!**
### 1) sub query
```sql
WITH T0 AS (
  SELECT
  visit_date as target_day
  , COUNT(DISTINCT(customer_id)) as dau
  FROM log_data
  GROUP BY visit_date
),

SELECT 
  target_day
  , dau
  , (
    SELECT COUNT(DISTINCT(customer_id))
    FROM log_data
    WHERE visit_date > date_add(target_day, INTERVAL -7 day) 
    AND visit_date <= target_day
    ) AS wau
FROM T0


```

### 2) left join & on
```sql
SELECT
  d.visit_date as target_day
  , COUNT(DISTINCT(d.customer_id)) as dau
  , COUNT(DISTINCT(w.customer_id)) as wau
FROM log_data AS d
LEFT JOIN log_data AS w
ON w.visit_date BETWEEN date_add(d.visit_date, INTERVAL -6 day) and d.visit_date
GROUP BY d.visit_date
```


---

<br>

---
## 3. Consecutive Boolean

```sql
select
    round(
        count(case when datediff(t1.event_date , t2.first_event_date) = 1 then 1 end) /
        count(distinct t1.player_id) , 2
        ) as fraction 
from (
    select player_id , min(event_date) as first_event_date
    from Activity
    group by player_id
) as t2
left join activity t1 on t1.player_id = t2.player_id

```
