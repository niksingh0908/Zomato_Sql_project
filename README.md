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


