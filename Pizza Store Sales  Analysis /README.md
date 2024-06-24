# Pizza Store Sale Analysis

## Code

````sql
-- Retrieve the total number of orders placed.

SELECT 
    COUNT(order_id) AS total_order_placed
FROM
    orders

-- Calculate the total revenue generated from pizza sales.

SELECT 
    ROUND(SUM(p.price * o.quantity), 2) AS total_revenue
FROM
    order_details o
        INNER JOIN
    pizzas p ON o.pizza_id = p.pizza_id

-- Identify the highest-priced pizza.

SELECT 
    t.name, p.price
FROM
    pizza_types t
        INNER JOIN
    pizzas p ON p.pizza_type_id = t.pizza_type_id
ORDER BY p.price DESC
LIMIT 1


-- Identify the most common pizza size ordered.

SELECT 
    p.size, COUNT(*) AS no_times_ordered
FROM
    pizzas p
        INNER JOIN
    order_details o ON o.pizza_id = p.pizza_id
GROUP BY p.size
ORDER BY no_times_ordered DESC
LIMIT 1


-- List the top 5 most ordered pizza types along with their quantities.
SELECT 
    t.name AS pizza_name, SUM(o.quantity) AS qty_ordered
FROM
    pizza_types t
        INNER JOIN
    pizzas p ON p.pizza_type_id = t.pizza_type_id
        JOIN
    order_details o ON o.pizza_id = p.pizza_id
GROUP BY t.name
ORDER BY qty_ordered DESC
LIMIT 5




-- Intermediate:
-- Join the necessary tables to find the total quantity of each pizza category ordered.

SELECT 
    t.category AS pizza_category, SUM(o.quantity) AS qty_ordered
FROM
    pizza_types t
        INNER JOIN
    pizzas p ON p.pizza_type_id = t.pizza_type_id
        JOIN
    order_details o ON o.pizza_id = p.pizza_id
GROUP BY t.category
ORDER BY qty_ordered DESC


-- Determine the distribution of orders by hour of the day.

SELECT 
    HOUR(time) AS hours, COUNT(*) AS orders_placed
FROM
    orders
GROUP BY hours


-- Join relevant tables to find the category-wise distribution of pizzas.
SELECT 
    category, COUNT(name)
FROM
    pizza_types
GROUP BY category



-- Group the orders by date and calculate the average number of pizzas ordered per day.
with cte as (
SELECT 
    DATE(date) AS dates, SUM(quantity) AS pizza_ordered
FROM
    orders o
        JOIN
    order_details d ON d.order_id = o.order_id
GROUP BY dates
 )
SELECT 
    ROUND(AVG(pizza_ordered), 0) AS pizza_ordered
FROM
    cte
 
-- Determine the top 3 most ordered pizza types based on revenue.

SELECT 
    t.name AS pizza_type,
    ROUND(SUM(p.price * o.quantity), 2) AS total_revenue
FROM
    order_details o
        INNER JOIN
    pizzas p ON o.pizza_id = p.pizza_id
        INNER JOIN
    pizza_types t ON t.pizza_type_id = p.pizza_type_id
GROUP BY pizza_type
ORDER BY total_revenue DESC
LIMIT 3

-- Advanced:
-- Calculate the percentage contribution of each pizza type to total revenue.

SELECT 
    t.category AS pizza_type,
    ROUND((SUM(p.price * o.quantity) / (SELECT 
                    SUM(p.price * o.quantity)
                FROM
                    order_details o
                        INNER JOIN
                    pizzas p ON p.pizza_id = o.pizza_id)) * 100,
            2) AS total_revenue_contribution
FROM
    order_details o
        INNER JOIN
    pizzas p ON o.pizza_id = p.pizza_id
        INNER JOIN
    pizza_types t ON t.pizza_type_id = p.pizza_type_id
GROUP BY pizza_type
ORDER BY total_revenue_contribution DESC




-- Analyze the cumulative revenue generated over time.

with cte as (  
    SELECT 
   date(date) as dates ,
    round(SUM(p.price * d.quantity),2)  AS revenue
FROM 
    orders o
INNER JOIN 
    order_details d ON d.order_id = o.order_id
INNER JOIN 
    pizzas p ON p.pizza_id = d.pizza_id
GROUP BY 
    dates
ORDER BY 
    dates)
    select dates, sum(revenue) over (order by dates asc) cumulative_revenue
    from cte



-- Determine the top 3 most ordered pizza types based on revenue for each pizza category.
with cte as (
select t.category as pizza_type, t.name as pizza_name, SUM(p.price * o.quantity) as revenue,
dense_rank() over (partition by t.category order by SUM(p.price * o.quantity) desc) as rnk
from pizza_types t
inner join pizzas p on p.pizza_type_id = t.pizza_type_id 
inner join order_details o on o.pizza_id = p.pizza_id
group by pizza_type, pizza_name
)
select pizza_type, pizza_name, revenue from cte
where rnk < 4 


-- Find the Least Popular Pizza in Each Category
select pizza_type, pizza_name, total_qty from (select t.category as pizza_type, t.name as pizza_name, sum(d.quantity) total_qty,
dense_rank() over(partition by t.category order by sum(d.quantity) ) as qty_ordered
from order_details d
inner join pizzas p on p.pizza_id = d.pizza_id
inner join pizza_types t on t.pizza_type_id = p.pizza_type_id
group by pizza_type, pizza_name
) t1
where qty_ordered = 1

-- Find the Average Time Between Orders

SELECT 
    AVG(time_diff) / 60 AS avg_time_diff_minutes
FROM (
    SELECT 
        o.date,
        o.order_id,
        o.time,       
         LAG(o.time) OVER (PARTITION BY o.date ORDER BY o.time) AS time_diffs, 
         TIME_TO_SEC(TIMEDIFF(o.time, LAG(o.time) OVER (PARTITION BY o.date ORDER BY o.time))) AS time_diff
    FROM 
        orders o
) AS time_diffs
WHERE 
    time_diff IS NOT NULL;

-- Identify Pizzas That Have Never Been Ordered
SELECT 
    p.pizza_id,
    pt.name AS pizza_name
FROM 
    pizzas p
INNER JOIN 
    pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
LEFT JOIN 
    order_details od ON p.pizza_id = od.pizza_id
WHERE 
    od.pizza_id IS NULL;

-- Find the Day with the Highest Total Revenue

SELECT 
    o.date,
    SUM(p.price * od.quantity) AS total_revenue
FROM 
    orders o
INNER JOIN 
    order_details od ON o.order_id = od.order_id
INNER JOIN 
    pizzas p ON od.pizza_id = p.pizza_id
GROUP BY 
    o.date
ORDER BY 
    total_revenue DESC
LIMIT 1;
````

