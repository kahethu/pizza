# Pizza Sales Portfolio Project – SQL & Power BI

Watch this project on Youtube at https://www.youtube.com/watch?v=eQ83-J57DZY

The raw data for this project is presented in 4 CSV files. This data represents a year’s worth of sales for a pizza outlet, and they need to answer a few questions which will help them make important decisions to increase sales and improve their business.

The project is done in Microsoft SQL server and presented in Power BI. The data was loaded into 4 tables. This project involves the use of simple joins and sub-queries. 

## Data source:
You can download the data from https://www.kaggle.com/datasets/mysarahmadbhat/pizza-place-sales

A zip file is also included in this repository.

## Data Analysis
This project was done on SQL server 2022

## Data Visualization
Data visualization was done in Microsoft Power BI

![pizza-sales-report](https://github.com/kahethu/pizza/assets/27964625/ca0416db-6897-46df-8c0d-156531548e4e)

![pizza-sales-report-1](https://github.com/kahethu/pizza/assets/27964625/99bcef03-08dd-4b23-885e-fd5953f46b77)


## QUESTIONS TO BE ANSWERED:
### KPIs

 1) Total Revenue (How much money did we make this year?)
 2) Average Order Value
 3) Total Pizzas Sold
 4) Total Orders
 5) Average Pizzas per Order

### QUESTIONS TO ANSWER 

 1) Daily Trends for Total Orders
 2) Hourly Trend for Total Orders
 3) Percentage of Sales by Pizza Category
 4) Percentage of Sales by Pizza Size
 5) Total Pizzas Sold by Pizza Category
 6) Top 5 Best Sellers by Total Pizzas Sold
 7) Bottom 5 Worst Sellers by Total Pizzas Sold



## FINDINGS:
### KPIs

 1) Total Revenue for the year was $817,860
 2) Average Order Value was $38.31
 3) Total Pizzas Sold – 50,000
 4) Total Orders – 21,000
 5) Average Pizzas per Order – 2

### ANSWER TO QUESTIONS
 1) The busiest days are Thursday (3239 orders), Friday (3538 orders) and Saturday (3158 orders). Most sales are recorded on Friday
 2) Most orders are placed between 12pm to 1pm, and 5pm to 7pm
 3) Classic pizza has the highest percentage sales (26.91%), followed by Supreme (25.46%), Chicken (23.96%) and Veggie (23.68%) pizzas 
 4) Large size pizzas record the highest sales (45.89%) followed by medium (30.49%), then small (21.77%). XL and XXL only account for 1.72% and 0.12% respectively 
 5) Classic Pizza accounts for the highest sales (14,888 pizzas) followed by Supreme (11,987 pizzas), Veggie (11,649 pizzas) and Chicken (11,050 pizzas)
 6) Top 5 Best Sellers are the Classic Deluxe (2453 pizzas), Barbecue Chicken (2432 pizzas), Hawaiian (2422), Peperoni (2418 pizzas) and Thai Chicken (2371 pizzas)
 7) Bottom 5 Worst Sellers are Brie Carre (490 pizzas), Mediterranean (934 pizzas), Calabrese (937 pizzas), Spinach Supreme (950 pizzas) and Soppressata (961).


## CONCLUSION:
The outlet should capitalize on Large size Classic, Supreme, Veggie and Chicken pizzas.

Since XL and XXL pizzas account for such a small percentage of their sales (just 1.94%), they can safely get rid of these pizza sizes.

Even though the Brie Carre pizza is the worst seller, it recorded 490 pizzas sold. It would still be a good idea to keep it in the menu. 


## QUERIES USED:
You can download all the full queries from the SQL file in this repository. Below is a summary of the main queries:

### KPIs

#### 1) Total Revenue
``` SQL
SELECT 
 round(SUM(quantity * price), 2)
FROM order_details AS o
 JOIN pizzas AS p 
 ON o.pizza_id = p.pizza_id
```

#### 2) Average Order Value
- total order value/order count
``` SQL
SELECT 
 SUM(quantity * price)/ COUNT(DISTINCT order_id) AS [Average Order Value]
FROM order_details AS o
 JOIN pizzas AS p 
 ON o.pizza_id = p.pizza_id
```

#### 3) Total pizzas sold
``` SQL
 SELECT
  SUM(quantity) AS [Total Pizzas Sold]
FROM
  order_details
```


#### 4) Total Orders
``` SQL
SELECT
  COUNT(DISTINCT order_id) AS [Total Orders]
FROM
  order_details

```

#### 5) Average Pizzas Per Order
- quantity sold/order IDs
``` SQL
SELECT
  ROUND(SUM(quantity)/COUNT(DISTINCT order_id),2) AS [Average Pizzas Per Order]
FROM
  order_details
```

### Sales Analysis Questions

#### 1) Daily Trends for Total Orders
``` SQL
SELECT 
 FORMAT(date, 'dddd') AS DayOfWeek
 ,COUNT(DISTINCT order_id) AS total_orders
FROM orders
GROUP BY FORMAT(date, 'dddd')
ORDER BY total_orders DESC
```


#### 2) Hourly TrendS for Total Orders
``` SQL
SELECT 
    DATEPART(HOUR, time) AS [Hour]
	,COUNT(DISTINCT order_id) AS Total_Orders
FROM orders
GROUP BY DATEPART(HOUR, time)
ORDER BY [Hour]
```


### 3) Percentage of Sales by Pizza Category
- a: calculate total revenue per category
- % sales calculated as (a:/total revenue) * 100
``` SQL
SELECT 
    category,
    ROUND(SUM(quantity * price), 2) AS revenue,
    ROUND(SUM(quantity * price) * 100.0 / (SELECT SUM(quantity * price) FROM pizzas AS p2 JOIN order_details AS od2 ON od2.pizza_id = p2.pizza_id), 2) AS percentage_of_sales
FROM 
    pizzas AS p
JOIN 
    pizza_types AS pt ON p.pizza_type_id = pt.pizza_type_id
JOIN 
    order_details AS od ON od.pizza_id = p.pizza_id
GROUP BY 
    category;
```



#### 4) Percentage of Sales by Pizza Size
``` SQL
SELECT 
    size
    ,ROUND(SUM(quantity * price), 2) AS revenue
    ,ROUND(SUM(quantity * price) * 100.0 / (SELECT SUM(quantity * price) FROM pizzas AS p2 JOIN order_details AS od2 ON od2.pizza_id = p2.pizza_id), 2) AS percentage_of_sales
FROM 
    pizzas AS p
JOIN 
    pizza_types AS pt ON p.pizza_type_id = pt.pizza_type_id
JOIN 
    order_details AS od ON od.pizza_id = p.pizza_id
GROUP BY 
    size;
```


#### 5) Total Pizzas Sold by Pizza Category
``` SQL
SELECT
 category
 ,SUM(quantity) AS quantity_sold
FROM 
    pizzas AS p
JOIN 
    pizza_types AS pt ON p.pizza_type_id = pt.pizza_type_id
JOIN 
    order_details AS od ON od.pizza_id = p.pizza_id
GROUP BY category;
```


#### 6) Top 5 Best Sellers by Total Pizzas Sold
``` SQL
SELECT top 5
  name
  ,SUM(quantity) AS total_quantity_sold
FROM 
    pizzas AS p
JOIN 
    pizza_types AS pt ON p.pizza_type_id = pt.pizza_type_id
JOIN 
    order_details AS od ON od.pizza_id = p.pizza_id
GROUP BY name
ORDER BY total_quantity_sold DESC;
```


#### 7) Bottom 5 Best Sellers by Total Pizzas Sold
``` SQL
SELECT top 5
  name
  ,SUM(quantity) AS total_quantity_sold
FROM 
    pizzas AS p
JOIN 
    pizza_types AS pt ON p.pizza_type_id = pt.pizza_type_id
JOIN 
    order_details AS od ON od.pizza_id = p.pizza_id
GROUP BY name
ORDER BY total_quantity_sold ASC;
```


