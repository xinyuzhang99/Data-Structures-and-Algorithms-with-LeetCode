# SQL Cheatsheet

## 1. SELECT/UPDATE/DELETE/INSERT

- `SELECT` - extract data from a database

  ```sql
  SELECT column1, column2, ...  # field name
  FROM table_name
  
  # select all fields in the table
  SELECT * FROM table_name;  # = SELECT COUNT(1) FROM table_name;
  
  # SELECT DISTINCT: return only distinct (different/unique) values
  SELECT DISTINCT column1, column2, ...
  FROM table_name;
  
  # list the number of different (distinct) field records
  SELECT COUNT(DISTINCT Country) FROM Customers;
  ```

  - <u>Aliases</u>

    --> give a table or a column a temporary name

    ```sql
    SELECT column1_name AS alias1_name, column2_name AS alias2_name
    FROM table_name;
    
    SELECT column_name(s)
    FROM table_name AS alias_name;
    
    # Note: It requires double quotation marks or square brackets if the alias name contains spaces:
    SELECT ContactName AS [Contact Person]
    FROM Customers;
    ```

    Example 1:

    ```sql
    # combine multiple columns to create an alias
    SELECT CustomerName, Address + ', ' + PostalCode + ' ' + City + ', ' + Country AS Address
    FROM Customers;
    ```

    Result:

    <img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20230523172707244.png" alt="image-20230523172707244" style="zoom:50%;" />

    Example 2: select all the orders from the customer with CustomerID=4 (Around the Horn). We use the "Customers" and "Orders" tables, and give them the table aliases of "c" and "o" respectively

    ```sql
    # with aliases
    SELECT o.OrderID, o.OrderDate, c.CustomerName
    FROM Customers AS c, Orders AS o
    WHERE c.CustomerName='Around the Horn' AND c.CustomerID=o.CustomerID;
    
    # without aliases
    SELECT Orders.OrderID, Orders.OrderDate, Customers.CustomerName
    FROM Customers, Orders
    WHERE Customers.CustomerName='Around the Horn' AND Customers.CustomerID=Orders.CustomerID;
    ```

  - `SELECT INTO`: copy data from one table into a new table

    ```sql
    # Copy all columns into a new table
    SELECT *
    INTO newtable [IN externaldb]
    FROM oldtable
    WHERE condition;
    
    # Copy only some columns into a new table
    SELECT column1, column2, column3, ...
    INTO newtable [IN externaldb]
    FROM oldtable
    WHERE condition;
    
    # Create a new, empty table using the schema of another
    SELECT * INTO newtable
    FROM oldtable
    WHERE 1 = 0;  # return no data --> create a new table
    ```

    Example:

    ```sql
    SELECT Customers.CustomerName, Orders.OrderID
    INTO CustomersOrderBackup2017
    FROM Customers
    LEFT JOIN Orders ON Customers.CustomerID = Orders.CustomerID;
    ```

- `WHERE`: used in `SELECT`, `UPDATE`, `DELETE` statements to filter records

  ```sql
  SELECT * FROM Customers
  WHERE Country='Mexico';  # or: WHERE CustomerID=1;
  ```

  - <u>Operators</u>

    - `=/>/</>=/<=`

    - `<>/!=`: not equal

    - `BETWEEN`: select values between a certain range. <u>The `BETWEEN` operator is inclusive []</u>

      ```sql
      SELECT column_name(s)
      FROM table_name
      WHERE column_name BETWEEN value1 AND value2;
      
      # Example: BETWEEN Number Values
      SELECT * FROM Products
      WHERE Price BETWEEN 10 AND 20 
      AND CategoryID NOT IN (1, 2, 3);
      
      # Example: BETWEEN Text Values
      SELECT * FROM Products
      WHERE ProductName BETWEEN 'Carnarvon Tigers' AND 'Mozzarella di Giovanni'
      ORDER BY ProductName;
      
      # Example: BETWEEN Dates
      SELECT * FROM Orders
      WHERE OrderDate BETWEEN '1996-07-01' AND '1996-07-31'; # or: #07/01/1996# AND #07/31/1996# 
      ```

    - `LIKE`: search for a specified pattern. Eg. `SELECT * FROM Customers WHERE City LIKE 's%'`: select cities which start at 's'

      -  The percent sign (%) represents **zero, one, or multiple** characters
      -  The underscore sign (_) represents one, single character

      <img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20230515172503004.png" alt="image-20230515172503004" style="zoom:50%;" />

      ```sql
      SELECT * FROM Customers
      WHERE CustomerName NOT LIKE 'a%'  # not start with 'a'
      ```

      - <u>Wildcard Characters</u>: used with the `LIKE` operator

        ![image-20230515174505491](/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20230515174505491.png)

        All the wildcards can also be used in combinations!

        ```sql
        # Example
        SELECT * 
        FROM Customers
        WHERE City LIKE '[a-c]%'|'[abc]%'; # start with "a""b""c"
        WHERE City LIKE '[!bsp]%'; # = WHERE City NOT LIKE '[bsp]%'
        ```

    - `IN`: To specify multiple possible values for a column.

      Eg. `SELECT * FROM Customers WHERE City IN ('Paris','London');`

      ```sql
      SELECT column_name(s)
      FROM table_name
      WHERE column_name IN (value1, value2, ...);
      
      SELECT column_name(s)
      FROM table_name
      WHERE column_name IN (SELECT STATEMENT);
      
      ## Example:
      SELECT * FROM Customers
      WHERE Country IN ('Germany', 'France', 'UK');
      
      # Select all customers from the same countries as the Suppliers
      SELECT * FROM Customers
      WHERE Country IN (SELECT Country FROM Suppliers);
      ```

    - `AND/OR/NOT`: 

      ```sql
      SELECT * FROM Customers WHERE Country='Germany' AND (City='Berlin' OR City='München');
      
      SELECT * FROM Customers
      WHERE NOT Country='Germany' AND NOT Country='USA'
      ```

- `ORDER BY`: sort the result-set in ascending or descending order

  ```sql
  SELECT column1, column2, ...
  FROM table_name
  ORDER BY column1, column2, ... ASC|DESC;  # Ascending by default
  
  # Example
  SELECT * FROM Customers
  ORDER BY Country, CustomerName;  # order by Country, but if some rows have the same Country, order them by CustomerName
  
  SELECT * FROM Customers
  ORDER BY Country ASC, CustomerName DESC; # sort ascending by "Country" and descending by "CustomerName"
  ```

- `GROUP BY`: group **rows that have the same values** into summary rows

- `INSERT INTO`: insert new records in a table

  - Specify both the column names and the values to be inserted:

    ```sql
    INSERT INTO table_name (column1, column2, column3, ...)
    VALUES (value1, value2, value3, ...);
    ```

  - Add values for all the columns of the table:

    ```sql
    INSERT INTO table_name
    VALUES (value1, value2, value3, ...);
    ```

  - Copy all columns from one table to another table:

    ```sql
    INSERT INTO table2
    SELECT * FROM table1
    WHERE condition;
    ```

  - Copy only some columns from one table into another table:

    ```sql
    INSERT INTO table2 (column1, column2, column3, ...)
    SELECT column1, column2, column3, ...
    FROM table1
    WHERE condition;
    ```

- NULL values

  ```sql
  SELECT column_names
  FROM table_name
  WHERE column_name IS NULL;  # or: IS NOT NULL
  ```

- `TOP, LIMIT, FETCH FIRST or ROWNUM`

  ```sql
  ## SELECT TOP: specify the number of records to return
  # SQL Server/ MS Access Syntax
  SELECT TOP number|percent column_name(s)|*
  FROM table_name
  WHERE condition;
  
  # Important!!: MySQL Syntax
  SELECT column_name(s)
  FROM table_name
  WHERE condition
  LIMIT number;
  # LIMIT K OFFSET X; --> from xth row, return K records after
  
  # Eg.
  SELECT TOP 3 *
  FROM Customers
  WHERE Country='Germany'
  ```

- `Min/Max`: return the `min/max` values of the selected column

  ```sql
  ## Min()
  SELECT MIN(column_name)
  FROM table_name
  WHERE condition;
  
  ## Max()
  SELECT MAX(column_name)
  FROM table_name
  WHERE condition;
  
  ## Eg.
  SELECT MIN(Price) FROM Products;  # Result: MIN(Price): 2.5
  SELECT MIN(Price) FROM Products AS SmallestPrice; # Result: SmallestPrice: 2.5
  ```

- `COUNT(), AVG(), SUM()`

  ```sql
  ## Count(): return the number of rows that matches a specified criterion
  SELECT COUNT(column_name)
  FROM table_name
  WHERE condition;
  
  ## AVG(): return the average value of a numeric column
  SELECT AVG(column_name)
  FROM table_name
  WHERE condition;
  
  ## SUM(): return the total sum of a numeric column
  SELECT SUM(column_name)
  FROM table_name
  WHERE condition;
  
  SELECT SUM(Quantity)
  FROM OrderDetails;
  ```

  - NULL values are ignored during count/avg/sum

- `UPDATE` - update data in a database

  ```sql
  UPDATE table_name
  SET column1=value1, column2=value2, ...
  WHERE condition;  # specify which record(s) that should be updated. 
  # If you omit the WHERE clause, all records in the table will be updated!
  
  UPDATE Customers
  SET ContactName='', City=''
  WHERE CustomerID=1
  ```

- `DELETE` - delete data from a database

  ```SQL
  DELETE FROM table_name WHERE condition;
  ```

- `EXISTS`: test for the existence of any record in a subquery --> return TRUE if the subquery returns one or more records

  ```sql
  SELECT column_name(s)
  FROM table_name
  WHERE EXISTS
  (SELECT column_name FROM table_name WHERE condition);
  ```

- `ANY/ALL`: perform a **comparison** between a single column value and a range of other values

  - The `ANY` operator:

    - return a boolean value as a result
    - return TRUE if ANY of the subquery values meet the condition

    `ANY` means that the condition will be true if the operation is true for any of the values in the range.

    ```sql
    SELECT column_name(s)
    FROM table_name
    WHERE column_name operator ANY
      (SELECT column_name
      FROM table_name
      WHERE condition);
    ```

  - The `ALL` operator:

    - return a boolean value as a result
    - return TRUE if ALL of the subquery values meet the condition
    - is used with `SELECT`, `WHERE` and `HAVING` statements

    `ALL` means that the condition will be true only if the operation is true for all values in the range. 

    ```sql
    # ALL Syntax With SELECT
    SELECT ALL column_name(s)   # list all elements in the column
    FROM table_name
    WHERE condition;
    
    # ALL Syntax With WHERE or HAVING
    SELECT column_name(s)
    FROM table_name
    WHERE column_name operator ALL
      (SELECT column_name
      FROM table_name
      WHERE condition);
    ```

- `CASE`: like `if-then-else` statement

  ```sql
  CASE
      WHEN condition1 THEN result1
      WHEN condition2 THEN result2
      WHEN conditionN THEN resultN
      ELSE result
  END
  AS new_column_name;
  ```

## 2. JOIN / UNION

--> used to combine rows from two or more tables, based on a related column between them.

- Here are the different types of the JOINs in SQL:

  - `(INNER) JOIN`: Returns records that have matching values in both tables
  - `LEFT (OUTER) JOIN`: Returns **all records from the left** table, and the **matched records from the right** table
  - `RIGHT (OUTER) JOIN`: Returns **all records from the right** table, and the **matched records from the left** table
  - `FULL (OUTER) JOIN`: Returns all records when there is a match in either left or right table
  - `CROSS JOIN`: return all records from both tables (table1 and table2)

  <img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20230523180059886.png" alt="image-20230523180059886" style="zoom: 50%;" />

- `INNER JOIN`: select records that have matching values in both tables

  ```sql
  SELECT column_name(s)    # the column_name(s) will be the shown columns in the final table
  FROM table1
  INNER JOIN table2
  ON table1.column_name = table2.column_name;
  
  # Join three tables
  SELECT Orders.OrderID, Customers.CustomerName, Shippers.ShipperName
  FROM ((Orders
  INNER JOIN Customers ON Orders.CustomerID = Customers.CustomerID)
  INNER JOIN Shippers ON Orders.ShipperID = Shippers.ShipperID);
  ```

- `LEFT JOIN`: return all records from the left table (table1), and the matching records from the right table (table2). If there are no matching records from the right table, the value will be NULL.

  ```sql
  SELECT column_name(s)
  FROM table1
  LEFT JOIN table2
  ON table1.column_name = table2.column_name;
  ```

- `RIGHT JOIN`: return all records from the right table (table2), and the matching records from the left table (table1). If there are no matching records from the left table, the value will be NULL.

  ```sql
  SELECT column_name(s)
  FROM table1
  RIGHT JOIN table2
  ON table1.column_name = table2.column_name;

- `FULL OUTER JOIN`: return all records when there is a match in left (table1) **<u>or</u>** right (table2) table records

  ```sql
  SELECT column_name(s)
  FROM table1
  FULL OUTER JOIN table2   # cannot do in MySQL
  ON table1.column_name = table2.column_name
  WHERE condition;
  ```

  --> even if there aren't matches, also will be listed

  - Implement **OUTER JOIN** in MySQL by taking a LEFT JOIN and RIGHT JOIN union

    ```sql
    SELECT column_name(s)
    FROM
    	(SELECT * FROM table 1 LEFT JOIN table 2 USING()
       UNION
       SELECT * FROM table 1 RIGHT JOIN table 2 USING())
    AS new_table_name     # Important!!
    WHERE condition;
  
- `CROSS JOIN`: return all records from both tables

  <font color=red>--> perform a cartesian product between two tables -->  return all matching records from both tables whether the other table matches or not/all possible combinations of all rows</font>

  - Have no `ON` clause because you're just joining everything to everything
  - `inner join` without `on` condition = `cross join`

  ![MySQL CROSS JOIN](https://www.w3schools.com/mysql/img_crossjoin.png)

  ```sql
  SELECT column_name(s)
  FROM table1
  CROSS JOIN table2;
  ```

  - Use `CROSS JOIN` and `LEFT JOIN/RIGHT JOIN` to concatenate three tables

    ```sql
    SELECT column_name(s)
    FROM table1 CROSS JOIN table2
    LEFT JOIN table 3 ON ...;

- Self join: the table is joined with itself <font color=blue>**[used when there exists <u>duplicate</u>]**</font>

  ```sql
  SELECT column_name(s)
  FROM table1 T1, table1 T2  # T1 and T2 are different table aliases for the same table.
  WHERE condition;
  ```

  Example: match customers that are from the same city

  ```sql
  SELECT A.CustomerName AS CustomerName1, B.CustomerName AS CustomerName2, A.City
  FROM Customers A, Customers B    # Customers is the table
  WHERE A.CustomerID <> B.CustomerID  # <>: not equal
  AND A.City = B.City
  ORDER BY A.City;
  ```

- `UNION`: combine the result-set of two or more `SELECT` statements ==[Union连接行]==

  - Every `SELECT` statement within `UNION` must have the **same number of columns**
  - The columns must also have **similar data types**
  - The columns in every `SELECT` statement must also be in the **same order**
  - **Note:** The column names in the result-set are usually equal to the column names in the first `SELECT` statement.

  ```sql
  # UNION: show only distinct values
  SELECT column_name(s) FROM table1
  UNION
  SELECT column_name(s) FROM table2;
  
  # UNION ALL: show duplicate values
  SELECT column_name(s) FROM table1
  UNION ALL
  SELECT column_name(s) FROM table2;
  ```

## 3. 

- `CREATE DATABASE` - creates a new database
- `ALTER DATABASE` - modifies a database
- `CREATE TABLE` - creates a new table
- `ALTER TABLE` - modifies a table
- `DROP TABLE` - deletes a table
- `CREATE INDEX` - creates an index (search key)
- `DROP INDEX` - deletes an index

## 4. MySQL String Functions

- `UPPER(A)/LOWER(A)` (where A is a string)

- `SUBSTR(A,index,length)`: extract the substring (length is optional)

- `CONCAT(A,B)`: concat two strings A + B

- `GROUP_CONCAT()`: 

  ```sql
  GROUP_CONCAT(DISTINCT expression   # 在连接分组之前消除组中的重复值
      ORDER BY expression						 # 在连接之前按升序或降序排序值
      SEPARATOR sep);								 # 指定在组中的值之间插入的文字值。如果不指定分隔符，则GROUP_CONCAT函数使用逗号(，)作为默认分隔符。
  ```

- `LENGTH()`: return the string length in "bytes"

  `CHAR_LENGTH()`: return the string length in "characters"

  --> especially relevant for Unicode (most characters are encoded in two bytes) and UTF-8 (the number of bytes varies)

  ```sql
  select length(_utf8 '€'), char_length(_utf8 '€')
  # --> 3, 1
  ```

## 5. MySQL Numeric Functions

- `ROUND(number, num of decimals)`: round a number to a specified number of decimal places
- `SUM(expression)` --> **Note:** NULL values are ignored.

## 6. MySQL Date Functions

- `DATEDIFF()`: return the <u>number of days</u> between two date values --> `DATEDIFF(date1, date2) = date1 - date2` 包含负值 

- `DATE_FORMAT(date, format)`: format a date as specified --> `DATE_FORMAT("2017-06-15", "%Y")`

  ![image-20230718095107059](/Users/xinyuzhang/Library/Application%20Support/typora-user-images/image-20230718095107059.png)

- `DATE_SUB(date, INTERVAL value interval)`: subtract a time/date interval from a date and then return the date

  Eg. `SELECT DATE_SUB("2017-06-15 09:34:21", INTERVAL 15 MINUTE);`

  <img src="/Users/xinyuzhang/Library/Application%20Support/typora-user-images/image-20230724093106481.png" alt="image-20230724093106481" style="zoom:50%;" />

## 7. MySQL Advanced Functions

- `ISNULL(expression, value)`: If `expression` is NULL, return `value` --> replace values with the value we want

- `SELECT IFNULL(expression, alt_value)`: If `expression` is NULL, return `alt_value`; if `expression` is not NULL, keep the value of `expression`

- `IF(condition, value_if_true, value_if_false)`: return the second parameter if a condition is TRUE, return the third parameter if a condition is FALSE

- `CAST(expression AS datatype(length))`: convert a value (of any type) into a specified datatype
  - Datatype: bigint, int, smallint, tinyint, bit, decimal, numeric, money, smallmoney, float, real, datetime, smalldatetime, char, varchar, text, nchar, nvarchar, ntext, binary, varbinary, or image

- **Regular Expressions (Regexp)**: a pattern matching operation in SQL

  <img src="../../../../Library/Application%20Support/typora-user-images/image-20240111143424573.png" alt="image-20240111143424573" style="zoom:50%;" />

  Example:

  ```sql
  SELECT name FROM person_tbl WHERE name REGEXP '^st';
  ```

## 8. MySQL Window Function

- **概念**

  窗口函数，包括两个内容，一个是**窗口**，要明白窗口的概念，另一个是**函数**，特指应用在窗口上的函数。<font color=blue>**窗口实现数据选择，函数实现数据处理。**</font>

  `函数名（字段名）OVER（子句）`

  函数参数部分填需要被加工的字段名称，over是关键字，用来指定函数执行的窗口范围，包含三个分析子句：

  1. 分组子句：partiton by <要分列的组>
  2. 排序子句：order by <要排序的列>
  3. 窗口子句：rows between <数据范围> --> 限定具体窗口大小

  `函数名(字段名) over(partiton by <要分列的组> order by <要排序的列> rows between <数据范围>)`

  ![窗口函数](https://lusuzi.com/images/%E4%B8%80%E6%96%87%E6%90%9E%E6%87%82%E7%AA%97%E5%8F%A3%E5%87%BD%E6%95%B0/window_func.svg)

- **数据选择**

  group by分组后做的是**整组聚合运算**，各组里如果有多个行处理后最终只会有一行返回，而partition by它分组后做的是<font color=blue>**逐行按窗口计算**，不管用什么函数处理，一行记录得到一行记录，返回的行还是那么多 --> 适用于进行滚动运算时！</font>。

  ```sql
  rows between 2 preceding and current row         # 取当前行和前面行
  rows between unbounded preceding and current row # 包括本行和之前所有的行
  rows between current row and unbounded following # 包括本行和之后所有的行
  rows between 3 preceding and current row         # 包括本行和前面三行
  rows between 3 preceding and 1 following         # 从前面三行和下面一行,总共五行
  ```

  <img src="https://lusuzi.com/images/%E4%B8%80%E6%96%87%E6%90%9E%E6%87%82%E7%AA%97%E5%8F%A3%E5%87%BD%E6%95%B0/image-20220218222650750.png" alt="image-20220218222650750" style="zoom:50%;" />

- **数据处理**

  - <u>1. 专有函数</u>: 窗口环境下专有的函数，主要有排序函数三巨头，偏移函数，切分函数ntile

    1. `rank()`：为跳跃排序，结果相同的两个数据并列，例如并列第一，则为1134
    2. `dense_rank()`：为有重复的连续排序，结果相同的两个数据并列，例如并列第一，则为1123
    3. `row_number()`：为不重复的连续排序，例如并列第一，照样为1234
    4. `ntile(n)`：用于将分组数据平均切分成n块，如果切分的每组数量不均等，则第一组分得的数据更多
    5. 偏移函数，用于取出同一字段的前N行数据或后N行数据，作为单独的列，分别是`lead(str, n, default)`和`lag(str, n, default)`，n表示前/后n行数据,默认值为1，default表示如果取值范围已经超过整个表的返回值，可以不填，不填默认返回N/A
    6. 位置函数，用于取出第一行的`first_value()`函数和最后一行的`last_value()`函数，以及取出第n个的`nth_value(str,n)`函数
    7. 分布函数， 包括`PERCENT_RANK()`每行按照公式`(rank-1) / (rows-1)`进行计算。其中，`rank`为`RANK()函数`产生的序号，`rows`为当前窗口的记录总行数。`CUME_DIST()`则按rank/rows进行计算，即当前行的行号/总行数。

  - <u>2. 聚合类函数 Aggregate Window Function</u>：普通场景下与group by一起使用的那类函数，作用相同

    `SUM(), COUNT(), AVERAGE(), MAX(), MIN()`

Window functions applies **aggregate and ranking** functions over a particular window (set of rows). OVER clause is used with window functions to define that window. OVER clause does two things : 

- Partitions rows into form set of rows. (`PARTITION BY` clause is used) 
- Orders rows within those partitions into a particular order. (`ORDER BY` clause is used) 

**Note:** If partitions aren’t done, then ORDER BY orders all rows of table. 

```sql
SELECT coulmn_name1, 
 window_function(cloumn_name2)
 OVER([PARTITION BY column_name1] [ORDER BY column_name3]) AS new_column
FROM table_name;
```

- **Aggregate Window Function :** 
  Various aggregate functions such as `SUM(), COUNT(), AVERAGE(), MAX(), MIN()` applied over a particular window (set of rows)

## 9. Attention

- The `WHERE` clause is not only used in `SELECT` statements, it is also used in `UPDATE`, `DELETE`, etc.!

- `WHERE`与`HAVING`的根本区别在于：

  - `WHERE`子句在`GROUP BY`分组和聚合函数**之前**对数据行进行过滤；`WHERE`子句中不能使用聚合函数（比如`COUNT/AVG/SUM`等）
  - `HAVING`子句对`GROUP BY`分组和聚合函数**之后**的数据行进行过滤。

  ```sql
  select dept_id, count(*)
  from employee
  group by dept_id
  having count(*) > 5;  # or WHERE employee_count > 5;
  ```

- SQL里的index从1开始

- 在多表查询的时候，需要给表一个别名 --> 保证每个派生的表都有一个自己的别名

  - `MIN(event_date) OVER (PARTITION BY player_id) first_login`: 当`OVER`和`	PARTITION BY`合在一起时，这里指根据`player_id` 计算`MIN(event_date)`，结果命名为`first_login`

- `SELECT DISTINCT`和`GROUP BY`的用处是一样的

- `LEFT JOIN`: return all records from the left table (table1), and the matching records from the right table (table2). If there are no matching records from the right table, the value will be <u>NULL</u>. `RIGHT JOIN`类似，会包含右表所有的值以及左表对应的值，如果没有对应值则返回NULL

- <font color=blue>一定要记得使用`LEFT JOIN`和`RIGHT JOIN`的时候要使用`ON`或`USING`指明联结的行</font>

- Use `CROSS JOIN` and `LEFT JOIN/RIGHT JOIN` to concatenate three tables

  ```sql
  SELECT column_name(s)
  FROM table1 CROSS JOIN table2
  LEFT JOIN table 3 ON ...;
  ```

- 当有多表联结时，使用`ORDER BY`一定要注意是按照哪张表的order来排序！！

- 需要从另一个表提取值的时候，一定要加上`SELECT`！切记`SELECT`和`FROM`缺一不可！

- `COUNT`函数用于计算行数，`SUM`函数用于计算数值的总和。 它们的区别在于`COUNT`函数只计算非空值，而`SUM`函数只能用于数值类型的列。

- 当题目计算的结果需要筛选特定的行时，使用`IN`和`SELECT`联合使用

  ```sql
  # Example
  SELECT ROUND(
          (SUM(order_date = customer_pref_delivery_date) / COUNT(*)) * 100, 
          2) AS immediate_percentage
  FROM Delivery
  WHERE (customer_id, order_date) IN (
          SELECT customer_id, MIN(order_date)
          FROM Delivery
          GROUP BY customer_id
  )
  ```

- 如果题目有选择最早/最晚的日期时，考虑使用`MIN()/MAX()`提取

- 题目中包含running sum/runnning count/rolling average等，考虑使用window function!

- `COUNT(column)`: 根据指定的列统计记录总数，包含重复的记录，不包括NULL或空的值                           --> 计算该列一共有多少航

  `COUNT(DISTINCT column)`: 根据指定的列统计记录总数，不包含重复的记录，不包括NULL或空的值     --> 如果有行的值一样，则只计算一次

- 如果在没找到相关的row时，希望返回no rows而不是返回带有NULL的行，使用group by，这样表会只返回存在有值的时候

- Join连接列，Union连接行 --> 如果有想筛序出多种情况的，可以使用`UNION`，联合查询 UNION 是自动去重的

- `CASE`在`SELECT`语句里的用法：

  ```sql
  SELECT *, 
      # create a new column
      CASE 
          WHEN x + y > z AND x + z > y AND y + z > x THEN 'Yes'
          ELSE 'No'
      END AS triangle 
  FROM Triangle
  ```


- 如果想创建一张新表进行联结：

  ```sql
  WITH Category AS (
      SELECT "Low Salary" AS category
      UNION
      SELECT "Average Salary" AS category
      UNION
      SELECT "High Salary" AS category
  )
  ```

- 计算表格行数：`SELECT COUNT(*) FROM table`
- `FROM CONCAT(lat, lon) IN `: 如果想表达多个变量的筛选使用`IN`时，要使用`CONCAT`!
- 使用窗口函数时，一定要注意函数后`OVER`前要加()！！
- 注意`SELECT`的最后一个列后不要逗号！
- **Regular Expressions:** A valid e-mail has a prefix name and a domain where:

  - **The prefix name** is a string that may contain letters (upper or lower case), digits, underscore `'_'`, period `'.'`, and/or dash `'-'`. The prefix name **must** start with a letter.

  - **The domain** is `'@leetcode.com'`.

  Solution: `WHERE mail REGEXP '^[A-Za-z][A-Za-z0-9_.-]*@leetcode[.]com$'`

  记得在正则表达式里，`.,-,_,\`符号都要escape，可以放在`[]`里或者`\.`

- 当题目是highest/second highest等排序时，使用`DENSE_RANK()`或`RANK()`创造排序列，再进行筛选

- SQL创建函数:

  ```sql
  CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
  BEGIN
    RETURN (
        # Write your MySQL query statement below.
        SELECT DISTINCT salary
        FROM (
            SELECT *, DENSE_RANK() OVER(ORDER BY salary DESC) AS rnk
            FROM Employee
        ) t
        WHERE rnk = N  
    );
  END
  ```

- 如果题目要求列的名称为SQL自带函数的名称，可以添加`''`表示名字是string
