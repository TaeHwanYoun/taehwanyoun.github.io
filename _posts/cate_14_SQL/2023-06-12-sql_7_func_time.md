---
layout: post
title: SQL_7_Basic Function
date : 12 Jun 2023
category : SQL
comments : true
---


# 1. 문자 함수

| **함수**  |  **설명**  | 
|:-: |:-----: |
| **LOWER** | 모든 문자 소문자 변환 |
| **UPPER** | 모든 문자 대문자 변환 |
| **LENGTH** | 문자 길이 |
| **SUBSTR(컬럼, 시작점, 글자수)** | 문자 값 중 원하는 시작점의 글자수 만큼만 출력 |
| **RTRIM** | 문자열 오른쪽 공백 제거 |
| **LTRIM** | 문자열 왼쪽 공백 제거 |
| **TRIM** | 문자열 양쪽 공백 제거 |
| **REPLACE(컬럼, 'A', 'B')** | 특정 문자열을 다른 문자열로 대체 |
| **COALESCE(컬럼, 'A')** | NULL값을 치환 |
| **INITCAP** | (\*only oracle) 첫 글자는 대문자, 나머지는 소문자로 변환 |


```sql
SELECT SUBSTR(seg,2, 3) from card_tran_201311;
-- +------------------+
-- | substr(seg,2, 3) |
-- +------------------+
-- | B                |
-- | B                |
-- | ASS              |
-- | ASS              |
-- | B                |
-- +------------------+
```


--- 

<br>

# 2. 숫자 함수

| **함수**  |  **설명**  | 
|:-: |:-----: |
| **ROUND** | 소수점 자릿수를 지정하여 반올림 |
| **TRUNC(Oracle)<br>TRUNCATE (MySQL)** | 해당 소수점 자리에서 잘라내기 |
| **MOD(M,N)** | M을 N으로 나눈 나머지 반환 |
| **ABS** | 절대값 |
| **SIGN** | 양수는 1, 음수는 -1, 0은 0으로 반환 |
| **SQRT** | 제곱근|
| **PI** | 지정한 각도의 파이값 |
| **SIN** | 지정한 각도의 SIN값 |
| **COS** | 지정한 각도의 COS값 |
| **TAN** | 지정한 각도의 TAN값 |

```sql
select round(123.456,2);
-- +------------------+
-- | round(123.456,2) |
-- +------------------+
-- |           123.46 |
-- +------------------+

select truncate(123.456,2);
-- +---------------------+
-- | truncate(123.456,2) |
-- +---------------------+
-- |              123.45 |
-- +---------------------+

select mod(123,10);
-- +-------------+
-- | mod(123,10) |
-- +-------------+
-- |           3 |
-- +-------------+

select pi();
-- +----------+
-- | pi()     |
-- +----------+
-- | 3.141593 |

```

--- 

<br>

# 3. 날짜 함수
### 1) 날짜 연사

| **함수**  |  **설명**  | 
|:-: |:-----: |
| **ADD_MONTHS** | (Oracle) 지정한 날짜에 개월 수를 더하거나 뺀 날짜 출력 |
| **DATE_ADD** | (MySQL) 지정한 날짜에 개월 수를 더한 날짜 출력 |
| **DATE_SUB** | (MySQL) 지정한 날짜에 개월 수를 뺀 날짜 출력 |
| **DATE_FORMAT** | (MySQL) 지정한 날짜를 희망하는 포맷으로 변경 |
| **DATE_PART** | (PostgreSql) 지정한 날짜를 희망하는 포맷으로 변경 * isoweeknum 활용!|


```sql
SELECT DATE_ADD(DLQ_ST, INTERVAL 1 MONTH) from DLQ_PARTY;
-- +------------------------------------+
-- | DATE_ADD(DLQ_ST, INTERVAL 1 MONTH) |
-- +------------------------------------+
-- | 2012-08-30                         |
-- | 2012-10-21                         |

SELECT DATE_ADD(DLQ_ST, INTERVAL 1 DAY) from DLQ_PARTY;
-- +----------------------------------+
-- | DATE_ADD(DLQ_ST, INTERVAL 1 DAY) |
-- +----------------------------------+
-- | 2012-07-31                       |
-- | 2012-09-22                       |

SELECT DATE_ADD(DLQ_ST, INTERVAL -10 DAY) from DLQ_PARTY;
-- +------------------------------------+
-- | DATE_ADD(DLQ_ST, INTERVAL -10 DAY) |
-- +------------------------------------+
-- | 2012-07-20                         |
-- | 2012-09-11                         |


SELECT DATE_SUB(DLQ_ST, INTERVAL 10 DAY) from DLQ_PARTY;
-- +------------------------------------+
-- | DATE_ADD(DLQ_ST, INTERVAL -10 DAY) |
-- +------------------------------------+
-- | 2012-07-20                         |
-- | 2012-09-11                         |


SELECT DATE_ADD(DLQ_ST, INTERVAL 30 SECOND) from DLQ_PARTY;
-- +--------------------------------------+
-- | DATE_ADD(DLQ_ST, INTERVAL 30 SECOND) |
-- +--------------------------------------+
-- | 2012-07-30 00:00:30                  |
-- | 2012-09-21 00:00:30                  |

SELECT DATE_FORMAT(DLQ_ST,'%Y-%m-%d') AS DLQ_SQ from DLQ_PARTY;
-- +------------+
-- | DLQ_SQ     |
-- +------------+
-- | 2012-07-30 |
-- | 2012-09-21 |

-- postgresql
SELECT DATE_PART('week', '2020-10-07') AS ISO_WEEK_NUM;
-- +-------------+
-- | ISO_WEEK_NUM|
-- +-------------+
-- |      41     |
```


### 2) 지금 또는 특정 날짜 반환

| **함수**  |  **설명**  | 
|:-: |:-----: |
| **SYSDATE** | 현재 시스탬의 날짜 데이터 반환 |
| **LAST_DAY** | 해당 월의 마지막 날짜 반환 |
| **MONTH_BETWEEN** | (Oracle)지정된 월 간의 월 수를 반환 |
| **DATEDIFF** | (MySQL) 지정된 날짜간 차이를 일단위로 반환 |
| **TIMESTAMPDIFF** | (MySQL) 지정된 날짜간 차이를 다양한 단위로 반환 |

```sql
SELECT SYSDATE();
-- +---------------------+
-- | SYSDATE()           |
-- +---------------------+
-- | 2023-06-12 22:05:32 |
-- +---------------------+

SELECT LAST_DAY('2022-01-07');
-- +------------------------+
-- | LAST_DAY('2022-01-07') |
-- +------------------------+
-- | 2022-01-31             |
-- +------------------------+

-- 차이를 일 단위로 표현
SELECT DATEDIFF('1992-01-07', now());
-- +-------------------------------+
-- | DATEDIFF('1992-01-07', now()) |
-- +-------------------------------+
-- |                        -11479 |
-- +-------------------------------+

-- 차이를 분/시/일/주/월/연 단위로 표현
SELECT TIMESTAMPDIFF(MINUTE, '1992-01-07', now());
SELECT TIMESTAMPDIFF(HOUR, '1992-01-07', now());
SELECT TIMESTAMPDIFF(DAY, '1992-01-07', now());
SELECT TIMESTAMPDIFF(WEEK, '1992-01-07', now());
SELECT TIMESTAMPDIFF(MONTH, '1992-01-07', now());
SELECT TIMESTAMPDIFF(YEAR, '1992-01-07', now());
-- +------------------------------------------+
-- | TIMESTAMPDIFF(YEAR, '1992-01-07', now()) |
-- +------------------------------------------+
-- |                                       31 |
-- +------------------------------------------+
```


--- 

<br>

## + 연습문제
 - CUST_INFO 테이블
```
+---------------+----------+---------+-----------+
| RESIDENCE_ID  | FIRST_NM | LAST_NM | ANNL_PERF |
+---------------+----------+---------+-----------+
| 6705302777666 | MIJA     | HAN     |    -76.77 |
| 7012012888888 | JINYOUNG | LEE     |    857.61 |
| 8411011555666 | YOUNGJUN | HA      |    468.54 |
+---------------+----------+---------+-----------+
```

```sql
-- 1) 주민번호 7번째 숫자가 1이면 남자, 2면 여자 일때, GENDER 컬럼을 만들어 남자면 1, 여자면 2로 출력
SELECT FIRST_NM, LAST_NM, SUBSTR(RESIDENCE_ID, 7,1) AS GENDER FROM CUST_INFO;
-- +----------+---------+--------+
-- | FIRST_NM | LAST_NM | GENDER |
-- +----------+---------+--------+
-- | MIJA     | HAN     | 2      |
-- | JINYOUNG | LEE     | 2      |
-- | YOUNGJUN | HA      | 1      |
-- +----------+---------+--------+

-- 2) 고객별 풀네임 생성
SELECT RESIDENCE_ID, CONCAT(LAST_NM,', ',FIRST_NM) AS FULL_NM FROM CUST_INFO;
-- +---------------+---------------+
-- | RESIDENCE_ID  | FULL_NM       |
-- +---------------+---------------+
-- | 6705302777666 | HAN, MIJA     |
-- | 7012012888888 | LEE, JINYOUNG |
-- | 8411011555666 | HA, YOUNGJUN  |
-- +---------------+---------------+

-- 3) 고객별 1년간 수익(ANNL_PERF)을 소숫점 둘째 자리에서 반올림하여, 첫째 짜리 까지 나타내고 별칭은 NEW_ANNL_PERF로 출력
SELECT CONCAT(LAST_NM,', ',FIRST_NM) AS FULL_NM ,
    ROUND(ANNL_PERF, 1) AS NEW_ANNL_PERF
    FROM CUST_INFO;
-- +---------------+------------------+
-- | FULL_NM       | NEW_ANNL_PERF로  |
-- +---------------+------------------+
-- | HAN, MIJA     |            -76.8 |
-- | LEE, JINYOUNG |            857.6 |
-- | SUNG, DAYOUNG |           -890.0 |
-- | SEO, HYEJIN   |             47.4 |
-- | KIM, JIHUN    |            330.1 |
-- | HA, YOUNGJUN  |            468.5 |
-- +---------------+------------------+

```


#### Reference
- 칼퇴족 김대리는 알고 나만 모르는 SQL - 책밥
