## **Retrieving Data From Multiple Tables**

Many times we might need to retrieve data from multiple tables. In times like this we have JOINS for our rescue.

### Inner Joins
Inner keyword is optional. By this 2 tables will be joined.  
Use ON phrase to tell sql on which basis you want to join the tables.  
We can use alias just after the table name. or use the AS keyword for alias (It is optional)
```
-- 
SELECT order_id, o.customer_id, first_name, last_name
FROM orders o
INNER JOIN customers c
		ON o.customer_id = c.customer_id
--
SELECT order_id, oi.product_id, name, quantity, oi.unit_price
FROM order_items oi
JOIN products p
ON oi.product_id = p.product_id 
```
Once we declare the alias then we cannot use the full name of the table.  
In the select statement, table name should be used as a prefix where the column name is ambiguous (exist in both the columns)

### Joining across Databases
We need to prefix the table with the database that does not belong to the current database.  
```
SELECT *
FROM order_items oi
JOIN sql_inventory.products p
ON oi.product_id = p.product_id
```

### Self Join
```
USE sql_hr;

SELECT e.employee_id, e.first_name, m.first_name AS manager
FROM employees e 
JOIN employees m
	ON e.reports_to = m.employee_id
```

### Joining Multiple Tables
```
USE sql_store;

SELECT o.order_id, o.order_date,c.first_name, c.last_name, os.name AS status
FROM orders o
JOIN customers c
	ON o.customer_id = c.customer_id
JOIN order_statuses os
	ON o.status = os.order_status_id
```

#### Exercise
From sql_invoicing DB, Join payments, payment_methods and  

### Compound Join Conditions
Till now we have been using single column to identify a row in the table.   
When we have composite primary key in the table then we join it using Compound Join. A composite primary contains more than one column 
```
USE sql_store;

SELECT *
FROM order_items oi
JOIN order_item_notes oin 
ON oi.product_id = oin.product_id AND oi.order_id = oin.order_id
```

### Implicit Join Syntax
```
SELECT *
FROM orders o, customers c
WHERE o.customer_id = c.customer_id;
```
Be aware to use WHERE clause in this case otherwise we'll get cross join. It is recommended to use JOIN clause to join tables and avoid implicit Join syntax.

### Outer Join
Outer keyword is optional. Outer Joins are of 2 types 
1. Left Outer Join - see all the columns of left table even if they don't satisfy the ON condition
2. Right Outer Join - see all the columns of right table even if they don't satisfy the ON condition
```
SELECT p.product_id, p.name, oi.quantity
FROM products p
LEFT JOIN order_items oi
ON p.product_id = oi.product_id
ORDER BY product_id 
```

### Outer Join between multiple tables
As a best practice use left join only to join multiple tables.
```
SELECT o.order_date, o.order_id, c.first_name, sh.name AS shipper, os.name AS status
FROM orders o
JOIN customers c
	ON o.customer_id = c.customer_id
LEFT JOIN shippers sh
	ON o.shipper_id = sh.shipper_id
LEFT JOIN order_statuses os
	ON o.status = os.order_status_id
ORDER BY status
```

### Self Outer Joins
```
USE sql_hr;

SELECT e.first_name AS empoyee_name, m.first_name AS manager_name
FROM employees e
LEFT JOIN employees m
	ON e.reports_to = m.employee_id
```

### The USING Clause
In the ON Clause if the column name is same then we can use USING Clause. It can be used in both Inner and Outer Join
```
USE sql_store;

SELECT o.order_date, o.order_id, c.first_name, sh.name AS shipper
FROM orders o
JOIN customers c
	-- ON o.customer_id = c.customer_id same as the below line
    USING (customer_id)
LEFT JOIN shippers sh
	-- ON o.shipper_id = sh.shipper_id
    USING (shipper_id)
---------------------------------------------------------------
USE sql_store;

SELECT *
FROM order_items oi
JOIN order_item_notes oin  
-- ON oi.product_id = oin.product_id AND oi.order_id = oin.order_id
USING (order_id, product_id)
```

### Natural Joins
*[Not Recommended]*  
We don't mention the column name rather SQL takes the common column itself.  
```
USE sql_store;

SELECT *
FROM customers
NATURAL JOIN orders
```
Because we don't have control over the common column it is discouraged to use natural join.

### Cross Joins
Join every record from the first table to every record of the second table.  
```
-- Explicit Syntax

SELECT 
	c.first_name AS customer,
    p.name AS product
FROM customers c
CROSS JOIN products p
ORDER BY c.first_name

-- Implicit Syntax

SELECT 
	c.first_name AS customer,
    p.name AS product
FROM customers c, products p
ORDER BY c.first_name
```

### Unions
Combining rows from multiple tables. With UNION we can combine rows of different queries. This query can be against the same table or with the multiple tables.
```
SELECT 
	order_id,
    order_date,
    'Active' AS status
FROM orders
WHERE order_date >= '2019-01-01'
UNION
SELECT 
	order_id,
    order_date,
    'Archived' AS status
FROM orders
WHERE order_date < '2019-01-01';
```
But the number of columns from both the queries should be same. Name of the column is based on the first query result.