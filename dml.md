## Data Manipulation Language

All operation commands that affect the data stored in tables, but not the structure of tables, are called `Data Manipulation Language`



### Insert

```sql
INSERT INTO tablename [(columnname,...)]
	VALUES (columnname, ....)
```

- <u>if the data  entered in the `VALUES` clause contains a value for every column and is in the same order as columns in the table. the column names can be ommited in the `INSERT INTO` clause.</u>
- If you enter data for only some columns, or the columns are listed in a different order than listed in a table, you must provide the column names in the `INSERT INTO` clause in the same order as they are given in the `VALUES` clause.
- you must use single quotes to enclose the nonnumeric data inserted in a column.

```sql
INSERT INTO acctmanager
	VALUES ('T500', 'NICK', 'TAYLOR', '05-SEP-09', 42000, 3500, 'NE');

INSERT INTO acctmanager (amid, amfirst, amlast, amedate, amsal, amcomm, region)
	VALUES ('T500', 'NICK', 'TAYLOR', '05-SEP-09', 42000, 3500, 'NE');
	
INSERT INTO acctmanager (amid, amfirst, amlast, amedate, amsal, amcomm, region)
	VALUES ('T500', 'NICK', 'TAYLOR', SYSDATE, 42000, 3500, 'NE');

INSERT INTO acctmanager (amid, amfirst, amlast, amedate, amsal, amcomm, region)
	VALUES ('T500', 'NICK', 'TAYLOR', DEFAULT, 42000, 3500, 'NE');

INSERT INTO acctmanager (amid, amfirst, amlast, amedate, amsal, amcomm, region)
	VALUES ('T500', 'NICK', 'TAYLOR', NULL, 42000, 3500, 'NE');

INSERT INTO acctmanager (amid, amfirst, amlast, amsal, amcomm, region)
	VALUES ('T500', 'NICK', 'TAYLOR', 42000, 3500, 'NE');
	
ALTER TABLE acctmanager MODIFY (amsal DEFAULT ON NULL 1);

INSERT INTO acctmanager (amid, amfirst, amlast, amedate, amsal, amcomm, region)
	VALUES ('T500', 'NICK', 'TAYLOR', NULL, NULL, 3500, 'NE');

INSERT INTO acctmanager (amid, amfirst, amlast, amedate, amsal, amcomm, region)
	VALUES ('T500', 'NICK', 'O''hara', NULL, NULL, 3500, 'NE');
	
SELECT table_name, column_name, data_default, default_on_null 
	FROM user_tab_columns
	WHERE table_name = 'acctmanager';
```

- If you want to use the `DEFAULT` value for one column, please use the keyword `DEFAULT` in the `VALUE` clause or omit both the column name in the `INSERT INTO` clause and the data value in the `VALUES` clause.
- <u>The `DEFAULT` command option setting will be overridden when the explicit `NULL` value is inserted, otherwise you could use `DEFAULT ON NULL` clause to apply on the column.</u>
- the `Virtual Column` must be ignored in `DML` operations because the database system generates it automatically.
- To instruct Oracle to treat the single quote as part of a string value, enter two single quotes together in the value.

**Inserting Data From an Existing Table**

```sql
INSERT INTO tablename [(columnname,...)]
	subquery;

INSERT INTO acctbonus (amid, amsal, region)
	SELECT amid, amsal, region
		FROM acctmanager;
```



### Update

```sql
UPDATE tablename
SET columname = newvalue, ...
[WHERE condition];

UPDATE acctmanager
SET amedate = '01-AUG-09'
WHERE region in ('NE', 'NW');
```

`Substitution Variable` in the SQL command instructs Oracle to substitute a value in place of the variable at the time the command is actually executed.

```sql
UPDATE customers
SET region = '&region'
WHERE state = '&state';
```



### Delete

```sql
DELETE FROM tablename
[WHERE condition];

DELETE FROM acctmanager
WHERE amid = 'J500';
```



### Merge

`MERGE` command can compare two data sources or tables, and then determine which row need to be updated, which row need to be inserted.

```sql
MERGE INTO books_1 a
	USING books_2 b ON a.isbn = b.isbn
	WHEN MATCHED THEN
		UPDATE SET a.retail = b.retail, a.category = b.category
	WHEN NOT MATCHED THEN
		INSERT INTO (isbn, title, pubdate, retail, category) 
		VALUES (b.isbn, b.title, b.pubdate, b.retail, b.category);
```

- `MERGE INTO books_1`: books_1 is to be changed
- `USING books_2`: books_2 provides the data to update or insert into books_1.
- `WHEN MATCHED THEN`: if a row match based on isbn is discovered, excute the `UPDATE` command.
- `WHEN NOT MATCHED THEN`: If no match is not found based on isbn (exist in books_2, not in books_1), perform `INSERT` command.



### Transaction Control

`Transaction` is a term used to describe `DML` statements representing data actions that should be logically performed together. You can either save all modified data permanently or undo all actions.

#### Commit

`COMMIT` command issued implicitly or explicityly permanently save the `DML` statements issued perviously.

- **explicit commit:** Occurs when you execute `COMMIT` command.
- **implicit commit:** 
  - Occurs when you exit client tool
  - Occurs when you execute `DDL` command. the records manipulated perviously before a `DDL` command is issued are commited automatically and implicitly.

#### Rollback

all record manipulated perviously can be undone by issuing  the `ROLLBACK` command, unless execute `COMMIT` command.

- `ROLLBACK` command can reversed all `DML` operations since the last commit was issued.
- `DDL` commands can not be rolled back, and a commit occurs automatically when they are executed.
- If the system crashes, a `ROLLBACK` command occurs automatically after the system restarts, any uncommited operations are undone.

#### SavePoint

`SAVPOINT` command is to create a type of bookmark in the transaction. After pervious commands are completed to identify a particular point in the transaction - potential `ROLLBACK` point. If subquent portion of the transaction is canceled, any `SQL` statements after the `SAVEPOINT` command will be undone.

```sql
UPDATE acctmanager 
	SET region = 'E'
	WHERE amid = 'M500';

COMMIT;

UPDATE acctmanager
	SET REGION = 'M'
	WHERE amid = 'T500';

SAVEPOINT one;

UPDATE acctmanager
	SET amcomm = 6600
	WHERE amid = 'T500';

ROLLBACK TO one;
COMMIT
```

