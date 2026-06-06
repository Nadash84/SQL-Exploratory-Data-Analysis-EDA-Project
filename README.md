# SQL-Exploratory-Data-Analysis-EDA-Project
SQL Exploratory Data Analysis (EDA) Project – Summary

This project focused on performing Exploratory Data Analysis (EDA) using SQL to gain insights from a sales data warehouse. The objective was to explore, understand, and validate the data before moving on to advanced analysis and reporting.

The project began with database exploration, where the structure of the database, tables, columns, and relationships were examined to understand the available data. This was followed by dimension exploration, analysing key business dimensions such as customers, products, and categories to identify unique values, distributions, and data quality issues.

A significant part of the project involved date exploration, where the range of sales data was investigated by identifying the first and last order dates and calculating the period covered by the dataset. This helped establish the timeframe available for analysis.

The project also included measure exploration, focusing on important numerical metrics such as sales revenue, quantity sold, prices, and order counts. Aggregate functions including SUM, COUNT, AVG, MIN, and MAX were used to understand overall business performance and identify key trends.

Further analysis explored the magnitude of business performance, highlighting top-performing products, customers, and categories based on sales and profitability. Ranking techniques and sorting methods were used to identify the highest and lowest contributors to business results.

The final stage involved ranking analysis, where SQL queries were used to determine the Top N and Bottom N performers across different business dimensions. This provided actionable insights into customer behaviour, product performance, and sales trends.

Through this project, practical experience was gained in:

Writing SQL queries for data exploration and analysis.
Using aggregate and date functions.
Performing data validation and quality checks.
Analysing sales, customer, and product data.
Generating business insights from large datasets.
Applying EDA techniques commonly used in data analytics projects.

Overall, this project demonstrated how SQL can be used to transform raw data into meaningful insights, supporting data-driven decision-making and forming a strong foundation for future reporting and dashboard development.

 -- Explore all objects in the database 

Select * from INFORMATION_SCHEMA.TABLES

-- Explore all columns in the database

select * from INFORMATION_SCHEMA.COLUMNS
where TABLE_NAME = 'dim_customers'

-- Explore all countries our customers come from.
select DISTINCT country FROM dim_customers

-- Explore all categories 'The Major Divisions'
SELECT DISTINCT category, subcategory, product_name FROM dim_products
order by 1,2,3

-- find the date of the first and lasr order 
select 
MIN(order_date) AS first_order_date,
MAX(order_date) AS last_order_date,
DATEDIFF(YEAR, MIN(order_date), MAX(order_date)) AS order_range_years
from fact_sales;

--Find the youngest and the oldest customer 

SELECT 
MIN(birthdate) as oldest_birthdate,
DATEDIFF(year, MIN(birthdate), GETDATE()) AS oldest_age,
MAX(birthdate) as youngest_birthdate,
DATEDIFF(year, MAX(birthdate), GETDATE()) AS youngest_customer
from dim_customers;



-- Find total sales
SELECT SUM(sales_amount) AS total_sales FROM fact_sales

-- Find how many times are sold
SELECT SUM(quantity) AS total_quantity FROM fact_sales

-- Find the average selling price 
SELECT AVG(price) AS average_price FROM fact_sales


-- Find total number of orders 
SELECT COUNT(order_number) AS total_orders FROM fact_sales
SELECT COUNT(DISTINCT order_number) AS total_orders FROM fact_sales

-- Find the total number of products 
SELECT COUNT(product_key) AS total_products FROM dim_products
SELECT COUNT(DISTINCT product_key) AS total_products FROM dim_products

--Find the total number of customers 
SELECT COUNT(customer_key) as total_customers FROM dim_customers


-- Find the total of customers that has placed an order 
SELECT COUNT(DISTINCT customer_key) as total_orders FROM fact_sales


-- Generate a report that shows all key metrics of the business
SELECT 'Total Sales' as measure_name, SUM(sales_amount) AS measure_Value FROM fact_sales
UNION ALL 
SELECT 'Total Quantity' as measure_name, SUM(quantity) AS measure_value FROM fact_sales
UNION ALL
SELECT 'Average Price' as measure_name, AVG(price) AS measure_value FROM fact_sales
UNION ALL 
SELECT 'Total Orders' as measure_name, COUNT(DISTINCT order_number) AS measure_value FROM fact_sales
UNION ALL 
SELECT 'Total Products' as measure_name, COUNT(DISTINCT product_key) AS measure_value FROM dim_products
UNION ALL 
SELECT 'Total Customers' as measure_name, COUNT(customer_key) as measure_value  FROM dim_customers


-- Find total customers by country
SELECT 
country,
COUNT(customer_key) as total_customers
from dim_customers
group by country 

-- find total customers by gender
SELECT 
gender,
COUNT(customer_key) as total_customers_by_gender
from dim_customers
group by gender 

-- find total product by category 
SELECT 
category,
COUNT(product_key) as total_products
from dim_products
group by category

-- What is the average cost in each category
SELECT 
category,
AVG (cost) as average_cost
from dim_products
group by category
order by average_cost DESC 



-- What is the total revenue generated for each category

SELECT 
p.category,
SUM(f.sales_amount) as total_revenue
from fact_sales f
LEFT JOIN dim_products p
ON P.product_key = f.product_key
group by category
order by total_revenue DESC 

-- What is the total revenue generated by each customer
SELECT 
c.customer_key,
c.first_name,
c.last_name,
sum(f.sales_amount) as total_revenue 
from fact_sales f
left join dim_customers c
on c.customer_key = f.customer_key
group by 
c.customer_key,
c.first_name,
c.last_name
order by total_revenue desc 


-- what is the distribution of sold items across countires
select
c.country,
sum(f.quantity) as total_sold_items 
from fact_sales f
left join dim_customers c
on c.customer_key = f.customer_key
group by 
c.country
order by total_sold_items desc

-- which 5 products generate the highest revenue

SELECT top 5
p.product_name,
sum(f.sales_amount) as total_revenue 
from fact_sales f
left join dim_products p
on p.product_key = f.product_key
group by 
p.product_name
order by total_revenue desc 


SELECT *
from(
	select
	p.product_name,
	sum(f.sales_amount) total_revenue,
	ROW_NUMBER() OVER(ORDER BY sum(f.sales_amount) DESC) AS rank_products
	from fact_sales f
	left join dim_products p
	on p.product_key = f.product_key
	group by 
	p.product_name)t
WHERE rank_products <= 5

-- the 3 customers with the fewest order placed 

SELECT TOP 3
c.customer_key,
c.first_name,
c.last_name,
COUNT(DISTINCT order_number) as total_orders 
from fact_sales f
left join dim_customers c
on c.customer_key = f.customer_key
group by 
c.customer_key,
c.first_name,
c.last_name
order by total_orders 




-- What are the worst-performing products in term of sales


SELECT top 5
p.product_name,
sum(f.sales_amount) as total_revenue 
from fact_sales f
left join dim_products p
on p.product_key = f.product_key
group by 
p.product_name
order by total_revenue  


 

<img width="1520" height="970" alt="image" src="https://github.com/user-attachments/assets/b0f46f04-177d-4d7d-b2cd-f9e89f8952b2" />

