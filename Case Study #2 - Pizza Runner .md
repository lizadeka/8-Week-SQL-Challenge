# Case Study #2 - Pizza Runner
<img width="415" height="416" alt="2" src="https://github.com/user-attachments/assets/915c36c0-29cd-4a93-bbc7-54a202e434da" />

## 📌 Introduction

Danny, a pizza enthusiast, wanted to start a pizza delivery business. However, instead of managing everything traditionally, he recruited a group of runners who would pick up pizzas and deliver them to customers. 
Danny stored all order, customer, and runner information in a relational database.
Now, Danny wants to analyze his business operations using SQL to answer key questions about customers, orders, deliveries, and performance.

## 🧾 Problem Statement

Danny wants to gain clear insights into his growing pizza business, including:

Menu Insights → Which pizzas and extras are most popular?

Runner Performance → How efficient is each runner in terms of delivery success and timing?

Operational Optimization → Where can delivery operations be improved to reduce delays and failed deliveries?

The goal is to clean, transform, and analyze the data using SQL, enabling Pizza Runner to make better business decisions and optimize logistics.

## Dataset

The case study is centered around three tables:

- runners – Information about each delivery runner and their registration date

- customer_orders – Records of pizza orders placed by customers, including extras and exclusions

- runner_orders – Delivery data with timestamps, pickup/drop info, and cancellations

- pizza_names – Mapping between pizza IDs and their names

- pizza_recipes – Recipe details showing which toppings are used in each pizza

- pizza_toppings – Full list of available toppings and their unique IDs

---

## Entity Relationship Diagram

<img width="428" height="380" alt="image" src="https://github.com/user-attachments/assets/0751047a-9eb5-429e-b84d-8a02feed546b" />

---

## Case Study Questions and Answers

### A. Pizza Metrics 🍕

- How many pizzas were ordered?
```
select count(pizza_id) as pizzas_ordered
from customer_orders;
```
<img width="147" height="77" alt="image" src="https://github.com/user-attachments/assets/41121ff8-3be6-4367-9ce7-ac072e65333b" />

- How many unique customer orders were made?
```
select count(distinct order_id) as unique_customer_orders
from runner_orders;
```
<img width="182" height="70" alt="image" src="https://github.com/user-attachments/assets/cd60e8c6-b707-4e5f-bcb6-b612381ea631" />

- How many successful orders were delivered by each runner?
```
select runner_id,
		count(distinct order_id) as successful_orders
from runner_orders
where pickup_time is not null
group by runner_id;
```
<img width="255" height="98" alt="image" src="https://github.com/user-attachments/assets/2321e025-3970-4fbd-b755-9ed36cff8271" />

- How many of each type of pizza was delivered?
```
select pn.pizza_id,
		pn.pizza_name,
        count(*) as pizzas_delivered
from pizza_names pn
join customer_orders co on co.pizza_id = pn.pizza_id
join runner_orders ro on ro.order_id = co.order_id
where ro.pickup_time is not null
group by pn.pizza_id, pn.pizza_name
;
```
<img width="290" height="97" alt="image" src="https://github.com/user-attachments/assets/b86158e1-b1cd-42a1-ba42-42a34c5868f8" />

- How many Vegetarian and Meatlovers were ordered by each customer?
```
select co.customer_id,
		pn.pizza_name,
		count(co.pizza_id) as total_pizzas
from customer_orders co
join pizza_names pn on pn.pizza_id = co.pizza_id
group by co.customer_id, pn.pizza_name
order by pn.pizza_name
;
```
<img width="286" height="202" alt="image" src="https://github.com/user-attachments/assets/c86785cb-2aac-40aa-8a70-551a4166e3cc" />

- What was the maximum number of pizzas delivered in a single order?
```
select ro.order_id,
		count(co.pizza_id) as total_pizzas
from runner_orders ro
join customer_orders co on co.order_id = ro.order_id
group by ro.order_id
order by total_pizzas desc
limit 1
;
```
<img width="182" height="75" alt="image" src="https://github.com/user-attachments/assets/3c3409ac-412a-4921-9255-5d5378e90da4" />

- For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
```
select co.customer_id,
		sum(case 
		when ((co.exclusions is not null and co.exclusions <> 'null'and length(co.exclusions) > 0) or 
				(co.extras is not null and co.extras <> 'null' and length(co.extras) > 0))
            then 1
			else 0
			end) as changes,
		sum(case 
		when ((co.exclusions is not null and co.exclusions <> 'null'and length(co.exclusions) > 0) or 
				(co.extras is not null and co.extras <> 'null' and length(co.extras) > 0)) 
			then 0
			else 1
			end) as no_changes
from customer_orders co
join runner_orders ro on ro.order_id = co.order_id
where ro.cancellation is null   -- only delivered pizzas
group by customer_id
;
```
<img width="283" height="152" alt="image" src="https://github.com/user-attachments/assets/6f594f9c-b6ad-433d-a706-eaea7f5405c1" />

- How many pizzas were delivered that had both exclusions and extras?
```
select count(co.pizza_id) as total_pizzas_with_extras_and_exclusions
from customer_orders co
join runner_orders ro on ro.order_id = co.order_id
where ro.pickup_time is not null and (co.exclusions is not null and length(exclusions) > 0)
and (co.extras is not null and length(extras) > 0)
;
```
<img width="281" height="81" alt="image" src="https://github.com/user-attachments/assets/4492fd46-9b8f-473b-b66f-9dc184720391" />

- What was the total volume of pizzas ordered for each hour of the day?
```
select count(pizza_id) as total_pizzas,
		hour(order_time) as hour
from customer_orders
group by hour(order_time)
order by  hour(order_time)
;
```
<img width="147" height="166" alt="image" src="https://github.com/user-attachments/assets/c998e59d-ce4e-4589-bbd3-eb6d0e50640d" />

- What was the volume of orders for each day of the week?
```
select 
		dayname(order_time) as day_of_week,
        dayofweek(order_time) AS day_number,
        count(pizza_id) as pizzas_ordered
from customer_orders
group by dayname(order_time), dayofweek(order_time)
order by dayofweek(order_time)
;
```
<img width="332" height="127" alt="image" src="https://github.com/user-attachments/assets/3b402fc7-8555-41ca-a351-61d56ba630a4" />

### B. Runner and Customer Experience 💁‍♂️🍕

## 📌 Key Insights
