# 🍔  Zomato Food Delivery SQL Analysis
![](https://raw.githubusercontent.com/niksingh0908/Zomato_Sql_project/refs/heads/main/Zomato_logo.png)

## 📌 Project Overview

This project focuses on analyzing a ** Zomato Food Delivery Database using SQL** to answer real-world business questions related to customers, restaurants, orders, deliveries, riders, revenue, and customer behavior.

The project contains **19 SQL analysis questions** covering customer analytics, restaurant performance, rider efficiency, sales trends, customer segmentation, and revenue analysis.

---

## 🗄️ Database Schema

The database consists of **5 tables**:

### 👤 Customers
Stores customer information such as customer ID, name, and registration date.

### 🏪 Restaurants
Contains restaurant details including restaurant ID, name, city, and opening hours.

### 🛒 Orders
Contains customer orders, including the ordered item, order date/time, status, and total amount.

### 🚴 Rider
Stores rider information including rider ID, name, and signup date.

### 📦 Deliveries
Contains delivery information such as delivery status, delivery time, order ID, and rider ID.

The tables are connected using **Primary Keys and Foreign Keys**.

---

## 🔗 Database Relationships

```text
Customers
    │
    │ customer_id
    ▼
Orders ──────────► Restaurants
    │
    │ order_id
    ▼
Deliveries
    │
    │ rider_id
    ▼
Rider
```

---

![](https://raw.githubusercontent.com/niksingh0908/Zomato_Sql_project/refs/heads/main/ERD%20For%20database.png)

``` sql
drop table if exists customers
drop table if exists restaurants
drop table if exists orders
drop table if exists rider
drop table if exists deliveries

create table customers (
customer_id	int primary key,
customer_name	VARCHAR(30),
reg_date date 
)

create table restaurants(
restaurant_id	int primary key,
restaurant_name	varchar (35),
city	varchar (30),
opening_hours varchar(45)
)

create table orders (
order_id int primary key,
customer_id	int,		 --This is coming from customer_table.
restaurant_id int, 		 --This is coming from resturant_table.
order_item	varchar(50), 		
order_date	date,
order_time	time,
order_status VARCHAR(55),	
total_amount float,
FOREIGN Key (customer_id) REFERENCES customers(customer_id),
FOREIGN key (restaurant_id) REFERENCES restaurants(restaurant_id)
)

create table rider(
rider_id int primary key,
rider_name	varchar (50),
sign_up date
)


create table deliveries(
delivery_id	int	primary key,
order_id	int , 				-- this is coming from order_table
delivery_status	VARCHAR (20),
delivery_time	time,
rider_id int , 				--this is coming from rider_table

FOREIGN key (order_id) REFERENCES orders(order_id),
FOREIGN Key (rider_id) REFERENCES rider(rider_id)
);


select* from customers
select* from deliveries
select* from orders
select* from restaurants
select* from rider

```

## 📊 Business Questions & Objectives

## 1. Write a query to find the top 5 most frequently ordered dishes by customer called "Arjun Mehta".

```sql
SELECT 
    c.customer_name,
    o.order_item AS dish,
    COUNT(*) AS total_orders
FROM orders AS o
JOIN customers AS c
    ON c.customer_id = o.customer_id
WHERE c.customer_name = 'Arjun Mehta'
GROUP BY c.customer_name, o.order_item
ORDER BY total_orders DESC
LIMIT 5;
```
**Objective:** Identify the top 5 dishes most frequently ordered by a specific customer to understand individual food preferences.


## 2. Popular Time Slots  Question: Identify the time slots during which the most orders are placed. based on 2-hour intervals

```sql

SELECT
    CASE
        WHEN EXTRACT(HOUR FROM order_time) BETWEEN 0 AND 1 THEN '00:00 - 02:00'
        WHEN EXTRACT(HOUR FROM order_time) BETWEEN 2 AND 3 THEN '02:00 - 04:00'
        WHEN EXTRACT(HOUR FROM order_time) BETWEEN 4 AND 5 THEN '04:00 - 06:00'
        WHEN EXTRACT(HOUR FROM order_time) BETWEEN 6 AND 7 THEN '06:00 - 08:00'
        WHEN EXTRACT(HOUR FROM order_time) BETWEEN 8 AND 9 THEN '08:00 - 10:00'
        WHEN EXTRACT(HOUR FROM order_time) BETWEEN 10 AND 11 THEN '10:00 - 12:00'
        WHEN EXTRACT(HOUR FROM order_time) BETWEEN 12 AND 13 THEN '12:00 - 14:00'
        WHEN EXTRACT(HOUR FROM order_time) BETWEEN 14 AND 15 THEN '14:00 - 16:00'
        WHEN EXTRACT(HOUR FROM order_time) BETWEEN 16 AND 17 THEN '16:00 - 18:00'
        WHEN EXTRACT(HOUR FROM order_time) BETWEEN 18 AND 19 THEN '18:00 - 20:00'
        WHEN EXTRACT(HOUR FROM order_time) BETWEEN 20 AND 21 THEN '20:00 - 22:00'
        WHEN EXTRACT(HOUR FROM order_time) BETWEEN 22 AND 23 THEN '22:00 - 00:00'
    END AS time_slot,
    COUNT(order_id) AS order_count
FROM Orders
GROUP BY time_slot
ORDER BY order_count DESC;

```

**Objective:** Identify the 2-hour time slots with the highest order volume to understand peak ordering hours.


# 3. Order Value Analysis
## Question: Find the average order value per customer who has placed more than 750 orders.

```sql
SELECT 
	o.customer_id,
	c.customer_name,
	AVG(o.total_amount) as aov
FROM orders as o
	JOIN customers as c
	ON c.customer_id = o.customer_id
GROUP BY 1,2
HAVING  COUNT(order_id) > 750;

```
**Objective:** Calculate the average order value of high-frequency customers to understand their average spending behavior.


# 4. High-Value Customers
## Question: List the customers who have spent more than 100K in total on food orders.

```sql
SELECT 
	o.customer_id,
	c.customer_name,
	SUM(o.total_amount) as total_spent
FROM orders as o
	JOIN customers as c
	ON c.customer_id = o.customer_id
GROUP BY 1,2
HAVING SUM(o.total_amount) > 100000;
```

**Objective:** Identify customers who have spent more than 100K to recognize high-value customers and potential loyalty opportunities.


# 5. Orders Without Delivery
##  Write a query to find orders that were placed but not delivered.


```sql
SELECT 
    r.restaurant_name,
    r.city,
    COUNT(*) AS not_delivered_orders
FROM orders AS o
JOIN restaurants AS r
    ON r.restaurant_id = o.restaurant_id
JOIN deliveries AS d
    ON d.order_id = o.order_id
WHERE d.delivery_status = 'Not Delivered'
GROUP BY 
    r.restaurant_name,
    r.city
ORDER BY not_delivered_orders DESC;

```
**Objective:** Identify restaurants and cities with undelivered orders to highlight potential delivery and operational issues.


# 6. Most Popular Dish by City:
## Identify the most popular dish in each city based on the number of orders.
```sql

SELECT * 
FROM
(SELECT 
	r.city,
	o.order_item as dish,
	COUNT(order_id) as total_orders,
	RANK() OVER(PARTITION BY r.city ORDER BY COUNT(order_id) DESC) as rank
FROM orders as o
JOIN 
restaurants as r
ON r.restaurant_id = o.restaurant_id
GROUP BY 1, 2
) as t1
WHERE rank = 1;
```
**Objective:** Identify the most popular dish in each city to understand regional customer preferences.


# 7. Customer Churn:
## Find customers who haven’t placed an order in 2024 but did in 2023.

```sql
select distinct customer_id
from orders
where  extract(year from order_date) = 2023 and customer_id not in
	( select distinct customer_id
	from orders
	where extract(year from order_date) = 2024
	)
```
**Objective:** Identify customers who ordered in 2023 but not in 2024 to detect potential customer churn.


# 8. Cancellation Rate Comparison:
## Calculate and compare the order cancellation rate for each restaurant between the current year (2024)and the previous year(2023).

```sql
WITH yearly_data AS (
    SELECT
        restaurant_id,
        EXTRACT(YEAR FROM order_date) AS year,
        COUNT(*) AS total_orders,
        COUNT(
            CASE 
                WHEN order_status = 'Not Fulfilled' THEN 1
            END
        ) AS not_fulfilled
    FROM orders
    WHERE EXTRACT(YEAR FROM order_date) IN (2023, 2024)
    GROUP BY restaurant_id, EXTRACT(YEAR FROM order_date)
)

SELECT
    restaurant_id,
    year,
    total_orders,
    not_fulfilled,
    ROUND(
        100.0 * not_fulfilled / NULLIF(total_orders, 0),
        2
    ) AS cancellation_rate
FROM yearly_data
ORDER BY restaurant_id, year;

```
**Objective:** Compare restaurant cancellation/non-fulfillment rates between 2023 and 2024 to evaluate changes in restaurant performance.

# 9. Rider Average Delivery Time:
## Determine each rider's average delivery time.

```sql
SELECT 
    o.order_id,
    o.order_time,
    d.delivery_time,
    d.rider_id,
    d.delivery_time - o.order_time AS time_difference,
	EXTRACT(EPOCH FROM (d.delivery_time - o.order_time + 
	CASE WHEN d.delivery_time < o.order_time THEN INTERVAL '1 day' ELSE
	INTERVAL '0 day' END))/60 as time_difference_insec
FROM orders AS o
JOIN deliveries AS d
ON o.order_id = d.order_id
WHERE d.delivery_status = 'Delivered';

```
**Objective:** Calculate delivery times for completed orders to evaluate delivery performance.


# 10. Customer Segmentation:
## Customer Segmentation: Segment customers into 'Gold' or 'Silver' groups based on their total spending compared to the average order value (AOV). If a customer's total spending exceeds the AOV,label them as 'Gold'; otherwise, label them as 'Silver'. Write an SQL query to determine each segment total number of orders and total revenue

```sql
SELECT 
	cx_category,
	SUM(total_orders) as total_orders,
	SUM(total_spent) as total_revenue
FROM

	(SELECT 
		customer_id,
		SUM(total_amount) as total_spent,
		COUNT(order_id) as total_orders,
		CASE 
			WHEN SUM(total_amount) > (SELECT AVG(total_amount) FROM orders) THEN 'Gold'
			ELSE 'silver'
		END as cx_category
	FROM orders
	group by 1
	) as t1
GROUP BY 1;
```
**Objective:** Segment customers into Gold and Silver groups based on spending and compare the revenue and order contribution of each segment.

# 11. Rider Monthly Earnings:
## Calculate each rider's total monthly earnings, assuming they earn 8% of the order amount.

```sql
SELECT 
	d.rider_id,
	TO_CHAR(o.order_date, 'mm-yy') as month,
	SUM(total_amount) as revenue,
	SUM(total_amount)* 0.08 as riders_earning
FROM orders as o
JOIN deliveries as d
ON o.order_id = d.order_id
GROUP BY 1, 2
ORDER BY 1, 2;
```
**Objective:** Calculate monthly rider earnings based on an 8% share of order revenue.

# 12 Rider Ratings Analysis:
##  Find the number of 5-star, 4-star, and 3-star ratings each rider has riders receive this rating based on delivery time.
### If orders are delivered less than 15 minutes of order received time the rider get 5 star rating, -- if they deliver 15 and 20 minute they get 4 star rating -- if they deliver after 20 minute they get 3 star rating.

```sql
SELECT 
	rider_id,
	stars,
	COUNT(*) as total_stars
FROM
(
	SELECT
		rider_id,
		delivery_took_time,
		CASE 
			WHEN delivery_took_time < 15 THEN '5 star'
			WHEN delivery_took_time BETWEEN 15 AND 20 THEN '4 star'
			ELSE '3 star'
		END as stars
		
	FROM
	(
		SELECT 
			o.order_id,
			o.order_time,
			d.delivery_time,
			EXTRACT(EPOCH FROM (d.delivery_time - o.order_time + 
			CASE WHEN d.delivery_time < o.order_time THEN INTERVAL '1 day' 
			ELSE INTERVAL '0 day' END
			))/60 as delivery_took_time,
			d.rider_id
		FROM orders as o
		JOIN deliveries as d
		ON o.order_id = d.order_id
		WHERE delivery_status = 'Delivered'
	) as t1
) as t2
GROUP BY 1, 2
ORDER BY 1, 3 DESC;
```
**Objective:** Evaluate rider performance by assigning ratings based on delivery time and analyzing the number of 3-, 4-, and 5-star deliveries.


# 13 Order Frequency by Day:
## Analyze order frequency per day of the week and identify the peak day for each restaurant.

```sql
SELECT * FROM
(
	SELECT 
		r.restaurant_name,
		-- o.order_date,
		TO_CHAR(o.order_date, 'Day') as day,
		COUNT(o.order_id) as total_orders,
		RANK() OVER(PARTITION BY r.restaurant_name ORDER BY COUNT(o.order_id)  DESC) as rank
	FROM orders as o
	JOIN
	restaurants as r
	ON o.restaurant_id = r.restaurant_id
	GROUP BY 1, 2
	ORDER BY 1, 3 DESC
	) as t1
WHERE rank = 1;
```
**Objective:** Identify the busiest day of the week for each restaurant to understand weekly ordering patterns.

# 14. Customer Lifetime Value (CLV):
## Calculate the total revenue generated by each customer over all their orders.

```sql
SELECT 
	o.customer_id,
	c.customer_name,
	SUM(o.total_amount) as CLV
FROM orders as o
JOIN customers as c
ON o.customer_id = c.customer_id
GROUP BY 1, 2;
```
**Objective:** Calculate total revenue generated by each customer to identify customers with the highest lifetime value.

# 15. Monthly Sales Trends:
## Identify sales trends by comparing each month's total sales to the previous month.

```sql
SELECT 
    EXTRACT(YEAR FROM order_date) AS year,
    EXTRACT(MONTH FROM order_date) AS month,
    SUM(total_amount) AS total_sales,
    LAG(SUM(total_amount)) OVER (
        ORDER BY 
            EXTRACT(YEAR FROM order_date),
            EXTRACT(MONTH FROM order_date)
    ) AS previous_month_sales
FROM orders
GROUP BY 1, 2
ORDER BY 1, 2;
```
**Objective:** Compare monthly sales with the previous month to identify revenue growth and decline trends.


# 16. Rider Efficiency:
## Evaluate rider efficiency by determining average delivery times and identifying those with the lowest and highest averages.

```sql
WITH new_table
AS
(
	SELECT 
		*,
		d.rider_id as riders_id,
		EXTRACT(EPOCH FROM (d.delivery_time - o.order_time + 
		CASE WHEN d.delivery_time < o.order_time THEN INTERVAL '1 day' ELSE
		INTERVAL '0 day' END))/60 as time_deliver
	FROM orders as o
	JOIN deliveries as d
	ON o.order_id = d.order_id
	WHERE d.delivery_status = 'Delivered'
),

riders_time
AS

(
	SELECT 
		riders_id,
		AVG(time_deliver) avg_time
	FROM new_table
	GROUP BY 1
)
SELECT 
	MIN(avg_time),
	MAX(avg_time)
FROM riders_time;
```
**Objective:** Compare riders based on average delivery time to identify the fastest and slowest delivery performers.


# 17. Order Item Popularity:
## Track the popularity of specific order items over time and identify seasonal demand spikes.

```sql
SELECT 
	order_item,
	seasons,
	COUNT(order_id) as total_orders
FROM 
(
SELECT 
		*,
		EXTRACT(MONTH FROM order_date) as month,
		CASE 
    WHEN EXTRACT(MONTH FROM order_date) IN (3,4,5) 
        THEN 'Spring'
    WHEN EXTRACT(MONTH FROM order_date) IN (6,7,8) 
        THEN 'Summer'
    WHEN EXTRACT(MONTH FROM order_date) IN (9,10) 
        THEN 'Autumn'
    ELSE 'Winter'
END as seasons
	FROM orders
) as t1
GROUP BY 1, 2
ORDER BY 1, 3 DESC;
```
**Objective:** Analyze dish popularity across seasons to identify seasonal demand patterns.

# 18. Restaurant Revenue Ranking:
## Rank restaurants by their total revenue from the last year, including their name,total revenue, and rank within their city.

```sql
WITH ranking_table
AS
(
	SELECT 
		r.city,
		r.restaurant_name,
		SUM(o.total_amount) as revenue,
		RANK() OVER(PARTITION BY r.city ORDER BY SUM(o.total_amount) DESC) as rank
	FROM orders as o
	JOIN 
	restaurants as r
	ON r.restaurant_id = o.restaurant_id
	WHERE o.order_date >= CURRENT_DATE - INTERVAL '1 year'
	GROUP BY 1, 2
)
SELECT 
	*
FROM ranking_table
WHERE rank = 1;
```
**Objective:** Rank restaurants within each city based on revenue generated during the last year to identify top-performing restaurants.


## 19. Rank each city based on the total revenue for last year 2023

```sql
SELECT 
	r.city,
	SUM(total_amount) as total_revenue,
	RANK() OVER(ORDER BY SUM(total_amount) DESC) as city_rank
FROM orders as o
JOIN
restaurants as r
ON o.restaurant_id = r.restaurant_id
GROUP BY 1;
```
**Objective:** Rank cities based on total revenue to identify the highest-performing markets.

# 🛠️ SQL Concepts Used

This project demonstrates the following SQL concepts:

- `CREATE TABLE`
- `DROP TABLE`
- Primary Keys
- Foreign Keys
- `SELECT`
- `WHERE`
- `JOIN`
- `GROUP BY`
- `HAVING`
- `ORDER BY`
- `LIMIT`
- `COUNT()`
- `SUM()`
- `AVG()`
- `MIN()`
- `MAX()`
- `CASE WHEN`
- Subqueries
- CTEs (`WITH`)
- Window Functions
- `RANK()`
- `LAG()`
- `EXTRACT()`
- `TO_CHAR()`
- Date & Time calculations
- `INTERVAL`
- `EPOCH`

---


# 📌 Business Areas Covered

| Business Area | Analysis |
|---|---|
| Customer Analytics | Churn, CLV, Segmentation |
| Restaurant Analytics | Revenue, Popular Dishes, Fulfillment |
| Rider Analytics | Delivery Time, Ratings, Earnings |
| Sales Analytics | Monthly Sales, Revenue Ranking |
| Product Analytics | Dish Popularity, Seasonal Demand |
| Operations | Peak Hours, Delivery Performance |
| Geographic Analysis | City-level Revenue |

---
# 🧰 Tools & Technologies

- **PostgreSQL**
- **SQL**
- **GitHub**

---


# 🎯 Project Objective

The main objective of this project is to demonstrate the ability to use SQL to transform raw food delivery data into meaningful business analysis.

The project focuses on using SQL to answer practical questions around:

**Customers → Orders → Restaurants → Deliveries → Riders → Revenue**

## 👨‍💻 Author

**Nikhil Singh**

Data Analytics Portfolio Project  
SQL | PostgreSQL | Data Analysis




