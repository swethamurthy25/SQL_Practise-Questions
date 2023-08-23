Assume you're given the table on user viewership categorised by device type where the three types are laptop, tablet, and phone.

Write a query that calculates the total viewership for laptops and mobile devices where mobile is defined as the sum of tablet and phone viewership. 
Output the total viewership for laptops as laptop_reviews and the total viewership for mobile devices as mobile_views.

<img width="271" alt="image" src="https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/577dc79f-6a03-4502-aab4-8913e6817c9b">

<img width="204" alt="image" src="https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/b8a80c6f-8e4c-4617-975d-3dae57532248">

### Solution

```sql
SELECT 
  SUM(CASE WHEN device_type = 'laptop' THEN 1 ELSE 0 END) AS laptop_views,
  SUM(CASE WHEN device_type IN ('tablet','phone') THEN 1 ELSE 0 END) AS mobile_views
FROM viewership;
```



