## Table Lock

Table locks can be used to prevent users from mistakenly overwriting changes made by other users.

- `shared lock`: means other users can view the data in the table, but they can not perform any `DDL` and `DML` operations while the lock is active.

- `exclusive lock`: it is the most restrict lock that prevent users view and modify data util the lock is released.
- A lock is released automatically when the transaction control statement is issued, a `DDL` statement is executed, or exiting system by using `EXIT` command.
- `SELECT ...FOR UPDATE` command places a shared lock on the records to be changed and prevent other users from acquiring a lock on the same records.

```sql
LOCK TABLE tablename in SHARED MODE;

LOCK TABLE tablename in EXCLUSIVE MODE;

SELECT columnname,...
	FROM tablename
	[WHERE condition]
	FOR UPDATE;
```



