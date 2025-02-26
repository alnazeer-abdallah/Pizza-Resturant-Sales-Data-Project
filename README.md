# Pizza Resturant Sales Project

----------------
## Overview
The client wants to extract valuable insights from server data by analyzing it, including key metrics such as revenue, trends, customer preferences, and more.

## Dataset
The database consists of four tables (Pizza_Types, Pizzas, Orders, and Order_Details) containing extensive data on pizza types, ingredients, prices, order details, and more.

## Data Manipulation and Analysis
The data was efficiently handled and manipulated using SQL, allowing complex queries to extract, filter, and aggregate information. SQL was used to join tables, update records, and ensure data accuracy, transforming raw information into meaningful insights.

## SQL Code :

### --========== Explore the data ===========--

```
SELECT * FROM Pizzas;
SELECT * FROM Pizza_types;
SELECT * FROM Orders;
SELECT * FROM Order_details;
```

### --========== Total Orders ===========--

```
SELECT Count(DISTINCT(order_id)) AS Total_Orders FROM Orders;
```

### --========== Total Pizzas Sold ===========--

```
SELECT SUM(quantity) AS Total_Pizzas_Sold FROM Order_details;
```

### --========== Average Pizzas Per Order ===========--

```
SELECT Round(SUM(quantity)/Count(DISTINCT(order_id)),0) AS Average_Pizzas_Per_Order 
FROM Order_details;
```

### --========== Total Revenue ===========--

```
SELECT ROUND(SUM(O.quantity*P.price),2) AS Total_Revenue FROM Order_details as O
LEFT JOIN Pizzas AS P
ON O.pizza_id = P.pizza_id;
```

### --========== AVERAGE ORDER VALUE ===========--

```
SELECT ROUND(SUM(O.quantity*P.price)/Count(DISTINCT(O.order_id)),0) AS AVERAGE_ORDER_VALUE FROM Order_details as O
LEFT JOIN Pizzas AS P
ON O.pizza_id = P.pizza_id;
```

### --========== Daily Trends For Total Orders by Weekday ===========--

```
SELECT FORMAT(date,'dddd') AS Week_day, Count(DISTINCT(order_id)) AS Orders_Number FROM Orders
GROUP BY FORMAT(date,'dddd')
ORDER BY Orders_Number DESC;
```

### --========== Daily Trends For Total Orders ===========--

```
--First Calculate Order Price

WITH CTE AS (SELECT O.order_id,SUM(O.quantity*P.price) AS ORDER_PRICE FROM Order_details as O
LEFT JOIN Pizzas AS P
ON O.pizza_id = P.pizza_id
GROUP BY O.order_id)

--Join with Order table to get the trend
SELECT  O.date,Count(DISTINCT(O.order_id)) AS Orders_Number ,ROUND(SUM(C.ORDER_PRICE),2) AS Daily_Price FROM Orders as O
LEFT JOIN CTE AS C
ON O.order_id = C.order_id
GROUP BY O.date
ORDER BY O.date;
```

### --========== Hourly Trends Per Orders ===========--

```
SELECT  DATEPART(HOUR,time) AS Hour,Count(DISTINCT(order_id)) AS Orders_Number
FROM Orders 
GROUP BY DATEPART(HOUR,time)
ORDER BY Orders_Number DESC;
```

### --========== Percentage of sales by Pizza Category ===========--

```
WITH CTE AS (SELECT PT.category, P.pizza_id,P.price FROM Pizza_types AS PT 
LEFT JOIN Pizzas AS P
ON PT.pizza_type_id = P.pizza_type_id)

SELECT C.category,SUM(OD.quantity) AS Quantity, ROUND(SUM(OD.quantity*C.price),2) AS PRICE,
(SUM(OD.quantity) * 100.0) / (SELECT SUM(quantity) FROM Order_details) AS Percentage
FROM Order_details AS OD
LEFT JOIN CTE AS C
ON OD.pizza_id = C.pizza_id
GROUP BY C.category
ORDER BY Quantity DESC;
```

### --========== Percentage of sales by Pizza Size ===========--

```
SELECT P.size, SUM(O.quantity) AS Quantity,ROUND (SUM(O.quantity*P.price),2) AS Total_PRICE,
SUM(O.quantity)*100.0/(SELECT COUNT(order_id) FROM Order_details) AS Percentage
FROM Order_details as O
LEFT JOIN Pizzas AS P
ON O.pizza_id = P.pizza_id
GROUP BY P.size
ORDER BY Quantity DESC;
```

### --========== TOP 5 BEST Sellers by Total Pizzas Sold ===========--

```
WITH CTE AS (SELECT PT.name, P.pizza_id,P.price FROM Pizza_types AS PT 
LEFT JOIN Pizzas AS P
ON PT.pizza_type_id = P.pizza_type_id)

SELECT TOP 5 WITH TIES C.name,SUM(OD.quantity) AS Quantity, ROUND(SUM(OD.quantity*C.price),2) AS PRICE,
(SUM(OD.quantity) * 100.0) / (SELECT SUM(quantity) FROM Order_details) AS Percentage
FROM Order_details AS OD
LEFT JOIN CTE AS C
ON OD.pizza_id = C.pizza_id
GROUP BY C.name
ORDER BY Quantity DESC;
```

### --========== TOP 5 WORST Sellers by Total Pizzas Sold ===========--

```
WITH CTE AS (SELECT PT.name, P.pizza_id,P.price FROM Pizza_types AS PT 
LEFT JOIN Pizzas AS P
ON PT.pizza_type_id = P.pizza_type_id)

SELECT TOP 5 WITH TIES C.name,SUM(OD.quantity) AS Quantity, ROUND(SUM(OD.quantity*C.price),2) AS PRICE,
(SUM(OD.quantity) * 100.0) / (SELECT SUM(quantity) FROM Order_details) AS Percentage
FROM Order_details AS OD
LEFT JOIN CTE AS C
ON OD.pizza_id = C.pizza_id
GROUP BY C.name
ORDER BY Quantity;
```
