# 🍜 Case Study #1: Danny's Diner 

<img width="580" alt="Screenshot 2023-11-06 225103" src="https://github.com/GauravvThakurr/SQL-projects/assets/141028751/5e1659c3-4317-434e-b8f3-9b62dc14314a">

***

## 📑 Task
Danny aims to enhance customer experiences and assess the expansion of his loyalty program using three key datasets: sales, menu, and members. To gain insights, he plans to analyze customer visit patterns, total spending, and favorite menu items. Additionally, he needs basic datasets generated for his team's easy data inspection.

***

## 📍Entity Relationship Diagram

<img width="568" alt="Screenshot 2023-11-06 221800" src="https://github.com/GauravvThakurr/SQL-projects/assets/141028751/4869b0c9-fcf4-4b95-8c11-4cca03626cad">

***

# Problem and Solutions
We are going to use Postgresql DBMS for this setup, but we can use any of our choice

**1. What is the total amount each customer spent at the restaurant?**

````sql
SELECT
  s.customer_id AS Customers_Id,
  concat('$', SUM(m.price)) AS Total_amt_customer_spend
FROM dannys_diner.sales s
INNER JOIN dannys_diner.menu m
  ON s.product_id = m.product_id
GROUP BY s.customer_id
ORDER BY SUM(m.price) DESC;
````

#### 🔖 Setps
- Performe **INNER JOIN** between `dannys_diner.sales` and `dannys_diner.menu` table and got `s.customer_id AS Customers_Id` and `m.price as Total_amt_customer_spend`
- Performe Sum on `m.price` and Group the aggregated results by `s.customer_id`
- Also add `$` to aggregated values for better readability (optional)



#### 🎯Insights
<img width="585" alt="Screenshot 2023-11-07 125434" src="https://github.com/GauravvThakurr/SQL-projects/assets/141028751/353c9d9f-564f-40a4-bcaa-05818de89c1a">


- Customer_Id A spents total of $76
- Customer_id B spents total of$74
- Customer_id C spents total of$36

***

**2. How many days has each customer visited the restaurant?**

````sql
SELECT
  customer_id,
  COUNT(DISTINCT order_date) AS Days_visited
FROM dannys_diner.sales
GROUP BY customer_id;
````

#### 🔖 Setps
- To find unique visits use  `COUNT(DISTINCT order_date)` than group by `customer_id` 

#### 🎯Insights
<img width="489" alt="Screenshot 2023-11-07 125550" src="https://github.com/GauravvThakurr/SQL-projects/assets/141028751/4e28d8bf-317a-45b0-b3db-6cce4b33dc91">


- Customer_Id A visited 4 times
- Customer_Id B visited 6 times
- Customer_Id c visited 2 times

***

**3. What was the first item from the menu purchased by each customer?**

````sql
WITH firstcte
AS (SELECT
  s.customer_id,
  s.order_date,
  m.product_name,
  DENSE_RANK() OVER (
  PARTITION BY s.customer_id
  ORDER BY s.order_date) AS first_order
FROM dannys_diner.sales s
INNER JOIN dannys_diner.menu m
  ON s.product_id = m.product_id)

SELECT
  customer_id,
  product_name
FROM firstcte
WHERE first_order = 1
GROUP BY customer_id,
         product_name
````

#### 🔖 Setps 
- Create a Common Table Expression (CTE) called `firstcte` Within the CTE, include a new column named `first_order` which calculates the row number using the `DENSE_RANK()` window function. The data is divided into partitions based on `customer_id` and the rows within each partition are ordered by `order_date`
- In outer Query select required columns and then apply where clause to filter `first_order = 1` which will only select first orders
- Group them all

#### 🎯Insights
<img width="539" alt="Screenshot 2023-11-07 125658" src="https://github.com/GauravvThakurr/SQL-projects/assets/141028751/3351dd3a-8bae-445b-9802-d7650a4c0f14">


- Customer_id A brought Sushi and curry
- Customer_id B ordered curry
- Customer_id A brought Ramen

Note: A ordered twice on the same day, we have 2 orders as First since we do not have the exact order time we took the first visit date as the first order day

***

**4. What is the most purchased item on the menu and how many times was it purchased by all customers?**
````sql
with most_ordered as (
select product_id
from dannys_diner.sales
group by product_id
order by count(*) desc
limit 1
),

ordered_count as 
(
  select customer_id, product_id , count(*) as no_order
from dannys_diner.sales
where product_id in (select product_id from most_ordered )
group by customer_id, product_id

)

select o.customer_id, m.product_name, o.no_order
from ordered_count o
join dannys_diner.menu m on m.product_id = o.product_id
order by o.customer_id
````

#### 🔖 Setps
- First we find most ordered product, than limited it to 1 row
- Using subquery and CTE we found how many times each customer ordered most ordered product
- 
#### 🎯Insights
<img width="706" alt="Screenshot 2023-11-07 125836" src="https://github.com/GauravvThakurr/SQL-projects/assets/141028751/03fbc385-fb95-466f-9a63-190b2cc39cc5">


- Customer_id A placed 3 ramen order
- Customer_id B placed 2 ramen order
- Customer_id c placed 3 ramen order

***

**5.Which item was the most popular for each customer?**

````sql
with rankedorder as (

select customer_id, product_id, count(*) as no_order,
dense_rank() over (partition by customer_id order by count(*) desc) as rn
from dannys_diner.sales
group by customer_id, product_id

)
select r.customer_id, m.product_name,r.no_order from rankedorder r
join dannys_diner.menu m on m.product_id = r.product_id
where r.rn = 1
order by r.no_order desc
````

#### 🔖 Setps
- We found which product was orderd how many times using `dense_rank()` then enclosed in cte named `rankedorder`
- In main query we join `Menu` table for to pull names of product and used `r.rn = 1` to pull only the top order from each customer
  
#### 🎯Insights
<img width="595" alt="Screenshot 2023-11-07 132041" src="https://github.com/GauravvThakurr/SQL-projects/assets/141028751/0bc724ba-c7d6-480c-b9a2-4d6fd6174a60">


***

**6.Which item was purchased first by the customer after they became a member?**

