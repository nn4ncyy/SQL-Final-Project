# Final-Project-Transforming-and-Analyzing-Data-with-SQL

## Project/Goals
This project aims towards encapsulating the data analytics process from importing data, to cleaning it, to analysing it in PostgreSQL. The major goals involve:
1. Creating tables and importing data with the correct data types
2. Cleaning data
3. Exploring data through SQL queries
4. Making inferences about data

All while keeping quality assurance in mind. The dataset itself explores the behaviors of sales data. We can hopefully use this data in the future to make inferences on how we can address the market going forward.

## Process
### Step 1. Loading Data
This step entails creating tables and assigning data types. I took a trial and error approach where I attempted to import the csv files, and tweaked the data types until the import worked. The data type "text" proved to be especially useful during this step to act as a filler word.

### Step 2. Answering Questions
I found it most fitting to get to know the data first. The best way I found doing this was by doing what we had been doing for weeks: answering questions by writing queries. The general approach taken was to start with simple queries and then expand.

### Step 3. Writing Queries
In step three, I took the data exploration one step further by writing my 5 questions and answering them. I centered my questions around areas the questions in step two didn't address and explored variables that intrigued me.

### Step 4. Cleaning Data
Now that I had oriented myself around the data, it was finally time to clean it. This meant addressing the presence of duplicates and nulls. I also cleaned up some variables and reformatted some units. 

### Step 6. ERD Creation
The ERD step was pretty simple, now was the time to assign primary and foreign keys. The cleaning step helped to allow this to be a pretty straight forward process, by removing duplicate values.

### Step 6. QA Process
Finally, the last step was reflecting on how the previous steps lead to a strong quality assurance. This was done exploring the tables relationships with each other, and trying to make sense of the data. Upon reflection, I realized that the cleaning step proved to be the most tricky for me. 

## Results
Analyzing this data provided key insights into market profitability. First, the United States emerged as the top contributor to total revenue, indicating it as a prime target for future initiatives. Additionally, home goods were identified as the most profitable product category, with a strong preference in the U.S. market for both home goods and security products. These findings will help guide our future market focus and advertising strategies.

Beyond marketing insights, the data also provided valuable information on user experience. By analyzing bounce rates, we identified that users referred by other sites are the most likely to make purchases, often buying immediately upon arrival. This insight allows us to study how referral customers are effectively engaged and apply similar strategies to optimize experiences for other user groups.
These two insights will be useful in the future to guide us on where to focus the market and how to advertise these products.

## Challenges 
I had three major challenges for this project. One being the importing stage. I found that it took a lot of time to get data types correct in order for an import to be successful. Especially given that a lot of the data was NULL values, this made determining data types extra challenging.

It was quite difficult for me to understand when it was or was not appropriate to remove certain data. There were a lot of NULL values, but not enough to where it seemed appropriate to delete the columns. I did by best to coalesce what I could, but even then, im still wondering if coalescing is the appropriate step, or if the NULLs represent something needing further investigation. 

In the same vein, making inferences on the relationship between two tables was a challenge. For example, I came to the conclusion that the sales_by_sku tables had SKU’s that were not in the products table, so I added the SKUs to the products table. I’m unsure if this was the correct conclusion, because maybe the reality is, that product is just no longer available. Overall, the QA process made me realize the importance of Data cleaning, and any future extensions of this project would involve diving deeper on what data cleaning entails, and how to be better at it. 

## Future Goals
Overall, the QA process made me realize the importance of Data cleaning, and any future extensions of this project would involve diving deeper on what data cleaning entails, and how to be better at it. I'd like to turn these results into more tangible forms of analysis such as visualizations.
