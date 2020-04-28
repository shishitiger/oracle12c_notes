## Function

Function is the predefined block of code that accepts one or more arguments listed inside the parentheses, and then returns value as output.



### Single-Row Function

Single-row function returns one row of results for each record processed.

| Type of Function                 | Functins                                                     |
| -------------------------------- | ------------------------------------------------------------ |
| Case Conversion Functions        | `UPPER`,  `LOWER`,  `INITCAP`                                |
| Character Manipulation Functions | `SUBSTR`,  `INSTR`,  `LENGTH`,  `LPAD` , `RPAD`, `LTRIM`, `RTRIM`, `REPLACE`, `TRANSLATE`, `CONCAT` |
| Numeric Functions                | `ROUND`, `TRUNC`, `MOD`, `ABS`, `POWER`                      |
| Date Functions                   | `MONTHS_BETWEEN`, `ADD_MONTHS`, `NEXT_DAY`, `LAST_DAY`, `TO_DATE`, `ROUND`, `TRUNC`, `CURRENT_DATE` |
| Other Functions                  | `NVL`, `NVL2`, `NULLIF`, `TO_CHAR`, `DECODE`, `CASE expression`, `SOUNDEX`, `TO_NUMBER` |



#### Case Conversion Functions

```sql
-- LOWER
-- Convert characters into lowercase letters.
SELECT LOWER(firstname), LOWER(lastname)
		  FROM customers

-- UPPER
-- Convert characters into uppercase letters.
SELECT firstname, lastname
		 FROM customers
		 FROM lastname = UPPER('NELSON')

-- INITCAP
-- Convert the first letter of each word in the character string to uppercase and remaining characters into lowercase
SELECT INITCAP(fristname) AS "First Name", INITCAP(lastname) AS "Last Name"
	   FROM customers
```



#### Character Manipulation Functions

```sql
-- SUBSTR
-- Return a subString (a portion of string)
-- SUBSTR(STRING, POSITION, LENGTH),  
--      `STRING` represents charcter_string
--      `POSITION` represents the beginning character position for extraction (the first index is 1)
--      `LENGTH` represents the length of string to return the query results.
SELECT DISTINCT SUBSTR(zip, 1, 3) FROM customers;

-- INSTR
-- Search a string for a substring, and then return the first character position in which the substring is found. if the substring does not exist in the string value, 0 is returned.
-- INSTR(STRING, SUBSTRING, [START_POSITION], [OCCURRENCE])
--       `STRING` represents the string value to search
--       `SUBSTRING` represents the substring to locate
--       `START_POSITION`, is optional, defaule value is 1. indicate on which character of the string the search should begin
--       `OCCURRENCE`, is optional, default value is 1. indicate which is the instance of search value to locate (the first occurrence, the second occurrence).
SELECT name, INSTR(name, ',') AS "First Comma" FROM contacts;

-- Used with SUBSTR function. extract the firstname and lastname from "Crew,Ben,352-555-3638"
SELECT SUBSTR(name, 1, INSTR(name, ',') - 1) AS "Last Name", 
			 SUBSTR(name, INSTR(name, ',') + 1, INSTR(name, ',', 1, 2) - INSTR(name, ',') - 1) AS "Last Name"
			 FROM contacts;
			 
--LENGTH
-- Detemine the number of character in string.
-- Using the LENGTH function on the column with CHAR data type always return the total size of this column.
SELECT DISTINCT LENGTH(address)
		 FROM customers 
		 ORDER BY LENGTH(address) DESC;

-- RPAD LPAD
-- To fill in the area to the (left | right) of a character string with a specific character.
-- RPAD(STRING, LENGTH, CHARACTER)
--      STRING represent a character string to pad
--      LENGTH represent the length of the character string after padding
--      CHARACTER represent the symbol or character to use as padding.
SELECT LPAD(firstname, 12, '*'), RPAD(lastname, 12, "&")
			FROM customers;
			
-- LTRIM RTRIM
-- To remove the specific character string from the (left | right) side of a character string.
-- LTRIM(STRING, SPECIFIC_STRING)
--      STRING represent a character string to trim
--      SPECIFIC_STRING represent a string to be removed from the particular side of data.
SELECT LTRIM(address, 'P.O. BOX'), RTRIM(address, 'P.O. BOX')
			FROM customers;
			

-- REPLACE
-- replace the specific string with another given string in a character string.
-- REPLACE(STRING, SPECIFIC_STRING, REPLACE_STRING)
--        STRING represents the character string to search
--        SPECIFIC_STRING represents the character string to find.
--        REPLACE_STRING represents the character string to substutites for STRING
SELECT REPLACE(address, 'P.O.', 'POST OFFICE')
		 FROM customers;

-- TRANSLATE
-- Similar with Replace methods, replace one character in the string with a new value.
-- TRANSLATE(STRING, CHARACTER, REPLACE_STRING)
SELECT TRANSLATE(name, ',', '-') FROM contacts;

-- CONCAT
-- Used to concatenate data from two columns.
-- CONCAT(data1, data2)
-- The main difference between the concatenation operator and CONCAT function is that you can only combine any long lists of columns and string literal with concatenation operator, but not only two columns with CONCAT function.
SELECT CONCAT('Customer''s Name:', customer#) FROM customers;
```



#### Numeric Functions

```sql
-- ROUND
-- Used to round numbers to the stated precision.
-- ROUND(number, position)
--    a positive value for p indicates a position to the right of decimal
--    a negative value for p indicates a position to the left of decimal
SELECT ROUND(retail, 2), ROUND(retail, 0), ROUND(retail, -1) FROM books;

-- TRUNC
-- Used to truncate value to the specific position, any number after that position will be removed simply.
-- TRUNC(number, position)
--    a positive value for p indicates a position to the right of decimal
--    a negative value for p indicates a position to the left of decimal
SELECT TRUNC(retail, 2), TRUNC(retail, 0), TRUNC(retail, -1) FROM books;

-- MOD
-- Return a reminder of a division operation
-- MOD(numerator, denominator)
SELECT TRUNC(235/16, 0), MOD(235, 16) FROM DUAL;

-- ABS
-- Return a positive or absolute value of numeric values supplied as arguments.
-- ABS(number)
SELECT ABS(ROUND(pubdate - SYSDATE, 0)) FROM books;
```



#### Date Functions

By default, Oracle 12C displays data value in a `DD-MON-YY` format: 02-FEB-09.

Although user references a date value as a nonnumeric field, it is actually stored in a numeric format that includes century, year, month, day, hours, minutes and seconds.

The valid range is from `January 1, 4712 B.C.` to `December 31 9999 A.D.`

```sql
-- MONTH_BETWEEN
-- determines the number of months between two dates.
-- MONTH_BETWEEN(date1, date2)
--   		date2 is subtracted from date1
SELECT MONTH_BETWEEN(pubdate, SYSDATE) FROM books;

-- ADD_MONTH
-- ADD_MONTH(date, month)
--		date represents the begining date for the calculation.
--    month represents the number of month to be added to the date.
SELECT ADD_MONTH(SYSDATE, 2) FROM DUAL;

-- NEXT_DAY
-- determine the next occurrence of a specific day of the week after a given day
-- NEXT_DAY(date, DAY)
--			date represents the start date
--      DAY represents the day of week to identify.
SELECT NEXT_DAY(orderdate, 'MONDAY') FROM orders;

-- LAST_DAY
-- determine the last day of the month of the given date
-- LAST_DAY(date)
SELECT LAST_DAY(orderdate) FROM orders;

-- ROUND
-- A date can be rounded by the unit of month or year
-- ROUND(date, unit)
--      date represents the date value
--      unit represents the unit to use for rounding
SELECT pubdate, ROUND(pubdate, 'YEAR'), ROUND(pubdate, 'MONTH') FROM books;

-- CURRENT_DATE
-- Return the current date and time from the user session, client tool that connects to database server. Generally, they are in separated computer so that the session time zone setting is different from database's time zone setting.

-- SYSDATE
-- Return the current date and time from the database server

-- TIMESTAMP VS DATE
-- TIMESTAMP datetype is available for storing time zone as part of date value.
-- DATE does not store time zone information.
```



#### Other Function

```sql
-- NVL
-- Can address the problem caused when performing arithmetic operations with fields might be NULL value. It is used to substitute a value for the existing NULL so that the calculation can be executed.
-- NVL(x, y)
-- 			y represents the value to substitute if x is NULL.
SELECT retail - NVL(discount, 0) - cost AS "profit" FROM books;
SELECT  NVL(shipdate, '06-APR09') FROM orders;

-- NVL2
-- a variation of NVL function.
-- NVL2(x, y, z)
-- 			y represents the value to substitute if x is not NULL.
--      z represents the value to substitute if x is NULL.
SELECT NVL2(shipdate, 'SHIPPED', 'NOT SHIPPED') AS status FROM orders;

-- NULLIF
-- Used to compare two values for equality. If they are equal, return NULL. Otherwise, return the first of the two values.
-- NULLIF(first, second)
SELECT NVL2(NULLIF(oi.paideach, b.retail), 'Sale Price', 'Regular Price')
	FROM books b
	INNER JOIN orderitem oi ON oi.isbn = b.isbn;

-- TO_CHAR
-- Used to convert the data or numeric to a formatted character string.
-- TO_CHAR(value, format)
SELECT title, TO_CHAR(pubdate, 'MONTH DD YYYYY') AS "Publication Date",
	TO_CHAR(retial, '$99999.99') AS "Retail Price"
	FROM books;

-- DECODE
-- Takes a specific value and compare it to values in a list. if a match is found, return one specified value, otherwise, return a default value. If no default value, NUll will be returned.
-- DECODE(value, L1, R1, L2, R2,.... default)
--       value represents the value that you are searching for.
--       L1 L2 represents the item value in a list.
--       R1, R2 represents the result if L1 matches value.
--       default represents the result if no match is found.
SELECT customer#, state, 
	DECODE(
		state, 'CA', .08,
    			 'FL', .07,
    							0
	) AS "Sale Tax Rate"
from customers;

-- CASE
-- is similar to the DECODE function, but it can perform IF ELSE processing. 
SELECT lname, fname,
	CASE
		WHEN (MONTH_BETWEEN('01-JUL-09', hiredate) /12) < 4 THEN 'LEVEL 1'
		WHEN (MONTH_BETWEEN('01-JUL-09', hiredate) /12) < 8 THEN 'LEVEL 2'
		WHEN (MONTH_BETWEEN('01-JUL-09', hiredate) /12) < 11 THEN 'LEVEL 3'
		WHEN (MONTH_BETWEEN('01-JUL-09', hiredate) /12) < 15 THEN 'LEVEL 4'
		ELSE 'LEVEL 5'
	END "Retire Level"
FROM employees;
```



### Group-Rows Function

Also called `Multiple-Rows Function`, returns a result per group of rows processed.

<u>All `Group-Rows Function`, except `COUNT(*)`, ignore `NULL` values. If you want to include `NULL` values, please nest the `NVL` inside the `Group-Rows Function`.</u>

```sql
-- SUM
-- Used to calculate the total amount stored in a numeric column for a group of records.
-- SUM([ALL|DISTINCT] n)
--			n represents the column that contains numeric values.
--      DISTINCT is optional that instructs oracle to include only unique value in the calculation.
--      ALL is the default value and also optional that instructs oracle to include all occurrences of values when totaling a field.
SELECT SUM((paideach - cost) * quantity) AS "Total Profit"
	FROM books 
	INNER JOIN orderitem USING(isbn)
	
-- AVG
-- Used to calculate the average amount stored in a numeric column for a group of records.
-- AVG([ALL|DISTINCT] n)
SELECT AVG(retail - cost - NVL(discount, 0)) AS 'Average Profit'
	FROM books;
	
-- COUNT
-- Can count the records  having non-NULL values in a specified column, or count the total records meating a specific condition including those containing NULL values.
-- COUNT([ALL|DISTINCT] n)
SELECT COUNT(DISTINCT category) FROM books;
SELECT COUNT(category) FROM books;

SELECT COUNT(shipdate) FROM orders;
SELECT COUNT(*) FROM orders;

-- MAX
-- Used to calculate the largest values stored in a numeric column for a group of records.
-- MAX([ALL|DISTINCT] c)
--			c can represent any numeric, character, or date column.
SELECT title, MAX(retail - cost - NVL(discount, 0)) AS "Highest Profit" FROM books;

-- MIN
-- Used to calculate the smallest values stored in a numeric column for a group of records.
-- MIN([ALL|DISTINCT] c)
--			c can represent any numeric, character, or date column.
SELECT title, MIN(retail - cost - NVL(discount, 0)) AS "Lowest Profit" FROM books;
```

