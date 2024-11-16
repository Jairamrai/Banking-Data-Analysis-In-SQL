


1: Write a query to list all customers who haven't made any transactions in the last year.

```bash
SELECT c.customer_id, c.first_name, c.last_name
FROM customers c
inner JOIN accounts a ON c.customer_id = a.customer_id
inner JOIN transactions t ON a.account_number = t.account_number
WHERE t.transaction_date IS NULL OR t.transaction_date < DATE_SUB(CURDATE(), INTERVAL 1 YEAR)
GROUP BY c.customer_id, c.first_name, c.last_name
order by c.customer_id;
```

2: Summarize the total transaction amount per account per month.

```bash
SELECT t.account_number, YEAR (t.transaction_date) AS year, MONTH(t.transaction_date) AS month, SUM (t.amount) AS total_amount FROM transactions t
GROUP BY t.account_number, YEAR(t.transaction_date), MONTH(t.transaction_date)
order by YEAR(t.transaction_date), MONTH(t.transaction_date);
```

3: Rank branches based on the total amount of deposits made in the last quarter.
```bash
SELECT b.branch_id, ROUND (SUM(CASE WHEN t.transaction_type = 'deposit' THEN t.amount ELSE 0 END), 2) AS totaldeposit,
DENSE_RANK() OVER (ORDER BY SUM(CASE WHEN t.transaction_type = 'deposit' THEN t.amount ELSE 0 END) DESC) AS branch_rank
FROM Transactions AS t
JOIN Accounts AS a
ON t.account_number=a.account_number
JOIN branches AS b
ON b.branch_id=a.branch_id
WHERE t.transaction_date>= DATE_SUB(CURDATE(), INTERVAL 3 MONTH)
GROUP BY b.branch_id
ORDER BY branch_rank asc;
```

4: Find the name of the customer who has deposited the highest amount.
```bash
select c.customer_id, c.First_name, c.Last_name, sum (t.amount) as amount
from customers as c join
accounts as a on
c.customer_id = a.customer_id
join transactions as T on
t.account_number a.account_number
where Transaction_type = "Deposit"
group by c.customer_id,c.First_name, c.last_name
order by sum(t.amount) desc limit 1;
```

5: Identify any accounts that have made more than two transactions in a single day, which could indicate fraudulent activity.

```bash
select account_number, day(transaction_date) as day_of_transaction, count (transaction_id) as transaction_count
from transactions
group by account_number, day (transaction_date)
having transaction_count > 2
order by day(transaction_date);
```

6: Calculate the average number of transactions per customer per account per month over the last year

```bash
SELECT a.customer_id, a.account_number, YEAR (t.transaction_date) AS transaction_year, MONTH(t.transaction_date) AS transaction_month,
  COUNT(t.transaction_id) AS transaction_count,
  COUNT(t.transaction_id) / 12 AS avg_transactions_per_month_last_year
FROM transactions t
JOIN accounts a ON t.account_number = a.account_number
WHERE t.transaction_date >= DATE_SUB(CURDATE(), INTERVAL 1 YEAR)
GROUP BY a.customer_id, a.account_number, transaction_year, transaction_month
ORDER BY a.customer_id, a.account_number, transaction_year, transaction_month;
```

7: Write a query to find the daily transaction volume (total amount of all transactions) for the past month.

```bash
SELECT date(t.transaction_date) as transaction_date, round (SUM(t.amount), 2) AS daily_volume
FROM transactions t
WHERE t.transaction_date >= DATE_SUB(CURDATE(), INTERVAL 1 MONTH)
GROUP BY date(t.transaction_date)
order by date(t.transaction_date);
```


8: Calculate the total transaction amount performed by each age group in the past year.
(Age groups: 0-17, 18-30, 31-60, 60+)

```bash
SELECT
CASE
WHEN TIMESTAMPDIFF(YEAR, c.date_of_birth, CURDATE()) BETWEEN AND 17 THEN '0-17'
WHEN TIMESTAMPDIFF(YEAR, c.date_of_birth,CURDATE()) BETWEEN 18 AND 30 THEN '18-30
WHEN TIMESTAMPDIFF (YEAR, c.date_of_birth,CURDATE()) BETWEEN 31 AND 60 THEN '31-60'
ELSE '60+'
END AS age_group,
round(SUM(t.amount), 3) AS total_transaction_amount
FROM customers as c
INNER JOIN accounts a ON a.customer_id = c.customer_id
INNER JOIN Transactions as t on a.account_number = t.account_number
WHERE t.transaction_date >= DATE_SUB(CURDATE(), INTERVAL 1 YEAR)
GROUP BY age_group ORDER BY age_group;
```


9: Find the branch with the highest average account balance.

```bash
SELECT a.branch_id, AVG(a.balance) AS avg_balance
FROM accounts as a
JOIN branches as b ON a.branch_id= b.branch_id
GROUP BY a.branch_id
ORDER BY avg_balance DESC LIMIT 1;
```

10: Calculate the average balance per customer at the end of each month in the last year.

```bash
select c.customer_id, c.first_name, c.last_name,
date_format(a.created_at, '%Y-%m') as month_end,
round(avg(a.balance), 2) as avg_balance from customers as c
inner join accounts as a
on c.customer_id = a.customer_id
where a.created_at >= date_sub(curdate(), interval 1 year)
group by c.customer_id,c.first_name, c.last_name, month_end order by c.customer_id;
```





