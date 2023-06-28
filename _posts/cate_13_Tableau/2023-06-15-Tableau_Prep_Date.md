---
layout: post
title: Tableau_Study_1
date : 15 Jun 2023
category : Tableau
comments : true
---

## Prep - Datetime
 : 태블로 날짜 기반 함수

#### 1) DATEDIFF
 : 주어진 날짜 사이의 값을 반환 (뒷 날짜 - 앞 날짜)
```sql 
DATEDIFF('month', #2023-01-07, #2023-03-01)
-- 2
DATEDIFF('month', #2023-08-07, #2023-03-023)
-- -5
```

#### 2) DATEPART
 : 주어진 날짜를 정수로 반환
 ```sql 
DATEPART('month', #2023-01-07) 
-- 01
DATEPART('day', #2023-01-07) 
-- 07
```

### 6. 조건문을 활용한 필터링 방법 
    : 테블루에서 필터값을 많이 만들면, 연산 과정이 무거워지기에, 조건문을 활용하여 필터를 적용한 것과 같은 변수를 생성
  - 1) 선택하고자 하는 일자만 True값을 반환하는 변수 생성
    ```sql
    -- 선택한 월/일 
    DATEDIFF('month', [Order Date], [p.Date]) = 0
    AND
    DATEPART('day', [Order Date]) <= DATEPART('day', [p.Date])
    ```

  - 2) True로 선택된 일자의 Profit만 출력하는 변수 생성
    ```sql
    -- 선택한 월/일변수를 활용하여
    IIF([선택한_월_TF], [Profit], NULL)
    ```
## Vis
### 1. 동일 차트내 그래프 결합
    - Numeric 변수를 끌어다가, 축에다 놓기


#### Reference
