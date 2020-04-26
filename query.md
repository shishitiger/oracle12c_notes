## SELECT STATEMENT



Select statements could enable users to retrieve data from tables. users can view all fields and records in a table or specify displaying only certain fields and records. in essence, the `SELECT` statement asks the database a question, which is why it is also known as `QUERY`.

```sql
SELECT [DISTINCT | UNIQUE] (*, columnname [AS] alias, ....)
			 FROM tablename
			 [WHERE conditions]
			 [GROUP BY group_by_expression]
			 [HAVING group_by_condition]
			 [ORDER BY (columnname, .....)];
```

Each section begining with a keyword is referred to as a `Clause` (`SELECT CLAUSE`, `FROM CLAUSE`... ).

- The only clauses required for the `Select Statement` are `SELECT` and `FROM`.
- Square brackets indicate optional portions of the statement.
- An `Select Statement` ends with a semicolon.



### Basic Sql Select Statement

```sql
-- Selecting All Data From Tables
SELECT * FROM books;

-- Selecting One Column From Tables
SELECT isbn FROM books;

-- Selecting Multiply Columns From Tables
SELECT isbn, title FROM books;

-- Using Column Aliases
-- if the column alias contains spaces or special symbols, or if you do not want it to appear in all uppercase letter, you
-- must enclose it in quotation marks ("")
SELECT isbn, title AS "BOOK's NAME" FROM books;
SELECT isbn, title "BOOK's NAME" FROM books;
SELECT isbn, title books_name FROM books;

-- Using Arithmetic Operations
SELECT isbn, title, retail - cost AS profit FROM books;

-- Null
-- if no value is entered for a column in a row of data, the value is considered `NULL`, indicating an absence of data.
-- `NUll` can lead to undesirable results in operations. If any value in arithmetic operation is `NULL`, the result is `NULL`
SELECT isbn, title, retail - cost - discount AS profit FROM books;

-- Distinct or Unique
-- The Distinct is applied to all column listed in the SELECT Clause to eliminate duplicated rows.
SELECT DISTINCT city, state FROM customers;
SELECT UNIQUE city, state FROM customers;

-- Concatenation
-- Combining the content of two or more columns is known as Concatenation. Using two vertical bars ||
'' single quotation marks here is called String Literal
SELECT firstname || ' ' || lastname FROM customers;

-- Show Structure of Table
-- DESC command is a SQL*PLUS command, not a SQL command. Therefore, a semicolon is not necessary to end with this statement.
DESC customers
DESCRIBE customers

-- List All of Table Information In The Current Account
SELECT * FROM user_tables WHERE table_name = 'customers';
```

The alignment of data value in columns of `Select Statement` result:

- By default, the data for text, or character, fields is left-aligned.
- Data for numeric fields is right-aligned.
- Oracle 12c does not display insignificant zeros.



#### Search Conditions

A condition identifies what must exist or a requirement that must be met for a record to be included in the results.

```sql
WHERE (columnname) (comparison operator) (another columnname | a value | subquery)
OR
WHERE exist (subquery)
```



##### Rules for Character Strings

- If there are conditions that compare with character strings, please use the `String Literal` that the character values are enclosed in single quotation marks.

- Using single quotation marks if the column is defined with anything other than a numeric data type. 
- You can use a numeric value to compare with a column that is defined as the string type but does not contain any letters. However, it is better to use the corresponding data type.
- Issue the `DESC` or `DESCRIBE` command to check the data types of columns if you do not make certain the structure of the table.

```sql
-- Using String Literal For Character Strings
SELECT lastname, firstname FROM customers
			WHERE state = 'FL';
			

-- Using Numeric Value to compare with a string column that just contains numbers.
SELECT title, retail FROM books
			WHERE isbn = 1915762492;

```



##### Rules For Date

<u>Oracle 12c display a data in the default format `DD-MON-YY` that contains letters and hyphens, `MON` is the standard three-letters abbreviation for the month. Please make sure the date value is enclosed in single quotation marks.</u>



##### Comparison Operator

**The Order of All Operators:**

1. Arithmetic  operators are solved at first.
2. All of comparison operators are solved at second.
3. Logical Operators have the low precedence and are evaluated last - in the order `NOT`, `AND`, `OR`.



**Mathematical Comparison Operator***

| Operator             | Description              |
| -------------------- | ------------------------ |
| `=`                  | Equality or "equal to"   |
| `>`                  | Greater Than             |
| `<`                  | Less Than                |
| `<>` or `!=` or `^=` | Not Equal to             |
| `>=`                 | Greater Than or Equal to |
| `<=`                 | Less Than or Equal to    |

**Other Comparison Operator**

| Operator                | Description                                                  |
| ----------------------- | ------------------------------------------------------------ |
| `[NOT] BETWEEN x AND y` | It is used when searching a field for values falling within a specified range. |
| `[NOT] IN (x,y,...)`    | Similar to a group of `OR` logical operators. Return records matching anyone of the values listed in the condition. |
| `[NOT] LIKE`            | It is used with wildcard character to search for patterns.   |
| `[NOT] IS NULL`         | Search the records that does not have entity in one column.  |

**Logical Operator**

| Operator | Description                                                  |
| -------- | ------------------------------------------------------------ |
| `AND`    | Combining two conditions, it is true if both conditions are ture. Otherwise, it is false. |
| `OR`     | Combining two conditions, it is true if at lease one of both conditions is ture. Otherwise, it is false. |
| `NOT`    | Reverse the meaning of search conditions rather than combining conditions. |

```sql
-- =
SELECT lastname, firstname FROM customers
			WHERE state = 'FL';
     
-- !=
SELECT title, retail FROM books
			WHERE isbn != '1915762492';
 
-- >
SELECT title, retail - cost AS profit FROM books
			WHERE retail - cost > cost * 0.2;
			
-- BETWEEN AND
--		It is always used to replace the combination of ">=" and "<=" for one column.
SELECT title, pubid FROM books
			WHERE pubid BETWEEN 1 and 3;
SELECT title, pubid FROM books
			WHERE pubid >= 1 and pubid <= 3;
			
-- 		an alphabetical range of character column could be queried with the BETWEEN ... AND comparison operator.
SELECT title FROM books 
			WHERE title BETWEEN 'A' AND 'B';

-- IN
-- It is always used to replace the combination of "OR" for one column
SELECT title, pubid FROM books
			WHERE pubid in (1, 3, 5);
SELECT title, pubid FROM books
			WHERE pubid = 1 OR pubid = 3 OR pubid = 5;
			
-- LIKE
-- percent sign represents any number of characters (zero, one or more)
-- underscore symbol represents exactly one characters.
-- starts with 'P' and don’t care about the remaining letters of the last name
SELECT * FROM customers
			WHERE lastname LIKE 'P%';

-- Escape Character "\"
-- The LIKE operator includes the ESCAPE option for indicating when wildcard symbols should be used as literals rather than translated as wildcard characters.
SELECT * FROM customers
			WHERE lastname LIKE '\%' ESCAPE '\';

-- [NOT] IS NULL
SELECT order#, shipdate FROM orders
			WHERE shipdate IS NOT NULL;
```



#### Order By Clause

When sorting in ascending order, values are listed in this order:

1. Blank and special characters
2. Numeric values
3. Character values (Uppercase first)
4. NULL values.

<u>Ascending order is default, unless you choose the descending order.</u>

```sql
-- Ascending Order
SELECT title FROM books 
		  ORDER BY retial [ASC];

-- Descending Order
SELECT * FROM publishers 
			ORDER BY name DESC;

-- [NULL FIRST | NULL LAST]
-- Change the order for the NULL values 
-- By default, NULL values are listed last when results are sorted in ascending order, and listed first when they are sorted in descending order.
SELECT title FROM books 
		  ORDER BY retial NULL FIRST;
SELECT * FROM publishers 
			ORDER BY name DESC NULL LAST;
			
-- primary sort, secondary sort
-- The number of columns in ORDER BY clause is limited by 255
SELECT lastname, firstname, state, city FROM customers
			ORDER BY state DESC, city;

-- Order By SELECT Order
-- Instead of listing these column names again in the ORDER BY clause, you can reference them by their positions in the SELECT clause’s column list.
SELECT lastname, firstname, state, city FROM customers
			ORDER BY 3 DESC, 4;
```



### Joining Table



#### Cartesian Join

`Cartesian Join`,  also called `Cartesian Product` or `Cross Join`. Means creating a join between tables by displaying any possible records combination. It is very useful when you are performing certain statistical processdures for data analysis.

The Resulting number of rows = {rows in Table 1} * {rows in Table 2} * {rows in Table N}. 

Can be created by two ways: `Traditional Method` , `JOIN Method (CROSS JOIN)`

```sql
-- Traditional Method
SELECT isbn, title, location FROM books, warehouses
		  ORDER BY location, title;
		  
-- Join Method
SELECT isbn, title location FROM books CROSS JOIN warehouses
		  ORDER BY location, title;
```



#### Equality Joins

`Equality JOINS` is also called `Equijoins`, `inner joins`, or `simple joins`,  which is based on two or more tables having equivalent datas stored in a `Common Column`.

`Common Column` is a column with the equivalent data existing in two or more tables.

`Column Qualifier` is used to qualify the common column in `SELECT Clause`. it can be `Table Name` or `Table Alias`.

**Advantages of `Table Alias`:**

- improve processing efficiency, as system no longer need to identify which table the specific column is in.
- coding is simplified when the full table name doesn’t have to be indicated

**Rule of `Table Alias`**

<u>If a `Table Alias` is assigned in the `FROM Clause`, it must be used any time the table is referenced in that `SQL Statement`.</u>

```sql
-- Traditional Method (Table Name)
SELECT books.title, publisher.name, publisher.pubid FROM books, publisher
		  WHERE books.pubid = publisher.pubid;

-- Traditional Method (Table Alias)
SELECT b.title, p.name, p.pubid FROM books, publisher
			WHERE b.pubid = p.pubid;

-- Traditional Method With Multiple Tables
SELECT c.lastname, c.firstname, b.title 
			FROM customers c, orders o, orderitems oi, books b
			WHERE c.customer#= o.customer#
				AND o.order# = oi.order#
				AND oi.isbn = b.isbn
			ORDER BY c.lastname, c.firstname;


-- Three Approaches Of Join Method

-- NATURAL JOIN
-- Create a join automiaticaly between two tables having at least one common column with same column name and same data type.
-- you are not allowed to use `Column Qualifier` for the column
-- Please avoid using this approach
SELECT title, name, pubid
			FROM books NATURAL JOIN publisher;
			
-- JOIN USING
-- Create a join based on one column that has same column name and same data type.
-- The common column referenced by USING Clause can not contain Column Qualifier anywhere in the SELECT statement, but qualifying other columns in the SELECT Clause is still good practice.
SELECT b.title, p.name, pubid
			FROM books [INNER] JOIN publisher p USING(pubid);
			
-- JOIN ON
SELECT b.title, p.name, p.pubid
			FROM books b [INNER] JOIN publisher p ON p.pubid = b.pubid
			
-- JOIN ON With Multiple Tables
SELECT c.lastname, c.firstname, b.title 
			FROM customers c
				INNER JOIN orders o ON o.customer# = o.customer#
        INNER JOIN orderitems oi ON oi.order# = o.order#
        INNER JOIN books b ON b.isbn = oi.isbn;
      ORDER BY c.lastname, c.firstname;
```



#### Non-Equality JOIN

Joins tables when there are no equivalent rows in the tables to be joined. Often used to match values in one column with a range of values in another column. Can use any comparison operator except the equal sign.

```sql
--Traditional Method
SELECT b.title, p.gift FROM books b, promotion p
		 WHERE b.retail BETWEEN p.minretail AND p.maxretail;
		 
-- JOIN Method
SELECT b.title, p.gift 
		 FROM books b [INNER] JOIN promotion p
		 ON b.retail BETWEEN p.minretail AND p.maxretail;
```



#### Self-Join

used when a table must be joined to itself to retrieve the data you need. Table aliases are required in the `FROM Clause` to perform a `SELF-JOIN`.

```sql
-- Traditional Method
SELECT r.firstname, r.lastname, c.lastname AS "Referred"
		 FROM customers c, customers r
		 WHERE c.referred = c.customer#;

-- JOIN Method
-- USING clause can not be used with a self-join.
SELECT r.firstname, r.lastname, c.lastname AS "REferred"
			FROM customer c [INNER] JOIN customers r
			WHERE c.referred = c.customer#;
```



#### Outer-Join

`Outer-Join` is created when records need to be included in the results without having corresponding records in the join tables. These records are matched with `NULL` records so that they’re included in the output.



##### Traditional Method

`Deficient Table` is the table with missing data.

`Outer Join Operator` : `+`, It is placed immediately after the reference to the `Deficient Table` in the `WHERE Clause`.

**Limitation of  `Outer Join Operator`**

- The `Outer Join Operator` can be used for one table in the join condition. In the other words, you cannot create `NULL` row in both tables at the same time.
- A condition that includes the `Outer Join Operator` can’t use the `IN` or `OR` operator.

```sql
-- Traditional Method

SELECT c.lastname, c.firstname, o.order#, r.lastname AS referral
		 FROM customers c, orders o, customers r
		 WHERE c.customer# = o.customer#(+)
		 	 AND c.referred = r.customer#(+)
     ORDER BY c.lastname, c.firstname;
```



##### Join Method

`Left Outer Join`:  includes all records from the table listed on the left side of the join, even if no match is found with the other table in the join operation.

`Right Outer Join`:  includes all records from the table listed on the right side of the join, even if no match is found with the other table in the join operation.

`Full Outer Join`:   includes all records from both tables, even if no corresponding record in the other table is found.

```sql
-- Left Outer Join
SELECT c.lastname, c.firstname, o.order#
		 FROM customers c LEFT OUTER JOIN orders o
		 			USING(customer#)
		 ORDER BY c.lastname, c.firstname;
		 
-- Right Outer Join
SELECT c.lastname, c.firstname, o.order#
		 FROM customers c RIGHT OUTER JOIN orders o
		 			USING(customer#)
		 ORDER BY c.lastname, c.firstname;

-- Full Outer Join
SELECT c.lastname, c.firstname, o.order#
		 FROM customers c FULL OUTER JOIN orders o
		 			USING(customer#)
		 ORDER BY c.lastname, c.firstname;
```



#### Set Operations

`Set Operations` are used to comibine the results of two (or more) `SELECT Statement`.

| Set Operator | Description                                                  |
| ------------ | ------------------------------------------------------------ |
| `UNION`      | Return the results of both queries and remove the duplicates. |
| `UNION ALl`  | Return the results of both queries but includes duplicates.  |
| `MINUS`      | Subtract the results from the second query if they're also returned in the first query's result. |
| `INTERSECT`  | Return the results includes in  results of both queries.     |

```sql
(
	SELECT ba.authorid 
  		 FROM books b INNER JOIN bookauthor ba
  				USING(isbn)
  		 WHERE b.category = 'FAMILY LIFE'
)
(UNION ALL | UNION | MINUS | INTERSECT)
(
	SELECT ba.authorid 
  		 FROM books b INNER JOIN bookauthor ba
  				USING(isbn)
  		 WHERE b.category = 'CHILDREN'
)
ORDER BY authorid
```

**Guidelines For Set Operations **

- All columns are included to perform the `Set Operations`
- Each query must have the same number of columns, which are compared positionally.
- Column names can be different in each query.