## 📌 Introduction

Danny is a big fan of Japanese food, and in early 2021 he decided to open a small restaurant, Danny’s Diner, serving his three favorite dishes: sushi, curry, and ramen.
The diner has been running for a few months and has collected some basic sales and customer data. However, Danny is unsure how to interpret this data to improve his business.

## 📌 Problem Statement

Danny wants to analyze the data to gain insights into his customers’ behavior, such as:
- Their visiting patterns
- How much they have spent overall
- Which menu items are the most popular

By answering these questions, Danny hopes to:
- Build stronger relationships with his loyal customers through personalized experiences.
- Decide whether to expand the customer loyalty program.
- Generate easy-to-use datasets for his team so they don’t always rely on SQL for quick analysis.

## Dataset

The case study is centered around three tables:

- sales – Records of customer orders
- menu – Menu items and their prices
- members – Customers who joined the loyalty program

## Entity Relationship Diagram

<img width="728" height="380" alt="image" src="https://github.com/user-attachments/assets/bbe63ba3-17b8-4d90-a0ec-2eddb0ee133b" />

## Case Study Questions and Answers

- What is the total amount each customer spent at the restaurant?

```sql
select s.customer_id, sum(m.price) as total_spent
from sales s
join menu m on m.product_id = s.product_id
group by s.customer_id
;
```

<img width="201" height="93" alt="image" src="https://github.com/user-attachments/assets/8c9e61a2-d531-4023-b7be-9802069cf104" />

- How many days has each customer visited the restaurant?

```sql
select customer_id,
		count(distinct order_date) as days_visited
from sales
group by customer_id
;
```

<img width="206" height="95" alt="image" src="https://github.com/user-attachments/assets/41ab9713-9dee-46f9-85a1-d22d95a652d5" />
<br>
-  What was the first item from the menu purchased by each customer?

```sql
select * from(
select s.customer_id, 
		m.product_name,
        s.order_date,
        rank() over(partition by s.customer_id order by s.order_date) as rank_1
from sales s
join menu m on m.product_id = s.product_id
) as temp
 where rank_1 = 1
;
```
<img width="367" height="141" alt="image" src="https://github.com/user-attachments/assets/8408cf87-9714-4d8a-979f-caeca9160574" />

