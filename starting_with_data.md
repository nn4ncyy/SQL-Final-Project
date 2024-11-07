### Question 1:  What are the top 5 most popular products (by site vists) among visitors who eventually made a purchase?

SQL Queries:

```
SELECT v2ProductName AS product_name, COUNT(*) AS views_by_purchasers 
FROM all_sessions 
WHERE transactions > 0 AND v2ProductName IS NOT NULL
GROUP BY v2ProductName
ORDER BY views_by_purchasers DESC;
```

Answer: This question reveals which products are highly viewed by visitors who eventually made a purchase, which may indicate popular products that attract paying customers.4/5 of the top 5 products are from the brand Nest. All products fit into the security, thermostat, or sunglasses category.

<img width="526" alt="Screenshot 2024-11-07 at 12 28 00 AM" src="https://github.com/user-attachments/assets/287ac1a4-65ec-4093-a986-f3f422686106">




### Question 2: What is the average transaction revenue by referring site (channel) for each product category?

SQL Queries: 
```
SELECT a.channelGrouping AS referring_site, a.v2ProductCategory AS product_category, AVG(a.totalTransactionRevenue) AS avg_transaction_revenue
FROM all_sessions AS a
JOIN analytics AS an ON a.fullVisitorId = an.fullvisitorId AND a.visitId = an.visitId
WHERE a.totalTransactionRevenue IS NOT NULL 
GROUP BY a.channelGrouping, a.v2ProductCategory 
ORDER BY avg_transaction_revenue DESC;
```
Answer: By joining all_sessions with analytics, this query provides insights into how different channels contribute to revenue across product categories, useful for refining marketing strategies. Referrals took up the top 3 most transaction revenue. as for product category, "Housewares", "Nest-USA", "Home/Nest/Nest-USA/" were the top categories. Highest revenue categories are in home goods, and referrals are the most lucrative channel to target.
<img width="644" alt="Screenshot 2024-11-07 at 12 29 49 AM" src="https://github.com/user-attachments/assets/7f254bbc-c74d-4614-8210-1701f725e90c">


### Question 3: How does the bounce rate compare across different traffic sources?

SQL Queries: 
```
SELECT channelGrouping AS traffic_source, COUNT(CASE WHEN pageviews = 1 THEN 1 END) * 100.0 / COUNT(*) AS bounce_rate 
FROM all_sessions 
GROUP BY channelGrouping 
ORDER BY bounce_rate DESC; 
```
Answer: The bounce rate (visitors who only view one page and leave) can indicate which sources provide the most engaged traffic. (Other) is the category with the highest bounce rate, followed by Organic Search. The lowest is Referral. This may suggest users who use Referral channels are more likely to come to the site ready to purchase. 

<img width="347" alt="Screenshot 2024-11-07 at 12 30 18 AM" src="https://github.com/user-attachments/assets/bcfb2bcc-04f0-45ef-a5bc-4661552f934e">



### Question 4: What is the average session duration for each unique product viewed by visitors who also made a purchase?

SQL Queries:
```
SELECT a.fullvisitorId, a.v2ProductName AS product_name, AVG(an.timeonsite) AS avg_session_duration 
FROM all_sessions AS a 
JOIN analytics AS an ON a.fullVisitorId = an.fullvisitorId AND a.visitId = an.visitId 
WHERE a.transactions > 0 AND a.v2ProductName IS NOT NULL  
GROUP BY a.v2ProductName, a.fullvisitorId 
ORDER BY avg_session_duration DESC;
```
Answer: By joining all_sessions with analytics, we can analyze session engagement for each product based on users who completed a purchase. This helps understand if certain products lead to longer engagement. Interestingly, all products that were purchased held a duration > 100.
<img width="697" alt="Screenshot 2024-11-07 at 12 30 48 AM" src="https://github.com/user-attachments/assets/a0cbfe89-da7c-42a0-815f-e2fd3151ca26">


Question 5: How many returning visitors (non-first-time) made a purchase?

SQL Queries: 
```
SELECT COUNT(DISTINCT a.fullVisitorId) AS returning_purchasers 
FROM all_sessions AS a 
JOIN analytics AS an ON a.fullVisitorId = an.fullvisitorId AND a.visitId = an.visitId 
WHERE an.visitNumber > 1 AND a.transactions > 0;
```
Answer: This query helps to understand the impact of returning visitors on sales, which can guide retention strategies. 14 users returned to make a purchase. We may use these user' analytics to explore what metrics about them encourage their propensity to revisit the site.
<img width="199" alt="Screenshot 2024-11-07 at 12 31 10 AM" src="https://github.com/user-attachments/assets/774ee3f6-cb2d-4dfc-9425-727287f809d5">
