---
layout: post
title: SQL_2_basic query
date : 27 May 2023
category : SQL
comments : true
---


## SQL 사용을 위한 데이터 베이스 용어
   - 데이터 베이스  : 여러 사람에게 공유되어, 사용될 목적으로 관리되는 데이터 집합
   - 테이블(table) : 특정 종류의 데이터를 구조적 목록으로 구분한 것
   - 스키마(schema) : 테이블에 어떤 데이터를 어떤 '형식'으로 저장할 것인가를 정의한 것
   - 열 (schema) : 테이블을 구성하는 각각의 정보 (= field)
   - 행 (recod) : 데이터가 한 줄에 저장된 것


## 1. 기본 구조 및 Basic 문법
#### 1) 데이터 베이스 선택
```sql  
SHOW DATABASE -- 데이터 베이스 조회
```

#### 1) basic 문법
```sql  
SELECT '컬럼 이름'
FROM '테이블 이름'
WHERE '조건'
GROUP BY '그룹화할 컬럼'
HAVING '그룹화한 뒤 조건'
LIMIT '제한할 개수'
```


### 1) SELECT / FROM
 - SELECT `[불러오고자 하는 대상 (Columns)]`
 - FROM [찾을 대상이 있는 공간(Table)]
```sql
-- HR 테이블에서, 'ID','GENDER','AGE' 변수를 불러와라.
SELECT ID, GENDER, AGE
FROM HR
```
<br>

### 2) ORDER BY
 : 컬럼들을 특정 규칙에 맞춰서 정렬 (default : asc - 오름차순)
```sql
-- grade 오름차순 & ID 기준 내림차순 정렬
SELECT *
FROM EMP
ORDER BY GARADE ASC, ID DESC;
```

<br>

### 3) WHERE
 : 조건문 추가
```sql
-- HR 테이블에서, 'ID','GENDER','AGE' 변수를 불러오며,
-- 이때, 성별은 남성이며 'AGE'는 60세 이상
SELECT ID, GENDER, AGE
FROM HR
WHERE 'GENDER' == 'male' & 'AGE' >= 60
```

<br>

### 4) GROUP BY
 : 컬럼들을 특정 규칙에 맞춰서 그룹화함(Aggregate)




#### 연습 [sqlfiddle.com](http://sqlfiddle.com/)
 - 1) “1”번 유저의 모든 이벤트 로그를 확인.
```sql
SELECT *
FROM user_log
WHERE user_id = '1';
```

- 2) “1”번 유저의 이벤트별 로그 '횟수'를 계산
```sql
SELECT user_id, event, event_id
  COUNT (user_id) AS 'event_cnt'
FROM user_log
WHERE user_id = '1'
GROUP BY user_id, event, event_id;
```



#### Reference
[1] [비전공자를 위한 SQL](https://zzsza.github.io/development/2018/03/18/sql-for-everyone/)
