Question 1:  What are the top 5 most popular products (by site vists) among visitors who eventually made a purchase?

SQL Queries:

SELECT v2ProductName AS product_name, COUNT(*) AS views_by_purchasers <br/> 
FROM all_sessions <br/> 
WHERE transactions > 0 AND v2ProductName IS NOT NULL <br/> 
GROUP BY v2ProductName <br/> 
ORDER BY views_by_purchasers DESC; <br/> 

Answer: This question reveals which products are highly viewed by visitors who eventually made a purchase, which may indicate popular products that attract paying customers.



Question 2: What is the average transaction revenue by referring site (channel) for each product category?

SQL Queries: 

SELECT a.channelGrouping AS referring_site, a.v2ProductCategory AS product_category, AVG(a.totalTransactionRevenue) AS avg_transaction_revenue <br/> 
FROM all_sessions AS as <br/> 
JOIN analytics AS an ON a.fullVisitorId = an.fullvisitorId AND a.visitId = an.visitId <br/> 
WHERE as.totalTransactionRevenue IS NOT NULL <br/> 
GROUP BY as.channelGrouping, as.v2ProductCategory <br/> 
ORDER BY avg_transaction_revenue DESC;

Answer: By joining all_sessions with analytics, this query provides insights into how different channels contribute to revenue across product categories, useful for refining marketing strategies.


Question 3: How does the bounce rate compare across different traffic sources?

SQL Queries: 

SELECT channelGrouping AS traffic_source, COUNT(CASE WHEN pageviews = 1 THEN 1 END) * 100.0 / COUNT(*) AS bounce_rate <br/>
FROM all_sessions <br/>
GROUP BY channelGrouping <br/>
ORDER BY bounce_rate DESC; 

Answer: The bounce rate (visitors who only view one page and leave) can indicate which sources provide the most engaged traffic.



Question 4: What is the average session duration for each unique product viewed by visitors who also made a purchase?

SQL Queries:

SELECT a.fullvisitorId, a.v2ProductName AS product_name, AVG(an.timeonsite) AS avg_session_duration <br/> 
FROM all_sessions AS a <br/> 
JOIN analytics AS an ON a.fullVisitorId = an.fullvisitorId AND a.visitId = an.visitId <br/> 
WHERE a.transactions > 0 AND a.v2ProductName IS NOT NULL <br/> 
GROUP BY a.v2ProductName, a.fullvisitorId <br/> 
ORDER BY avg_session_duration DESC;
    
Answer: By joining all_sessions with analytics, we can analyze session engagement for each product based on users who completed a purchase. This helps understand if certain products lead to longer engagement.


Question 5: How many returning visitors (non-first-time) made a purchase?

SQL Queries: 

SELECT COUNT(DISTINCT a.fullVisitorId) AS returning_purchasers <br/> 
FROM all_sessions AS a <br/> 
JOIN analytics AS an ON a.fullVisitorId = an.fullvisitorId AND a.visitId = an.visitId <br/> 
WHERE an.visitNumber > 1 AND a.transactions > 0;

Answer: This query helps to understand the impact of returning visitors on sales, which can guide retention strategies.
