## **Inserting, Updating and Deleting Data**

### Column Attributes
Click on the spanner when you hover over a table. This gives you table design. 
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
DEFAULT is passed while passing a key because our key has auto increment on. So MySQL is responsible for generating a value for it.
```
INSERT INTO customers
VALUES (DEFAULT, 'John', 'Smith', '1999-09-09', DEFAULT, 'address', 'city', 'CA', DEFAULT) 

Order of the column does not matter
INSERT INTO customers (first_name, last_name, birth_date, address, city, state)
VALUES ('John', 'Smith', '1999-09-09', 'address', 'city', 'CA') 
```

### Inserting Multiple Rows
```
INSERT INTO shippers (name)
VALUES ('Shippers1'),
	   ('Shippers2'),
       ('Shippers3')
```

#### Exercise
Insert three rows in the product

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
-- Instead of writing many insert statements but, MySQL will ignore the attributes such as auto increment, primary key ...
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
Syntax for updating multiple rows is similar as above. Only thing is the WHERE condition needs to be more generic. MySQL Workbench won't allow us to update multiple rows out of the box. To make MySQL allow you to update multiple rows. We untick the Safe update option in setting of SQL Workbench and reload the DB Connection.
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
A subquery is a SELECT statement within other statement. We have to use IN operator because the sub query can return multiple rows.
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