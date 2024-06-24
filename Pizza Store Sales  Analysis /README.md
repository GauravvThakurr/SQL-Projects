# Pizza Sales Store Analysis

code
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

select hour(time) as hours, count(*) as orders_placed from orders
group by hours


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

use pizza




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
````
