---
tags:
  - Programming_Languages
---
# Core principles
## Definition
_SQL stands for Structured Query Language_
A language to query [[Databases#Relational database|relational databases]].
A SQL database consists of several [[#Table|tables]].
## Database
Contains several [[#Table|tables]].
![[SQL-idea.png]]
## Table
Contains several rows, also known as _entries_.
- Each column represents an attribute
- Each row represents an object.
Therefore, the table represents a set of objects.
For example:

| user_index | Name  | Age | Job        |
| :--------: | :---: | --- | ---------- |
|     0      |  Bob  | 43  | Engineer   |
|     1      | Alice | 32  | Engineer   |
|     2      | Mark  | 45  | Salesman   |
|     3      | Maria | 24  | Researcher |
|     4      | Sara  | 28  | Researcher |
# SQL Queries
_NOTE: Remember the ; character at the end of each query!_
## CREATE DATABASE
```sql
CREATE DATABASE <database_name>;
```
## DROP DATABASE
```sql
DROP DATABASE <database_name>;
```
## CREATE TABLE
```sql
CREATE TABLE <table_name> (
	field1 <DATA_TYPE> <CONSTRATINS> DEFAULT <default_value>,
	field2 <DATA_TYPE> <CONSTRAsTINS> DEFAULT <default_value>
	...
);
```
## DROP TABLE
```SQL
DROP TABLE <table_name>;
```
## INSERT ROW IN TABLE
```SQL
INSERT INTO <table_name> (column1, column2...) VALUES(12, 'Hello');
```
_NOTE: If you are running this command from [[PostgreSQL]], when inserting a string, use single-quoted strings with ' instead of double-quoted strings._
## SELECT + FROM
To retrieve data from a certain table.
Supposing you have this table:

| user_index | Name  | Age | Job        |
| :--------: | :---: | --- | ---------- |
|     0      |  Bob  | 43  | Engineer   |
|     1      | Alice | 32  | Engineer   |
|     2      | Mark  | 45  | Salesman   |
|     3      | Maria | 24  | Researcher |
|     4      | Sara  | 28  | Researcher |
 You can get its information through `SELECT` query:
```SQL
SELECT <column_to_get> FROM <table_name>;
```
- If you want to get all the ages, set `<column_to_get> = Age`
- If you want to get a subset of columns, specify them comma-separated:
```SQL
SELECT <column_to_get1>, <column_to_get2>, ... FROM <table_name>;
```
- If you want to get all the columns, set `<column_to_get> = *`
You can also format the output column names as you want with `AS`:
```SQL
SELECT Name AS usernames FROM <table_name>;
```
Would result in:

| ==usernames== |
| :-----------: |
|      Bob      |
|     Alice     |
|     Mark      |
|     Maria     |
|     Sara      |

You can perform some operations over the selected data, such as a [[Regular Expressions|regexp]] to the rows content. Supposing you want to keep only the initial letter of the usernames, use the `regexp_replace()` method:

```SQL
SELECT regexp_replace(Name, '([a-z])(.*)', '\1') AS usernames FROM <table_name>;
```

## JOIN + ON
To concatenate two table columns. For instance:

- `table_1:`

| user_index | Name  |
| :--------: | :---: |
|     0      |  Bob  |
|     1      | Alice |
|     2      | Mark  |
|     3      | Maria |
|     4      | Sara  |
- `table_2:`

| favourite_colour | user_number | Year |
| :--------------: | :---------: | :--: |
|       red        |      0      | 1999 |
|      green       |      1      | 1999 |
|       red        |      2      | 2001 |
|       red        |      3      | 1980 |
|       blue       |      4      | 1989 |
|      yellow      |      5      | 2003 |
A `JOIN` operation consists of two parts:
Syntax:
```SQL
<source_table> INNER/LEFT/RIGHT/FULL JOIN <dest_table>
	ON <source_tablename>.<fieldname_in_src> = <dest_tablename>.<fieldname_in_dst>;
```
Regardless of the `JOIN` type, the first part:
```SQL
<source_table> INNER/LEFT/RIGHT/FULL JOIN <dest_table>
```
Will take each row in `<source_table>` and, for each one, that row will be appended to each row in `<dest_table>`. For the example above:
```SQL
SELECT * FROM table_1 INNER/LEFT/RIGHT/FULL JOIN table_2;
```
Would result in:

| user_index | Name  | favourite_colour | user_number | Year |
| :--------: | :---: | :--------------: | :---------: | :--: |
|     0      |  Bob  |       red        |      0      | 1999 |
|     0      |  Bob  |      green       |      1      | 1999 |
|     0      |  Bob  |       red        |      2      | 2001 |
|     0      |  Bob  |       red        |      3      | 1980 |
|     0      |  Bob  |       blue       |      4      | 1989 |
|     0      |  Bob  |      yellow      |      5      | 2003 |
|     1      | Alice |       red        |      0      | 1999 |
|     1      | Alice |      green       |      1      | 1999 |
|     1      | Alice |       red        |      2      | 2001 |
|     1      | Alice |       red        |      3      | 1980 |
|     1      | Alice |       blue       |      4      | 1989 |
|     1      | Alice |      yellow      |      5      | 2003 |
|     2      | Mark  |       red        |      0      | 1999 |
|     2      | Mark  |      green       |      1      | 1999 |
|     2      | Mark  |       red        |      2      | 2001 |
|     2      | Mark  |       red        |      3      | 1980 |
|     2      | Mark  |       blue       |      4      | 1989 |
|     2      | Mark  |      yellow      |      5      | 2003 |
|     3      | Maria |       red        |      0      | 1999 |
|     3      | Maria |      green       |      1      | 1999 |
|     3      | Maria |       red        |      2      | 2001 |
|     3      | Maria |       red        |      3      | 1980 |
|     3      | Maria |       blue       |      4      | 1989 |
|     3      | Maria |      yellow      |      5      | 2003 |
|     4      | Sara  |       red        |      0      | 1999 |
|     4      | Sara  |      green       |      1      | 1999 |
|     4      | Sara  |       red        |      2      | 2001 |
|     4      | Sara  |       red        |      3      | 1980 |
|     4      | Sara  |       blue       |      4      | 1989 |
|     4      | Sara  |      yellow      |      5      | 2003 |
Once we have the joined table, we can perform a filtering with `ON`. For example, to take only the rows where the `user_index` coincides with `user_number`:
```SQL
SELECT * FROM table_1 INNER/LEFT/RIGHT/FULL JOIN table_2
ON table_1.user_index = table_2.user_number;
```
The result would be:

| user_index | Name  | favourite_colour | user_number | Year |
| :--------: | :---: | :--------------: | :---------: | :--: |
|     0      |  Bob  |       red        |      0      | 1999 |
|     1      | Alice |      green       |      1      | 1999 |
|     2      | Mark  |       red        |      2      | 2001 |
|    ...     |  ...  |       ...        |     ...     | ...  |

The available `JOIN` operations are:
### INNER JOIN (= JOIN)
_It is the default JOIN_
Supposing the [[#JOIN + ON|same example above]] for `table_1` and `table_2`, when we run an `ON` condition with `INNER JOIN` (or simply `JOIN`; it's the same):

```SQL
SELECT * FROM table_1 INNER/LEFT/RIGHT/FULL JOIN table_2
ON table_1.user_index = table_2.user_number;
```

The result would be:

| user_index | Name  | favourite_colour | user_number | Year |
| :--------: | :---: | :--------------: | :---------: | :--: |
|     0      |  Bob  |       red        |      0      | 1999 |
|     1      | Alice |      green       |      1      | 1999 |
|     2      | Mark  |       red        |      2      | 2001 |
|     3      | Maria |       red        |      3      | 1980 |
|     4      | Sara  |       blue       |      4      | 1989 |
**As you can see, `table_2` contains another row, which is:**

|  5  | yellow | 2003 |
| :-: | :----: | :--: |

However, the `ON` operation has not found any entry row that fulfills the condition `table_1.user_index = table_2.user_number` for the user 5, so that row won't be considered in `INNER JOIN`:

| user_index | Name  | favourite_colour | user_number | Year |
| :--------: | :---: | :--------------: | :---------: | :--: |
|     0      |  Bob  |       red        |      0      | 1999 |
|     0      |  Bob  |      green       |      1      | 1999 |
|     0      |  Bob  |       red        |      2      | 2001 |
|     0      |  Bob  |       red        |      3      | 1980 |
|     0      |  Bob  |       blue       |      4      | 1989 |
|     0      |  Bob  |      yellow      |      5      | 2003 |
|     1      | Alice |       red        |      0      | 1999 |
|     1      | Alice |      green       |      1      | 1999 |
|     1      | Alice |       red        |      2      | 2001 |
|     1      | Alice |       red        |      3      | 1980 |
|     1      | Alice |       blue       |      4      | 1989 |
|     1      | Alice |      yellow      |      5      | 2003 |
|     2      | Mark  |       red        |      0      | 1999 |
|     2      | Mark  |      green       |      1      | 1999 |
|     2      | Mark  |       red        |      2      | 2001 |
|     2      | Mark  |       red        |      3      | 1980 |
|     2      | Mark  |       blue       |      4      | 1989 |
|     2      | Mark  |      yellow      |      5      | 2003 |
|    ...     |  ...  |       ...        |     ...     | ...  |
|     4      | Sara  |       red        |      0      | 1999 |
|     4      | Sara  |      green       |      1      | 1999 |
|     4      | Sara  |       red        |      2      | 2001 |
|     4      | Sara  |       red        |      3      | 1980 |
|     4      | Sara  |       blue       |      4      | 1989 |
|     4      | Sara  |      yellow      |      5      | 2003 |

### LEFT JOIN
Supposing the [[#JOIN + ON|same example above]] for `table_1` and `table_2`, when we run an `ON` condition with `LEFT JOIN`, the ON's non-matching rows will be kept for the left-side table.
For example, we make a `LEFT JOIN` from table **2** to table 1:
```SQL
SELECT * FROM table_2 LEFT JOIN table_1;
```

| favourite_colour | user_number | Year | user_index | Name  |
| :--------------: | :---------: | :--: | :--------: | :---: |
|       red        |      0      | 1999 |     0      |  Bob  |
|       red        |      0      | 1999 |     1      | Alice |
|       red        |      0      | 1999 |     2      | Mark  |
|       red        |      0      | 1999 |     3      | Maria |
|       red        |      0      | 1999 |     4      | Sara  |
|      green       |      1      | 1999 |     0      |  Bob  |
|      green       |      1      | 1999 |     1      | Alice |
|      green       |      1      | 1999 |     2      | Mark  |
|      green       |      1      | 1999 |     3      | Maria |
|      green       |      1      | 1999 |     4      | Sara  |
|       red        |      2      | 2001 |     0      |  Bob  |
|       red        |      2      | 2001 |     1      | Alice |
|       red        |      2      | 2001 |     2      | Mark  |
|       red        |      2      | 2001 |     3      | Maria |
|       red        |      2      | 2001 |     4      | Sara  |
|       red        |      3      | 1980 |     0      |  Bob  |
|       red        |      3      | 1980 |     1      | Alice |
|       red        |      3      | 1980 |     2      | Mark  |
|       red        |      3      | 1980 |     3      | Maria |
|       red        |      3      | 1980 |     4      | Sara  |
|       blue       |      4      | 1989 |     0      |  Bob  |
|       blue       |      4      | 1989 |     1      | Alice |
|       blue       |      4      | 1989 |     2      | Mark  |
|       blue       |      4      | 1989 |     3      | Maria |
|       blue       |      4      | 1989 |     4      | Sara  |
|      yellow      |      5      | 2003 |     0      |  Bob  |
|      yellow      |      5      | 2003 |     1      | Alice |
|      yellow      |      5      | 2003 |     2      | Mark  |
|      yellow      |      5      | 2003 |     3      | Maria |
|      yellow      |      5      | 2003 |     4      | Sara  |

Then, we make a `ON` condition with the `LEFT JOIN`:
```SQL
SELECT * FROM table_2 LEFT JOIN table_1
ON table_2.user_numer=table_1.user_index;
```
As we are performing a `LEFT JOIN`, we are enforcing to keep all **distinct** entries of the left-side table (`table_2`), so the entry:

|      yellow      |      5      | 2003 |
| :--------------: | :---------: | :--: |

Will be in the result, but with `NULL` matching. Consequently, the result will be:

| favourite_colour | user_number | Year | user_index | Name  |
| :--------------: | :---------: | :--: | :--------: | :---: |
|       red        |      0      | 1999 |     0      |  Bob  |
|      green       |      1      | 1999 |     1      | Alice |
|       red        |      2      | 2001 |     2      | Mark  |
|       red        |      3      | 1980 |     3      | Maria |
|       blue       |      4      | 1989 |     4      | Sara  |
|      **yellow**      |      **5**      | **2003** |            |       |
### RIGHT JOIN
The same as [[#LEFT JOIN]], but for right-side table: we enforce the result to have all the distinct rows regardless of whether there is matching in the left-side table or not.
For the [[#JOIN + ON|example above]]:
```SQL
SELECT * FROM table_1 RIGHT JOIN table_2 ON table_1.user_index=table_2.user_number;
```
Would result in:

| user_index | Name  | favourite_colour | user_number | Year |
| :--------: | :---: | :--------------: | :---------: | :--: |
|     0      |  Bob  |       red        |      0      | 1999 |
|     1      | Alice |      green       |      1      | 1999 |
|     2      | Mark  |       red        |      2      | 2001 |
|     3      | Maria |       red        |      3      | 1980 |
|     4      | Sara  |       blue       |      4      | 1989 |
|            |       |      yellow      |      5      | 2003 |

### FULL JOIN
Supposing you have a user in `table_1` that does not exist in `table_2`, but also you have a user in `table_2` that does not exist in `table_1`:

- `table_1:`

| user_index |   Name    |
| :--------: | :-------: |
|     0      |    Bob    |
|     1      |   Alice   |
|     2      |   Mark    |
|     3      |   Maria   |
|     4      |   Sara    |
|    ==999==     | ==Anonymous== |
- `table_2:`

| favourite_colour | user_number | Year |
| :--------------: | :---------: | :--: |
|       red        |      0      | 1999 |
|      green       |      1      | 1999 |
|       red        |      2      | 2001 |
|       red        |      3      | 1980 |
|       blue       |      4      | 1989 |
|      ==yellow==      |      ==5==      | ==2003== |
With `FULL JOIN`, you can ensure that all the distinct entries will be kept for both sides: left and right tables. Therefore, the result of the following command:
```SQL
SELECT * FROM table_1 FULL JOIN table_2 ON table_1.user_index=table_2.user_number;
```
Will be:

| user_index |   Name    | favourite_colour | user_number | Year |
| :--------: | :-------: | :--------------: | :---------: | :--: |
|     0      |    Bob    |       red        |      0      | 1999 |
|     1      |   Alice   |      green       |      1      | 1999 |
|     2      |   Mark    |       red        |      2      | 2001 |
|     3      |   Maria   |       red        |      3      | 1980 |
|     4      |   Sara    |       blue       |      4      | 1989 |
|            |           |      yellow      |      5      | 2003 |
|    999     | Anonymous |                  |             |      |

## WHERE
To filter the resulting table based on some field value. For example, if you have this table:

| user_index |   Name    | favourite_colour | user_number | Year |
| :--------: | :-------: | :--------------: | :---------: | :--: |
|     0      |    Bob    |       red        |      0      | 1999 |
|     1      |   Alice   |      green       |      1      | 1999 |
|     2      |   Mark    |       red        |      2      | 2001 |
|     3      |   Maria   |       red        |      3      | 1980 |
|     4      |   Sara    |       blue       |      4      | 1989 |
|            |           |      yellow      |      5      | 2003 |
|    999     | Anonymous |                  |             |      |

```SQL
SELECT * FROM table_1 FULL JOIN table_2 ON table_1.user_index=table_2.user_number
WHERE Year=1999;
```
Would result in:

| user_index | Name  | favourite_colour | user_number | Year |
| :--------: | :---: | :--------------: | :---------: | :--: |
|     0      |  Bob  |       red        |      0      | 1999 |
|     1      | Alice |      green       |      1      | 1999 |

## GROUP BY
We can flatten several rows into a single one. However, what do we do with the elements belonging to the same group?
- We can compute their average, their sum, their max, their min... Summarizing, we must compute some operation to convert several values into one. These are called **AGGREGATE FUNCTIONS** and are defined in [[#SELECT + FROM|SELECT]] part.

Example: we want to compute the average user height grouping by favourite colour from this table:

| user_index | Name  | favourite_colour | user_number | Year | Height |
| :--------: | :---: | :--------------: | :---------: | :--: | ------ |
|     0      |  Bob  |       red        |      0      | 1999 | 176    |
|     1      | Alice |      green       |      1      | 1999 | 177    |
|     77     | Seth  |       red        |     77      | 1999 | 171    |
|     78     | Josh  |       red        |     78      | 1999 | 175    |
|     79     | Julia |      green       |     79      | 1999 | 172    |
|    ...     |  ...  |       ...        |     ...     | ...  | ...    |

```SQL
SELECT *, AVG(Height) FROM table_1 FULL JOIN table_2 ON table_1.user_index=table_2.user_number
WHERE Year=1999
GROUP BY favourite_colour;
```

The result would look like this:

| favourite_colour | AVG(Height) |
| ---------------- | ----------- |
| red              | ...         |
| green            | ...         |

### HAVING
To filter the grouped results.
For example, we have the previous table:

```SQL
SELECT *, AVG(Height) FROM table_1 FULL JOIN table_2 ON table_1.user_index=table_2.user_number
WHERE Year=1999
GROUP BY favourite_colour;
```

| favourite_colour | AVG(Height) |
| ---------------- | ----------- |
| red              | ...         |
| green            | ...         |

And we want to get all the rows where `AVG(Height) > 170`:

```SQL
SELECT *, AVG(Height) FROM table_1 FULL JOIN table_2 ON table_1.user_index=table_2.user_number
WHERE Year=1999
GROUP BY favourite_colour
HAVING AVG(Height) > 170;
```
## ORDER BY
To order the results by some column in ascending or descending order.
Example:
Order in descending order:
```SQL
SELECT *, AVG(Height) FROM table_1 FULL JOIN table_2 ON table_1.user_index=table_2.user_number
WHERE Year=1999
GROUP BY favourite_colour
HAVING AVG(Height) > 170
ORDER BY AVG(Height) DESC;
```
Order in ascending order:
```SQL
SELECT *, AVG(Height) FROM table_1 FULL JOIN table_2 ON table_1.user_index=table_2.user_number
WHERE Year=1999
GROUP BY favourite_colour
HAVING AVG(Height) > 170
ORDER BY AVG(Height) ASC;
```
## LIMIT
To limit the numer of rows in response. For example, to only show the first `4` rows:
```SQL
SELECT *, AVG(Height) FROM table_1 FULL JOIN table_2 ON table_1.user_index=table_2.user_number
WHERE Year=1999
GROUP BY favourite_colour
HAVING AVG(Height) > 170
ORDER BY AVG(Height) ASC
LIMIT 4;
```
## OFFSET
We may want to show only `4` rows, but starting from a certain row number. To do so, we use `OFFSET`, where we set where row to start, indexed from `0`. For example, we want to show only `4` rows in query result, but starting from the third row:

```SQL
SELECT *, AVG(Height) FROM table_1 FULL JOIN table_2 ON table_1.user_index=table_2.user_number
WHERE Year=1999
GROUP BY favourite_colour
HAVING AVG(Height) > 170
ORDER BY AVG(Height) ASC
LIMIT 4
OFFSET 2;
```
## UPDATE
Update a row in a [[#Table]]:
```SQL
UPDATE mytable
SET column_name1 = value1,
	column_name2 = value2,
	...
WHERE condition;
```
### Append string to current column value
```SQL
UPDATE mytable
SET column_name1 = CONCAT(column_name1, 'mystring'),
	column_name2 = value2,
	...
WHERE condition;
```
_Note: Use single quotes as second argument for CONCAT() function!_
## Example template
```SQL
SELECT DISTINCT column, AGG_FUNC(_column_or_expression_) AS ..., …
FROM mytable JOIN another_table
	ON mytable.column = another_table.column
WHERE _constraint_expression_
GROUP BY column HAVING _constraint_expression_
ORDER BY _column_ ASC/DESC
LIMIT _count_
OFFSET _COUNT_;
```
## DELETE
```SQL
DELETE FROM mytable WHERE condition;
```
# References
- [SQLBolt - Learn SQL - Introduction to SQL](https://sqlbolt.com/)