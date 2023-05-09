# Case Study #2 - Pizza Runner
## A. Pizza Metrics
### 1. How many pizzas were ordered?
#### Steps:
  - Used the aggregate funtion COUNT to get the Total_oders

```sql
Select COUNT(1) as Total_Orders
From pizza_runner.customer_orders;
```
### 2. How many unique customer orders were made?
#### Steps:
  - Used the DISTINCT function to find the unique customer orders  
```sql
Select COUNT(distinct order_id) as unique_customer_order
From pizza_runner.customer_orders;
```

3. How many successful orders were delivered by each runner?

```sql
Select runner_id,Count(*) as successful_dilervery
From pizza_runner.runner_orders 
Where distance != 'null'
Group by runner_id;
```

4. How many of each type of pizza was delivered?

```sql
Select pizza_id,Count(*) as successful_dilervery
From pizza_runner.customer_orders co
Join pizza_runner.runner_orders ro on co.order_id = ro.order_id
Where distance != 'null'
Group by pizza_id
```

5. How many Vegetarian and Meatlovers were ordered by each customer?

```sql
SELECT customer_id,
COALESCE(SUM(CASE WHEN pizza_id = 1 THEN 1 END),0) AS meatlovers,
COALESCE(SUM(CASE WHEN pizza_id = 2 THEN 1 END),0) AS vegetarian
FROM pizza_runner.customer_orders
GROUP BY customer_id
```

6. What was the maximum number of pizzas delivered in a single order?

```sql
Select co.order_id,Count(*) as successful_dilervery
From pizza_runner.customer_orders co
Join pizza_runner.runner_orders ro on co.order_id = ro.order_id
Where distance != 'null'
Group by co.order_id
Order by 2 desc
```

7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?

```sql
With temp_table as (
	SELECT co.order_id,customer_id,exclusions,extras,distance,
	CASE WHEN exclusions IN ('null','') THEN 0 ELSE 1 End AS exlusions_change,
	Case when extras IN ('null','') Or extras IS NULL THEN 0 ELSE 1 END as extras_change
	From pizza_runner.customer_orders co
	Join pizza_runner.runner_orders ro on co.order_id = ro.order_id
	Where distance != 'null') 
Select customer_id,
 COUNT (CASE WHEN exlusions_change = 0 and extras_change = 0 THEN 0 END) AS NO_CHANGE,
 COUNT (CASE WHEN exlusions_change = 1 OR extras_change = 1 THEN 1 END) AS Changed_Occured
FROM temp_table
Group by customer_id
```

8. How many pizzas were delivered that had both exclusions and extras?

```sql
With temp_table as (
	SELECT co.order_id,customer_id,exclusions,extras,distance,
	CASE WHEN exclusions IN ('null','') THEN 0 ELSE 1 End AS exlusions_change,
	Case when extras IN ('null','') Or extras IS NULL THEN 0 ELSE 1 END as extras_change
	From pizza_runner.customer_orders co
	Join pizza_runner.runner_orders ro on co.order_id = ro.order_id
	Where distance != 'null') 
Select 
 COUNT (CASE WHEN exlusions_change = 1 and extras_change = 1 THEN 1 END) AS Double_change
-- COUNT (CASE WHEN exlusions_change = 1 OR extras_change = 1 THEN 1 END) AS Changed_Occured
FROM temp_table
```

9. What was the total volume of pizzas ordered for each hour of the day?

```sql
SELECT HOD, COUNT(*) AS pizza_ordered
 FROM(	SELECT DATEPART(hour FROM order_time) AS hod
		FROM pizza_runner.customer_orders) AS temp_table
GROUP BY hod
ORDER BY pizza_ordered DESC
```
10. What was the volume of orders for each day of the week?

```sql
SELECT DOW, COUNT(*) AS pizza_ordered
 FROM(	SELECT DATENAME(WEEKDAY,order_time) AS DOW -- I got to use datename
		FROM pizza_runner.customer_orders) AS temp_table
GROUP BY DOW
ORDER BY 2 DESC
```

