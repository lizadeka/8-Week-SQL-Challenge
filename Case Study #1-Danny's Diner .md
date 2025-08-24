<img width="715" height="716" alt="image" src="https://github.com/user-attachments/assets/a7f40169-693c-4042-b09f-990459f1024e" />

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

---

## Entity Relationship Diagram

<img width="728" height="380" alt="image" src="https://github.com/user-attachments/assets/bbe63ba3-17b8-4d90-a0ec-2eddb0ee133b" />

---

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
<br>

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

- What was the first item from the menu purchased by each customer?

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
<br>

- What is the most purchased item on the menu and how many times was it purchased by all customers?

```sql
select m.product_name,
	count(s.order_date) as times_ordered
from sales s
join menu m on m.product_id = s.product_id
group by m.product_name
order by times_ordered desc
limit 1
;
```

<img width="228" height="61" alt="image" src="https://github.com/user-attachments/assets/51da602c-30ec-4885-ac97-a9357aefe9b8" />
<br>

- Which item was the most popular for each customer?

```sql
with cte as
(select s.customer_id,
		m.product_name,
		count(s.order_date) as times_ordered,
		rank() over(partition by s.customer_id order by count(s.order_date) desc) as rank_1
from sales s
join menu m on m.product_id = s.product_id
group by s.customer_id, m.product_name )

select cte.customer_id,
		cte.product_name,
        cte.times_ordered
from cte
where rank_1 = 1
;
```

<img width="320" height="145" alt="image" src="https://github.com/user-attachments/assets/859cb833-7288-45ff-9e15-4d138b0b753a" />
<br>

- Which item was purchased first by the customer after they became a member?

```sql
with cte as
(select s.customer_id,
		m.product_name,
		count(s.order_date) as times_ordered,
		rank() over(partition by s.customer_id order by count(s.order_date) desc) as first_i
from sales s
join menu m on m.product_id = s.product_id
join members me on me.customer_id = s.customer_id
where me.join_date >= s.order_date
group by s.customer_id, m.product_name 
)
select cte.customer_id,
		cte.product_name
from cte
where first_i = 1
;
```

<img width="240" height="77" alt="image" src="https://github.com/user-attachments/assets/c4eeeede-4ba0-496e-ab6e-d83be87ca402" />
<br>

- Which item was purchased just before the customer became a member?

```sql
with cte as
(select s.customer_id,
		m.product_name,
        order_date,
		rank() over(partition by s.customer_id order by order_date desc) as f_item
from sales s
join menu m on m.product_id = s.product_id
join members me on me.customer_id = s.customer_id
where s.order_date < me.join_date
-- group by s.customer_id, m.product_name 
)
select 
	cte.customer_id,
	cte.product_name
from cte
where f_item=1
;
```
<img width="218" height="111" alt="image" src="https://github.com/user-attachments/assets/d9811d69-9679-4c9d-8416-679b317bcd93" />
<br>

-  What is the total items and amount spent for each member before they became a member?

```sql
with before_member as
(select s.customer_id,
		count(s.order_date) as times_ordered,
        sum(m.price) as total_spent
		-- rank() over(partition by s.customer_id order by sum(m.price) desc) as first_i
from sales s
join menu m on m.product_id = s.product_id
join members me on me.customer_id = s.customer_id
where me.join_date > s.order_date
group by s.customer_id
)
select * from before_member;
```

<img width="297" height="87" alt="image" src="https://github.com/user-attachments/assets/9933eb4f-2ff7-433c-9584-5ce032f5d10c" />
<br>

- If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?

```sql
select s.customer_id,
        -- m.price,
        sum(case
			when m.product_name="sushi" then m.price * 10 * 2
            else m.price * 10
            end )as points
from sales s
join menu m on m.product_id = s.product_id
group by s.customer_id
order by points
;
```

<img width="162" height="108" alt="image" src="https://github.com/user-attachments/assets/25dc5ea4-bf61-4933-a1cc-2bd84bbdf2de" />
<br>

- In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?

```sql
select s.customer_id,
        -- m.price,
        sum(case
			when s.order_date between me.join_date and DATE_ADD(me.join_date, INTERVAL 6 DAY) then m.price * 2 * 10 
			when m.product_name="sushi" then m.price * 10 * 2
            else m.price * 10
            end )as points
from sales s
join menu m on m.product_id = s.product_id
join members me on me.customer_id = s.customer_id
where s.order_date <= '2021-01-31'
group by s.customer_id
order by points desc
;
```

<img width="181" height="82" alt="image" src="https://github.com/user-attachments/assets/c4fef7d4-f54d-410d-92ba-7aedb913e1b6" />


## 📌 Key Insights

- Customer spending varies a lot - Danny can expand the customer loyalty program.
- Tracking how often each customers visit can help Danny spot regular ones.
- The first item ordered by each customer can be promoted more.
- The most popular item was ramen and identifying it can help in promotions and pricing decisions.
- Identifying each customer's favourite food item can help in offering discounts on days with lower sales.
- Comparing purchases before and after customers joined the loyalty program helps measure its impact.
- A simple point system encourages spending a little more and can encourage repeat visits.
- Offering double points during the first 7 days after sign-up creates excitement and boosts early engagement.
