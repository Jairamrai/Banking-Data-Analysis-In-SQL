


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


