Question 1:  What are the top 5 most popular products (by views) among visitors who eventually made a purchase?

SQL Queries:

SELECT v2ProductName AS product_name, COUNT(*) AS views_by_purchasers <br/> 
FROM all_sessions <br/> 
WHERE transactions > 0 AND v2ProductName IS NOT NULL <br/> 
GROUP BY v2ProductName <br/> 
ORDER BY views_by_purchasers DESC; <br/> 

Answer: This question reveals which products are highly viewed by converting visitors, which may indicate popular products that attract paying customers.



Question 2: What are the top referring sites by revenue?

SQL Queries:

SELECT channelGrouping AS referring_site, SUM(totalTransactionRevenue) AS total_revenue <br/> 
FROM all_sessions <br/> 
WHERE totalTransactionRevenue IS NOT NULL <br/> 
GROUP BY channelGrouping <br/> 
ORDER BY total_revenue DESC; <br/> 

Answer: This helps identify which referral sources drive the most revenue, guiding partnership or affiliate marketing strategies.


Question 3: How does the bounce rate compare across different traffic sources?

SQL Queries: 

SELECT channelGrouping AS traffic_source, COUNT(CASE WHEN pageviews = 1 THEN 1 END) * 100.0 / COUNT(*) AS bounce_rate <br/>
FROM all_sessions <br/>
GROUP BY channelGrouping <br/>
ORDER BY bounce_rate DESC; 

Answer: The bounce rate (visitors who only view one page and leave) can indicate which sources provide the most engaged traffic.




Question 4: What is the average session duration for visitors from each country?

SQL Queries:

SELECT country, AVG(timeOnSite) AS avg_session_duration <br/>
FROM all_sessions <br/>
WHERE timeOnSite IS NOT NULL <br/>
GROUP BY country <br/>
ORDER BY avg_session_duration DESC; <br/>

Answer: This question can help you understand user engagement levels across different locations, which might indicate regions with higher interest in your products.




Question 5: What are the top 5 most-viewed products on the site?

SQL Queries:

Answer:
