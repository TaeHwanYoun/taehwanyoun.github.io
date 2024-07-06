---
layout: post
title: datarian_sql_study_UTM_Conv
date : 06 May 2024
category : SQL
comments : true
---

## 1. Calc Conversion by UTM source
### ■ 1) calc before & after event
```sql
-- 1) PV
WITH PV_TABLE AS (
  SELECT * FROM ga WHERE event_name = 'page_view' and page_title = '백문이불여일타 SQL 캠프 입문반'
  )

-- 2) scroll 
, SC_TABLE AS (
  SELECT * FROM ga WHERE event_name = 'scroll' and page_title = '백문이불여일타 SQL 캠프 입문반'
  ) 

-- 3) CLICK 
, CK_TABLE AS (
  SELECT * FROM ga WHERE event_name = 'click' and page_title = '백문이불여일타 SQL 캠프 입문반'
  ) 

, MERGE_TABLE AS (
  SELECT 
    PT.source, PT.medium
    , COUNT(DISTINCT PT.user_pseudo_id, PT.ga_session_id) AS PV_COUNT
    , COUNT(DISTINCT ST.user_pseudo_id, ST.ga_session_id) AS SC_COUNT
    , COUNT(DISTINCT CT.user_pseudo_id, CT.ga_session_id) AS CK_COUNT
  FROM PV_TABLE PT
  LEFT JOIN SC_TABLE ST ON PT.user_pseudo_id = ST.user_pseudo_id AND PT.ga_session_id = ST.ga_session_id AND PT.event_timestamp_kst <= ST.event_timestamp_kst
  LEFT JOIN CK_TABLE CT ON ST.user_pseudo_id = CT.user_pseudo_id AND ST.ga_session_id = CT.ga_session_id AND ST.event_timestamp_kst <= CT.event_timestamp_kst
  GROUP BY PT.source, PT.medium
  )

SELECT 
  *
  , SC_COUNT/PV_COUNT AS PV_SC_RATE
  , CK_COUNT/SC_COUNT AS SC_CK_RATE
  , CK_COUNT/PV_COUNT AS PV_CK_RATE
FROM MERGE_TABLE
ORDER BY CK_COUNT DESC, PV_COUNT DESC


limit 100
```


