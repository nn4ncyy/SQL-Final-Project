Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries: 

SELECT country, city, SUM(totaltransactionrevenue) AS total_revenue <br/> 
FROM all_sessions <br/> 
WHERE totaltransactionrevenue IS NOT NULL <br/> 
GROUP BY country, city <br/> 
ORDER BY total_revenue DESC ;


Answer:




**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:

SELECT country, city, AVG(productQuantity) AS avg_products_ordered <br/>
FROM all_sessions <br/>
WHERE productQuantity IS NOT NULL <br/>
GROUP BY country, city <br/>
ORDER BY avg_products_ordered DESC;



Answer:





**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:

SELECT all_sessions.country, all_sessions.city, all_sessions.v2ProductCategory AS product_category, SUM(all_sessions.productQuantity) AS total_products_ordered, products.orderedquantity <br/>
FROM all_sessions <br/>
LEFT JOIN products ON all_sessions.productSKU = products.SKU <br/>
WHERE country != '(not set)' AND productQuantity IS NOT NULL AND orderedquantity IS NOT NULL <br/>
GROUP BY all_sessions.country, all_sessions.city, all_sessions.v2ProductCategory, products.orderedquantity <br/>
ORDER BY all_sessions.country, all_sessions.city, total_products_ordered DESC; 


Answer:





**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:

WITH ProductSales AS ( <br/>
&nbsp;&nbsp;&nbsp;&nbsp;SELECT country, city, productSKU, SUM(productQuantity) AS total_quantity_sold <br/>
&nbsp;&nbsp;&nbsp;&nbsp;FROM all_sessions <br/>
&nbsp;&nbsp;&nbsp;&nbsp;WHERE productQuantity IS NOT NULL AND city != 'not available in demo dataset' <br/>
&nbsp;&nbsp;&nbsp;&nbsp;GROUP BY country, city, productSKU <br/>
) <br/>

SELECT country,city,productSKU, total_quantity_sold<br/>
FROM ProductSales<br/>
WHERE (country, city, total_quantity_sold) IN (<br/>
&nbsp;&nbsp;&nbsp;&nbsp;SELECT country, city, MAX(total_quantity_sold) <br/>
&nbsp;&nbsp;&nbsp;&nbsp;FROM ProductSales<br/>
&nbsp;&nbsp;&nbsp;&nbsp;GROUP BY country, city<br/>
&nbsp;&nbsp;&nbsp;&nbsp;)<br/>
ORDER BY country, city, total_quantity_sold DESC;

Answer:





**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

SELECT country, city, SUM(totalTransactionRevenue) AS total_revenue, AVG(totalTransactionRevenue) AS avg_revenue_per_transaction, COUNT(DISTINCT transactionId) AS transaction_count <br/>
FROM all_sessions <br/>
WHERE totalTransactionRevenue IS NOT NULL <br/>
GROUP BY country, city <br/>
ORDER BY total_revenue DESC; <br/>


Answer:







