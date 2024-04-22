---
tags:
  - Programming_Languages
---
# Core principles
## Definition
_SQL stands for Structured Query Language_
A language to query [[Databases#Relational Database|relational databases]].
A SQL database consists of several [[#Table|tables]].
## Table


# SQL Queries
## FROM
To define what [[#Table|table]] to query from.
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
	ON <source_tablename>.<fieldname_in_src> = <dest_tablename>.<fieldname_in_dst>
```
Regardless of the `JOIN` type, the first part:
```SQL
<source_table> INNER/LEFT/RIGHT/FULL JOIN <dest_table>
```
Will take each row in `<source_table>` and, for each one, that row will be appended to each row in `<dest_table>`. For the example above:
```SQL
table_1 INNER/LEFT/RIGHT/FULL JOIN table_2
```
It would result in:

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

The available `JOIN` operations are:
### INNER JOIN




### LEFT JOIN

### RIGHT JOIN

### FULL JOIN


## WHERE
## GROUP BY
### HAVING
## ORDER BY
## LIMIT
## OFFSET
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
# References
- [SQLBolt - Learn SQL - Introduction to SQL](https://sqlbolt.com/)
- 