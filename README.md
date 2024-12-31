# SQL-notebook
> *The notes in this notebook, as well a exercises were done using https://sqliteonline.com/, a web-based and free SQL compiler/RDBMS. SQLite does not require installation! You can access the compiler by visiting the official website at [https://www.sqlite.org/download.html](https://www.sqlite.org/download.html) and importing the databases you want to work on.

I would also like to credit the repository [SQL-101](https://github.com/biancainocencio/SQL-101/tree/main), from where I drew inspiration on the markdown code and notebook organization, as well as overall tips. 

## SQL Basics
If you are looking for the very basics on how to work with SQL, e.g. what a `SELECT` is, I suggest you visit the [SQL-101](https://github.com/biancainocencio/SQL-101/tree/main) repo, or check the other files in this repository. I've uploaded my personal notes on the simplest SQL commands.


# Table of Contents
1. [Data types & constraints](#data-types--constraints)
2. [Filtering data](#filtering-data)
3. [Creating & manipulating tables](#creating-and-manipulating-tables)

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

## Nested filters


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
## Aggregating functions
There are multiple specific functions / SQL commands that are specifically focused on numbers. If you're familiar with Excel, first I'm sorry!, second there will be no surprises here. 

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
