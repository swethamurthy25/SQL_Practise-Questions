A ski resort company plans to construct a new ski slope using a pre-existing network of mountain huts and trails between them. 
A new slope has to begin at one of the mountain huts, have a middle station at another hut connected with the first one by a direct trail, and end at 
the third mountain hut connected by a direct trail to the second hut. The altitude of the three huts chosen for constructing the ski slope has 
to be strictly decreasing.

You are given two SQL tables, mountain_huts, and trails, with the following structure:

Each entry in the table trails represents a direct connection between huts with IDs hut1 and hut2. Note that all trails are bidirectional.

Create a query that finds all triplets(startpt,middlept,endpt) representing the mountain huts that may be used for the construction of a ski slope.
The output returned by the query can be ordered in any way.

```SQL
create table mountain_huts 
(
	id 			integer not null unique,
	name 		varchar(40) not null unique,
	altitude 	integer not null
);
insert into mountain_huts values (1, 'Dakonat', 1900);
insert into mountain_huts values (2, 'Natisa', 2100);
insert into mountain_huts values (3, 'Gajantut', 1600);
insert into mountain_huts values (4, 'Rifat', 782);
insert into mountain_huts values (5, 'Tupur', 1370);

SELECT * FROM mountain_huts;
```

![image](https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/55da3c6f-a1c5-4c58-8d97-538bf88667d6)

```SQL
create table trails (
 hut1 integer not null,
 hut2 integer not null
 );

 insert into trails values (1, 3);
insert into trails values (3, 2);
insert into trails values (3, 5);
insert into trails values (4, 5);
insert into trails values (1, 5);

SELECT * FROM trails;
```

![image](https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/d1bde292-d1a6-4d82-aa2d-562fa2b73ad9)

### SOLUTION

Reference: https://www.youtube.com/watch?v=rM1BVoBke04

1. The very step here is, we have some common fields in both the tables and we need to combine the tables to get the altitude of both hut1 and hut2
2. First combine mountain_huts with trails t1 ON id = hut 1 .
3. Then put it inside a CTE and combine the CTE again with hut2 ON id = hut 2

```SQL
WITH cte_trails1 AS (
		SELECT m1.* , t1.*
		FROM mountain_huts m1 
		JOIN trails t1 ON m1.id = t1.hut1)
SELECT * 
FROM cte_trails1 t2
JOIN mountain_huts m2 ON t2.hut2 = m2.id;
```

![image](https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/0bdbabb5-a301-4d9f-9dd1-1da999aed876)


4. Now remove the unnecessary columns from the table, and rename the columns to get a clear picture of the data.
5. Rename as below:

   m1.name --> start_hut_name
   
   m1.altitude ===> start_hut_altitude

   h1.hut1 --> start_hut
   
   h1.hut2 --> end_hut

   m2.name --> end_hut_name
   
   m2.altitude --> end_hut_altitude

```SQL
WITH cte_trails1 AS (
		SELECT t1.hut1 AS 'start_hut', 
			   m1.name AS 'start_hut_name', 
			   m1.altitude AS 'start_hut_altitude',
			   t1.hut2 AS 'end_hut'
		FROM mountain_huts m1 
		JOIN trails t1 ON m1.id = t1.hut1)
SELECT t2.* , m2.name AS 'end_hut_name', m2.altitude AS 'end_hut_altitude'
FROM cte_trails1 t2
JOIN mountain_huts m2 ON t2.end_hut = m2.id;
```

![image](https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/5832290e-221d-4435-86dd-fc72d866ee06)

6. Now check for the altitude of start_hut and end_hut , it should be in decreasing order.  When I look at the data, two records are not satisfying
   our condition [Gajantut , Natisa] AND [Rifat & Tupur]
7. Since the trail is bidirectional I can use those records in reverse way. To identify those records, set up a flag to differentiate them.

```SQL
WITH cte_trails1 AS (
		SELECT t1.hut1 AS 'start_hut', 
			   m1.name AS 'start_hut_name', 
			   m1.altitude AS 'start_hut_altitude',
			   t1.hut2 AS 'end_hut'
		FROM mountain_huts m1 
		JOIN trails t1 ON m1.id = t1.hut1)
SELECT t2.* , m2.name AS 'end_hut_name', m2.altitude AS 'end_hut_altitude',
CASE WHEN start_hut_altitude > m2.altitude THEN 1 ELSE 0 END AS 'altitude_flag'
FROM cte_trails1 t2
JOIN mountain_huts m2 ON t2.end_hut = m2.id;
```

![image](https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/8cf3e63f-9658-475a-bcbf-0a0856bb4fcd)

8. Now combine everything into another CTE.

```SQL
WITH cte_trails1 AS (
		SELECT t1.hut1 AS 'start_hut', 
			   m1.name AS 'start_hut_name', 
			   m1.altitude AS 'start_hut_altitude',
			   t1.hut2 AS 'end_hut'
		FROM mountain_huts m1 
		JOIN trails t1 ON m1.id = t1.hut1),
	cte_trails2 AS (
		SELECT t2.* , m2.name AS 'end_hut_name', m2.altitude AS 'end_hut_altitude',
		CASE WHEN start_hut_altitude > m2.altitude THEN 1 ELSE 0 END AS 'altitude_flag'
		FROM cte_trails1 t2
		JOIN mountain_huts m2 ON t2.end_hut = m2.id)
SELECT * FROM cte_trails2;
```

![image](https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/454bcc2a-3ec5-4615-8b0d-a62bf5434d4c)

9. Now USE CASE STATEMENTS TO REVERSE THE huts. If the altitude_flag is 1 consider the start_hut as start_hut , or else for other records consider end_hut as start_hut

  ```SQL
  CASE WHEN altitude_flag = 1 THEN start_hut ELSE end_hut END AS start_hut
  ```

10. The same logic applies to hut_name also

  ```SQL
   CASE WHEN altitude_flag = 1 THEN start_hut ELSE end_hut END AS start_hut
   CASE WHEN altitude_flag = 1 THEN start_hut_name ELSE end_hut_name END AS start_hut_name
  ```

11. We also need to finalize the end_huts. If the altitude_flag is 1 then end_hut as end_hut or else start_hut as end_hut
12. The same logic applies to end_hut_name as well

  ```SQL
   CASE WHEN altitude_flag = 1 THEN end_hut ELSE start_hut END AS end_hut
   CASE WHEN altitude_flag = 1 THEN end_hut_name ELSE start_hut_name END AS end_hut_name
  ```
```SQL

WITH cte_trails1 AS (
		SELECT t1.hut1 AS 'start_hut', 
			   m1.name AS 'start_hut_name', 
			   m1.altitude AS 'start_hut_altitude',
			   t1.hut2 AS 'end_hut'
		FROM mountain_huts m1 
		JOIN trails t1 ON m1.id = t1.hut1),
	cte_trails2 AS (
		SELECT t2.* , m2.name AS 'end_hut_name', m2.altitude AS 'end_hut_altitude',
		CASE WHEN start_hut_altitude > m2.altitude THEN 1 ELSE 0 END AS 'altitude_flag'
		FROM cte_trails1 t2
		JOIN mountain_huts m2 ON t2.end_hut = m2.id)
SELECT CASE WHEN altitude_flag = 1 THEN start_hut ELSE end_hut END AS 'start_hut',
	     CASE WHEN altitude_flag = 1 THEN start_hut_name ELSE end_hut_name END AS 'start_hut_name',
	     CASE WHEN altitude_flag = 1 THEN end_hut ELSE start_hut END AS 'end_hut',
       CASE WHEN altitude_flag = 1 THEN end_hut_name ELSE start_hut_name END AS 'end_hut_name'
	   FROM cte_trails2;
```

![image](https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/65df3452-53cb-4aff-b314-ef77626a3977)

13. Now based on the start_hut and end_hut we can form the complete end-to-end trail route. For example 1 ---> 3 in first row , now again search for 3 in start_hut,
    we could see that 3 ---> 5 , so  1 --> 3 ---> 5 can be a route.
14. Apply this logic and find all possible routes now. To do this, use SELF JOIN to join the table with itself.

```SQL
WITH cte_trails1 AS (
		SELECT t1.hut1 AS 'start_hut', 
			   m1.name AS 'start_hut_name', 
			   m1.altitude AS 'start_hut_altitude',
			   t1.hut2 AS 'end_hut'
		FROM mountain_huts m1 
		JOIN trails t1 ON m1.id = t1.hut1),
	cte_trails2 AS (
		SELECT t2.* , m2.name AS 'end_hut_name', m2.altitude AS 'end_hut_altitude',
		CASE WHEN start_hut_altitude > m2.altitude THEN 1 ELSE 0 END AS 'altitude_flag'
		FROM cte_trails1 t2
		JOIN mountain_huts m2 ON t2.end_hut = m2.id),
CTE_Final AS(SELECT 
	   CASE WHEN altitude_flag = 1 THEN start_hut ELSE end_hut END AS 'start_hut',
	   CASE WHEN altitude_flag = 1 THEN start_hut_name ELSE end_hut_name END AS 'start_hut_name',
	   CASE WHEN altitude_flag = 1 THEN end_hut ELSE start_hut END AS 'end_hut',
       CASE WHEN altitude_flag = 1 THEN end_hut_name ELSE start_hut_name END AS 'end_hut_name'
	   FROM cte_trails2)
SELECT * FROM CTE_Final c1 
JOIN CTE_Final c2 ON c1.end_hut = c2.start_hut;
```

![image](https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/956776a8-154d-4779-883e-ba75c2c648a8)

15. Now rename the columns as expected in the output.

```SQL
WITH cte_trails1 AS (
		SELECT t1.hut1 AS 'start_hut', 
			   m1.name AS 'start_hut_name', 
			   m1.altitude AS 'start_hut_altitude',
			   t1.hut2 AS 'end_hut'
		FROM mountain_huts m1 
		JOIN trails t1 ON m1.id = t1.hut1),
	cte_trails2 AS (
		SELECT t2.* , m2.name AS 'end_hut_name', m2.altitude AS 'end_hut_altitude',
		CASE WHEN start_hut_altitude > m2.altitude THEN 1 ELSE 0 END AS 'altitude_flag'
		FROM cte_trails1 t2
		JOIN mountain_huts m2 ON t2.end_hut = m2.id),
CTE_Final AS(SELECT 
	   CASE WHEN altitude_flag = 1 THEN start_hut ELSE end_hut END AS 'start_hut',
	   CASE WHEN altitude_flag = 1 THEN start_hut_name ELSE end_hut_name END AS 'start_hut_name',
	   CASE WHEN altitude_flag = 1 THEN end_hut ELSE start_hut END AS 'end_hut',
       CASE WHEN altitude_flag = 1 THEN end_hut_name ELSE start_hut_name END AS 'end_hut_name'
	   FROM cte_trails2)
SELECT c1.start_hut_name AS 'startpt' , c1.end_hut_name AS 'middlept', c2.end_hut_name AS 'endpt'
FROM CTE_Final c1 
JOIN CTE_Final c2 ON c1.end_hut = c2.start_hut;
```

![image](https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/42603345-4c98-4227-96c1-a82458c2506a)


   

   
