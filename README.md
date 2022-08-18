# Product analyst


✨ ✨ Solution for Product Analyst task for Turing College✨ ✨ 

<a href = 'https://docs.google.com/spreadsheets/d/1hdvsIdRvmQovVIamQZnHJdXOmietTu9O4ZMb1BQmOiY/edit?usp=sharing
'> The dashboard with visualisations.</a> It explains the main insights of the analysis and provides a solution for finding the average time on website and its link to revenue.

:rocket: SQL (BigQuery), Excel, Google Sheets, A/B test, Funnel Analysis

### The task:

Your PM would like to see the duration from first visit of a user on a particular day until first purchase on that same day. Your final result should show the duration dynamic daily.

-Create a presentation centered around the dynamic daily duration.
-See whether you can apply 1-2 techniques learned in this module course material to enhance your presentation on this subject.
-Explore the data. See whether there are interesting data points that can give more insights to your presentation.
-Provide analytical insights, what are the drawbacks of this analysis, what further analysis could you recommend?

### Main SQL Query:

``` SQL 
WITH purchase AS 
(
  SELECT 
    PARSE_DATE("%Y%m%d",  event_date) AS event_date,
    user_pseudo_id AS user,
    MIN(event_timestamp) AS timestamp
  FROM `tc-da-1.turing_data_analytics.raw_events` 
  WHERE event_name = 'purchase'
  GROUP BY 1,2
),

visit AS 
(
  SELECT 
    PARSE_DATE("%Y%m%d",  event_date) AS event_date,
    user_pseudo_id AS user,
    MIN(event_timestamp) AS timestamp
  FROM `tc-da-1.turing_data_analytics.raw_events` 
  WHERE event_name = 'page_view'
  GROUP BY 1,2
),

time_diff_for_users AS 
(
  SELECT v.event_date AS date, 
    v.user AS user,
    TIMESTAMP_SECONDS(CAST(CAST(v.timestamp as INT64)/1000000 as INT64)) AS visit_date,
    TIMESTAMP_SECONDS(CAST(CAST(p.timestamp as INT64)/1000000 as INT64)) AS purchase_date,
    TIMESTAMP_DIFF(TIMESTAMP_SECONDS(CAST(CAST(p.timestamp as INT64)/1000000 as INT64)), 
    TIMESTAMP_SECONDS(CAST(CAST(v.timestamp as INT64)/1000000 as INT64)), minute) AS time_difference
  FROM visit AS v
  JOIN purchase AS p
  ON p.user = v.user AND p.event_date = v.event_date
)

SELECT date,
  AVG(time_difference) AS average_minutes
FROM time_diff_for_users AS t
GROUP BY 1
ORDER BY 1
```
