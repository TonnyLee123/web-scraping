# 1. Database
- A collection of data stored in a format that can easily be accessed

# 2. Database Management System(DBMS)
- 2 types of DBMS
  - Relational
    - MySQL
    - SQL Server
    - Oracle
  - NoSQL(non relational)

# 3.SQL
- Programming language
- 被使用在Relational DBMS
- 唸(Squel)


Schemas
- Show the database the we have in the database server
- sys
  - Database the MySQL internally to do its work. 

Query editor window
## --
- Comment

## USE
- Select database you want to execute.
- 通常key word大寫
  - USE sql_store;
## SELECT
- Specify the columns the we want to retrieve.
  - SELECT * (all columns)
  - SELECT customer_id,  first_name

## FROM
- Specify the table the we want to query.
  - FROM customers   
## JOIN ... ON
- (INNER) JOIN
- OUTER JOIN
    - LEFT JOIN
    - RIGHT JOIN 
    - 當B加入到A時，因為B有些是null，所以不會顯示在A，使用outer table可以把null也顯示在A。
- 加入其他table
- ON (join condition)
- USING(____)      
```sql
SELECT *
-- alias name o
FROM orders o
-- 加入customers Table, 並且確認連結的column
JOIN customers c ON o.customer_id = c.customer_id
```
- Self Join
 ```sql
SELECT * 
FROM employees e
JOIN employees m ON e.reports_to = m.employee_id
 ```
 - Join multiple Tables
 ```sql
 SELECT 
    o.order_id,
    o.order_date,
    c.first_name,
    c.last_name,
    os.name AS status
FROM orders o
-- join multiple tables
JOIN customers c ON o.customer_id = c.customer_id
JOIN order_statuses os ON o.status = os.order_status_id
```
- LEFT JOIN
```sql
SELECT
    c.customer_id,
    c.first_name,
    o.order_id
FROM customers c
-- all the records from the left table are returned whether condition is true or not. 
LEFT JOIN orders o
	ON c.customer_id = o.customer_id
ORDER BY c.customer_id
```
- 綜合
```sql
SELECT 
	o.order_date,
    o.order_id,
	c.first_name,
    s.name,
    os.name AS status
FROM orders o
JOIN customers c
	ON o.customer_id = c.customer_id
    
LEFT JOIN order_statuses os
	ON o.status = os.order_status_id
    
LEFT JOIN shippers s
	ON o.shipper_id = s.shipper_id
ORDER BY o.order_date
```
- USING
```sql
SELECT 
	o.order_id,
    c.first_name
FROM orders o
JOIN customers c
	USING (customer_id)
-- ON o.customer_id = c.customer_id
```
## WHERE
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
## LIMIT 
- 只顯使某row
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
LIMIT
```sql
SELECT *
FROM customers
-- 只顯示前三row
LIMIT 3

-- skip前6行，顯示後3行(7, 8, 9)
LIMIT 6, 3
```
# ORDER BY
- Sort the ___ column.
- DESC
```sql
SELECT *
FROM customers
ORDER BY state  DESC, first_name
-- DESC (descending)
-- 相同state, 會再依照first_name排序
```
# 總結
- 這四個不一定要使用，但是當需要他們時必須按照以下順序。
- SELECT
- FROM
- WHERE
- ORDER BY
- LIMIT
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
