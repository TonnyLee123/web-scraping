Database
- A collection of data stored in a format that can easily be accessed

Database Management System(DBMS)
- 2 types of DBMS
  - Relational
    - MySQL
    - SQL Server
    - Oracle
  - NoSQL(non relational)





SQL
- Programming language
- 被使用在Relational DBMS
- 唸(Squel)


Schemas
- Show the database the we have in the database server
- sys
  - Database the MySQL internally to do its work. 

Query editor window
# --
- Comment

# USE
- Select database you want to execute.
- 通常key word大寫
  - USE sql_store;
# SELECT
- Specify the columns the we want to retrieve.
  - SELECT * (all columns)
  - SELECT customer_id,  first_name

# FROM
- Specify the table the we want to query.
  - FROM customers   

# WHERE
- Filter the result with specify condition(row).
- 利用WHERE, 選出符合___條件的row
- 先AND後OR
- IN / NOT IN
    - Conbine mutiple OR conditions using OR operator.
- BETWEEN
- LIKE/NOT LIKE 
- REGEXP (Regular Expression)
- IS／IS NOT NULL
    - Find NULL   
```sql
SELECT *
FROM customers
-- 符合___條件的row
WHERE points > 300 AND birth_date > 1986-02-18 AND state = 'VA'
```
IN
```sql 
SELECT *
FROM customers
WHERE state IN('VA', 'GA', 'FL')
-- WHERE state = 'VA' or 'GA' or 'FL'
``` 
BETWEEN
```sql
SELECT *
FROM customers
WHERE points BETWEEN 1000 and 3000
-- WHERE points >= 1000 and points <= 3000
```
LIKE
```sql
SELECT *
FROM customers
WHERE last_name LIKE 'b%'
-- % any number of characters
-- _ single character
-- b% = b 開頭
-- %b% = 中間有b
-- %b = b 結尾
-- _b = 第二個字母為b
-- b__y
```
REGEXP
```sql
SELECT *
FROM customers
WHERE last_name REGEXP 'field'
-- WHERE last_name LIKE '%field%'
-- ^ beginning
-- $ end
-- | or
-- [abcd] 
-- [a-d]
```
IS / IS NOT NULL
```sql
SELECT *
FROM customers
WHERE phone IS NULL
-- WHERE phone IS NOT NULL
```
# ORDER BY
- Sort the ___ column.
  - ORDER BY first_name 

# 總結
- 這四個不一定要使用，但是當需要他們時必須按照以下順序。
- SELECT
- FROM
- WHERE
- ORDER BY

### 範例一
```sql
USE sql_store;

SELECT *
FROM customers
-- WHERE customer_id = 1
ORDER BY first_name 
-- ABC..
```
### 範例二 column數學運算
對原有的 ColumnA 進行運算產生 ColumnB
```sql
USE sql_store;

-- 在points column中運算: points +10
-- AS 為新column取名稱，不一定要AS
SELECT 
	first_name, 
	last_name, 
	points, 
	points + 10 AS new_column, 
-- 'new column' 沒有_
FROM customer
```

### 在column中移除重複的row
```sql
USE sql_store;
-- 在state column中，移除相同的state。
SELECT DISTINCT state 
FROM customers
```
