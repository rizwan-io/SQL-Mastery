# SQL Mastery (Course by Mosh Hamedani)
<br>

## **Getting Started**
<br>

### What is a Database?
> A Database is a collection of data stored in a format that can easily be accessed. 

In order to manage databases we use DBMS (Database management system).  
US <-> DBMS <-> DB

DBMS are classified into 2 categories:
1. Relational (RDBMS) -  
    Data is stored in tables that are related to each other.  
    SQL is the language that we use to query and manage this DB.  
    Some of the most popular RDBMSs are - 
    1. MySQL
    2. SQL Server
    3. Oracle & many more  
2. Non-Relational (NoSQL) - 
    NoSQL doesn't understand SQL. They have their own query language.

All of the RDBMS uses the standard SQL with some of their added tweaks.  
SQL (Structured Query Language) = SQUEL (Structured Query English Language).

### Installing MySQL on Mac
Download the MySQL Server from https://dev.mysql.com/downloads/mysql/  
Download the MySQL Workbench (GUI tool) from https://dev.mysql.com/downloads/workbench/

### Creating the DBs
Import the SQL script and run it.  
Each DB has 4 objects -
1. Tables
2. Views - Virtual tables based on the result-set of an SQL statement. 
3. Stored Procedure
4. Functions

## **Retrieving Data From a Single Table**
<br>

### The SELECT Clause
```
USE sql_store;

SELECT * 
FROM customers
-- WHERE customer_id = 1
ORDER BY first_name;
```

It is not necessary to 
1. use UPPERCASE notations for clause but it is a standard.
2. Write each clause on different lines but is a good practice.

Comments are marked by -- in front of the statement.  
Order of the clauses matters.

### SELECT Clause in Detail

```
SELECT 
	first_name, 
    last_name, 
    points,
    (points + 10) * 100 AS 'discount factor'
FROM customers;
```

The order in which the columns are mentioned in the select clause will be displayed in the result.  
AS can be used for alias that would be the name of the column.  

```
SELECT DISTINCT state
FROM customers;
```
Use DISTINCT keyword for removing the duplicates from the result.

### WHERE Clause
Using the WHERE clause we can filter out the data.  
We can use the comparison operators >, >=, <, <=, =, != OR <>
```
SELECT *
FROM orders
WHERE order_date >= '2019-01-01';
```
Get the orders placed in the current year.  
The solution is not complete but it is valid.

### The AND, OR and NOT Operator
AND takes more precedence over OR. They are the logical operator used to concatenate different conditions.

### The IN Operator
```
SELECT *
FROM products
WHERE quantity_in_stock IN (49, 38, 72);
```

### The BETWEEN Operator
```
SELECT *
FROM customers
WHERE birth_date BETWEEN '1990-01-01' AND '2000-01-01'
```

### The LIKE Operator
```
SELECT *
FROM customers
WHERE address LIKE '%TRAIL%' OR address LIKE '%AVENUE%'
```
The string matcher -  
1. % - any number of characters
2. _ &nbsp;- a single character

### The REGEXP Operator
This is the basic regular expression  
^  - Beginning of the String ex: '^field'  
\$ - Ending of the String ex: 'james$'  
[] - Contains any of the mentioned characters ex: '[gie]f'  
| &nbsp; - To Check for this or that   
```
-- Get the customers whose
-- 		First name is ELKA or AMBUR
SELECT *
FROM customers
WHERE first_name REGEXP 'ELKA|AMBUR';

--      Last name ends with EY or ON
SELECT *
FROM customers
WHERE last_name REGEXP 'EY$|ON$';

--      Last name start with MY or contains SE
SELECT *
FROM customers
WHERE last_name REGEXP '^MY|SE';

--      Last name contains B followed by R or U
SELECT *
FROM customers
WHERE last_name REGEXP 'B[RU]';

```

### IS NULL Operator
```
-- Get the orders that are not shipped
SELECT *
FROM orders
WHERE shipped_date IS NULL
```

### ORDER BY Operator
```
SELECT *
FROM order_items
WHERE order_id = 2 
ORDER BY quantity * unit_price DESC;
```
1. MySQL can take argument in ORDER BY that is not mentioned in SELECT but other DBMS Might not take it.  
2. Use DESC to sort in descending order.  
3. We can sort using more than 1 column. In this items will be sorted based on the first mentioned column and then if there is a clash they will be sorted based on the second mentioned column.  

### The LIMIT Clause
```
SELECT *
FROM customers
ORDER BY points DESC
LIMIT 3 
```
If use ```LIMIT 6, 3``` It will  select customers 7, 8, 9 the first argument is known as the OFFSET. 

## **Retrieving Data From Multiple Tables**
### Inner Joins
Inner keyword is optional. By this 2 tables will be joined.  
Use ON phrase to tell sql on which basis you want to join the tables.  
Can use alias just after the table name.
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
table name should be used as a prefix where the column name is ambiguous (exist in both the columns)

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
1. Left Outer Join - see all the columns of left table even if the ON condition is false
2. Right Outer Join - see all the columns of right table even if the ON condition is false
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
We don't mention the common column rather SQL takes the common column itself.  
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
But the number of columns from both the queries should be same. Name of the column is dependent on the first query result.

## **Inserting, Updating and Deleting Data**

### Column Attributes
1. Datatype -  
    1. INT(11) - Integer of max 11 digits
    2. VARCHAR(50) - Variable character of 50. Unused space will not be used
    3. CHAR(50) - Character of 50. Unused space will be filled with blanks
    4. DATE - To store dates
2. Columns attributes - 
    1. PK - Primary Key
    2. NN - Not Null - Should provide a default value if a column can be NULL
    3. AI - Auto Increment

### Inserting a Single Row
```
INSERT INTO customers
VALUES (DEFAULT, 'John', 'Smith', '1999-09-09', DEFAULT, 'address', 'city', 'CA', DEFAULT) 

Order of the mentioned column does not matter
INSERT INTO customers (first_name, last_name, birth_date, address, city, state)
VALUES ('John', 'Smith', '1999-09-09', 'address', 'city', 'CA') 
```

### Inserting Multiple Rows
```
INSERT INTO shippers (name)
VALUE ('Shippers1'),
	  ('Shippers2'),
      ('Shippers3')
```

### Inserting Hierarchical Rows
Inserting data in multiple table  
```
INSERT INTO orders (customer_id, order_date, status)
VALUES (11, '2022-01-01', 1);

INSERT INTO order_items
VALUES (LAST_INSERT_ID(), '2', '3', '2.95'),
       (LAST_INSERT_ID(), '2', '3', '2.95');
```

### Creating a Copy of a Table
```
-- Instead of writing many insert statements but, it won't mark row as Primary
CREATE TABLE order_items_archived AS
SELECT * FROM order_items;

-- Inserting using sub-queries
INSERT INTO orders_archived
SELECT * FROM orders
WHERE order_date < '2019-01-01';
```
Exercise 
```
CREATE TABLE invoices_archives AS
SELECT invoice_id, number, name AS client_name, invoice_total, payment_total, invoice_date, due_date, payment_date
FROM invoices i JOIN clients c
ON i.client_id = c.client_id
WHERE payment_date IS NOT NULL;
```
### Updating a Single Row
```
UPDATE invoices
SET payment_total = 10, payment_date = '2022-08-16'
WHERE invoice_id = 1;

UPDATE invoices
SET 
	payment_total = invoice_total * 0.5,
    payment_date = due_date
WHERE invoice_id = 1;
-- The value will be truncated
```

### Updating Multiple Rows
Syntax for updating multiple rows is similar as above. Only thing is the WHERE condition needs to be more generic
```
USE sql_invoicing;

UPDATE invoices
SET 
    payment_date = due_date
WHERE client_id = 3;

-- Giving 50 points to customers who are born before 1990-01-01
UPDATE customers
SET points = points + 50
WHERE birth_date < '1990-01-01';
```
WHERE Clause is optional if we want to update all the rows of the table.

### Using Subqueries in Update Statement
A subquery is a SELECT statement within other statement
```
UPDATE orders
SET comments = 'GOLD CUSTOMERS'
WHERE customer_id IN 
			(SELECT customer_id FROM customers
			WHERE points > 3000);
```

### Deleting Rows
Be careful while using the DELETE Clause.
WHERE Clause is optional if not provided it will delete the all rows
```
DELETE FROM invoices
WHERE client_id = 
	(SELECT client_id
    FROM clients
    WHERE name = 'Myworks')
```

## **Summarizing Data**
### Aggregate Functions
Functions are piece of code that we can reuse. MySQL comes with many built-in functions, Some of which are called aggregate functions. These functions only work on non null value. So, If the column contains null value it will not include it.
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
    SUM(invoice_total * 1.1) AS sum,
    -- COUNT(*) AS total_rows	-- does not take duplicates in consideration so
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

Grouping based on multiple columns. We get 1 record for each city and state combination.

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
Only available in MySQL. It is not a part of Standard SQL. It summarized our entire group. It only applies to column that aggregate value.
When you group by multiple column you see summary value of each group as well as the entire result set
```
SELECT pm.name AS payment_method, SUM(amount) AS total
FROM payments p
JOIN payment_methods pm
WHERE p.payment_method = pm.payment_method_id
GROUP BY pm.name WITH ROLLUP
```