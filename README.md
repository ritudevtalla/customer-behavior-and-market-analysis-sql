# customer-behavior-and-market-analysis-sql
SQL-based business analysis project focused on customer behavior, market segmentation, order frequency, and actionable business insights.
Project Overview
This project analyzes customer behavior, product performance, sales trends, and inventory management for an e-commerce business using SQL. The objective is to generate actionable insights that support marketing, sales, customer retention, and inventory planning decisions.
Dataset
The analysis is based on the following tables:
Customers
•	customer_id
•	name
•	location
Products
•	product_id
•	name
•	category
•	price
Orders
•	order_id
•	order_date
•	customer_id
•	total_amount
OrderDetails
•	order_id
•	product_id
•	quantity
•	price_per_unit
________________________________________


Business Problems Solved
1. Market Segment Analysis
Objective: Identify the top 3 cities with the highest number of customers.
SELECT location, COUNT(*) AS number_of_customers
FROM Customers
GROUP BY location
ORDER BY COUNT(*) DESC
LIMIT 3;
Insight: Delhi, Chennai, and Jaipur emerged as the top customer markets and should be prioritized for marketing campaigns and logistics optimization.
________________________________________
2. Engagement Depth Analysis
Objective: Determine how many customers fall into each order frequency category.
WITH cust_orders AS (
    SELECT customer_id,
           COUNT(order_id) AS NumberOfOrders
    FROM Orders
    GROUP BY customer_id
)

SELECT NumberOfOrders,
       COUNT(customer_id) AS CustomerCount
FROM cust_orders
GROUP BY NumberOfOrders
ORDER BY NumberOfOrders;
Insight: The company experiences the highest number of Occasional Shoppers, indicating an opportunity to improve customer retention and repeat purchases.
________________________________________
3. Purchase High-Value Products
Objective: Identify products with an average purchase quantity of 2 but generating high revenue.
SELECT product_id AS Product_Id,
       AVG(quantity) AS AvgQuantity,
       SUM(quantity * price_per_unit) AS TotalRevenue
FROM OrderDetails
GROUP BY product_id
HAVING AVG(quantity) = 2
ORDER BY TotalRevenue DESC;
Insight: Product 1 indicate premium purchasing behavior where customers buy fewer units but contribute significant revenue.
________________________________________
4. Category-Wise Customer Reach
Objective: Determine customer reach across product categories.
SELECT p.category AS category,
       COUNT(DISTINCT o.customer_id) AS unique_customers
FROM Products p
JOIN OrderDetails od
    ON p.product_id = od.product_id
JOIN Orders o
    ON od.order_id = o.order_id
GROUP BY p.category
ORDER BY unique_customers DESC;
Insight: Electronics category with higher customer reach have broader market appeal and greater cross-selling opportunities.
________________________________________
5. Sales Trend Analysis
Objective: Calculate month-on-month sales growth.
WITH mom AS (
    SELECT DATE_FORMAT(order_date, '%Y-%m') AS Month,
           SUM(total_amount) AS TotalSales
    FROM Orders
    GROUP BY DATE_FORMAT(order_date, '%Y-%m')
)

SELECT Month,
       TotalSales,
       ROUND(
           (TotalSales - LAG(TotalSales) OVER (ORDER BY Month)) * 100
           / LAG(TotalSales) OVER (ORDER BY Month),
           2
       ) AS PercentChange
FROM mom;
Insight: Month-on-month growth analysis helps monitor business performance and identify seasonal demand patterns. Feb 2024 is the month in which sales experience the largest decline.
________________________________________
6. Average Order Value Fluctuation
Objective: Analyze changes in average order value over time.
WITH mom AS (
    SELECT DATE_FORMAT(order_date, '%Y-%m') AS Month,
           ROUND(AVG(total_amount), 2) AS AvgOrderValue
    FROM Orders
    GROUP BY Month
)

SELECT Month,
       AvgOrderValue,
       ROUND(
           AvgOrderValue - LAG(AvgOrderValue) OVER (ORDER BY Month),
           2
       ) AS ChangeInValue
FROM mom
ORDER BY ChangeInValue DESC;
Insight: Tracking average order value helps evaluate pricing effectiveness and promotional impact. December has the highest change in the average order value.
________________________________________
7. Inventory Refresh Rate
Objective: Identify products with the fastest turnover rates.
SELECT product_id,
       COUNT(quantity) AS SalesFrequency
FROM OrderDetails
GROUP BY product_id
ORDER BY SalesFrequency DESC
LIMIT 5;
Insight: Fast-moving products require frequent replenishment to avoid stock-outs.
________________________________________
8. Low Engagement Products
Objective: Identify products purchased by less than 40% of customers.
WITH total_customers AS (
    SELECT COUNT(DISTINCT customer_id) AS total_cnt
    FROM Customers
)

SELECT p.product_id AS Product_id,
       p.Name AS Name,
       COUNT(DISTINCT o.customer_id) AS UniqueCustomerCount
FROM Products p
JOIN OrderDetails od
    ON p.product_id = od.product_id
JOIN Orders o
    ON od.order_id = o.order_id
GROUP BY p.product_id, p.Name
HAVING COUNT(DISTINCT o.customer_id) <
       0.4 * (SELECT total_cnt FROM total_customers)
ORDER BY UniqueCustomerCount;
Insight: Low-engagement products may require repositioning, promotion, or inventory rationalization. Due to poor visibility on the platform products have purchase rates below 40% of the total customer base.
________________________________________
9. Customer Acquisition Trends
Objective: Analyze month-on-month customer acquisition.
WITH first_purchase AS (
    SELECT customer_id,
           MIN(order_date) AS first_order_date
    FROM Orders
    GROUP BY customer_id
),

monthly_new_customers AS (
    SELECT DATE_FORMAT(first_order_date, '%Y-%m') AS FirstPurchaseMonth,
           COUNT(customer_id) AS TotalNewCustomers
    FROM first_purchase
    GROUP BY DATE_FORMAT(first_order_date, '%Y-%m')
)

SELECT FirstPurchaseMonth,
       TotalNewCustomers
FROM monthly_new_customers
ORDER BY FirstPurchaseMonth ASC;
Insight: New customer trends help evaluate marketing effectiveness and market expansion efforts. There is a downward trend which shows marketing campaign are not much effective.
________________________________________
10. Peak Sales Period Identification
Objective: Identify months with the highest sales volume.
SELECT DATE_FORMAT(order_date, '%Y-%m') AS Month,
       SUM(total_amount) AS TotalSales
FROM Orders
GROUP BY Month
ORDER BY TotalSales DESC
LIMIT 3;
Insight: Peak sales months assist in inventory planning, staffing decisions, and campaign scheduling. September and December will require major restocking of product and increased staffs.
________________________________________
SQL Skills Demonstrated
•	Joins
•	Aggregate Functions
•	Common Table Expressions (CTEs)
•	Window Functions (LAG)
•	Group By & Having
•	Date Functions
•	Customer Segmentation
•	Revenue Analysis
•	Trend Analysis
Business Impact
The analysis provides insights into:
•	Customer segmentation and engagement
•	Revenue growth trends
•	Product performance
•	Inventory optimization
•	Customer acquisition effectiveness
•	Seasonal demand forecasting
Tools Used
•	SQL
•	MySQL
•	GitHub
Author
Ritu Devtalla
Aspiring Data Analyst | SQL | Python | Power BI

