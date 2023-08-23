Assume you're given two tables containing data about Facebook Pages and their respective likes (as in "Like a Facebook Page").

Write a query to return the IDs of the Facebook pages that have zero likes. The output should be sorted in ascending order based on the page IDs.

<img width="289" alt="image" src="https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/5417d7ab-1804-4c82-a3b0-5ac55cb41d99">

<img width="280" alt="image" src="https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/e4ac75a7-c9f6-4834-a15d-1dea268b02c0">


### Solution

```sql
SELECT pg.page_id FROM pages pg
LEFT JOIN page_likes pl ON pg.page_id = pl.page_id
WHERE pl.page_id IS NULL;
```

Here the hint is the page_likes table will not have the page_id if it has no likes.
