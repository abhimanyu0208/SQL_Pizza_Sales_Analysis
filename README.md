# SQL_Pizza_Sales_Analysis
This project explores pizza sales data using SQL, moving from basic queries to more advanced analysis.
# 🍕 SQL Pizza Sales Data Analysis

## 📂 Dataset Description

The analysis is based on a pizza sales dataset consisting of four tables:

* **pizza\_types**: `pizza_type_id, name, category, ingredients`
* **orders**: `order_id, date, time`
* **order\_details**: `order_details_id, order_id, pizza_id, quantity`
* **pizzas**: `pizza_id, pizza_type_id, size, price`

---

## 🔹 Basic Analysis

**1. Total number of orders placed**

```sql
SELECT COUNT(order_id) AS total_orders 
FROM orders;
```

**2. Total revenue generated from pizza sales**

```sql
SELECT ROUND(SUM(order_details.quantity * pizzas.price)) AS total_sales
FROM order_details
JOIN pizzas ON pizzas.pizza_id = order_details.pizza_id;
```

**3. Highest-priced pizza**

```sql
SELECT pizza_types.name, pizzas.price
FROM pizza_types
JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
ORDER BY pizzas.price DESC
LIMIT 1;
```

**4. Most common pizza size ordered**

```sql
SELECT pizzas.size, COUNT(order_details.order_details_id) AS order_count
FROM pizzas
JOIN order_details ON pizzas.pizza_id = order_details.pizza_id
GROUP BY pizzas.size
ORDER BY order_count DESC;
```

**5. Top 5 most ordered pizza types by quantity**

```sql
SELECT pizza_types.name, SUM(order_details.quantity) AS order_quantity
FROM pizza_types
JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
JOIN order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.name
ORDER BY order_quantity DESC
LIMIT 5;
```

---

## 🔹 Intermediate Analysis

**1. Total quantity of each pizza category ordered**

```sql
SELECT pizza_types.category, SUM(order_details.quantity) AS quantity
FROM pizza_types
JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
JOIN order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.category
ORDER BY quantity DESC;
```

**2. Distribution of orders by hour of the day**

```sql
SELECT HOUR(time) AS order_hour, COUNT(order_id) AS total_orders
FROM orders
GROUP BY order_hour
ORDER BY order_hour;
```

**3. Category-wise distribution of pizzas**

```sql
SELECT category, COUNT(name) AS total_pizzas
FROM pizza_types
GROUP BY category;
```

**4. Average number of pizzas ordered per day**

```sql
SELECT ROUND(AVG(quantity), 0) AS avg_pizza_ordered_per_day
FROM (
    SELECT orders.date, SUM(order_details.quantity) AS quantity
    FROM orders
    JOIN order_details ON orders.order_id = order_details.order_id
    GROUP BY orders.date
) AS order_quantity;
```

**5. Top 3 most ordered pizza types based on revenue**

```sql
SELECT pizza_types.name, SUM(order_details.quantity * pizzas.price) AS revenue
FROM pizza_types
JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
JOIN order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.name
ORDER BY revenue DESC
LIMIT 3;
```

---

## 🔹 Advanced Analysis

**1. Percentage contribution of each pizza category to total revenue**

```sql
SELECT pizza_types.category,
ROUND(SUM(order_details.quantity * pizzas.price) / (
    SELECT SUM(order_details.quantity * pizzas.price)
    FROM order_details
    JOIN pizzas ON pizzas.pizza_id = order_details.pizza_id
) * 100, 2) AS revenue_percentage
FROM pizza_types
JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
JOIN order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.category
ORDER BY revenue_percentage DESC;
```

**2. Cumulative revenue over time**

```sql
SELECT date,
SUM(revenue) OVER(ORDER BY date) AS cum_revenue
FROM (
    SELECT orders.date, SUM(order_details.quantity * pizzas.price) AS revenue
    FROM order_details
    JOIN pizzas ON order_details.pizza_id = pizzas.pizza_id
    JOIN orders ON orders.order_id = order_details.order_id
    GROUP BY orders.date
) AS sales;
```

**3. Top 3 most ordered pizza types by revenue for each category**

```sql
SELECT name, revenue 
FROM (
    SELECT category, name, revenue,
    RANK() OVER(PARTITION BY category ORDER BY revenue DESC) AS rn
    FROM (
        SELECT pizza_types.category, pizza_types.name,
               SUM(order_details.quantity * pizzas.price) AS revenue
        FROM pizza_types
        JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN order_details ON order_details.pizza_id = pizzas.pizza_id
        GROUP BY pizza_types.category, pizza_types.name
    ) AS a
) AS b
WHERE rn <= 3;
```


## ✅ Conclusion

This project demonstrates **SQL data analysis skills** across multiple levels:

* **Basic**: Aggregations and ordering queries
* **Intermediate**: Joins, grouping, and averages
* **Advanced**: Window functions, cumulative analysis, and percentage contributions

It provides valuable business insights into **sales performance, customer preferences, and revenue distribution**.
