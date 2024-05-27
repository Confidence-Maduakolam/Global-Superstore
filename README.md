# Global-Superstore

### About Project
Global Superstore is a global online retailer based in New York, boasting a broad product catalog and aiming to be a one-stop-shop for its customers.
Global The superstore's clientele, hailing from 147 different countries, can browse through an endless offering with more than 10,000 products. This large selection comprises three main categories: office supplies (e.g., staples), furniture (e.g., chairs), and technology (e.g., smartphones). You are contracted as a Data Analyst to help Global Superstore analyze and draw out meaningful insight from the Superstore dataset which would aid management in making informed decisions to improve performance and profitability. Using skills learnt from the data Bootcamp, apply your knowledge of:

● Power Query

● Data Modeling

● Data Cleaning

● Data Visualization

● Knowledge of generating actionable insights Etc. To analyze the dataset and provide answers to the questions listed below.

### EXPLANATORY DATA ANALYSIS 
 1.a) What are the three countries that generated the highest total profit for Global Superstore in 2014?
 
   b) For each of these three countries, find the three products with the highest total profit. Specifically, what are the products' names and the total profit for each product?
   
 2.) Identify the 3 subcategories with the highest average shipping cost in the United States.
 
 3.a) Assess Nigeria's profitability (i.e., total profit) for 2014. How does it compare to other African countries?
 
   b) What factors might be responsible for Nigeria's poor performance? You might want to investigate shipping costs and the average discount as potential root causes.
   
 4.a) Identify the product subcategory that is the least profitable in Southeast Asia. Note: For this question, assume that Southeast Asia comprises Cambodia, Indonesia, Malaysia, 
      Myanmar (Burma), the Philippines, Singapore, Thailand, and Vietnam.
 
   b) Is there a specific country i n Southeast Asia where Global Superstore should stop offering the subcategory identified in 4a?
   
 5.a)Which city is the least profitable (in terms of average profit) in the United States? For this analysis, discard the cities with less than 10 Orders.
 
   b) Why is this city's average profit so low?
   
 6.)  Which product subcategory has the highest average profit in Australia?
 
 7.)  Who are the most valuable customers and what do they purchase?

Find the link to the Global superstore dataset in below: [Download here](https://docs.google.com/spreadsheets/d/1nxESpFzWjlGDMGDVLH69xmDzIl9l6OEq/edit#gid=633280281)
##### Tools: 
- Power BI 
- SQL
  
### DATA CLEANING AND TRANSFORMATION:
This dataset contain 3 Tables which are Order, People, and Return and each of this table has their own fields to make a table, I upload my data into the power query editor to clean, prepare, and transform my data to make it neat and structured. The tools used for cleaning are SQL and Power BI.
I went through my data to check for any null values which was corrected in my power quarry and SQL as seen below.

### VISUALISATOIN AND ANALYSIS
In this Analysis I used SQL and Power BI tools  for Question 1,2,4 and 5 while in Question 3,6 and 7 I used just the Power BI tool. Meanwhile all visualization was made with the Power BI Tool which is going to be revealed later in this report.
Meanwhile before my visualization I import my solved SQL queries to Power BI by clicking on get data on my Power BI home page, then I choose SQL server which took me to another step, which is to input my server name, database name and at the advance box where I Input my SQL Statement that was used for my visuals as shown below.
Note: This above process was repeated for all the SQL statement queries used for my visuals.

### INSIGHTS

Question 1a) The three countries that generated the highest total profit for Global Superstore in 2014:

•United States: $94K

•India: $49K

•China: $47K

1b) For each of these three countries, the three products with the highest total profit are:

•United States:

- Canon imageCLASS 2200 Advanced Copier: $15.7K

- Hewlett Packard LaserJet 3310 Copier: $3.6K

- Sauder Classic Bookcase: $2.4K

•India:

- Sauder Classic Bookcase: $2.4K

- GBC DocuBind TL300 Electric Binding System: $1.9K

- Cisco Smart Phone: $1.5K

•China:

- Sauder Classic Bookcase: $2.4K

- Hamilton Beach Refrigerator: $1.8K

- Bush Classic Bookcase: $1.7K
  

Question 2) The 3 subcategories with the highest average shipping cost in the United States:

•Copiers: $165.29

•Machines: $132.25

•Tables: $69.90


Question 3a) Nigeria's profitability assessment for 2014:

•Total Profit: Nigeria's total profit is significantly lower compared to other African countries, as indicated by the smaller size of the bubble representing Nigeria on the map.


b) Factors responsible for Nigeria's poor performance:

•Average Discount: 0.7

•Average Shipping Cost: 6.8 These factors indicate higher operational costs and possibly less effective discount strategies, contributing to lower profitability.


Question 4a) The product subcategory that is the least profitable in Southeast Asia:

•Tables: -18,618.31 approximately -$19K is the least profitable subcategory in Southeast Asia.


b) Recommendation on whether to stop offering the subcategory in a specific country in Southeast Asia:
The performance of the subcategory "Tables" in Indonesia with the Profit Of $-10680.28 within Southeast Asia which is a big loss so I recommendation to stop offering this subcategory in this country.


### SQL ANALYSIS
 - Question 1 a)What are the three countries that generated the highest total profit for Global Superstore in 2014?
``` sql
SELECT *
FROM dbo.Orders;
SELECT TOP 3 country, SUM(profit) AS total_profit
FROM orders
WHERE YEAR(order_date) = 2014
GROUP BY country
ORDER BY total_profit DES
```

 - 1.b) For each of these three countries, find the three products with the highest total profit. Specifically,
 - what are the products names and the total profit for each product?
``` sql
WITH top_countries AS (
SELECT TOP 3 country
FROM orders
WHERE YEAR(order_date) = 2014
GROUP BY country
ORDER BY SUM(profit) DESC
),
product_profits AS (
SELECT
o.country,
o.product_name,
SUM(o.profit) AS total_profit,
ROW_NUMBER() OVER (PARTITION BY o.country ORDER BY SUM(o.profit) DESC) AS rank
FROM orders o
WHERE YEAR(o.order_date) = 2014
AND o.country IN (SELECT country FROM top_countries)
GROUP BY o.country, o.product_name
)
SELECT country, product_name, total_profit
FROM product_profits
WHERE rank <= 3
ORDER BY country, rank;
```

- Question 2.) Identify the 3 subcategories with the highest average shipping cost in the United States.

```sql
SELECT TOP 3 sub_category, AVG(shipping_cost) AS avg_shipping_cost
FROM orders
WHERE country = 'United States'
GROUP BY sub_category
ORDER BY avg_shipping_cost DESC;
```

- Question 4a) Identify the product subcategory that is the least profitable in Southeast Asia.

```sql
SELECT TOP 1 sub_category, SUM(profit) AS total_profit
FROM orders
WHERE Region IN ('Southeast Asia')
GROUP BY sub_category
ORDER BY total_profit ASC;
```

- b) Is there a specific country in Southeast Asia where Global Superstore should stop offering the least profitable subcategory?

```sql
WITH least_profitable AS (
SELECT TOP 1 sub_category
FROM orders
WHERE Region IN ('Southeast Asia')
GROUP BY sub_category
ORDER BY SUM(profit) ASC
)
SELECT TOP 1 country, SUM(profit) AS total_profit
FROM orders
WHERE sub_category = (SELECT sub_category FROM least_profitable)
AND Region IN ('Southeast Asia')
GROUP BY country
ORDER BY total_profit ASC;
```

Question 6) Which product subcategory has the highest average profit in Australia?

•The product subcategory with the highest average profit in Australia is "Appliances" with an average profit of 139.

Question 7) Who are the most valuable customers and what do they purchase?

The most valuable customers as shown in the image are:

- Tamara Chand, who has generated a total profit of 8.4K.
  
- Raymond Buch, who has generated a total profit of 6.7K.
The specific products they purchase are not detailed in the image for these customers.

### SQL ANALYSIS
 - Question 5a) Which city is the least profitable (in terms of average profit) in the United States? For this analysis, discard the cities with less than 10 orders.

 ```sql
WITH city_order_count AS (
SELECT city, COUNT(*) AS order_count
FROM orders
WHERE country = 'United States'
GROUP BY city
HAVING COUNT(*) >= 10
)
SELECT TOP 1 o.city, AVG(o.profit) AS avg_profit
FROM orders o
JOIN city_order_count coc ON o.city = coc.city
WHERE o.country = 'United States'
GROUP BY o.city
ORDER BY avg_profit ASC;
```

- b) Why is this citys average profit so low? Investigate shipping costs and average discounts.

```sql
SELECT city, AVG(shipping_cost) AS avg_shipping_cost, AVG(discount) AS avg_discount
FROM orders
WHERE city = (
SELECT TOP 1 o.city
FROM orders o
JOIN (
SELECT city, COUNT(*) AS order_count
FROM orders
WHERE country = 'United States'
GROUP BY city
HAVING COUNT(*) >= 10
) coc ON o.city = coc.city
WHERE o.country = 'United States'
GROUP BY o.city
ORDER BY AVG(o.profit) ASC
)
GROUP BY city;
```
### RECOMMENDATIONS

1.Continuously monitor and analyze sales data to make informed decisions about product offerings, pricing, and marketing strategies.
2. Engage with customers through surveys and feedback mechanisms to understand their needs and preferences better.
3.Conduct regular competitive analysis to stay ahead of market trends and adjust strategies accordingly.
4.Increase the inventory and variety of appliances offered in Australia
5.Provide excellent customer service and support to ensure their satisfaction.
By implementing these recommendations, Global Superstore can optimize its operations, reduce costs, and improve profitability across different regions and product categories.
