## **Writing Complex Queries**

### Sub queries
> Sub queries are SELECT Statement within other SQL Statement.

```
-- In sql_hr database:
-- 		Find employees who earns more than average

USE sql_hr;

SELECT * FROM employees
WHERE salary > (
	SELECT AVG(salary)
	FROM employees
);
```

### The IN Operator
```
-- Find the clients without invoices

USE sql_invoicing;

SELECT * FROM clients
WHERE client_id NOT IN (
	SELECT DISTINCT client_id 
	FROM sql_invoicing.invoices
);
```

### Sub queries VS Joins
We can write sub queries as join and vice-versa. The above query can be written as 
```
SELECT * 
FROM clients
LEFT JOIN invoices USING (client_id)
WHERE invoice_id IS NULL;
```

### The ALL Keyword
```
-- Select invoices larger than all invoices of client 3

SELECT *
FROM invoices
WHERE invoice_total > ALL (
	SELECT invoice_total
    FROM invoices
    WHERE client_id = 3
)
```

### The ANY Keyword
Like 'ALL' Keyword we also have 'ANY' and 'SOME' keyword
```
-- select clients with at least 2 invoices

SELECT *
FROM clients
WHERE client_id IN (
	SELECT client_id
	FROM invoices
	GROUP BY client_id
	HAVING COUNT(*) >= 2
);

SELECT *
FROM clients
WHERE client_id = ANY (
	SELECT client_id
	FROM invoices
	GROUP BY client_id
	HAVING COUNT(*) >= 2
);
```

### Correlated Sub queries 
The inner sub query is dependent on the outer query
```
-- Get the invoices that are larger than the 
-- 		client's average invoice amount

USE sql_invoicing;

SELECT * 
FROM invoices i
WHERE invoice_total > (
	SELECT AVG(invoice_total)
    FROM invoices
    WHERE client_id = i.client_id
);
```

### The EXIST Operator
It is better to use EXISTS rather than IN in cases where there are many inputs to IN because EXISTS only returns true or false and not the entire result.
```
-- Find the products that have never been ordered

USE sql_store;

SELECT * 
FROM products p
WHERE NOT EXISTS (
	SELECT *
    FROM order_items oi
    WHERE oi.product_id = p.product_id
);
```

### Sub queries in the SELECT Clause
```
USE sql_invoicing;

SELECT 
	client_id,
    name,
    (SELECT SUM(invoice_total)
        FROM invoices
        WHERE client_id = c.client_id
    ) AS total_sales,
    (SELECT AVG(invoice_total) FROM invoices) AS average,
    (SELECT total_sales - average) AS difference
FROM clients c;


SELECT SUM(invoice_total)
FROM invoices
GROUP BY client_id;
```

### Sub queries in the FROM Clause
We can also write sub queries in FROM clause but reserve it only for the simple queries
```
USE sql_invoicing;

SELECT * 
FROM (
	SELECT 
		client_id,
		name,
		(SELECT SUM(invoice_total)
			FROM invoices
			WHERE client_id = c.client_id
		) AS total_sales,
		(SELECT AVG(invoice_total) FROM invoices) AS average,
		(SELECT total_sales - average) AS difference
	FROM clients c
) AS sales_summary
WHERE total_sales IS NOT NULL
```