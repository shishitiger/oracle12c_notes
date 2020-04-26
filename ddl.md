## Data Definition Language (DDL)

`DDL` is basically SQL Command used specifically to create or modify `Data Objects`.

`Data Object` is defined, and self-contained structure in Oracle 12C. For exmaple: Table, View, Constriant.



### Table Creation And Management

**Rules For Naming:**

- The names of tables and columns can be up to 30 characters and must begin with a letter.
- The names of tables and columns can not contain any white space.
- Numbers, Underscore symbol(_), and number sign(#) are allowed in tables and column names.
- Each table owned by one user should have a unique name. And also, each column in one table should be unique.
- `Reserved Name` can not be used for table or column names.

**DataTypes**

| DataType       | Description                                                  |
| -------------- | ------------------------------------------------------------ |
| `VARCHAR2(n)`  | Variable-length character data, and `n` represents the maximum length of columns. <u>The maximum length is 4000 bytes. There is no default size for this datatype, a minimum value must be speicfied.</u> |
| `CHAR(n)`      | Fixed-length character data, and `n` represents the lengh of columns. <br /><u>The default length is 1, and the maximum length is 2000 bytes.</u> |
| `NUMBER(p, s)` | Numeric column.<br />`p` represents precision. The total number of digits to the left and right of the decimal position. <u>Maximum size is 38 digits</u>.<br />`s` represents scale, the number of positions to the right of the decimal. |
| `DATE`         | Stores date and time between January 1, 4712 BC and December 31, 9999 AD. <u>7 bytes are allocated to the column to store the century, year, month, day, hour, minute, and second of a date.</u> Oracle 12c displays the date in the format DD-MON-YY. |



#### Table Creation

```sql
CREATE TABLE [SCHEMA.] tablename (
  		columnname datatype [DEFAULT VALUE],
  		......
);
```

- `SCHEMA` indicates  who own the table. if the person creating the table is also the person who owns the table, the `SCHEMA` can be omitted, the current username is assumed  by default.
- <u>A table can contain a maximun of 1000 columns.</u>

**Syntax**

```sql
CREATE TABLE goods(
  goods# VARCHAR2(20),
  name VARCHAR2(100),
  cost NUMBER(10, 2) DEFAULT 0,
  price NUMBER(10, 2) DEFAULT 0,
  profit AS (price - cost),
  discount NUMBER(10, 2) INVISIBLE, 
  createTime DATE DEFAULT SYSDATE
);

CREATE TABLE goods1 
	AS SELECT * FROM goods;

-- display invisible column in SELECT statement.
SELECT goods#, discount FROM goods;

SELECT table_name FROM user_tables;

DESC goods;

SELECT column_name, data_type, data_default 
	FROM user_tab_columns 
	WHERE table_name = 'goods';
```

- profit is a `Virtual Column` that generates a value automatically based on other column values in the table. It is not stored physically in the row data; therefore, it can not be manipulated for `DML` operations.
- `INVISIBLE` column is not visible in `SELECT *` and `DESC` . The developer wish to make it invisible if they need to reference a value in application logic but does not intend to these data to be directly visible to users. A direct column reference in a query will access the invisible column.
- `USER_TABLES` is A data dictionary to verify all existing tables in your schema. It is very important when you start a new job and need to explore an existing database.
- `DESCRIBE` is a SQL*PLUS command that display the structure of table that check the column's name, the column's datatype, and whether the column allows `NULL` values.
- `USER_TAB_COLUMNS` is a data dictionary to verify the `DEFAULT` value and the `Virtual Column` definition additionally rather than `DESCRIBE`.



#### Create Table With Subquery

To create table with data from an existing table. 

```sql
CREATE TABLE tablename [(columnname,...)]
	AS (subquery)
```

- If you need to give columns of the new table different names from thoese in the existing table, <u>please list column names inside parentheses after the  table name or you can use the column aliases.</u>
- The column names must be in the same order as the columns listed in the `SELECT Clause` of subquery, if you do provider a column list for the table.
- The subquery must be enclosed in parentheses in order to distinguish the subquery from the rest of `CREATE TABLE` command.

```sql
CREATE TABLE cust_mkt
	AS (SELECT customer#, city, state, zip, referred, region FROM customers);
```



#### Modifying Existing Table

##### Add Column

```sql
ALTER TABLE tablename
	ADD (columnname datetype [DEFAULT], ...);
```

The new column is added at the end of the table - it will be the last column.

```sql
ALTER TABLE publisher ADD (ext NUMBER(4), ext1 NUMBER(4) DEFAULT 1);
```

##### Modify Column

```sql
ALTER TABLE tablename
	MODIFY (columnname datetype [DEFAULT], ...);
```

The changes that can be made to a column includes the following:

- Change the column size (increase | decrease).
- Change the data type.
- Change or add the default value of a column
- Set `INVISIBLE` or `VISIBLE` option of a column

**Three Rules of Modifying Columns:**

1. A column must be as wide as the data fields it already contains.
2. If a `NUMBER` column already contains data, you can not decrease the column's precision or scale.
3. Changing the default value of a column does not change the values of data already in the table. Therefore, <u>if the default values in existing rows must be changed, these changes must be performed manually.</u>

```sql
ALTER TABLE books
	MODIFY title VARCHAR2(12);

ALTER TABLE publisher
	MODIFY (rating DEFAULT 'N');
	
ALTER TABLE publisher
	MODIFY(rating INVISIBLE | VISIBLE);
```

##### Drop Column

It should be used with extreme caution because this clause delete both the column and its contents.

```sql
ALTER TABLE tablename
	DROP COLUMN columnname;
```

**Rules For Droping Column:**

- The `DROP Clause` can reference only one column.
- The deletion is permanent. In other words,  you can not undo the damage if you delete the wrong column by accident. The only option is to add the column back and recover data from backup.
- You can not delete the last column in the table.
- the primary key column can not be droped from the table.

```sql
ALTER TABLE customers
	DROP COLUMN ext;
```

##### UNUSED Column

This command is to mark the column for deletion at a later time. if a column is marked for deletion, it will be unavailable and displayed in the table structure, and also it does not appear in the results of any queries, and no longer be operated with any commands except `DROP UNUSED COLUMN`.

<u>Once a column is seted `UNUSED Column`, the column and its contents are no longer available and can not be recovered forever.</u>

```sql
ALTER TABLE tablename
	SET UNUSED (columname);

ALTER TABLE tablename
	SET UNUSED COLUMN columnname;

ALTER TABLE tablename
	DROP UNUSED COLUMNS;
```

```sql
ALTER TABLE cust_mkt
	SET UNUSED (state);

ALTER TABLE cust_mkt
	DROP UNUSED COLUMNS;

SELECT table_name, count
	FROM user_unused_col_tab;
```

`user_unused_col_tab` is a data dictionary that to check whether any columns are marked as `UNUSED`. It contains information on which table has the `UNUSED COLUMNS`, and how many `UNUSED COLUMNS` are.

##### Rename Table Name

```sql
RENAME old_tablename TO new_tablename;
```

```sql
RENAME cust_mkt TO cust_mkt_20191110;
```

##### Truncate Table

This command is basically the same as deleting all rows from a table. However, deleting all rows does not free up the storage space these columns occupy.

```sql
TRUNCATE TABLE cust_mkt_20191110;
```

##### Delete Table

```sql
DROP TABLE tablename [PURGE]
```

- A dropped table is placed to a recycle bin and can be restored - both table structure and data after performing `DROP TABLE` command.
- If you be sure to delete a table permanently, you could bypass moving to `recyclebin` by using `PURGE` option in the `DROP TABLE ` statement.

```sql
DROP TABLE cust_mkt_20191110;

SELECT object_name, original_name
	FROM recyclebin;

-- recover table from recyclebin
FLASHBACK TABLE cust_mkt_20191110 TO BEFORE DROP;

-- remove a table from recyclebin
PURGE table object_name;

-- remove all tables from recyclebin
PURGE recyclebin;

-- remove a table permanently
DROP TABLE cust_mkt_20191110 PURGE;
```



### Constraints

Constraints are the rules used to enfore busines rules, practices, and policies to ensure the accuracy and integrity of data.

**Five Constraints**

| Constraint    | Description                                                  | Abbreviation |
| ------------- | ------------------------------------------------------------ | ------------ |
| `PRIMARY KEY` | Detemine which column uniquely identifies each record. The primary key can not be `NULL`, and the data value must be unique. | _pk          |
| `FOREIGN KEY` | In one-to-many or parent-child relationship, the constraint is to add the "many" or "child" table. The constraint ensures that if a value is entered in a specified column, it must already exist in "one" or "parent" table. | _fk          |
| `CHECK`       | Ensure a specified condition is true when the data value is added to a table. | _ck          |
| `UNIQUE`      | Ensure that all data value in a specified column are unique. it allows `NULL` value. | _uk          |
| `NOT NULL`    | Ensure a specified column can not contain `NULL` value, and just be performed  by `Column Level` approach. | _nn          |



#### Create Constraints

You can add constraints during table creation as part of `CREATE TABLE` command, and also, you could do it after table creation by using `ALTER TABLE` commnad.

Industry convention is to use the format `tablename_columnname_constraintype` for the constraint name.

##### Creating Table

###### Column Level

Creating a constraint at column level means the constraint definition is included as parts of the column definition.

```sql
columnname [CONSTRAINT constraint_name] constraint_type,
```

- <u>You can not create an constraint for more than one columns at column level.</u>
- <u>`NOT NULL` constraint can be created only at column level.</u>

```sql
CREATE TABLE equipment(
  equipid NUMBER(3) CONSTRAINT equipment_equipid_pk PRIMARY KEY,
  ename VARCHAR2(10) CONSTRAINT equipment_ename_uk UNIQUE,
  edesc VARCHAR2(30),
  rating CHAR(1) NOT NULL CONSTRAINT equipment_rating_ck CHECK (rating in ('A', 'B', 'C')),
  deptid NUMBER(2) NOT NULL CONSTRAINT equipment_deptid_fk FOREIGN KEY REFERENCES department(deptid),
  etypeid NUMBER(2) NOT NULL,
)
```

###### Table Level

Creating a constraint at the table level means the constraint definition is separate from the column definition, always after the column definition inside parentheses.

```sql
[CONSTRAINT constraint_name] constraint_type (columnname),
```

- <u>You can create any types of constriants except `NOT NULL` constraint</u>

```sql
CREATE TABLE equipment(
  equipid NUMBER(3),
  ename VARCHAR2(10),
  edesc VARCHAR2(30),
  rating CHAR(1) NOT NULL,
  deptid NUMBER(2) NOT NULL,
  etypeid NUMBER(2) NOT NULL,
  CONSTRAINT equipment_equipid_pk PRIMARY KEY(equipid),
  CONSTRAINT equipment_rating_ck CHECK (rating in ('A', 'B', 'C')),
  CONSTRAINT equipment_ename_uk UNIQUE (ename),
  CONSTRAINT equipment_deptid_fk FOREIGN KEY (deptid) REFERENCES department(deptid)
);
```

The general practice in the industry is to create constraints with the table-level approach because a column list can be cluttered if a constraint name is entered in the middle of a list defining all the columns.

**Guideline of Applying Constraints to Tables**

- A `NOT NULL` constraint should not be assigned to `PRIMARY KEY` column because `PRIMARY KEY` enfores both  `NOT NULL` and `UNIQUE` constraints to the column.
- `CHECK`, `CHECK`, `UNIQUE` constraints do not requrie a value, A `NOT NULL`constraint must be used along with three constraints to require a value for the column.
- if the `DEFAULT` option is setted in a column, the `NOT NULL` constraint should not be used to this column.
- the conditions of `CHECK` constraint can not reference `SYSDATE` function or values stored in other rows.



##### Alter

###### PRIMARY KEY

```sql
ALTER TABLE tablename ADD [CONSTRAINT constraint_name] PRIMARY KEY (columnname);

-- primary key
ALTER TABLE customers ADD CONSTRAINT customers_customer#_pk PRIMARY KEY(customer#);
--      composite primary key
ALTER TABLE orderitems ADD CONSTRAINT orderitems_order#item#_pk PRIMARY KEY(order#, item#);
```

###### FOREIGN KEY

```sql
ALTER TABLE tablename ADD [CONSTRAINT constraint_name] FOREIGN KEY (columnname) REFERENCES 
		referenced_tablename (referenced_columnname) [ON DELETE CASCADE];
```

- `REFERENCES` keyword refers to `referential integrity`, which means the user is referring something existing in another table.
- <u>The column referenced must be the primary key column in the referenced table</u>
- If `ON DELETE CASCADE` is added at the end of the definition. it means that when a record in the parent table is deleted, any corresponding records in the child table will be deleted automatically.
- It is accepted that a record in the child table has `NULL` value for a column that has a `FOREIGN KEY` constraint.

```sql
-- The customers is the referenced table, which is the one side in the one-to-many relationship between customers table and orders tabale. Therefore, customers table is the parent table, and orders table is the child table because each customer can place many orders, one order can be placed by only one customer. 
ALTER TABLE orders ADD CONSTRAINT orders_customer#_fk FOREIGN KEY (customer#) REFERENCES 
		customers (customer#);
```

Two options are available to allow dropping the parent table:

1. Drop the child table first and then drop the parent table.
2. Drop the parent table with the `CASCADE CONSTRAINT`, which deletes the `FOREIGN KEY` in the child table, and then drop the parent table.

```sql
DROP TABLE customers CASCADE CONSTRAINT;
```

###### UNIQUE

`UNIQUE` constraint just perform a check on the column if a value is entered for the column. 

```sql
ALTER TABLE tablename ADD [CONSTRAINT constraint_name] UNIQUE (column_name);

ALTER TABLE books ADD CONSTRAINT books_title_uk UNIQUE (title);
```

###### CHECK

```sql
ALTER TABLE tablename ADD [CONSTRAINT constraint_name] CHECK (condition);

ALTER TABLE orders ADD CONSTRAINT orders_shipdate_ck CHECK (orderdate <= shipdate);
```

###### NOT NULL

```sql
ALTER TABLE tablename MODIFY (columnname [CONSTRAINT constraint_name] NOT NULL);

ALTER TABLE orders MODIFY customer# CONSTRAINT orders_customer#_nn NOT NULL;
```



#### View Constraints

##### `USER_CONSTRAINTS`

`USER_CONSTRAINTS` is a view which is a part of data dictionary, it is used to identify existing constraints. However, it does not include the specific column name that the constraint is assigned to.

```sql
SELECT constraint_name, constraint_type, search_condition, r_constraint_name
	FROM user_constraints
	WHERE table_name = 'EQUIPMENT';
```

- `constraint_name` is used to list all constraint names of this table. and notice the `NOT NULL` constraint has a system-generated constraint name because we define it at the column-level without a customized constraint name.
- `constraint_type`: 
  - `P` for  `PRIMARY KEY`
  - `C` for `CHECK` or `NOT NULL`
  - `U` for `UNIQUE`
  - `R` for `FOREIGN KEY`
- `search_condition` is used to display conditions for `CHECK` and `NOT NULL` constraints. The column is blank for any other types of constraints.
- `r_constraint_name` is used to provides the name of the PRIMARY KEY constraint on the column that a FOREIGN KEY references

##### `USER_CONS_COLUMNS`

`USER_CONS_COLUMNS` data dictionary view lists column names and assigned constraints.

```sql
SELECT constraint_name, column_name
	FROM user_cons_columns
	WHERE table_name = 'EQUIPMENT';
```



#### Disable And Drop Constraints

##### Using `Disable` | `Enable`

If you are adding a large of data to the table, the constraint validation process will severely slow down the adding process. you can disable the constraints temorarily if you can make certain the data adheres the constrains.

```sql
ALTER TABLE tablename DISABLE|ENABLE CONSTRAINT constraint_name;

ALTER TABLE equipment DISABLE CONSTRAINT equipment_rating_ck;

ALTER TABLE equipment ENABLE CONSTRAINT equipment_rating_ck;
```

##### Dropping Constraints

If you create a constraint and then decide it is no longer needed, you could delete it from table with `DROP` command. In addition, if you want to modify or change a constraint, the only way is to delete this constraint, and then create a new one.

```sql
ALTER TABLE DROP CONSTRAINT constraint_name;

ALTER TABLE DROP PRIMARY KEY;

ALTER TABLE DROP PRIMARY KEY CASCADE;

ALTER TABLE DROP UNIQUE (columnanme);
```

- If you need to delete `PRIMARY KEY`, using the keywords `PRIMARY KEY` is enough because only one such constraint is allowed for each table.
- If needed, the associated `FOREIGN KEY` is deleted along with `PRIMARY KEY` deletion by using `CASCADE` option.
- To delete `UNIQUE` constraint, only the column name affected the `UNIQUE` constraint is required because a column is referenced by only one `UNIQUE` constraint.
- Other constraints must be referenced by the constraint's actual names.

