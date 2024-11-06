Question 1:  What are the top 5 most popular products (by views) among visitors who eventually made a purchase?

SQL Queries:

SELECT v2ProductName AS product_name, COUNT(*) AS views_by_purchasers
FROM all_sessions
WHERE transactions > 0 AND v2ProductName IS NOT NULL
GROUP BY v2ProductName
ORDER BY views_by_purchasers DESC;

Answer: This question reveals which products are highly viewed by converting visitors, which may indicate popular products that attract paying customers.



Question 2: 

SQL Queries:

Answer:



Question 3: 

SQL Queries:

Answer:



Question 4: 

SQL Queries:

Answer:



Question 5: 

SQL Queries:

Answer:
