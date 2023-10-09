---
layout: post
title: SQL_1_intro_start
date : 25 May 2023
category : SQL
comments : true
---

## 1. SQL이란? (Structured Query Language)
<p> : 관계형 데이터베이스 관리 시스템(RDBMS)의 데이터를 관리하기 위해 특수 설계된 특수 목적 프로그래밍 언어.</p>
(SQL종류 : MySQL, PostgreSQL, MariaDB, Oracle)

<br>

### (1) 데이터베이스 관리 시스템(DBMS)의 특징

- 실시간 접근
- 계속적인 변화
- 동시 공용
- 내용에 의한 참조

<center> <strong>"데이터 저장소에서 데이터를 추출하기 위한 도구"</strong> </center>

### 1) SQL 분류
#### (1) DML(Data Manipulation Language)
 - 데이터 조작 목적의 언어
 - DML을 사용하기 위해서는 타겟이 되는 '테이블'이 반드시 정의되야함.
   - SELECT(선택)
   - INSERT(삽입)
   - UPDATE(수정)
   - DELETE(삭제)


#### (2) DDL(Data Definition Language)
 - 데이터 정의 언어
 - 데이터 베이스, 테이블, 뷰, 인덱스 등의 데이터 베이스 개체를 생성/삭제/변경 역할
 - 실행 즉시 MySQL에 적용되며, ROLLBACK 및 COMMIT 불가
   - CREATE
   - DROP
   - ALTER


#### (3) DCL(Data Control Language)
 - 데이터 제어 언어
 - 사용자에게 특정 권한을 부여/제거시 사용
   - GRANT
   - REVOKE
   - DENY 등

--- 

<br>


## 2. SQL 시작하기 (★★★)

### (1) MySQL 설치하기
 ※ [MacOS에 MySQL 설치](https://sean.tistory.com/291) <br>
 - [https://www.mysql.com/downloads/](https://sean.tistory.com/291) 사이트로 접속하여, 연습용 Commnunity version 다운로드.  


### (2) MySQL 시작하기 (in Terminal)
 ※ [SQL 설치하고 실행하기 by 다임하게](https://daimhada.tistory.com/121) <br>
 : MySQL 설치가 완료됐다면, 설치된 경로로 이동하여 root 권한으로 MySQL 접속.

#### 1) MySQL이 실행상태라면, root권한으로 MySQL에 접속해보자.
 : root 계정으로, 학습용 DB 생성

```sh
# 1) MySQL 디렉토리로 이동
cd /usr/local/mysql/bin

# 2) MySQL 실행 (root 권한)
./mysql -u root -p ## -u : 유저명, -p : 인증방식 Password (dbs1)
```

```sql
-- 3) 학습용 DB 생성
CREATE DATABASE {study_db} default CHARACTER SET UTF8; 
```

<center>

<img src = '/assets/14_SQL/sql_1/sql_1_1.png' width = '80%'>

</center>


#### 2) 신규 계정 생성 및 권한 부여
root계정은 위험하니, 학습용 계정을 생성하여 다시 접속해보자.<br>
신규 계정으로 로그인하여 DB를 검색해보면, root계정에서 생성한 학습용 DB가 보이지 않을 것이다.<br>
신규 계정은 해당 DB의 권한이 부재하기 때문이며, root 계정으로 학습용 계정에 학습용 DB의 사용 권한을 부여해주어야 한다.
```sql
-- 1) 신규 계정 생성 (root로 접속한 상태에서 진행)
-- local 학습용 계정명 : th_sql_study
CREATE USER '{신규 계정 이름}'@localhost IDENTIFIED BY '{인증 비번}';

-- 2) 신규 계정에 권한 부여
GRANT ALL PRIVILEGES ON study_db.* TO study_user@localhost; 
-- GRANT ALL PRIVILEGES ON study_db.* TO study_user@localhost IDENTIFIED BY 'study';
-- 위 커리에서 {IDENTIFIED BY 'study';} 이부분이 문법에러가 발생하나 원인을 파악하지 못하여 IDENTIFIED BY 구문없이 진행하였다.
```

#### 2) 신규 계정, DB 선택 및 테이블 생성
```sh
# 1) MySQL 실행 (신규계정)
./mysql -u '{신규 계정 이름}' -p 
```
```sql
-- 0) 현재 데이터 베이스 현황 확인
SHOW DATABASES;

-- 1) 학습용 DB 선택
USE study_db;

-- 2) 테스트 테이블 생성(삽입)
CREATE TABLE professor
(
     _id INT PRIMARY KEY AUTO_INCREMENT,
     name VARCHAR(32) NOT NULL,
     belong VARCHAR(12) DEFAULT 'FOO',
     phone VARCHAR(12)
) ENGINE=INNODB;


-- 3) 테스트 테이블 확인
DESC

-- 4) 학습용 테이블 삭제
DROP TABLE professor
```

<center>

<img src = '/assets/14_SQL/sql_1/sql_1_2.png' width = '80%'>

</center>



--- 

<br>
<br>



## 3. + SQL공부하기
 - [데이터 분석, 먹고 들어가기 위한 SQL 공부법(1편)](https://www.minwookim.kr/how-to-learn-sql-1/)
 - [데이터 분석, 먹고 들어가기 위한 SQL 공부법(2편)](https://www.minwookim.kr/how-to-learn-sql-2/)

<br>

### 1) 기본 문법 학습하기
#### (1) 책
- [데이터 분석을 위한 SQL 참고도서 추천](https://brunch.co.kr/@datarian/4)
- 칼퇴족 김대리는 알고 나만 모르는 SQL

<!-- #### (2) 온라인 강좌 -->

<br>

### 2) 온라인 실습 환경 및 연습 TABLE
[sqlfiddle.com](http://sqlfiddle.com/) 이 사이트에, 아래 CREATE 코드 입력 및 Build schema 버튼 실행
```sql
CREATE TABLE IF NOT EXISTS `user_log` (
  `index` INTEGER NOT NULL AUTO_INCREMENT,
  `user_id` VARCHAR(6) NOT NULL,
  `event` VARCHAR(200) NOT NULL,
  `event_date` date NOT NULL,
  PRIMARY KEY (`index`, `user_id`)
) DEFAULT CHARSET=utf8;
INSERT INTO `user_log` (`user_id`, `event`,`event_date`) VALUES
  ('1', 'login_facebook', '2018-03-12'),
  ('1', 'write_posting', '2018-03-12'),
  ('1', 'write_comment', '2018-03-12'),
  ('1', 'view_posting', '2018-03-12'),
  ('1', 'view_posting', '2018-03-12'),
  ('2', 'login_facebook', '2018-03-12'),
  ('2', 'view_posting', '2018-03-12'),
  ('2', 'view_posting', '2018-03-12'),
  ('2', 'write_comment', '2018-03-12'),
  ('2', 'logout', '2018-03-12'),
  ('2', 'login_facebook', '2018-03-13'),
  ('3', 'login_google', '2018-03-13'),
  ('3', 'write_posting', '2018-03-13'),
  ('3', 'view_posting', '2018-03-13'),
  ('3', 'view_posting', '2018-03-13'),
  ('3', 'purchase_item', '2018-03-15'),
  ('3', 'write_comment', '2018-03-14'),
  ('1', 'view_posting', '2018-03-14'),
  ('4', 'view_posting', '2018-03-14'),
  ('5', 'purchase_item', '2018-03-13');
```

---

<br>

#### Reference  
[1] [비전공자를 위한 SQL](https://zzsza.github.io/development/2018/03/18/sql-for-everyone/)  
[2] [MySQL 데이터베이스 한번에 끝내기 SQL Full Tutorial Course using MySQL Database](https://www.youtube.com/watch?v=vgIc4ctNFbc)
