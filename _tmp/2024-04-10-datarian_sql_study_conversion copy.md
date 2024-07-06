---
layout: post
title: datarian_sql_conversion
date : 10 Apr 2024
category : SQL
comments : true
---

## 1. Calc Conversion
### ■ 1) calc before & after event
```sql
WITH GPV AS (
  SELECT user_pseudo_id, ga_session_id, event_timestamp_kst as pv_time
  FROM ga
  WHERE event_name = 'page_view' AND page_title = '백문이불여일타 SQL 캠프 입문반'
)
, GSC AS (
  SELECT user_pseudo_id, ga_session_id, event_timestamp_kst as sc_time
  FROM ga 
  WHERE event_name = 'scroll' AND page_title = '백문이불여일타 SQL 캠프 입문반'
)

SELECT 
  COUNT(DISTINCT GPV.user_pseudo_id, GPV.ga_session_id) AS pv
  , COUNT(DISTINCT GSC.user_pseudo_id, GSC.ga_session_id) AS scroll_after_pv
  , COUNT(DISTINCT GSC.user_pseudo_id, GSC.ga_session_id)  / COUNT(DISTINCT GPV.user_pseudo_id, GPV.ga_session_id) as pv_scroll_rate
FROM GPV 
LEFT JOIN GSC ON GPV.user_pseudo_id = GSC.user_pseudo_id  
  AND GPV.ga_session_id = GSC.ga_session_id
  AND GPV.pv_time <= GSC.sc_time
```



### ■ 2) calc 1~3 conversion
```sql
-- step 1
WITH pv AS (
  SELECT user_pseudo_id, ga_session_id, event_timestamp_kst AS pv_at
  FROM ga
  WHERE page_title = '백문이불여일타 SQL 캠프 입문반' AND event_name = 'page_view'
), 

scroll AS (
  SELECT user_pseudo_id, ga_session_id, event_timestamp_kst AS scroll_at
  FROM ga
  WHERE page_title = '백문이불여일타 SQL 캠프 입문반' AND event_name = 'scroll'
), 

click AS (
  SELECT user_pseudo_id, ga_session_id, event_timestamp_kst AS click_at
  FROM ga
  WHERE page_title = '백문이불여일타 SQL 캠프 입문반'
  AND event_name in ('SQL_basic_form_click', 'SQL_basic_1day_form_click', 'SQL_package_form_click')
)  

, prep as (
  SELECT 
    COUNT(DISTINCT pv.user_pseudo_id, pv.ga_session_id) AS pv
    , COUNT(DISTINCT sc.user_pseudo_id, sc.ga_session_id) AS scroll_after_pv
    , COUNT(DISTINCT ck.user_pseudo_id, ck.ga_session_id) AS click_after_scroll
  FROM pv
  LEFT JOIN scroll sc ON pv.user_pseudo_id = sc.user_pseudo_id AND pv.ga_session_id = sc.ga_session_id AND pv.pv_at <= sc.scroll_at
  LEFT JOIN click ck ON pv.user_pseudo_id = ck.user_pseudo_id AND pv.ga_session_id = ck.ga_session_id AND sc.scroll_at <= ck.click_at
)

SELECT 
  prep.*
  , scroll_after_pv/pv as pv_scroll_rate
  , click_after_scroll/scroll_after_pv as scroll_click_rate
  , click_after_scroll/pv as pv_click_rate
FROM prep

```



