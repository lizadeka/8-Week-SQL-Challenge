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

-- 1. What is the total amount each customer spent at the restaurant?

select s.customer_id, sum(m.price) as total_spent
from sales s
join menu m on m.product_id = s.product_id
group by s.customer_id
;

<img width="201" height="93" alt="image" src="https://github.com/user-attachments/assets/8c9e61a2-d531-4023-b7be-9802069cf104" />



