# SQL-notebook
> *The notes in this notebook, as well a exercises were done using https://sqliteonline.com/, a web-based and free SQL compiler/RDBMS. SQLite does not require installation! You can access the compiler by visiting the official website at [https://www.sqlite.org/download.html](https://www.sqlite.org/download.html) and importing the databases you want to work on. **SQLite docs:** https://www.sqlite.org/docs.html

I would also like to credit the repository [SQL-101](https://github.com/biancainocencio/SQL-101/tree/main), from where I drew inspiration on the markdown code and notebook organization. 

## SQL Basics
If you are looking for the very basics on how to work with SQL, e.g. what a `SELECT` is, I suggest you visit the [SQL-101](https://github.com/biancainocencio/SQL-101/tree/main) repo, or check the other files in this repository. I've uploaded my personal notes on the simplest SQL commands.


# Table of Contents
1. [Resources](#cool-resources)
2. [Data types & constraints](#data-types--constraints)
3. [Filtering data](#filtering-data)
4. [Creating & manipulating tables](#creating-and-manipulating-tables)
5. Functions
6. Complex queries

# Cool resources

SQL Murder Mistery > https://mystery.knightlab.com/walkthrough.html

# Data types & constraints
## Data types
* **Text (String)**:
  * CHAR: Stores fixed-size strings. Used when values have a constant length.
  * VARCHAR: Stores variable-size strings. Suitable for values with variable lengths.
  * TEXT: Stores very long strings, such as documents or descriptions. TEXT types are database-specific (e.g., MySQL supports TEXT but other databases like SQL Server may use NVARCHAR(MAX)).

> *CHAR always uses the defined space (e.g., CHAR(10) always uses 10 bytes), while VARCHAR only uses the space required for the actual data. CHAR may pad with spaces to meet the defined size, which might affect certain operations like comparisons.*

* **Numeric**:
  * INTEGER (INT): Stores integer numbers.
  * FLOAT: Stores floating-point numbers, generally used for values with decimal places.
  * NUMERIC (DECIMAL): Stores numbers with specific precision, typically used in financial applications.

> *The precision of FLOAT can vary significantly between database systems. For high precision, use NUMERIC or DECIMAL. These types allow specifying precision (NUMERIC(p, s) where p is the total number of digits and s is the number of decimal digits).*

* **Date and Time**:
  * DATE: Stores dates without time information.
  * TIME: Stores time information.
  * TIMESTAMP: Combines date and time in a single type.

* **Boolean:**
  * BOOLEAN (BOOL): Stores true or false values.

* **Binary:**
  * BLOB (Binary Large Object): Stores binary data, such as images, videos, or files.
  * BIT: Stores binary values, such as 0 or 1.

## Constraints
- **Primary Key**: Ensures the uniqueness of a column's value in a table, typically used to uniquely identify each row. It is a record that cannot be repeated.
- **Foreign Key**: Establishes a relationship between two tables, enforcing referential integrity.
- **Unique Constraint**: Ensures the uniqueness of values in one or more columns.
- **Check Constraint**: Defines a condition that must be true for a row to be valid.

### Primary key
If your table does not have a constraint, you can add a primary key to it.

1. If the column already exists in the table:
```sql
ALTER TABLE table_name
ADD CONSTRAINT pk_table_name PRIMARY KEY (column_name);
```

2. If the column does not exist:
```sql
ALTER TABLE table_name
ADD column_name datatype NOT NULL;

ALTER TABLE table_name
ADD CONSTRAINT pk_table_name PRIMARY KEY (column_name);
```

### Foreign key 
The foreign key is the column that relates/connects two different tables. Say we have a table with the information of our clients and it contains a column `client_ID`, which also exists in another table - say, our order history table. In this case, `client_ID` exists in both tables and therefore works as a foreign key. 
In the example below, I'm establishing the foreign keys while creating a new table with product information. You use the command `FOREIGN KEY (column_table1) REFERENCES table2 (column_table2)`.

**❗IMPORTANT:** *The FOREIGN KEY will always reference a PRIMARY KEY of another table.* 

```sql
CREATE TABLE product_table (
  product_id INT PRIMARY KEY,
  product_name VARCHAR (50),
  product_desc TEXT,
  product_category INT,
  price DECIMAL (10, 2),
  units VARCHAR (50),
  supplier_ID INT,
  date_conclusion DATE,
  FOREIGN KEY (product_category) REFERENCES category_table (id_category),
  FOREIGN KEY (supplier_ID) REFERENCES supplier_table (id)
  );
```

If you want to add a foreign key to a table that already exists, you can use the `ÀLTER TABLE` command.

```sql
ALTER TABLE produtos 
ADD COLUMN fk_fornecedor INTEGER 
REFERENCES tabelafornecedores(id);
```

#### ON DELETE CASCADE
The resource `ON DELETE CASCADE` can be incorporated when creating a foreign key if you want to establish a hierarchy relationship between records.
Example: say you have a table with clients and a table with the order history of those clients. You establish that the client ID (which is the foreign key between the two tables) is parametrized `ON DELETE CASCADE`. 

```sql
CREATE TABLE clients (
    ID INT PRIMARY KEY,
    Nome VARCHAR(50)
);

CREATE TABLE orders (
    PedidoID INT PRIMARY KEY,
    ClienteID INT,
    Descricao VARCHAR(100),
    FOREIGN KEY (ClienteID) REFERENCES Clientes(ID) ON DELETE CASCADE
);
```

Now, if a client is deleted from `clients`, all orders placed by that client will also be deleted from `orders`.

# Filtering Data
## Basics
### `WHERE`
`WHERE` is used to filter the results queried by establishing limitations. In the example below, we're pulling out only clients whose name is Jorge Amado (who is, by the way, a brilliant Brazilian author about whom you can learn more on the [Britannica website](https://www.britannica.com/biography/Jorge-Amado)).  

```sql
SELECT * FROM clients
WHERE name = 'Jorge Amado';
```
However, it might be the case that we have multiple lines with a client named Jorge Amado. If you want to see, for example, which customers have bought from your store, you might end up with multiple client lines repeated. In this scenario, we can use the command `DISTINCT` to make our job easier.

### `DISTINCT`
With `DISTINCT` you'll query unique records. If you don't want repeated customers in your table, you can select distinct records. Example:

```sql
SELECT DISTINCT client_ID
FROM clients; 
```
Where `clients` is the name of the table where we are pulling the IDs from. 

### `IS NULL`/`NOT NULL`
If you want to limit your results to exclude empty lines or get only records for which a certain field is empty, you can use the syntax `IS NULL` or `NOT NULL` together with `WHERE`:

```sql
SELECT employee_id, employee_name, employee_salary
FROM employees
WHERE termination_date IS NULL
AND country = "USA"
ORDER BY employee_salary DESC
LIMIT 5
; 
```

In the query above, we are selecting a few columns from our `employees` table, filtering out all employees that have a termination date (meaning they don't work at the company anymore). In summary, we are pulling from our table the Top 5 most well-paid current employees in the United States.

# Creating and manipulating tables
## Create a table
When you're creating a new table, you must specify a few things:
* The table's name;
* The table's columns
  * And, here, which type of data each column will host (ex: VARCHAR for text, INT, FLOAT).

```sql
CREATE TABLE client_names(
  client_ID INT PRIMARY KEY,
  client_name VARCHAR (250),
  client_city VARCHAR (50),
);
```
In the example above, `client_names` is my table's name. Then, I open a parenthesis and specify the columns. In this example, I'm setting that `client_ID` is an integer column and will be my **PRIMARY KEY**. I also specify a couple of other columns of the type *VARCHAR*, which is used for text, and I determine that the content in these columns cannot exceed 250 characters. 

## Alter a table
You can add columns, delete columns, add constraints, all using the `ALTER TABLE` command. Example to create a column:

```sql
ALTER TABLE tabelaclientes
ADD endereco_cliente VARCHAR (200);
```

If you want to delete a table, you use the command `DROP`. For the command drop, you need to specify the type of record you want to delete and the name. See below:

```sql
DROP TABLE tabelaclientes;

DROP SCHEMA clientschema;

DROP DATABASE clientdb;
```

Then, if you want to delete a column of a table, we combine the commands `DROP` and `ALTER TABLE`.

```sql
ALTER TABLE table_name
DROP COLUMN col_name;
```

## Adding multiple rows to a table
For that, we can use different methods. 

1. Add `VALUES` manually:
```sql
INSERT INTO table_name (
column1,
column2,
column3)
VALUES
('value1', 32, 'value 3 information')
('value2', 47, 'value 3 information')
('value3', 89, 'value 3 information');
```

2. Using `SELECT`. With this method, you'll insert into the assigned columns of table1 the data from the specified columns of table2, filtering so that you only select data for which the column "value" is above 300. 
 
```sql
INSERT INTO table1_name1 (
column1,
column2,
column3)

SELECT
column_name,
column_origin
FROM table2_name2
WHERE value > 300
;
```

**Adding data conditionally:** In the example below, say we have a table **sales** and a table **inventory**. We want to deduct from inventory the amount of products sold. That could be done using the code below:

```sql
INSERT OR REPLACE INTO inventory (ID_product, qty)
SELECT sales.ID_product, inventory.qty - sales.qty
FROM sales
JOIN inventory ON sales.ID_product = inventory.ID_product;
```

This command either inserts a new row into the inventory table if a row with the same ID_product does not already exist or replaces the existing row (based on the primary key or unique constraint on ID_product) with the new data. Then, the SELECT statement computes the new quantity for each product by subtracting the sold quantity (sales.qty) from the current inventory quantity (inventory.qty), generating a list of product IDs and their updated quantities.

## UNION
**`UNION`** is a command used to unify data from different tables. If you have the same columns in two tables, but each with different records, you can unify the data using this command. 

> _**Important**: `UNION` by default deletes duplicates. So, if you have repeated records they will not be shown!_
If you need repeated records to be shown in the unified table, you can use `UNION ALL`.

## JOIN
_Important note:_ Not any material on SQL would be complete without a section on the `JOIN` commands, so I'm creating this section. However, for me the best way to understand this concept when I was getting started with SQL was through [Jeff Atwood's post on the Coding Horror blog](https://blog.codinghorror.com/a-visual-explanation-of-sql-joins/). He uses Venn Diagrams to explain the simplest use cases of `JOIN`, and the post contains some great comments from other readers developing the theory even further. I suggest you take a look! In my notebook, I'm not covering the basic syntax and logic of the `JOIN`, just taking notes on a few use cases. 

**RIGHT JOIN + SUB-QUERY**
```sql

--We're selecting the tables 'products' and 'orderedproducts', which contain product catalogue information and data on how many times each product was ordered. We're calling them 'op' and 'pr' for short.

SELECT
 pr.product_name,
 x.order_id,
 x.product_id
FROM (
 SELECT
   orders.id_order,
   orders.id_product
 FROM orders
 INNER JOIN orderedproducts op --Combining data from all historical orders and the orders history of specific products.  
 ON op.order_id = orders.id_order 
 WHERE strftime('%m', orders.date_time_order > '05') --Filter: only orders placed in June or later.
 ) x -- Output: returns the id_order and id_product for matching rows and stores this result as a derived table (alias x).
products pr
RIGHT JOIN orderedproducts op
ON pr.id = x.product_id
```

## Commands UPDATE and DELETE
This command can be used to alter the data or column names in the tables. 

Example: you want to update the status of all orders from "in progress" to "delivered".

```sql
UPDATE orders_history
SET status = 'Delivered'
WHERE status = 'In progress';
```
Now, if you want to delete some data, you can use the command `DELETE`. 
Example: you want to delete the contact information of all suppliers from the United States. 

```sql
DELETE FROM suppliers_table
WHERE supplier_country = 'USA';
```

# Functions
## Aggregate functions
There are multiple specific functions / SQL commands that are specifically focused on numbers. If you're familiar with Excel, first I'm sorry you had to learn it!, second there will be no surprises here. 

* **MAX** -> Highest value in a column.
* **MIN** -> Lowest value in a column.

The syntax for these commands is usually `FUNCTION(column_name)`, e.g `SELECT month, MAX(sales) FROM sales` will return the highest amount of sales and in which month that happened.

* **SUM** -> Sums the values of a certain column.

```sql
SELECT
 SUM(total_revenue) AS 'Total sales'
FROM sales
WHERE year LIKE '199%';
```
Here, I'm retrieving all sales that happened in the 90s and summing it all.

* **AVG** -> Takes the average of the values in a column. Same syntax as the others.
* **COUNT** -> Count is a very flexible and interesting function.
    * If you want to count all values/records in a column, you'll use `COUNT(column_name)`. It'll count the number of lines that satisfy your filters.
   
   
* **GROUP BY** -> With this function, you're collapsing the data and aggregating it by one of the columns. It's useful when you know multiple lines have the same tag.
* **HAVING** -> Having is a very important command. When we use these aggregation functions, such as `GROUP BY` we can't really use `WHERE`. In this case, we use `HAVING`.

```sql
SELECT institution, COUNT(courses) AS 'Number of courses'
FROM training_table
GROUP BY institution
HAVING COUNT(courses) > 2
ORDER BY COUNT(courses) DESC;
```
With the query above, we are selecting all institutions that offered more than two courses.

### Formatting numbers
To format the results of these aggregate functions (say, limit the number of decimals), you can use the following functions.

1. **ROUND()**
   - **Description**: Rounds a number to the specified number of decimal places.
   - **Syntax**: 
     ```sql
     ROUND(number, decimals)
     ```
   - **Example**:
     ```sql
     SELECT ROUND(123.456, 2) AS RoundedNumber;
     -- Result: 123.46
     ```

 2. **FORMAT()**
   - **Description**: Formats a number with a specific pattern or locale. Commonly used in MySQL and SQL Server.
   - **Syntax**:
     ```sql
     FORMAT(number, format, locale)
     ```
   - **Example**:
     ```sql
     SELECT FORMAT(123456.789, 'N2') AS FormattedNumber;
     -- Result: 123,456.79 (varies based on locale)
     ```

3. **TRUNC()**
   - **Description**: Truncates a number to a specified number of decimal places without rounding.
   - **Syntax**:
     ```sql
     TRUNC(number, decimals)
     ```
   - **Example**:
     ```sql
     SELECT TRUNC(123.456, 2) AS TruncatedNumber;
     -- Result: 123.45
     ```

4. **CAST()**
   - **Description**: Converts a number to a specific data type, such as `VARCHAR`, for formatting purposes.
   - **Syntax**:
     ```sql
     CAST(expression AS data_type)
     ```
   - **Example**:
     ```sql
     SELECT CAST(123.456 AS VARCHAR) AS FormattedString;
     -- Result: '123.456'
     ```

5. **CONVERT()**
   - **Description**: Similar to `CAST()` but allows additional style parameters in SQL Server.
   - **Syntax**:
     ```sql
     CONVERT(data_type, expression, style)
     ```
   - **Example**:
     ```sql
     SELECT CONVERT(VARCHAR, 1234.56, 1) AS FormattedString;
     -- Result: '1,234.56'
     ```

6. **CEIL() / FLOOR()**
   - **Description**: Rounds a number up (`CEIL`) or down (`FLOOR`) to the nearest integer.
   - **Syntax**:
     ```sql
     CEIL(number)
     FLOOR(number)
     ```
   - **Example**:
     ```sql
     SELECT CEIL(123.456) AS CeilNumber, FLOOR(123.456) AS FloorNumber;
     -- Result: 124 (Ceil), 123 (Floor)
     ```

7. **LPAD() / RPAD()**
   - **Description**: Pads a number with leading or trailing characters to achieve a fixed length.
   - **Syntax**:
     ```sql
     LPAD(string, length, pad_string)
     RPAD(string, length, pad_string)
     ```
   - **Example**:
     ```sql
     SELECT LPAD('123', 5, '0') AS PaddedNumber;
     -- Result: '00123'
     ```

8. **TO_CHAR()**
   - **Description**: Converts a number to a formatted string (commonly used in Oracle).
   - **Syntax**:
     ```sql
     TO_CHAR(number, format)
     ```
   - **Example**:
     ```sql
     SELECT TO_CHAR(12345.67, '999,999.99') AS FormattedNumber;
     -- Result: ' 12,345.67'
     ```

### 9. **NUMERIC Data Type Conversion**
   - **Description**: Converts numbers to specific numeric types like `DECIMAL` or `FLOAT` for precision control.
   - **Syntax**:
     ```sql
     CAST(number AS DECIMAL(p, s))
     ```
   - **Example**:
     ```sql
     SELECT CAST(123.456 AS DECIMAL(5, 2)) AS FormattedNumber;
     -- Result: 123.46
     ```

## String functions

* **LENGTH** -> Used to count how many characters a string has.
* **TRIM** -> Most basic syntax is `TRIM(string, [character to be trimmed]`. It'll remove white spaces by default. 
* **INSTR** -> Tells you the position of a certain text snipped within a string. 
* **REPLACE** -> Syntax is REPLACE(string, current_substring, new_substring). So if you want to replace all 'Hi' texts in a column for 'Hello', this is how you'd do it.
* **SUBSTR (or SUBSTRING)** -> Extracts a part of the string based on initial point (where the string starts) and a number of characters.


## Date functions
* **STRFTIME** -> Ir formats date. Syntax STRFTIME(format, timestring, modifier1, modifier2, ...). In summary:
    * %Y returns the year (YYYY)
    * %m returns the month (01-12)
    * %d returns the day of month (01-31)
    * %H returns the hour (00-23)
    * %M returns the minute (00-59)
    * %S returns the second (00-59)
* **CURRENT_TIMESTAMP**
* **DATETIME**
* **TIME**
* **DATE**

## The CASE function
Reference: [W3 Schools](https://www.w3schools.com/sql/sql_case.asp).

The CASE function works similarly to `IF / THEN / ELSE` in Python. It's used to add some logic to your code. The syntax is somewhat like this:

```sql
CASE
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    WHEN conditionN THEN resultN
    ELSE result
END;
```

Examples: 

```sql
--Example from W3 Schools.
SELECT OrderID, Quantity,
CASE
    WHEN Quantity > 30 THEN 'The quantity is greater than 30'
    WHEN Quantity = 30 THEN 'The quantity is 30'
    ELSE 'The quantity is under 30'
END AS QuantityText
FROM OrderDetails;
```
Here, we are creating a new column called "QuantityText" that will be populated following the conditions we specified inside the CASE function. 

```sql
```

# Complex queries
## Sub-queries
Sub-queries are essentially nested functions. See the example below:

```sql
SELECT name, telephone
FROM clients
WHERE client_id IN (
 SELECT DISTINCT id_client
 FROM orderhistory
 WHERE order_date <= '2024-02-01');
```

Here, we are selecting the name and telephone of all clients that placed an order before or on the 2nd of January. We use `IN` in this case, as the subquery (the code in parenthesis) might return more than one ID, and we use `SELECT DISTINCT` to make sure no repeated client IDs are returned. 

**Sub-queries using `HAVING`:** As said above, `HAVING` is equivalent to `WHERE`, but used when we apply aggregate functions. Example: We want to select all products whose prices are above our products' average price. 

First, I work on the subquery. In this case, that'll be the average price:

```sql
--Sub-query.
SELECT AVG(price)
FROM products
```

Then, I like to build the rest of the query from there. 

```sql
--Query + sub-query embedded.
SELECT
 product_id,
 product_price,
 product_name
FROM products
HAVING price > (
 SELECT AVG(price)
 FROM products);
```
So, whatever average price our subquery (the first one) returns will be used to filter our the products. 

# Data good practices & further tools

## TRIGGER
The `TRIGGER` command is SQL is a sort of custom function that you can create. Triggers are useful in scenarios like:

* Automatically logging changes to a table.
* Enforcing constraints that cannot be implemented with standard constraints.
* Validating data before an INSERT, UPDATE, or DELETE operation.
* Synchronizing related tables.

### Syntax for Creating a Trigger

```sql
Copy code
CREATE TRIGGER trigger_name
{BEFORE | AFTER | INSTEAD OF} {INSERT | UPDATE | DELETE}
ON table_name
[FOR EACH ROW]
BEGIN
   -- Trigger logic (SQL statements) goes here
END;
```

### Example using a trigger

**Scenario** 
Suppose you have a table `employees` and you want to maintain a log of any updates to employee salaries in a `salary_log` table.

---
#### Table Definitions

* **`employees` Table:**
```sql
CREATE TABLE employees (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    salary DECIMAL(10, 2)
);

* **`salary_log` Table:**
```sql
CREATE TABLE salary_log (
    log_id INT PRIMARY KEY AUTOINCREMENT,
    employee_id INT,
    old_salary DECIMAL(10, 2),
    new_salary DECIMAL(10, 2),
    change_date DATETIME DEFAULT CURRENT_TIMESTAMP
);
```
---
**Trigger Definition**
Create a trigger that logs changes to the employees salary column whenever it is updated:

```sql
Copy code
CREATE TRIGGER log_salary_changes
AFTER UPDATE OF salary ON employees
FOR EACH ROW
BEGIN
    INSERT INTO salary_log (employee_id, old_salary, new_salary)
    VALUES (OLD.id, OLD.salary, NEW.salary);
END;
```
---
**Explanation of the Trigger**
* Trigger Name: log_salary_changes.
* Event Type: AFTER UPDATE OF salary.
    * The trigger fires after an UPDATE operation on the salary column in the employees table.
* Affected Rows: FOR EACH ROW.
   * Executes the trigger logic for each row affected by the UPDATE operation.
* Logic:
    * Inserts the id of the employee, the old salary (OLD.salary), and the new salary (NEW.salary) into the salary_log table.
 
