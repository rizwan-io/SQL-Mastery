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
3. ```USE sql_store``` - Selecting the DB on which we want to make the query or Double click the DB to select it.
4. If we have multiple statements, terminate each SQL statement with a semi-colon.

Syntax for Retreiving the data -   
```
SELECT <columns to be selected separated by a comma. * if want all columns>   
FROM <table name>
```

Comments are marked by -- in front of the statement.  
Order of the clauses matters. Line breaks and tabs are ignored while executing the SQL query.

### SELECT Clause in Detail

```
SELECT 
	first_name, 
    last_name, 
    points,
    (points + 10) * 100 AS 'discount factor'
FROM customers;
```

The order in which the columns are mentioned in the select clause will be displayed in the result.  We can use mathematical operations in the SELECT clause as done above. The order of operators is based on BODMAS  
AS can be used for alias that would be the name of the column.  

```
SELECT DISTINCT state
FROM customers;
```
Use DISTINCT keyword before selecting the column for removing the duplicates from the result.

### Exercise - 2.1
Write a query to return all the products with the following columns (ON sql store DB).
1. name
2. unit price
3. new price (unit price * 1.1)

### WHERE Clause
Using the WHERE clause we can filter out the data.  
We can use the comparison operators >, >=, <, <=, =, != OR <>
```
SELECT *
FROM Customers
WHERE points >= 3000;
```
Whenever dealing with Strings we need to use 'Quotes'. (single quote is recommended)  

### Exercise - 2.2
Get the orders placed in or after the year 2019.  

### The AND, OR and NOT Operator
They are the logical operator used to concatenate different conditions.   
AND takes more precedence over OR. Use braces to override the default precedence.    

Remember DeMorgan's Law  
- !(A and B)  = A or B  
- !(A or B)  = A and B

### Exercise - 2.3
From the order_items table, get the items for order #6, where the total price is greater than 30

### The IN Operator
```
SELECT *
FROM products
WHERE quantity_in_stock = 49 OR quantity_in_stock = 38 OR quantity_in_stock = 72;
```
Instead of writing the query like above we can use the IN operator.
```
SELECT *
FROM products
WHERE quantity_in_stock IN (49, 38, 72);
```

### The BETWEEN Operator
Whenever we are comparing an attribute with a range of values. We can use BETWEEN Operator.
```
SELECT *
FROM customers
WHERE birth_date BETWEEN '1990-01-01' AND '2000-01-01'
```
YYYY-MM-DD is the format for date in SQL.

### The LIKE Operator
How to retrieve rows that match a specific string pattern.
```
SELECT *
FROM customers
WHERE address LIKE '%TRAIL%' OR address LIKE '%AVENUE%'
```
The string matcher (doesn't care if it is uppercase or lowercase) -  
1. % - any number of characters
2. _ &nbsp;- a single character

### Exercise - 2.4
1. Return the customers whose phone numbers ends with 9
2. Return the customers whose name has 'c' as the 3rd letter and ends with y.

### The REGEXP Operator
The LIKE Operator is an old operator in SQL. REGEXP is newer and more powerful operator to use.  

These are the basic regular expression  
^  - Beginning of the String ex: '^field' - must start with field  
\$ - Ending of the String ex: 'james$' - must end with james  
[] - Contains any of the mentioned characters ex: '[gie]f'. we can also use range ex: '[a-h]' it means chars from a to h - abcdefgh  
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
Null means absence of value.
```
-- Get the orders that are not shipped
SELECT *
FROM orders
WHERE shipped_date IS NULL
```

### ORDER BY Operator
Table is sorted based on the Primary key by default.
```
SELECT *
FROM order_items
WHERE order_id = 2 
ORDER BY quantity * unit_price DESC;
```
1. MySQL can take argument in ORDER BY that is not mentioned in SELECT but other DBMS Might not take it.  
2. Use DESC to sort in descending order.  
3. We can sort using more than 1 column. In this items will be sorted based on the first mentioned column and then if there is a clash they will be sorted based on the second mentioned column.  
4. We can write ORDER BY 1 and this will sort the able based on the first column that is mentioned in the select statement (This approach should be avoided).

### The LIMIT Clause
```
SELECT *
FROM customers
ORDER BY points DESC
LIMIT 3 
```
The above query will return only the first 3 customers.  
If use ```LIMIT 6, 3``` It will  select customers 7, 8, 9 the first argument is known as the OFFSET. It would be same as  

```
SELECT *
FROM customers
ORDER BY points DESC
OFFSET 6 
LIMIT 3 
```

The Order of the Clauses matters, So we have
SELECT  
FROM  
WHERE  
ORDER BY  
LIMIT
