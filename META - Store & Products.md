Below is the given schema: 

![image](https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/cf5fe9bd-a750-42df-b32b-a3868b70db62) ![image](https://github.com/swethamurthy25/SQL_Practise-Questions/assets/112581595/cfa55928-68f0-4366-a431-77ef61fb8dc7)

1. What brands have an average price above $3 and contain at least 2 different products?
```SQL
SELECT p.brand_name
FROM products p
LEFT JOIN sales s 
GROUP BY p.brand_name
HAVING AVG(s.store_cost) > 3 AND COUNT(DISTINCT p.product_id) >= 2
```
2. To improve sales, the marketing department runs various types of promotions.
The marketing manager would like to analyze the effectiveness of these promotion campaigns.
In particular, what percent of our sales transactions had a valid promotion applied?

* Filter the store_sales that has valid promotion id (CASE WHEN promotion_id IS NOT NULL)
* Total store_sales as total_value COUNT(*)
* Find the percentage using both
```SQL
SELECT 100.0 * SUM(CASE WHEN promotion_id IS NOT NULL THEN 1 ELSE 0 END) / COUNT(*) AS ratio
FROM sales
```

3. We want to run a new promotion for our most successful category of products
(we call these categories “product classes”).
Can you find out the top 3 selling product classes by total sales?
```SQL
WITH CTE1 AS (
    SELECT 
        p.product_class_id,
        SUM(s.store_sales) AS total_sales,
        RANK() OVER (ORDER BY SUM(s.store_sales) DESC) AS rnk
    FROM products p 
    JOIN sales s ON p.product_id = s.product_id
    GROUP BY p.product_class_id)
SELECT product_class_id, total_sales
FROM CTE1 
WHERE rnk <= 3;
```

