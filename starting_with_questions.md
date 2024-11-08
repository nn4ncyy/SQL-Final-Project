Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries: 
```
SELECT country, city, SUM(totaltransactionrevenue) AS total_revenue 
FROM all_sessions
WHERE totaltransactionrevenue IS NOT NULL
GROUP BY country, city 
ORDER BY total_revenue DESC ;
```

Answer: The United States, and more specifically, San Francisco had the highest level of transaction revenues on the site.

<img width="487" alt="Screenshot 2024-11-07 at 12 47 35 AM" src="https://github.com/user-attachments/assets/ddb886fe-b42d-4cd6-823a-41f1f07bd3ff">



**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:
```
SELECT country, city, AVG(productQuantity) AS avg_products_ordered 
FROM all_sessions 
WHERE productQuantity IS NOT NULL 
GROUP BY country, city 
ORDER BY avg_products_ordered DESC;
```

Answer:

<img width="543" alt="Screenshot 2024-11-07 at 12 48 20 AM" src="https://github.com/user-attachments/assets/0c5f7483-fa09-4d44-add9-0a6f22f27773">





**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:
```
SELECT all_sessions.country, all_sessions.city, all_sessions.v2ProductCategory AS product_category, SUM(all_sessions.productQuantity) AS total_products_ordered, products.orderedquantity
FROM all_sessions
LEFT JOIN products ON all_sessions.productSKU = products.SKU 
WHERE country != '(not set)' AND productQuantity IS NOT NULL AND orderedquantity IS NOT NULL 
GROUP BY all_sessions.country, all_sessions.city, all_sessions.v2ProductCategory, products.orderedquantity 
ORDER BY all_sessions.country, all_sessions.city, total_products_ordered DESC; 
```

Answer: The United states appears the most for demographics who ordered products, this suggests a broad market appeal towards Americans. Futhermore, Homegoods is a universally popular product category to order from.



<img width="927" alt="Screenshot 2024-11-07 at 12 48 47 AM" src="https://github.com/user-attachments/assets/685a1502-3d6c-4f91-b5d8-845b826a266b"><img width="929" alt="Screenshot 2024-11-07 at 12 48 58 AM" src="https://github.com/user-attachments/assets/a030a8f5-decb-4d16-99a0-28f1a5fb5f87">






**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:
```
WITH ProductSales AS (
    SELECT country, city, productSKU,v2productname, SUM(productQuantity) AS total_quantity_sold
    FROM all_sessions
    WHERE productQuantity IS NOT NULL AND city != 'not available in demo dataset'
    GROUP BY country, city, productSKU, v2productname
) 

SELECT country,city,productSKU, v2productname, total_quantity_sold
FROM ProductSales
WHERE (country, city, total_quantity_sold) IN (
    SELECT country, city, MAX(total_quantity_sold)
    FROM ProductSales
    GROUP BY country, city
)
ORDER BY country, city, total_quantity_sold DESC;
```
Answer: The top-selling product is "Waze Dress Socks" from Spain, with 10 units being sold. Though this is the highest purchased product, Spain only appears in the list once. Compared to the US' 20 times. 

<img width="982" alt="Screenshot 2024-11-07 at 12 55 48 AM" src="https://github.com/user-attachments/assets/eea0ab93-7967-4967-8510-e7b1e44adc8e">





**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:
```
SELECT country, city, SUM(totalTransactionRevenue) AS total_revenue, AVG(totalTransactionRevenue) AS avg_revenue_per_transaction, COUNT(DISTINCT transactionId) AS transaction_count 
FROM all_sessions 
WHERE totalTransactionRevenue IS NOT NULL 
GROUP BY country, city 
ORDER BY total_revenue DESC; 
```

Answer: The United States generated the highest revenue in total, claiming the top five slots. This indicates that it is a lucrative market to tap into. It claims 15/20 spots, which is quite significant suggesting that it generates a substantial amount of revenue.
<img width="798" alt="Screenshot 2024-11-07 at 12 50 25 AM" src="https://github.com/user-attachments/assets/e2008077-0f09-4f6f-be15-8d5884128cda">







