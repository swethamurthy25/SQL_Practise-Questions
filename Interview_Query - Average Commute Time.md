Let’s say you work at Uber. The rides table contains information about the trips of Uber users across America

Write a query to get the average commute time (in minutes) for each commuter in New York (NY) and the average commute time (in minutes) across all commuters in New York.

 ![image](https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/4aaf142a-af75-4f59-b900-c5ef3271e98d)

  ```SQL
WITH commute_time AS (
    SELECT *,
    EXTRACT(EPOCH FROM (end_dt - start_dt))/60 AS commute_time
    FROM rides),
average_commuter_time AS(
    SELECT commuter_id,
    FLOOR(AVG(commute_time)) AS avg_commuter_time
    FROM commute_time
    WHERE city = 'NY'
    GROUP BY 1),
avg_all_commute_time AS (
    SELECT FLOOR(AVG(commute_time)) AS avg_time
    FROM commute_time
    WHERE city = 'NY')

SELECT 
a.commuter_id,
a.avg_commuter_time,
b.avg_time
FROM average_commuter_time a 
JOIN avg_all_commute_time b ON TRUE
ORDER BY 1
```
