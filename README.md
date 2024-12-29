# SQL-notebook
> *The notes in this notebook, as well a exercises were done using https://sqliteonline.com/, a web-based and free SQL compiler/RDBMS. SQLite does not require installation! You can access the compiler by visiting the official website at [https://www.sqlite.org/download.html](https://www.sqlite.org/download.html) and importing the databases you want to work on.

I would also like to credit the repository [SQL-101](https://github.com/biancainocencio/SQL-101/tree/main), from where I drew inspiration on the markdown code and notebook organization, as well as overall tips. 

## SQL Basics
If you are looking for the very basics on how to work with SQL, e.g. what a `SELECT` is, I suggest you visit the [SQL-101](https://github.com/biancainocencio/SQL-101/tree/main) repo, or check the other files in this repository. I've uploaded my personal notes on the simplest SQL commands.


# Table of Contents
1. [Installation Guide](#installation-guide)
2. [Introduction to SQL](#introduction-to-sql)

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

# Filtering Data
## `WHERE`
`WHERE` is used to filter the results queried by establishing limitations. In the example below, we're pulling out only clients whose name is Jorge Amado (who is, by the way, a brilliant Brazilian author about whom you can learn more on the [Britannica website](https://www.britannica.com/biography/Jorge-Amado)).  

```sql
SELECT * FROM clients
WHERE name = 'Jorge Amado';
```
However, it might be the case that we have multiple lines with a client named Jorge Amado. If you want to see, for example, which customers have bought from your store, you might end up with multiple client lines repeated. In this scenario, we can use the command `DISTINCT` to make our job easier.

## `DISTINCT`
With `DISTINCT` you'll query unique records. If you don't want repeated customers in your table, you can select distinct records. Example:

```sql
SELECT DISTINCT client_ID
FROM clients; 
```
Where `clients` is the name of the table where we are pulling the IDs from. 

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
