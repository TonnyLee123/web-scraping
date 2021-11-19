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
- Filter the result with specify row
  - WHERE customer_id = 1

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
