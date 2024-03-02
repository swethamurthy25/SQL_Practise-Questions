Assume you have two tables: Product and Supplier.

Product_id | Product_Name | Supplier_id | Price
-----------|--------------|-------------|-------
1          | Laptop       | 1           | 1200
2          | Smartphone   | 2           | 800
3          | Camera       | 1           | 500
4          | Tablet       | 2           | 1000
5          | Headphones   | 3           | 150


Supplier_id | Supplier_Name | Country
------------|---------------|---------
1           | Supplier_A    | USA
2           | Supplier_B    | China
3           | Supplier_C    | Germany


Write an SQL query to find the name of the product with the highest price in each country.

1. First find the rank of each product based on price & country-wise

```sql
WITH CTE AS (
    SELECT P.Product_Name, S.Country, P.Price,
        RANK() OVER (PARTITION BY S.Country ORDER BY P.Price) AS Rank
    FROM Product P
    INNER JOIN Supplier S ON P.Supplier_id = S.Supplier_id
)

SELECT * FROM CTE;
```

Product_Name | Country | Price | Rank
--------------|---------|-------|-----
Camera        | USA     | 500   | 1
Laptop        | USA     | 1200  | 2
Smartphone    | China   | 800   | 1
Tablet        | China   | 1000  | 2
Headphones    | Germany | 150   | 1




