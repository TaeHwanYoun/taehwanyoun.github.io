---
layout: post
title: SQL_6_Text Mining을 활용한 조건 추가
date : 11 Jun 2023
category : SQL
comments : true
---



# 1. Text Mining

| **와일드 카드**  |  **설명**  | 
|:-: |:-----: |
| **%** | 숫자 0 또는 문자들을 대체하기 위해 사용 |
| **_** | 한 개의 단어 대체 |

### 1) LIKE
#### (1) 앞 또는 뒤에 나오는 문자들을 알 수 없는 경우
```sql
-- 뒤에 나오는 문자를 알 수 없는 경우
SELECT * FROM table_nm WHERE target_filter_col LIKE '찾고싶은 문자%';

-- 앞에 나오는 문자를 알 수 없는 경우
SELECT * FROM table_nm WHERE target_filter_col LIKE '%찾고싶은 문자';
```


#### (2) 앞/뒤에 나오는 문자들을 알 수 없는 경우
 : 앞뒤를 고려하지 않고, 원하는 키워드를 포함한 모든 데이터를 필터링 하는 방법
```sql
-- 뒤에 나오는 문자를 알 수 없는 경우
SELECT * FROM table_nm WHERE target_filter_col LIKE '%찾고싶은 문자%';
```

#### (3) 앞or뒤에 나오는 문자 '한 글자'를 알 수 없는 경우
 : 앞뒤를 고려하지 않고, 원하는 키워드를 포함한 모든 데이터를 필터링 하는 방법
```sql
-- 앞에 나오는 문자 하나를 알 수 없는 경우
SELECT * FROM table_nm WHERE target_filter_col LIKE '_찾고싶은 문자';

-- 뒤에 나오는 문자 하나를 알 수 없는 경우
SELECT * FROM table_nm WHERE target_filter_col LIKE '찾고싶은 문자_';
```


#### (4) 시작과 끝 문자만 아는 경우
```sql
-- 뒤에 나오는 문자 하나를 알 수 없는 경우
SELECT * FROM table_nm WHERE target_filter_col LIKE '첫 글자%마지막 글자';
```

<br>

### 2) NOT LIKE
#### (1) 특정 단어를 원하지 않을 경우 
```sql
-- 뒤에 나오는 문자 하나를 알 수 없는 경우
SELECT * FROM table_nm WHERE target_filter_col NOT LIKE '%원하지 않는 문자%';
```


--- 

<br>

# 2. 필드 결합하기
 \* ORCLE과 MYSQL의 코드가 서로 다르니 주의\*
### 1) 여러 열을 하나로 결합하기
```sql
-- ORCLE VERSION
SELECT col_nm_1 || col_nm_2  FROM table_nm;

-- MYSQL VERSION
SELECT CONCAT(col_nm_1, col_nm_2)  FROM table_nm;
```


### 2) 문자 삽입하기
```sql
-- ORCLE VERSION
SELECT col_nm_1 ||'삽입하고 싶은 문자열'||col_nm_2  FROM table_nm;

-- MYSQL VERSION
SELECT CONCAT(col_nm_1, '삽입하고 싶은 문자열',col_nm_2) FROM table_nm;

SELECT PARTY_NM, SEG, CMF, CONCAT(SEG,'_',PARTY_NM,'_',CMF) AS DEP 
FROM PERF_MAST_201312;
```

--- 

<br>

# 3. 공백 제거하기
### 1) 오른쪽 or 왼쪽 or 양쪽 공백 제거하기
```sql
-- 오른쪽 공백 제거 (RTRIM)
SELECT col_nm_1, RTRIM(col_nm_2) FROM table_nm;
-- 왼쪽 공백 제거 (LTRIM)
SELECT col_nm_1, LTRIM(col_nm_2) FROM table_nm;
-- 양쪽 공백 제거 (TRIM)
SELECT col_nm_1, TRIM(col_nm_2) FROM table_nm;
```







## + 연습문제
 - CUST_PERF 테이블

<center> <img src = '/assets/14_SQL/sql_6/sql_6_1.png' width = '80%'> </center>

```sql
-- 1) ES라는 문자를 포함하는 도시 중 세달 연속 구매 금액이 300만원이 넘는 고객들만 추출
SELECT * FROM CUST_PERF
WHERE CITY LIKE '%ES%'
AND COALESCE(TOT_AMT_1, 0) > 3000000 
AND COALESCE(TOT_AMT_2, 0) > 3000000 
AND COALESCE(TOT_AMT_3, 0) > 3000000;
```
<center> <img src = '/assets/14_SQL/sql_6/sql_6_2.png' width = '80%'> </center>



```sql
-- 2) 고객에게 감사 DM을 발송할 때, 맨 처음 문구가 아래와 같게끔 SQL 문장을 만들어라, 이때, B가 들어 가는 국가는 제외하며, 3개월 연속 구매 실적이 존재하는 고객에게만 전달하라
-- Dear {LEE}, your segment is {PRIORITY} in our company.
SELECT CONCAT('Dear ',NAME,', your segment is ', SEG,' in our company.')
AS DM_DE
FROM CUST_PERF
WHERE CITY NOT LIKE '%B%'
AND COALESCE(TOT_AMT_1, 0) > 0 
AND COALESCE(TOT_AMT_2, 0) > 0 
AND COALESCE(TOT_AMT_3, 0) > 0;
```

<center> <img src = '/assets/14_SQL/sql_6/sql_6_3.png' width = '80%'> </center>






#### Reference
- 칼퇴족 김대리는 알고 나만 모르는 SQL - 책밥
