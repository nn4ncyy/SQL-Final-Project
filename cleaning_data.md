What issues will you address by cleaning the data?
1. Duplicate rows in the all_sessions table
2. Referential integrity in all_sessions & analytics tables
3. Duplicate rows in the analytics table
4. Dealing with NULLs in the analytics table
6. unit_price column in the analytics table
7. Referential integrity between product SKU's in products, sales_by_sku, and sales_report tables


Queries:
Below, provide the SQL queries you used to clean your data.
-- 	DATA CLEANING

-- all_sessions 
-- DUPLICATE ROWS
SELECT * FROM all_sessions
SELECT visitid, fullvisitorid FROM all_sessions

SELECT DISTINCT(visitid, fullvisitorid) 
FROM all_sessions;
-- see different, display duplicates

SELECT fullvisitorid, visitid, COUNT(*)
FROM all_sessions
GROUP BY fullvisitorid, visitid
HAVING COUNT(*) > 1;

-- delete duplicates AS NEEDED
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

	-- This allows me to make fullvisitorid and visitid as my primary keys.

-- all_sessions + analytics
-- checking if analytics covers all_sessions
SELECT DISTINCT(visitid)
FROM analytics
WHERE NOT EXISTS 
    (SELECT visitid
     FROM all_sessions
     WHERE analytics.visitid = all_sessions.visitid)

-- analytics
SELECT * FROM analytics LIMIT 100;
SELECT visitid, fullvisitorid FROM analytics;

SELECT DISTINCT(visitid, fullvisitorid)
FROM analytics;
-- shows duplicates, so clean it up:

-- why are unitprice being higher than 1?
SELECT visitid, fullvisitorid, COUNT(*)
FROM analytics
GROUP BY fullvisitorid, visitid
HAVING COUNT(*) > 1

-- MIGHT MAKE MORE SENSE TO average out the unitprice to keep visitid's distinct:
SELECT visitid, fullvisitorid, AVG(unit_price) AS average_unitprice
FROM analytics
GROUP BY visitid, fullvisitorid;


-- ADDRESSING NULLS
SELECT * FROM analytics WHERE units_sold IS NOT NULL;
-- RETURNS 95000 ROWS, CONSIDER COALESCING THE OTHER ROWS TO 0:

SELECT * FROM analytics WHERE units_sold = 0;
-- RETURNS 0 ROWS

SELECT COALESCE(units_sold, 0) AS units_sold
FROM analytics;


SELECT * FROM analytics WHERE userid IS NOT NULL;
-- RETURNS NO ROWS, consider deleting the column:
ALTER TABLE analytics
DROP COLUMN userid;

SELECT * FROM analytics WHERE bounces IS NOT NULL;
SELECT * FROM analytics WHERE bounces = 0;
-- RETURNS 474839 ROWS, CONSIDER COALESCING THE OTHER ROWS TO 0:

SELECT COALESCE(bounces, 0) AS bounces
FROM analytics;

SELECT * FROM analytics WHERE revenue IS NOT NULL;
-- -- RETURNS 15355 ROWS, CONSIDER COALESCING THE OTHER ROWS TO 0:
SELECT COALESCE(revenue, 0) AS revenue
FROM analytics;

-- addressing unitprice
UPDATE analytics 
SET unit_price= unit_price/1000000;

-- products
SELECT * FROM products;

SELECT DISTINCT(sku)
FROM products;
-- same number

-- sales_by_sku
SELECT * FROM sales_by_sku;

SELECT DISTINCT(productsku)
FROM sales_by_sku;

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

SELECT * FROM saleS_report

SELECT DISTINCT(productsku)
FROM saleS_report;

-- sales report
