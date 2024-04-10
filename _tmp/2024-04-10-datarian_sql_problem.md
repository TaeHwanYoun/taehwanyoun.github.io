---
layout: post
title: datarian_sql_problem
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




