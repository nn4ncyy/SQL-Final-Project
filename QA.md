### What are your risk areas? Identify and describe them.

My main goal when going through my QA process was to ensure that all the data was properly represented in the tables. This is quite a broad goal, so heres the breakdown of my table-specific goals:

1. Ensure no tables needing primary keys have duplicates. Duplicates prevents primary keys from being added. It also doesn't make sense to have duplicate if the table requires that rows have a unique identifier.
2. Address NULL values in a way that they can logically be explained. Null values can be missing data that can aid in the analysis of said data. By examining what we can do to this data, we are making more accurate assertions about the data as a whole.
3. Ensure tables with foreign keys were in agreeance with each other. If the data in one table does not match the expected results of another table, this is a cause of concern. We want to make sure everything agrees so that the conclusions we draw aren't based on misrepresentative data.


### QA Process:
Describe your QA process and include the SQL queries used to execute it.

Removing the duplicates proved to be a very beneficial step in the QA process as I was able to use that to ensure that the data was properly represented in tables. This helped 1) in the creation of primary keys, and 2) allowed me to feel more confident in seeing how different tables interacted. 

**Queries to remove duplicates:**
```
# step 1. check if row numbers are different

SELECT visitid, fullvisitorid
FROM all_sessions;

SELECT DISTINCT(visitid, fullvisitorid) 
FROM all_sessions;

# step 2. row numbers are different, so display duplicate rows + their duplicate counts
SELECT fullvisitorid, visitid, COUNT(*)
FROM all_sessions
GROUP BY fullvisitorid, visitid
HAVING COUNT(*) > 1;

# step 3. delete duplicate rows as needed
WITH DuplicateRows AS (
    SELECT *,
           ROW_NUMBER() OVER (PARTITION BY fullvisitorid, visitid ORDER BY id) AS row_num
    FROM all_sessions
)

DELETE FROM all_sessions
WHERE fullvisitorid,visitid IN (
    SELECT fullvisitorid, visitid
    FROM DuplicateRows
    WHERE row_num > 1
);
```

On the other hand, it was quite difficult for me to understand when it was or was not appropriate to remove certain data. There were a lot of NULL values, but not enough to where it seemed appropriate to delete the columns. I did by best to coalesce what I could, but even then, im still wondering if coalescing is the appropriate step, or if the NULLs represent something more fishy that I’d need to investigate further.

**Queries to coalesce NULLS:**
```
# 1. units_sold
SELECT *
FROM analytics
WHERE units_sold IS NOT NULL;
-- RETURNS 95000 ROWS, CONSIDER COALESCING THE OTHER ROWS TO 0
-- does units_sold display items when no units were sold?

SELECT *
FROM analytics
WHERE units_sold = 0;
-- RETURNS 0 ROWS
-- consider NULLs may represent the value 0, coalesce:

SELECT COALESCE(units_sold, 0) AS units_sold
FROM analytics;

# 2. userid
SELECT *
FROM analytics
WHERE userid IS NOT NULL;
-- RETURNS NO ROWS, consider deleting the column:

ALTER TABLE analytics
DROP COLUMN userid;

# 3. bounce
SELECT *
FROM analytics
WHERE bounces IS NOT NULL;

SELECT *
FROM analytics
WHERE bounces = 0;
-- RETURNS 474839 ROWS, CONSIDER COALESCING THE OTHER ROWS TO 0:

SELECT COALESCE(bounces, 0) AS bounces
FROM analytics;

# 4. revenue
SELECT *
FROM analytics
WHERE revenue IS NOT NULL;
-- RETURNS 15355 ROWS, CONSIDER COALESCING THE OTHER ROWS TO 0:
SELECT COALESCE(revenue, 0) AS revenue
FROM analytics;
```

In the same vein, Making inferences on the relationship between two tables was difficult too. For example, I came to the conclusion that the sales_by_sku tables had SKU’s that were not in the products table, so I added the SKUs to the products table. I’m unsure if this was the correct conclusion, because maybe the reality is, that product is just no longer available. 

**Queries I used to address relationships:**
```
# step 1. check if every row displays a distinct SKU for products table, and sales_by_sku table
SELECT *
FROM products;

SELECT DISTINCT(sku)
FROM products;

SELECT *
FROM sales_by_sku;

SELECT DISTINCT(productsku)
FROM sales_by_sku;
-- yes, they both do

-- checking if sales_by_sku doesn't have any sku's not in the products table
SELECT DISTINCT(productsku)
FROM sales_by_sku
WHERE NOT EXISTS 
    (SELECT sku
     FROM products
     WHERE products.sku = sales_by_sku.productsku)

-- checking if sku doesn't have any productsku's not in the sales_by_sku table
SELECT DISTINCT(sku)
FROM products
WHERE NOT EXISTS 
    (SELECT productsku
     FROM sales_by_sku
     WHERE products.sku = sales_by_sku.productsku)

-- checking if sales_by_sku does indeed reflect even ordered that don't have orders
SELECT * FROM sales_by_sku WHERE total_ordered = 0;

-- so what do we do? add the skus to each table to make sure they both have all the skus
INSERT INTO products (sku)
SELECT DISTINCT productsku
FROM sales_by_sku
WHERE productsku NOT IN (SELECT sku FROM products);

INSERT INTO sales_by_sku (productsku)
SELECT DISTINCT sku
FROM products
WHERE sales_by_sku NOT IN (SELECT productsku FROM sales_by_sku);

```

Overall, the QA process made me realize the importance of Data cleaning, and any future extensions of this project would involve diving deeper on what data cleaning entails, and how to be better at it. 
