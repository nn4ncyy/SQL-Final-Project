What issues will you address by cleaning the data?
1. Duplicate rows in the all_sessions table
2. Duplicate rows in the analytics table
3. Dealing with NULLs in the analytics table
4. unit_price column in the analytics table
5. Referential integrity between product SKU's in products, sales_by_sku, and sales_report tables


Queries:
Below, provide the SQL queries you used to clean your data.

## 1. Cleaning duplicate rows in the all_sessions table
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
## 2. Cleaning duplicate rows in the analytics table

```
# step 1. check if row numbers are different
SELECT visitid, fullvisitorid FROM analytics;

SELECT DISTINCT(visitid, fullvisitorid)
FROM analytics;

# step 2. row numbers are different, so display duplicate rows + their duplicate counts
SELECT visitid, fullvisitorid, COUNT(*)
FROM analytics
GROUP BY fullvisitorid, visitid
HAVING COUNT(*) > 1

# step 3. it might make more sense to average out the unitprice to keep visitid's distinct:
SELECT visitid, fullvisitorid, AVG(unit_price) AS average_unitprice
FROM analytics
GROUP BY visitid, fullvisitorid;
-- now, visitid, fullvisitorid can be used as primary keys in the anlytics table
```
## 3. Addressing the many NULLs in the analytics table
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

## 4. Addressing unitprice
```
UPDATE analytics 
SET unit_price= unit_price/1000000;
```

## 5. Ensuring referential integrity between product SKU's in products, sales_by_sku, and sales_report tables

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
