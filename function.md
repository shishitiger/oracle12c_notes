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
| Regular Expressions              | `REGEXP_LIKE`, `REGEXP_SUBSTR`                               |
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

--
```



#### Numeric Functions



#### Date Functions



#### Regular Expression



#### Other Function

