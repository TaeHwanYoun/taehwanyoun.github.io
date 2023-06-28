---
layout: post
title: Tableau_Study_1
date : 15 Jun 2023
category : Tableau
comments : true
---

# 테블루 주요 기능 (그룹화 필요)

## Prep
### 1. 테이블 병합  
   —> 유니온- 열을 붙이기

### 2. 불일치 필드 병합
   - 개념 : 서로 Null인 행들로 교차된 열들을 결합
   - 사용법  : 드래그 하여, 병합하고자 하는 열들을 선택한 후, 옵션버튼 > 불일치 필드 병합
  
### 3. 정규식
   - REGEXP_EXTRACT([Column], ‘(\d+)')
   : 숫자만 추출
   - REGEXP_EXTRACT([Column], ‘(\D+)’)
   : 숫자가 아닌 나머지 추출

### 4. 빈값이 존재하는 열도 표기하기 
    - 1) 상단_분석 > 2) 메뉴_테이블 레이아웃 > 3) 빈열표시

### 5. T/F 반환하기 
    - 비교대상값을 중괄호 “{}” 로 감쌀 것!

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




#### Reference
