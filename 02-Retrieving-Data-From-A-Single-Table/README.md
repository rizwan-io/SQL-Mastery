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
