/* Problem Statement:
- For pairs of brands in the same year (e.g. apple/Samsung/2020 and Samsung/apple/2020) 
    - if custom1 = custom3 and custom2 = custom4 : then keep only one pair

- For pairs of brands in the same year 
    - if custom1 != custom3 OR custom2 != custom4 : then keep both pairs

- For brands that do not have pairs in the same year: keep those rows as well
*/

### First created the required tables and their entries

```SQL
DROP TABLE IF EXISTS brands;
CREATE TABLE brands 
(
    brand1      VARCHAR(20),
    brand2      VARCHAR(20),
    year        INT,
    custom1     INT,
    custom2     INT,
    custom3     INT,
    custom4     INT
);
```
```SQL
INSERT INTO brands VALUES ('apple', 'samsung', 2020, 1, 2, 1, 2);
INSERT INTO brands VALUES ('samsung', 'apple', 2020, 1, 2, 1, 2);
INSERT INTO brands VALUES ('apple', 'samsung', 2021, 1, 2, 5, 3);
INSERT INTO brands VALUES ('samsung', 'apple', 2021, 5, 3, 1, 2);
INSERT INTO brands VALUES ('google', NULL, 2020, 5, 9, NULL, NULL);
INSERT INTO brands VALUES ('oneplus', 'nothing', 2020, 5, 9, 6, 3);

SELECT * FROM brands;
```

![image](https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/8d72adf6-c823-49b0-a273-3a146d9f5b29)


Expected Output:

![image](https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/96038bb8-ae7d-493d-bca7-6c184a8d20e9)


### SOLUTION

Reference : https://www.youtube.com/watch?v=FRzbOb3jdLg&t=982s

1. First form the pairs of brands and their year - Eg: applesamsung2020

   Here when you check on the table the first row, applesamsung2020, and 2nd row samsungapple2020 both are the same.
   So we can use concat() function for forming the pair_id and also use the CASE STATEMENT

```SQL
SELECT * , 
	CASE WHEN brand1 < brand2 THEN CONCAT(brand1, brand2, year) 
		 ELSE CONCAT(brand2,brand1,year) END AS 'pair_id'
FROM brands;
```

![image](https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/28ec95cd-2617-4136-957b-1a00ac2a324c)

2. Now we need to rank each pair_id to get some clarity. To rank, we use the row_number() window function here.
3. Before doing that , bring the 1st query into a CTE function

```SQL
WITH CTE AS (
    SELECT *, CASE WHEN brand1 < brand2 THEN CONCAT(brand1, brand2, year) 
             ELSE CONCAT(brand2, brand1, year) END AS pair_id 
    FROM brands
),
CTE_rnk AS (
    SELECT *, ROW_NUMBER() OVER (PARTITION BY pair_id ORDER BY pair_id) AS rank
    FROM CTE
)
SELECT * FROM CTE_rnk;
```

![image](https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/ee8d7cec-f96a-41fd-85b8-7e345b3e8ae5)


3. Now we have the pair_id and its ranking. If we have two pairs where custom1 = custom3 and custom2 = custom4, we gonna retain only 1 among them in the output.
   So In the pair_id applesamsung2020 with ranks 1 & 2, both are the same, so we can select rank 1 here to be in the output
4. If we have two pairs where custom1 != custom3 and custom2 != custom4, we gonna retain both the records in the output.
5. Return all the records without pair also (which has rank 1)

```SQL
WITH CTE AS (
    SELECT *, CASE WHEN brand1 < brand2 THEN CONCAT(brand1, brand2, year) 
             ELSE CONCAT(brand2, brand1, year) END AS pair_id 
    FROM brands
),
CTE_rnk AS (
    SELECT *, ROW_NUMBER() OVER (PARTITION BY pair_id ORDER BY pair_id) AS rank
    FROM CTE
)
SELECT brand1, brand2, year, custom1, custom2, custom3, custom4
FROM CTE_rnk
WHERE rank = 1 
OR (custom1 <> custom3 AND custom2 <> custom4);
```

![image](https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/80bd1296-03c1-40de-8001-6f140ad0cdae)

