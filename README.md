# SQL-Codeflix-Subscription-Churn-Analysis

# Project Description: 

## Introduction:

Codeflix, a newly launched streaming platform, has been making waves in the world of digital entertainment. After four months of operation, the management is eager to gauge the company's performance, especially in terms of subscription churn rates. Churn rates play a pivotal role in understanding customer retention and satisfaction, which are critical metrics for a subscription-based business like Codeflix.

The marketing department at Codeflix is particularly keen on delving into churn rates and comparing them between two distinct segments of users. To facilitate this analysis, they have provided a dataset containing subscription data for users acquired through these two different channels. This project focuses on gaining insights from this dataset to help Codeflix understand and manage its churn effectively.

## Data Overview:

The dataset, aptly named "subscriptions," is a vital piece of the churn analysis puzzle. It offers valuable information regarding Codeflix's subscribers and their subscription behavior. The dataset is composed of the following key columns:

**subscriptions Table:** id, subscription_start, subscription_end, segment
    
    

Codeflix has established a policy that mandates a minimum subscription length of 31 days. As a result, subscribers cannot start and end their subscriptions within the same month.

## Project Objectives:

    
-   **Churn Rate Calculation:** Churn rates will be calculated, considering the Codeflix subscription policy of a minimum length of 31 days.
    
-   **Segment Comparison:** Churn rates will be compared between the two user segments to assess the effectiveness of different marketing channels.
    
-   **Time-Based Analysis:** The duration of subscriptions and the time it takes for subscribers to churn will be analyzed.

    
This project's findings will provide valuable insights for Codeflix to fine-tune its marketing strategies, improve subscriber retention, and enhance the overall user experience.


    -- 1
    
    -- Examine at the first 100 rows of data in the subscriptions table. How many different segments are there?
    
    SELECT *
    
    FROM subscriptions
    
    LIMIT  20;
![enter image description here](https://i.ibb.co/8XtPVn7/1.png)
    

    
      
    
    -- 2
    
    -- Determine the range of months of data provided. Which months will you be able to calculate churn for?
    
    SELECT  MIN(subscription_start)  AS  'earliest_sub',
    
    MAX(subscription_start)  AS  'lastest_sub'
    
    FROM subscriptions;
    
    -- The data shows I will be able to calculte churn rates for January, February, and March of 2017.
![enter image description here](https://i.ibb.co/zn4g3gd/2.png)
    
      
    
    -- 3
    
    -- I'll be calculating the churn rates for segments 87 and 30 over the first three months of 2017.
    
    -- Create a temporary table of months
    
    WITH months AS
    
    (SELECT
    
    '2017-01-01'  AS first_day,
    
    '2017-01-31'  AS last_day
    
    UNION
    
    SELECT
    
    '2017-02-01'  AS first_day,
    
    '2017-02-28'  AS last_day
    
    UNION
    
    SELECT
    
    '2017-03-01'  AS first_day,
    
    '2017-03-31'  AS last_day
    
    )
    
    SELECT *
    
    FROM months;
![enter image description here](https://i.ibb.co/YddrrGc/3.png)
    
      
    
    -- 4
    
    -- Create a temporary table from subscriptions and months.
    
    WITH months AS
    
    (SELECT
    
    '2017-01-01'  AS first_day,
    
    '2017-01-31'  AS last_day
    
    UNION
    
    SELECT
    
    '2017-02-01'  AS first_day,
    
    '2017-02-28'  AS last_day
    
    UNION
    
    SELECT
    
    '2017-03-01'  AS first_day,
    
    '2017-03-31'  AS last_day
    
    ),
    
    cross_join AS
    
    (SELECT *
    
    FROM subscriptions
    
    CROSS  JOIN months
    
    )
    
    SELECT *
    
    FROM cross_join
    
    LIMIT  10;
![enter image description here](https://i.ibb.co/0sd5c3n/4.png)
    
      
    
    -- 5
    
    -- Create a temporary table to find active users each month for the different segments.
    
    WITH months AS
    
    (SELECT
    
    '2017-01-01'  AS first_day,
    
    '2017-01-31'  AS last_day
    
    UNION
    
    SELECT
    
    '2017-02-01'  AS first_day,
    
    '2017-02-28'  AS last_day
    
    UNION
    
    SELECT
    
    '2017-03-01'  AS first_day,
    
    '2017-03-31'  AS last_day
    
    ),
    
    cross_join AS
    
    (SELECT *
    
    FROM subscriptions
    
    CROSS  JOIN months
    
    ),
    
    status AS
    
    (SELECT id,
    
    first_day AS  month,
    
    CASE
    
    WHEN  (subscription_start < first_day)
    
    AND  (subscription_end > first_day
    
    OR subscription_end IS  NULL)
    
    AND  (segment = 87)  THEN  1
    
    ELSE  0
    
    END  AS is_active_87,
    
    CASE
    
    WHEN  (subscription_start < first_day)
    
    AND  (subscription_end > first_day
    
    OR subscription_end IS  NULL)
    
    AND  (segment = 30)  THEN  1
    
    ELSE  0
    
    END  AS is_active_30
    
    FROM cross_join
    
    )
    
    SELECT *
    
    FROM status
    
    LIMIT  10;
![enter image description here](https://i.ibb.co/hMn7nvb/5.png)
    
      
    
    -- 6
    
    -- Add columns to the table to see if a subscriber from either segment canceled during a month.
    
    WITH months AS
    
    (SELECT
    
    '2017-01-01'  AS first_day,
    
    '2017-01-31'  AS last_day
    
    UNION
    
    SELECT
    
    '2017-02-01'  AS first_day,
    
    '2017-02-28'  AS last_day
    
    UNION
    
    SELECT
    
    '2017-03-01'  AS first_day,
    
    '2017-03-31'  AS last_day
    
    ),
    
    cross_join AS
    
    (SELECT *
    
    FROM subscriptions
    
    CROSS  JOIN months
    
    ),
    
    status AS
    
    (SELECT id,
    
    first_day AS  month,
    
    CASE
    
    WHEN  (subscription_start < first_day)
    
    AND  (subscription_end > first_day
    
    OR subscription_end IS  NULL)
    
    AND  (segment = 87)  THEN  1
    
    ELSE  0
    
    END  AS is_active_87,
    
    CASE
    
    WHEN  (subscription_start < first_day)
    
    AND  (subscription_end > first_day
    
    OR subscription_end IS  NULL)
    
    AND  (segment = 30)  THEN  1
    
    ELSE  0
    
    END  AS is_active_30,
    
    CASE
    
    WHEN  (subscription_end BETWEEN first_day AND last_day)
    
    AND  (segment = 87)  THEN  1
    
    ELSE  0
    
    END  AS is_canceled_87,
    
    CASE
    
    WHEN  (subscription_end BETWEEN first_day AND last_day)
    
    AND  (segment = 30)  THEN  1
    
    ELSE  0
    
    END  AS is_canceled_30
    
    FROM cross_join
    
    )
    
    SELECT *
    
    FROM status
    
    LIMIT  10;
![enter image description here](https://i.ibb.co/BCdJMfz/6.png)
    
      
    
    -- 7
    
    -- Create a temporary table that adds active and/or canceled subscriptions for each segment and month.
    
    WITH months AS
    
    (SELECT
    
    '2017-01-01'  AS first_day,
    
    '2017-01-31'  AS last_day
    
    UNION
    
    SELECT
    
    '2017-02-01'  AS first_day,
    
    '2017-02-28'  AS last_day
    
    UNION
    
    SELECT
    
    '2017-03-01'  AS first_day,
    
    '2017-03-31'  AS last_day
    
    ),
    
    cross_join AS
    
    (SELECT *
    
    FROM subscriptions
    
    CROSS  JOIN months
    
    ),
    
    status AS
    
    (SELECT id,
    
    first_day AS  month,
    
    CASE
    
    WHEN  (subscription_start < first_day)
    
    AND  (subscription_end > first_day
    
    OR subscription_end IS  NULL)
    
    AND  (segment = 87)  THEN  1
    
    ELSE  0
    
    END  AS is_active_87,
    
    CASE
    
    WHEN  (subscription_start < first_day)
    
    AND  (subscription_end > first_day
    
    OR subscription_end IS  NULL)
    
    AND  (segment = 30)  THEN  1
    
    ELSE  0
    
    END  AS is_active_30,
    
    CASE
    
    WHEN  (subscription_end BETWEEN first_day AND last_day)
    
    AND  (segment = 87)  THEN  1
    
    ELSE  0
    
    END  AS is_canceled_87,
    
    CASE
    
    WHEN  (subscription_end BETWEEN first_day AND last_day)
    
    AND  (segment = 30)  THEN  1
    
    ELSE  0
    
    END  AS is_canceled_30
    
    FROM cross_join
    
    ),
    
    status_aggregate AS
    
    (SELECT  month,
    
    SUM(is_active_87)  AS sum_active_87,
    
    SUM(is_active_30)  AS sum_active_30,
    
    SUM(is_canceled_87)  AS sum_canceled_87,
    
    SUM(is_canceled_30)  AS sum_canceled_30
    
    FROM status
    
    GROUP  BY  month
    
    )
    
    SELECT *
    
    FROM status_aggregate;
![enter image description here](https://i.ibb.co/52sWhhs/7.png)
    
      
    
    -- 8
    
    -- Calculate the churn rates for the two segments over the three month period. Which segment has a lower churn rate?
    
    WITH months AS
    
    (SELECT
    
    '2017-01-01'  AS first_day,
    
    '2017-01-31'  AS last_day
    
    UNION
    
    SELECT
    
    '2017-02-01'  AS first_day,
    
    '2017-02-28'  AS last_day
    
    UNION
    
    SELECT
    
    '2017-03-01'  AS first_day,
    
    '2017-03-31'  AS last_day
    
    ),
    
    cross_join AS
    
    (SELECT *
    
    FROM subscriptions
    
    CROSS  JOIN months
    
    ),
    
    status AS
    
    (SELECT id,
    
    first_day AS  month,
    
    CASE
    
    WHEN  (subscription_start < first_day)
    
    AND  (subscription_end > first_day
    
    OR subscription_end IS  NULL)
    
    AND  (segment = 87)  THEN  1
    
    ELSE  0
    
    END  AS is_active_87,
    
    CASE
    
    WHEN  (subscription_start < first_day)
    
    AND  (subscription_end > first_day
    
    OR subscription_end IS  NULL)
    
    AND  (segment = 30)  THEN  1
    
    ELSE  0
    
    END  AS is_active_30,
    
    CASE
    
    WHEN  (subscription_end BETWEEN first_day AND last_day)
    
    AND  (segment = 87)  THEN  1
    
    ELSE  0
    
    END  AS is_canceled_87,
    
    CASE
    
    WHEN  (subscription_end BETWEEN first_day AND last_day)
    
    AND  (segment = 30)  THEN  1
    
    ELSE  0
    
    END  AS is_canceled_30
    
    FROM cross_join
    
    ),
    
    status_aggregate AS
    
    (SELECT  month,
    
    SUM(is_active_87)  AS sum_active_87,
    
    SUM(is_active_30)  AS sum_active_30,
    
    SUM(is_canceled_87)  AS sum_canceled_87,
    
    SUM(is_canceled_30)  AS sum_canceled_30
    
    FROM status
    
    GROUP  BY  month
    
    )
    
    SELECT  month,
    
    ROUND((1.0 * sum_canceled_87 / sum_active_87), 3)  AS churn_rate_87,
    
    ROUND((1.0 * sum_canceled_30 / sum_active_30), 3)  AS churn_rate_30
    
    FROM status_aggregate;
    
    -- The data shows that segment 30 has a lower churn rate, therefore performing better than segment 87.
![enter image description here](https://i.ibb.co/KFvJwhy/8.png)
    
      
    
    -- 9
    
    -- Modify this code to support a larger number of segments.
    
    WITH months AS
    
    (SELECT
    
    '2017-01-01'  AS first_day,
    
    '2017-01-31'  AS last_day
    
    UNION
    
    SELECT
    
    '2017-02-01'  AS first_day,
    
    '2017-02-28'  AS last_day
    
    UNION
    
    SELECT
    
    '2017-03-01'  AS first_day,
    
    '2017-03-31'  AS last_day
    
    ),
    
    cross_join AS
    
    (SELECT *
    
    FROM subscriptions
    
    CROSS  JOIN months
    
    ),
    
    status AS
    
    (SELECT id,
    
    first_day AS  month,
    
    segment,
    
    CASE
    
    WHEN  (subscription_start < first_day)
    
    AND  (subscription_end > first_day
    
    OR subscription_end IS  NULL)  THEN  1
    
    ELSE  0
    
    END  AS is_active,
    
    CASE
    
    WHEN  (subscription_end BETWEEN first_day AND last_day)  THEN  1
    
    ELSE  0
    
    END  AS is_canceled
    
    FROM cross_join
    
    ),
    
    status_aggregate AS
    
    (SELECT  month,
    
    segment,
    
    SUM(is_active)  AS sum_active,
    
    SUM(is_canceled)  AS sum_canceled
    
    FROM status
    
    GROUP  BY  month, segment
    
    )
    
    SELECT  month,
    
    segment,
    
    ROUND((1.0 * sum_canceled / sum_active), 3)  AS churn_rate
    
    FROM status_aggregate;
![enter image description here](https://i.ibb.co/Thwb42t/9.png)


In conclusion, the churn rate of segment 30 is quite lower than that of segment 87. This observation shows that the marketing done for segment 30 is more effective, therefore the marketing team would benefit from further pushing this segment in their methods. 
