# E-Commerce-Company-Case-Study
## Overview
This project simulates the role of a data analyst in a thriving e-commerce environment. It focuses on utilizing comprehensive databases to derive valuable insights that inform strategic decisions across various departments, including marketing, supply chain, and customer service. The analysis aims to optimize operations, enhance customer satisfaction, and improve overall sales performance. Through this case study, practical SQL skills are applied to solve real-world business challenges, demonstrating the ability to extract, manipulate, and analyze data effectively for actionable outcomes.
## Objective
The objective of this project is to utilize SQL to explore and analyze e-commerce data, enabling the identification of critical insights that drive strategic improvements across various business functions. The focus is on solving practical problems, such as optimizing sales performance, streamlining operations, and improving customer experiences, while demonstrating the effective use of data analytics to support business goals.
## Dataset
Dataset is provided by Coding Ninjas
- **Dataset Link:** [E Commerce](https://1drv.ms/x/c/c1347d677a0a7ff8/EWjjpNYl0S9Lnu1SmmFpwhEB727Uz7J0CEfOT5F8K8acXA?e=MwFtGy)
## Schema
![schema](https://github.com/sibin-n/E-Commerce-Company-Case-Study/blob/main/schemaas.png?raw=true)

## Analysis
Top 3 cities with the highest number of customers to determine key markets for targeted marketing and logistic optimization.
```sql
select location, count(name) number_of_customers from customers
group by location
order by number_of_customers desc
limit 3;
```
Determine the distribution of customers by the number of orders placed. This insight will help in segmenting customers into one-time buyers, occasional shoppers, and regular customers for tailored marketing strategies.
```sql
SELECT r.order_count NumberOfOrders, COUNT(r.customer_id) CustomerCount FROM
(
    SELECT customer_id, COUNT(*) order_count FROM orders
    GROUP BY customer_id
)r
GROUP BY r.order_count
ORDER BY r.order_count;
```
Products where the average purchase quantity per order is 2 but with a high total revenue, suggesting premium product trends.
```sql
WITH Problem AS(
SELECT od.product_id, AVG(od.quantity) AvgQuantity, SUM(od.quantity*p.price) TotalRevenue
FROM orderdetails od
JOIN products p
ON od.product_id=p.product_id
GROUP BY od.product_id
HAVING AvgQuantity=2
)

SELECT product_id, AvgQuantity, TotalRevenue FROM Problem
ORDER BY TotalRevenue DESC;
```
Unique number of customers purchasing from each Category.
```sql
SELECT p.category, COUNT(DISTINCT o.customer_id) unique_customers
FROM products p
LEFT JOIN Orderdetails od
ON p.product_id=od.product_id
LEFT JOIN orders o
ON od.order_id=o.order_id
GROUP BY p.category
ORDER BY unique_customers DESC;
```
Month-on-month percentage change in total sales to identify growth trends.
```sql
WITH total_per_month AS (
SELECT DATE_FORMAT(order_date,"%Y-%m") Month, SUM(total_amount) TotalSales
FROM orders
GROUP BY Month
ORDER BY Month)

SELECT *, 
ROUND(((Totalsales - LAG(TotalSales) OVER (ORDER BY Month))/ LAG(TotalSales) OVER (ORDER BY Month))*100,2) PercentChange
FROM total_per_month;
```
Average order value changes Month-On-Month. Insights can guide pricing and promotional strategies to enhance order value.
```sql
WITH total_per_month AS (
SELECT DATE_FORMAT(order_date,"%Y-%m") Month, AVG(total_amount) AvgOrderValue
FROM orders
GROUP BY Month
ORDER BY Month)

SELECT *, 
ROUND(AvgOrderValue - LAG(AvgOrderValue) OVER (ORDER BY Month),2) ChangeInValue
FROM total_per_month
ORDER BY ChangeInValue DESC;
```
Products with the fastest turnover rates, suggesting high demand and the need for frequent restocking.
```sql
SELECT product_id, COUNT(order_id) SalesFrequency
FROM orderdetails
GROUP BY product_id
ORDER BY SalesFrequency DESC
LIMIT 5;
```
Products purchased by less than 40% of the customer base, indicating potential mismatches between inventory and customer interest.
```sql
SELECT p.product_id, p.name, COUNT(DISTINCT o.Customer_id) UniqueCustomerCount
FROM products p 
JOIN orderdetails od  
ON p.product_id= od.product_id
JOIN orders o 
ON od.order_id=o.order_id
GROUP BY p.product_id, p.name
HAVING (UniqueCustomerCount*100/ (SELECT COUNT(*) FROM Customers)) <40;
```
Month-On-Month growth rate in the customer base to understand the effectiveness of marketing campaigns and market expansion efforts.
```sql
SELECT DATE_FORMAT(r.date,'%Y-%m') FirstPurchaseMonth, COUNT(r.customer_id) TotalNewCustomers
FROM (
    SELECT customer_id,
    MIN(order_date) date FROM orders
    GROUP BY customer_id
)r
GROUP BY FirstPurchaseMonth
ORDER BY FirstPurchaseMonth;
```
Months with the highest sales volume, aiding in planning for stock levels, marketing efforts, and staffing in anticipation of peak demand periods.
```sql
SELECT DATE_FORMAT(order_date,'%Y-%m') Month, SUM(total_amount) TotalSales
FROM Orders
GROUP BY Month
ORDER BY TotalSales DESC
LIMIT 3;
```

## Findings And Conclusion
- Chennai, Delhi and Jaipur must be focused as a part of marketing strategies.
- In customer engagement, as the number of orders increases the customer count also decreases. So basically Occasional shoppers are more.
- Among products with an average purchase quantity of two, Product1 the highest total revenue.
- Electronic category needs more focus as it is in high demand among the customers.
- During Feb 2024 the sales experience the largest decline.
- As per Sales Trend Analysis, Sales fluctuated with no clear trends.
- The highest change in the average order value is on Dec and lowest on Feb.
- Product7 has the highest turnover rates with 78 purchase and needs to be restocked frequently.
- Product like Smartphone 6 & Wireless Earbuds customer base is low which ensure poor visibility on the platform. So implementing targeted marketing campaigns to raise awareness and interest.
- Month-On-Month Trend of new customer is decreasing. This downward trend implies the marketing campaign are not much effective.
- During September and December the sales revenue is high so it require major restocking of product and increased staffs on these months.
