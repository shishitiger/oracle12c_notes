## Views

Views are database objects that store select statements and allow using select query results as tables. It has  two purposes:

1. Simplify issuing complex SQL queries.
2. Restrict users' access to sensitive data.

| View Type             | Description                                                  |
| --------------------- | ------------------------------------------------------------ |
| **Simple View**       | A view based on a subquery that references only one table and does not include any goup functions, expressions, or GROUP BY clauses. |
| **Complex View**      | A view based on a subquery that retrieves or derives data from one or more  tables and can contain functions and grouped data. |
| **Inline View**       | A subquery used in the FROM clause of SELECT statement to create a "temporary" table that referenced by the outer query's SELECT and WHERE clauses. |
| **Materialized View** | A view that replicates data by physically storing the view query's results. |



### Syntax of Creating Views

```sql
CREATE [OR REPLACE] [FORCE|NOFORCE] VIEW 
     viewname (COLUMNNAME,....)
AS SELECT statement
[WITH CHECK OPTION [CONSTRAINT constraintname]]
[WITH READ ONLY]
```

- `OR REPLACE` notifies Oracle Server that a view with same name might already exist. if exists, the previous view will be replaced with the one in the new command.
- `NOFORCE` is the default option for the CREATE VIEW command, it means all tables and columns must be valided, it will return an error message when the tables and columns does not exist or are unavailable. `FORCE` means you creates the view in spite of the absence of any referenced tables.
- `COLUMNNAME`:
  1. if you want to assign new column names that this view display, please list them after viewname inside parentheses. the number of names listed must match the number of columns of SELECT statement.
  2. Alternative way to assign new column names is using column aliases in SELECT statements.
  3. if you want to assign the same column names as the column names of SELECT statement, please ignore columnnames with parentheses.
- <u> `SELECT` statement must be a complete query, but cannot include `ORDER BY` clause</u>.
- `WITH CHECK OPTION `: This constraint ensures that any DML operations performed on the view do not prevent the view accessing the row because it no longer meets the condition in the WHERE clause.
- `WITH READ ONLY`: This Option prevents any DML operations performed on the view.



### Creating a Simple View

```sql
CREATE OR REPLACE inventory
	AS SELECT isbn, title, retail FROM books
WITH READ ONLY
```

#### DML Rules For Simple View:

<u>As long as the view is not created with `WITH READ ONLY`, any DML operations are allowed if they do not violate any existing constraints of the underlying table.</u> 

All constraints are below:

- PRIMARY KEY
- NOT NULL
- UNIQUE
- FOREIGN KEY
- WITH CHECK OPTION



### Creating a Complex View

#### Complex View With Arithmetic Expression

```sql
CREATE VIEW price
AS SELECT isbn, title, cost, retail, retail - cost profit FROM books;

-- profit is a virtual column of this view and will be changed automatically while the changes in retail and cost.
UPDATE price SET retail = 29.99 WHERE title like '%MICKY%';

INSERT INTO price(isbn, title, cost, retail) VALUES (0202020202, 'A NEW BOOK', 8.95, 15.95);
```

#### Complex View With Joining Tables

```sql
CREATE OR REPLACE VIEW prices
AS SELECT isbn, title, cost, retail, retail - cost profit, name, pubid
	 FROM books JOIN publisher USING(pubid);
```

**Key-Preserved Table:**

> a key-preserved table is the table containing primary key that the view is using to uniquely identity each record it displays.
>
> In this case,  this view contains `isbn` and `pubid` two primary keys from different tables. However, Oracle 12c does not consider `pubid` as primary key because `pubid` could have appeared more than once in the view’s contents. 
>
> books is the key-preserved table, publisher is the non-key-preserved table.

#### Complex View With Group Functions or Group By Clause

```sql
CREATE OR REPLACE VIEW order_income
AS SELECT order#, SUM(paideach * quantity) FROM orderitems GROUP BY order#;
```

#### DML Rules For Complex View:

- <u>DML operations that violate a constraint aren’t permitted.</u>
- <u>A value can’t be added to a column containing an arithmetic expression.</u>
- <u>DML operations aren’t permitted on non-key-preserved tables.</u>
- <u>DML operations aren’t permitted on views that include group functions, a `GROUP BY` clause, the `DISTINCT` keyword, or the `ROWNUM` pseudocolumn.</u>



### Creating a Inline View

> Inline view is used most often to provider a temporary data source while a command is being executed. 
>
> Some common uses of inline view involve completing join operations and performing TOP-N analysis.

#### `CROSS APPLY` And `OUTER APPLY`

`CROSS APPLY` is equivalent to an `INNER JOIN` operations with subquery in `SELECT` or `FROM` clauses.

`OUTER APPLY` is equivalent to an `LEFT JOIN` operations with subquery in `SELECT` or `FROM` clauses.

```sql
-- CROSS APPLY
SELECT b.isbn, b.title, sale FROM books b
	CROSS APPLY (SELECT SUM(oi.paideach * oi.quantity) sale FROM orderitems oi WHERE oi.isbn =           
               b.isbn GROUP BY oi.isbn)
  ORDER BY b.title DESC;
  
-- INNER JOIN
SELECT b.isbn, b.title, sale FROM books b 
	INNER JOIN (SELECT SUM(oi.paideach * oi.quantity) sale FROM orderitems oi  GROUP BY 
              oi.isbn) ON oi.isbn = b.isbn
  ORDER BY b.title DESC;

-- OUTER APPLY
SELECT b.isbn, b.title, sale FROM books b
	OUTER APPLY (SELECT SUM(oi.paideach * oi.quantity) sale FROM orderitems oi WHERE oi.isbn =           
               b.isbn GROUP BY oi.isbn)
  ORDER BY b.title DESC;
  
-- LEFT JOIN
SELECT b.isbn, b.title, sale FROM books b 
	LEFT JOIN (SELECT SUM(oi.paideach * oi.quantity) sale FROM orderitems oi  GROUP BY 
              oi.isbn) ON oi.isbn = b.isbn
  ORDER BY b.title DESC;
```

#### TOP-N Analysis

> We always use TOP-N Analysis, in which the concept of an inline view and the `ROWUM` pseudo column are merged to create a temporary list of records in a sorted order. And then get the Top N records.

```sql
-- Syntax
SELECT columnnames,....
      FROM (subquery)
WHERE ROWNUM <= N

-- Example of ROWNUM
SELECT title, retial FROM (
     SELECT title, retail FROM books ORDER BY retail DESC
) WHERE ROWNUM <= 3


-- Example of Fetching First Number Rows
SELECT title, retail FROM books ORDER BY retail DESC 
		 FETCH FIRST 3 ROWS ONLY;

--Exmaple of Fetching Percentage of Rows
SELECT title, retail FROM books ORDER BY retail DESC 
		 FETCH FIRST 25 PERCENT ROWS ONLY;

--Exmaple of Fetching Data With Offset
SELECT title, retail FROM books ORDER BY retail DESC 
		 OFFSET 2 ROWS FETCH NEXT 3 ROWS ONLY;

--Example of Paging(pageNo, PageSize)
SELECT title, retail FROM books ORDER BY retail DESC 
		 OFFSET {(pageNo - 1) * pageSize} ROWS FETCH NEXT {pageNo} ROWS ONLY;
```



### Creating A Materialized View

> A materialized view allows replicating data from underlying table. These views are often referred to "Snapshot", as they take a picture or capture a set of data at a specific point in time.

**Purposes:**

1. Replicating data for report or analysis allows dedicating system resources to transactional process.
2. Data analysis needs might require freezing data for a specific time for comparison purpose.

**Disadvantages:**

1. Additional storage space is needed for the copied data.
2. the underlying table will be affected if modification is made to data through the materialized view.

```sql
CREATE MATERIALIZED VIEW custbal_mv
REFRESH COMPLETE
START WITH SYSDATE NEXT SYSDATE + 7
AS 
		SELECT customer#, city, state, order#, SUM(quantity * retail) AS amtdue 
		FROM customers JOIN orders USING(customer#)
									 JOIN orderitems USING(order#)
									 JOIN books USING(isbn)
		GROUP BY customer#, city, state, order#
```

- `START WITH` indicates when the initial materialized view should be constructed. In this case, means immediately creating.
- `NEXT` indicates Oracle server need to establish a schedule to rebuild the materialized view every specific time by including the `NEXT ` clause.

### Drop Views

```sql
-- Drop Simple And Complex View
DROP VIEW view_name;

-- Drop Materialized View
Drop MATERIALIZED VIEW view_name;
```