## **Summarizing Data**
### Aggregate Functions
Functions are piece of code that we can reuse. MySQL comes with many built-in functions, Some of these functions are called aggregate functions. These functions only work on non null value. So, If the column contains null value it will not include it. By default all of this function takes duplicate values. If we don't want duplicates in to considertaion then we can use the DISTINCT keyword.
1. MAX()
2. MIN()
3. AVG()
4. SUM()
5. COUNT()  
```
SELECT
	MAX(invoice_total) AS highest,
    MIN(invoice_total) AS lowest,
    AVG(invoice_total) AS average,
    SUM(invoice_total) AS sum,
    -- COUNT(*) AS total_rows	-- does allow duplicates in consideration
    COUNT(DISTINCT client_id) AS total_clients
FROM invoices
WHERE invoice_date > '2019-07-01';
```

```
-- Write a query on invoices table

SELECT
	'First Half of the Year' AS date_range,
	SUM(invoice_total) AS total_sales,
    SUM(payment_total) AS total_payments,
    SUM(invoice_total - payment_total) AS what_we_expect
FROM invoices
WHERE invoice_date BETWEEN '2019-01-01' AND '2019-06-01'
UNION
SELECT
	'Second Half of the Year' AS date_range,
	SUM(invoice_total) AS total_sales,
    SUM(payment_total) AS total_payments,
    SUM(invoice_total - payment_total) AS what_we_expect
FROM invoices
WHERE invoice_date BETWEEN '2019-07-01' AND '2019-12-31'
UNION
SELECT
	'Total' AS date_range,
	SUM(invoice_total) AS total_sales,
    SUM(payment_total) AS total_payments,
    SUM(invoice_total - payment_total) AS what_we_expect
FROM invoices
WHERE invoice_date BETWEEN '2019-01-01' AND '2019-12-31';
```

### The GROUP BY Clause
**Order of the clauses matters**
Grouping based on a single column
```
SELECT 
	client_id,
    SUM(invoice_total) AS total_sales
FROM invoices
WHERE invoice_date >= '2019-07-01'
GROUP BY client_id
ORDER BY total_sales DESC;
```

Grouping based on multiple columns. We get 1 record for each payment_method and date combination.

```
SELECT date, name AS payment_method, SUM(amount) AS total_payment
FROM payments p
LEFT JOIN payment_methods pm
ON p.payment_method = pm.payment_method_id
GROUP BY name, date
ORDER BY date
```

### The HAVING Clause
WHERE Clause is used to filter out data before grouping whereas
HAVING Clause is used to filter out data after grouping. Columns used in Having clause should be included in the SELECT clause.
```
-- Get the customers 
-- 		- located in Virginia
-- 		- who have spent more than $100

USE sql_store;

SELECT 
	c.customer_id,
    c.first_name,
    c.last_name,
    SUM(quantity * unit_price) AS spent
FROM customers c
JOIN orders o
ON c.customer_id = o.customer_id
JOIN order_items oi
ON o.order_id = oi.order_id
WHERE state = 'VA'
GROUP BY 
	c.customer_id,
    c.first_name,
    c.last_name
HAVING spent > 100;
```

### The ROLLUP Operator
Only available in MySQL. It is not a part of Standard SQL. ROLLUP operator calculates the summary for each group. It only applies to column that aggregate value.
When you group by multiple column you see summary value of each group as well as the entire result set
```
SELECT pm.name AS payment_method, SUM(amount) AS total
FROM payments p
JOIN payment_methods pm
WHERE p.payment_method = pm.payment_method_id
GROUP BY pm.name WITH ROLLUP
```