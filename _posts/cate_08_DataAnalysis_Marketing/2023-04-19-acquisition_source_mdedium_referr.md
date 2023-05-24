---
layout: post
title: 2023-04-19-acquisition_source_mdedium_referr
date : 11 May 2023
category : DataAnalysis_Marketing
comments : true
---
유입된 유저의 경로를 분석하는 것은,<br>
광고로 유입되었다면, 광고 매체는 어떤 광고 매체가 더 효율적이었는지 분석하여, 효율적인 광고매체에 예산을 늘리는 액션을 취할 수 있고,<br>
자연 검색을 통해 유입되었다면, 우리 사이트로 유입되는 가장 큰 키워드가 무엇인지 분석함으로써 서비스의 장점을 극대화 시킬 수 있습니다.<br>
따라서, 유입 경로 분석을 위한 UTM Referral를 구분하는 과정은 서비스 성장을 위해서 굉장히 중요한 분석이기에, 관련하여 주요 개념들을 정리해보려 합니다.

---
<br>


## 1.획득 (Acquisition)이란
 : GA에서 획득(Acquisition)이란, **사용자들이 어떤 경로(채널/캠페인)를 통해 우리 웹사이트를 방문**하였는지를 보여주는 개념. 이를 활용하여 마케팅 활동별 성과를 측정합니다.

---
<br>

## 2. UTM (Urchin Tracking Module)
: 유입 구분값을 살펴보기위해서는 우선 UTM이라는 개념을 알아야한다. UTM은 어느 사이트로 부터 유입되었는지를 알려주는 꼬리표로, 타겟 사이트를 외부에 공유/홍보시 사용하는 url을 의미한다. 이 utm 값을 분석하여 유입 경로를 구분할 수 있습니다.

<center>

<img src = '/assets/DataAnalysis_Marketing/acquistion_source/acquistion_source_1.png' width = '80%'>
<br> <이미지 출처 : 그로스클, 김신입_마케팅용어집>

</center>  

 - ex) https://taehwanyoun.git.io?utm_source=kakao&utm_medium=push&utm_campaign=fb_promotion&utm_term=promotionAE&utm_content=202305_contents
 - utm_source=kakao : 소스(soruce)  
 - utm_medium=push : 매체(medium)  
 - utm_campaign=fb_promotion : 캠페인(campagin)  
 - utm_term=promotionAE : 유입 키워드  
 - utm_content=202305_contents : 유입 컨텐츠  
  
이렇게 UTM값을 기준으로, 유입된 유저가 어느 **소스/매체**로부터 유입되었고, **유입 키워드/컨텐츠**는 무엇인지 분류할 수 있습니다.

---
<br>

## 3. 주요 용어 구분 [채널 / 매체 / 소스]
 : 다음으로는 유입 경로를 구분해주는 [채널 / 매체 / 소스]에 대해서 살펴봅시다.

### 1) 채널 / 매체 / 소스

| **구분**  |  **용어**  |  **정의**  | **예시**  | 
|:-: |:-----: |:-----: |:----- |
| **그룹** | **채널(channel)** | 각 매체에서 여러 소스를 포함하는 집합이나, 매체와 유사하게 구분됨  |  |
| **범주** | **매체(medium)** | 소스의 상위 카테고리 or 사용자를 사이트로 보내는 시스템 <br>(별도의 추가 설정이 없을 경우 기본값은 none, referral, organic 3가지뿐) | - none(없음) <br> : 직접(direct) url 입력을 통해 유입되거나, 소스 부재<br><br> - referral(추천) <br> : referral url을 클릭하여 유입 <br><br> - organic(자연검색) <br> : 구글/네이버 등 무료 "검색 결과" 유입<br><br> - cpc(유료검색) <br> : 키워드 광고등 유료 검색을 통해 유입 <br><br> - banner/display(디스플레이) <br> : 디스플레이/배너 광고 클릭<br><br> - social(소셜) <br> : Instagram, Facebook, Kakao 등<br> | 
| **출처** | **소스(source)** | 트레픽이 유입된 위치(검색엔진, 도메인 등)  | [google.com, naver.com, blog.naver.com] | 


## (1) 소스(source)
 : 소스는 트레픽이 유입된 위치(검색엔진, 도메인 등)로, 직전 사이트의 도메인 이름을 의미합니다.
 따라서, 비록 "네이버 블로그"와 "네이버 검색"처럼 서로 다른 경로라도, 동일하게 '네이버'라는 도메인이라면, 소스값은 '네이버'가 됩니다.
 추가로 레퍼러 값이 없다면, 소스값은 'direct'로 표기됩니다.
 - Ex) 
   - blog.naver.com --> source : 'naver'
   - search.naver.com --> source : 'naver'
   - '' --> source : direct
  
## (2) 매체 구분
 : 매체는 '소스의 상위 카테고리' 개념으로, 별도의 추가설정이 없다면 구글의 GA에서 기본적으로 분류하는 매체는 크게 3가지로 direct/ referral, organic으로 구분됩니다.
여기서 "별도의 추가설정이 없다면"이란, 네이버 검색을 통해 유입되더라도, 키워드 광고를 통한 유료검색과 무료 검색 2가지로 나뉘나 단순 레퍼러 URL만으로는 이를 구분할 수 없습니다. 따라서 별도의 추가설정인,"키워드 광고 UTM의 매체값에 CPC(유료검색)를 설정해 놓아야" 자연 검색을 통한 유입과 광고 유입을 통한 유입을 구분할 수 있습니다. 이같은 설정을 맞춤 캠페인 설정(custom campaign tagging)이라고 부르며, Campaign URL Builder를 활용하여 UTM값을 수동으로 설정해주어야 합니다.(이번 글은 개념을 구분하는데 있기에, URL Builder 세팅에 대한 내용은 다루지 않습니다.)

 <center>

<img src = '/assets/DataAnalysis_Marketing/acquistion_source/acquistion_source_3.png' width = '80%'>
<br> <이미지 출처 : analyticsmarketing.co.kr >

</center>  

 - none(없음) : 직접(direct) url 입력을 통해 유입되거나, 소스 부재
 - referral(추천) : referral url을 클릭하여 유입 
 - organic(자연검색) : 구글/네이버 등 무료 검색 결과 유입
<br><br>
 - cpc(유료검색) : 키워드 광고등 유료 검색을 통해 유입 
 - banner/display(디스플레이) : 디스플레이/배너 광고 클릭
 - social(소셜) : Instagram, Facebook, Kakao 등

## (3) 채널 구분
: 채널에 대한 정의는 "규칙을 기반으로 트래픽 소스를 그룹화한 것"이라고 합니다. (아직은 제 이해가 부족한 것인지 매체와 채널의 차이가 크지 않아보입니다.)<br>

매체와 채널은 굉장히 유사한 개념으로 보이며, 앞서 utm을 살펴보면 매체(medium)값은 수집되는 값이지만, 채널(channel)값은 따로 utm url을 통해 수집되는 것이 아닌, GA 구분을 통해 생성되는 것 같습니다. (개인 추측) 
 - 매체 : 소스를 마케팅 수단으로 그룹핑한 카테고리  
 - 채널 : 수집된 소스/매체값을 그룹핑한 카테고리




<center>

<img src = '/assets/DataAnalysis_Marketing/acquistion_source/acquistion_source_2.png' width = '80%'>
<br> <이미지 출처 : 파인데이터랩 >

</center>  





#### Reference
[1] [[GA기본] 5-2. 구글애널리틱스 표준 보고서 (Standard reports) - 분석마케팅](https://analyticsmarketing.co.kr/digital-analytics/google-analytics-basics/2318/)  
[2] [2022_디지털마케팅(30) 구글 애널리틱스(38) - 채널 분석](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=hsjeong106&logNo=221690237016)  
[3] [구글애널리틱스(GA) 사용법 : 획득보고서](https://finedata.tistory.com/39) 