# SQL LeetCode

## 1. 511. Game Play Analysis I

Table: `Activity`

```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| player_id    | int     |
| device_id    | int     |
| event_date   | date    |
| games_played | int     |
+--------------+---------+
(player_id, event_date) is the primary key of this table.
This table shows the activity of players of some games.
Each row is a record of a player who logged in and played a number of games (possibly 0) before logging out on someday using some device.
```

Write an SQL query to report the **first login date** for each player.

Return the result table in **any order**.

The query result format is in the following example.

**Example 1:**

```
Input: 
Activity table:
+-----------+-----------+------------+--------------+
| player_id | device_id | event_date | games_played |
+-----------+-----------+------------+--------------+
| 1         | 2         | 2016-03-01 | 5            |
| 1         | 2         | 2016-05-02 | 6            |
| 2         | 3         | 2017-06-25 | 1            |
| 3         | 1         | 2016-03-02 | 0            |
| 3         | 4         | 2018-07-03 | 5            |
+-----------+-----------+------------+--------------+
Output: 
+-----------+-------------+
| player_id | first_login |
+-----------+-------------+
| 1         | 2016-03-01  |
| 2         | 2017-06-25  |
| 3         | 2016-03-02  |
+-----------+-------------+
```

- **Solution**

  ```sql
  # Solution 1
  SELECT player_id, MIN(event_date) AS first_login
  FROM Activity
  GROUP BY player_id
  
  # Solution 2
  SELECT DISTINCT player_id, 																  # DISTINCT: avoid multiple results of each player_id
  MIN(event_date) OVER (PARTITION BY player_id) first_login   # OVER (PARTITION BY ...) 用法
  FROM Activity
  ```
  
  Note: 
  
  - 如果题目有选择最早/最晚的日期时，考虑使用`MIN()/MAX()`提取

## 2. 512. Game Play Analysis II

Table: `Activity`

```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| player_id    | int     |
| device_id    | int     |
| event_date   | date    |
| games_played | int     |
+--------------+---------+
(player_id, event_date) is the primary key of this table.
This table shows the activity of players of some games.
Each row is a record of a player who logged in and played a number of games (possibly 0) before logging out on someday using some device.
```

Write an SQL query to report the **device** that is first logged in for each player.

Return the result table in **any order**.

The query result format is in the following example.

**Example 1:**

```
Input: 
Activity table:
+-----------+-----------+------------+--------------+
| player_id | device_id | event_date | games_played |
+-----------+-----------+------------+--------------+
| 1         | 2         | 2016-03-01 | 5            |
| 1         | 2         | 2016-05-02 | 6            |
| 2         | 3         | 2017-06-25 | 1            |
| 3         | 1         | 2016-03-02 | 0            |
| 3         | 4         | 2018-07-03 | 5            |
+-----------+-----------+------------+--------------+
Output: 
+-----------+-----------+
| player_id | device_id |
+-----------+-----------+
| 1         | 2         |
| 2         | 3         |
| 3         | 1         |
+-----------+-----------+
```

- **Solution**

  ```sql
  # use a subquery: the query retrieves the player_id and device_id values from the Activity table --> for rows where the player_id and event_date combination matches the minimum event_date for each unique player_id in the same table.
  SELECT player_id, device_id
  FROM Activity
  WHERE (player_id, event_date) IN       # filtering condition: check if the combination of player_id and event_date exists 
    (SELECT player_id, MIN(event_date)
     FROM Activity
     GROUP BY player_id)
  ```

  当要取出来的信息是基于别的信息的，使用Subquery得到满足别的信息的该信息

#### 2.1 1070. Product Sales Analysis III

Table: `Sales`

```
+-------------+-------+
| Column Name | Type  |
+-------------+-------+
| sale_id     | int   |
| product_id  | int   |
| year        | int   |
| quantity    | int   |
| price       | int   |
+-------------+-------+
(sale_id, year) is the primary key of this table.
product_id is a foreign key to Product table.
Each row of this table shows a sale on the product product_id in a certain year.
Note that the price is per unit.
```

Table: `Product`

```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| product_id   | int     |
| product_name | varchar |
+--------------+---------+
product_id is the primary key of this table.
Each row of this table indicates the product name of each product.
```

Write an SQL query that selects the **product id**, **year**, **quantity**, and **price** for the **first year** of every product sold.

Return the resulting table in **any order**.

The query result format is in the following example. 

**Example 1:**

```
Input: 
Sales table:
+---------+------------+------+----------+-------+
| sale_id | product_id | year | quantity | price |
+---------+------------+------+----------+-------+ 
| 1       | 100        | 2008 | 10       | 5000  |
| 2       | 100        | 2009 | 12       | 5000  |
| 7       | 200        | 2011 | 15       | 9000  |
+---------+------------+------+----------+-------+
Product table:
+------------+--------------+
| product_id | product_name |
+------------+--------------+
| 100        | Nokia        |
| 200        | Apple        |
| 300        | Samsung      |
+------------+--------------+
Output: 
+------------+------------+----------+-------+
| product_id | first_year | quantity | price |
+------------+------------+----------+-------+ 
| 100        | 2008       | 10       | 5000  |
| 200        | 2011       | 15       | 9000  |
+------------+------------+----------+-------+
```

- **Solution**

  ```sql
  SELECT product_id, year AS first_year, quantity, price
  FROM Sales
  WHERE (product_id, year) IN      				# not (year, quantity, price)
      (
          SELECT product_id, MIN(year)
          FROM Sales
          GROUP BY product_id
      )
  ```

  <font color=red>**[Attention] 在做filter操作的时候，注意主要使用primary key和要filter的变量**</font>

## 3. 1581. Customer Who Visited but Did Not Make Any Transactions

Table: `Visits`

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| visit_id    | int     |
| customer_id | int     |
+-------------+---------+
visit_id is the primary key for this table.
This table contains information about the customers who visited the mall.
```

 Table: `Transactions`

```
+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| transaction_id | int     |
| visit_id       | int     |
| amount         | int     |
+----------------+---------+
transaction_id is the primary key for this table.
This table contains information about the transactions made during the visit_id.
```

Write a SQL query to find the IDs of the users who visited without making any transactions and the number of times they made these types of visits.

Return the result table sorted in **any order**.

The query result format is in the following example.

**Example 1:**

```
Input: 
Visits
+----------+-------------+
| visit_id | customer_id |
+----------+-------------+
| 1        | 23          |
| 2        | 9           |
| 4        | 30          |
| 5        | 54          |
| 6        | 96          |
| 7        | 54          |
| 8        | 54          |
+----------+-------------+
Transactions
+----------------+----------+--------+
| transaction_id | visit_id | amount |
+----------------+----------+--------+
| 2              | 5        | 310    |
| 3              | 5        | 300    |
| 9              | 5        | 200    |
| 12             | 1        | 910    |
| 13             | 2        | 970    |
+----------------+----------+--------+
Output: 
+-------------+----------------+
| customer_id | count_no_trans |
+-------------+----------------+
| 54          | 2              |
| 30          | 1              |
| 96          | 1              |
+-------------+----------------+
Explanation: 
Customer with id = 23 visited the mall once and made one transaction during the visit with id = 12.
Customer with id = 9 visited the mall once and made one transaction during the visit with id = 13.
Customer with id = 30 visited the mall once and did not make any transactions.
Customer with id = 54 visited the mall three times. During 2 visits they did not make any transactions, and during one visit they made 3 transactions.
Customer with id = 96 visited the mall once and did not make any transactions.
As we can see, users with IDs 30 and 96 visited the mall one time without making any transactions. Also, user 54 visited the mall twice and did not make any transactions.
```

- **Thoughts**

  <img src="/Users/xinyuzhang/Library/Application%20Support/typora-user-images/image-20230714094200805.png" alt="image-20230714094200805" style="zoom: 50%;" />

  注意：`count`是聚合函数，这里需要使用`group by`进行聚合分组，否则`count`会计算表里剩余的行总数

- **Solution**

  ```sql
  SELECT V.customer_id, COUNT(V.customer_id) AS count_no_trans
  FROM Visits V LEFT JOIN Transactions T ON V.visit_id = T.visit_id
  WHERE T.visit_id IS NULL
  GROUP BY V.customer_id
  ```

## 4. 1280. Students and Examinations

Table: `Students`

```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| student_id    | int     |
| student_name  | varchar |
+---------------+---------+
In SQL, student_id is the primary key for this table.
Each row of this table contains the ID and the name of one student in the school.
```

 

Table: `Subjects`

```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| subject_name | varchar |
+--------------+---------+
In SQL, subject_name is the primary key for this table.
Each row of this table contains the name of one subject in the school.
```

 

Table: `Examinations`

```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| student_id   | int     |
| subject_name | varchar |
+--------------+---------+
This table may contain duplicates (In other words, there is no primary key for this table in SQL).
Each student from the Students table takes every course from the Subjects table.
Each row of this table indicates that a student with ID student_id attended the exam of subject_name.
```

 

Find the number of times each student attended each exam.

Return the result table ordered by `student_id` and `subject_name`.

The result format is in the following example.

 

**Example 1:**

```
Input: 
Students table:
+------------+--------------+
| student_id | student_name |
+------------+--------------+
| 1          | Alice        |
| 2          | Bob          |
| 13         | John         |
| 6          | Alex         |
+------------+--------------+
Subjects table:
+--------------+
| subject_name |
+--------------+
| Math         |
| Physics      |
| Programming  |
+--------------+
Examinations table:
+------------+--------------+
| student_id | subject_name |
+------------+--------------+
| 1          | Math         |
| 1          | Physics      |
| 1          | Programming  |
| 2          | Programming  |
| 1          | Physics      |
| 1          | Math         |
| 13         | Math         |
| 13         | Programming  |
| 13         | Physics      |
| 2          | Math         |
| 1          | Math         |
+------------+--------------+
Output: 
+------------+--------------+--------------+----------------+
| student_id | student_name | subject_name | attended_exams |
+------------+--------------+--------------+----------------+
| 1          | Alice        | Math         | 3              |
| 1          | Alice        | Physics      | 2              |
| 1          | Alice        | Programming  | 1              |
| 2          | Bob          | Math         | 1              |
| 2          | Bob          | Physics      | 0              |
| 2          | Bob          | Programming  | 1              |
| 6          | Alex         | Math         | 0              |
| 6          | Alex         | Physics      | 0              |
| 6          | Alex         | Programming  | 0              |
| 13         | John         | Math         | 1              |
| 13         | John         | Physics      | 1              |
| 13         | John         | Programming  | 1              |
+------------+--------------+--------------+----------------+
Explanation: 
The result table should contain all students and all subjects.
Alice attended the Math exam 3 times, the Physics exam 2 times, and the Programming exam 1 time.
Bob attended the Math exam 1 time, the Programming exam 1 time, and did not attend the Physics exam.
Alex did not attend any exams.
John attended the Math exam 1 time, the Physics exam 1 time, and the Programming exam 1 time.
```

- **Solution**

  这道题的关键在于多表联结和` ORDER BY`的顺序决定

  ```sql
  SELECT st.student_id, st.student_name, su.subject_name, COUNT(e.subject_name) AS attended_exams
  FROM Students st CROSS JOIN Subjects su
  LEFT JOIN Examinations e ON st.student_id = e.student_id AND su.subject_name = e.subject_name
  GROUP BY st.student_name, su.subject_name  
  ORDER BY st.student_id, su.subject_name  # need to see which order required --> determine order by which table's subject_name
  ```

## 5. 570. Managers with at Least 5 Direct Reports

Table: `Employee`

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| name        | varchar |
| department  | varchar |
| managerId   | int     |
+-------------+---------+
In SQL, id is the primary key column for this table.
Each row of this table indicates the name of an employee, their department, and the id of their manager.
If managerId is null, then the employee does not have a manager.
No employee will be the manager of themself.
```

Find the managers with at least **five direct reports**.

Return the result table in **any order**.

The result format is in the following example.

**Example 1:**

```
Input: 
Employee table:
+-----+-------+------------+-----------+
| id  | name  | department | managerId |
+-----+-------+------------+-----------+
| 101 | John  | A          | None      |
| 102 | Dan   | A          | 101       |
| 103 | James | A          | 101       |
| 104 | Amy   | A          | 101       |
| 105 | Anne  | A          | 101       |
| 106 | Ron   | B          | 101       |
+-----+-------+------------+-----------+
Output: 
+------+
| name |
+------+
| John |
+------+
```

- **Thoughts**

  - Step 1: 按照 managerId 分组，使用 having 筛选出大于等于 5 名下属的经理 id

    `select managerId from employee group by managerId having count(managerId) >= 5`

  - Step 2: 使用 in 查出经理名字

- **Solution**

  ```sql
  SELECT name
  FROM Employee
  WHERE id IN
    (
      SELECT managerId
      FROM Employee
      GROUP BY managerId
      HAVING COUNT(managerId) >= 5
    )
  ```

## 6. 1934. Confirmation Rate

Table: `Signups`

```
+----------------+----------+
| Column Name    | Type     |
+----------------+----------+
| user_id        | int      |
| time_stamp     | datetime |
+----------------+----------+
user_id is the primary key for this table.
Each row contains information about the signup time for the user with ID user_id.
```

Table: `Confirmations`

```
+----------------+----------+
| Column Name    | Type     |
+----------------+----------+
| user_id        | int      |
| time_stamp     | datetime |
| action         | ENUM     |
+----------------+----------+
(user_id, time_stamp) is the primary key for this table.
user_id is a foreign key with a reference to the Signups table.
action is an ENUM of the type ('confirmed', 'timeout')
Each row of this table indicates that the user with ID user_id requested a confirmation message at time_stamp and that confirmation message was either confirmed ('confirmed') or expired without confirming ('timeout').
```

The **confirmation rate** of a user is the number of `'confirmed'` messages divided by the total number of requested confirmation messages. The confirmation rate of a user that did not request any confirmation messages is `0`. Round the confirmation rate to **two decimal** places.

Write an SQL query to find the **confirmation rate** of each user.

Return the result table in **any order**.

The query result format is in the following example. 

**Example 1:**

```
Input: 
Signups table:
+---------+---------------------+
| user_id | time_stamp          |
+---------+---------------------+
| 3       | 2020-03-21 10:16:13 |
| 7       | 2020-01-04 13:57:59 |
| 2       | 2020-07-29 23:09:44 |
| 6       | 2020-12-09 10:39:37 |
+---------+---------------------+
Confirmations table:
+---------+---------------------+-----------+
| user_id | time_stamp          | action    |
+---------+---------------------+-----------+
| 3       | 2021-01-06 03:30:46 | timeout   |
| 3       | 2021-07-14 14:00:00 | timeout   |
| 7       | 2021-06-12 11:57:29 | confirmed |
| 7       | 2021-06-13 12:58:28 | confirmed |
| 7       | 2021-06-14 13:59:27 | confirmed |
| 2       | 2021-01-22 00:00:00 | confirmed |
| 2       | 2021-02-28 23:59:59 | timeout   |
+---------+---------------------+-----------+
Output: 
+---------+-------------------+
| user_id | confirmation_rate |
+---------+-------------------+
| 6       | 0.00              |
| 3       | 0.00              |
| 7       | 1.00              |
| 2       | 0.50              |
+---------+-------------------+
Explanation: 
User 6 did not request any confirmation messages. The confirmation rate is 0.
User 3 made 2 requests and both timed out. The confirmation rate is 0.
User 7 made 3 requests and all were confirmed. The confirmation rate is 1.
User 2 made 2 requests where one was confirmed and the other timed out. The confirmation rate is 1 / 2 = 0.5.
```

- **Thoughts**

  这道题最关键的在于如何求confimation rate。可以很容易想到confirmation rate即为当`action = 'confirmed'`时的平均值。但根据题干，如果不存在任何request时，在联结表的时候会产生NULL值，我们以0处理，所以要用到`ISNULL`函数

- **Solution**

  ```sql
  SELECT s.user_id, 
      ROUND(IFNULL(AVG(c.action = 'confirmed'), 0), 2) AS confirmation_rate
  FROM Signups s LEFT JOIN Confirmations c ON s.user_id = c.user_id
  GROUP BY s.user_id
  ```

## 7. 1633. Percentage of Users Attended a Contest

Table: `Users`

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| user_id     | int     |
| user_name   | varchar |
+-------------+---------+
user_id is the primary key for this table.
Each row of this table contains the name and the id of a user.
```

Table: `Register`

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| contest_id  | int     |
| user_id     | int     |
+-------------+---------+
(contest_id, user_id) is the primary key for this table.
Each row of this table contains the id of a user and the contest they registered into.
```

Write an SQL query to find the percentage of the users registered in each contest rounded to **two decimals**.

Return the result table ordered by `percentage` in **descending order**. In case of a tie, order it by `contest_id` in **ascending order**.

The query result format is in the following example.

**Example 1:**

```
Input: 
Users table:
+---------+-----------+
| user_id | user_name |
+---------+-----------+
| 6       | Alice     |
| 2       | Bob       |
| 7       | Alex      |
+---------+-----------+
Register table:
+------------+---------+
| contest_id | user_id |
+------------+---------+
| 215        | 6       |
| 209        | 2       |
| 208        | 2       |
| 210        | 6       |
| 208        | 6       |
| 209        | 7       |
| 209        | 6       |
| 215        | 7       |
| 208        | 7       |
| 210        | 2       |
| 207        | 2       |
| 210        | 7       |
+------------+---------+
Output: 
+------------+------------+
| contest_id | percentage |
+------------+------------+
| 208        | 100.0      |
| 209        | 100.0      |
| 210        | 100.0      |
| 215        | 66.67      |
| 207        | 33.33      |
+------------+------------+
Explanation: 
All the users registered in contests 208, 209, and 210. The percentage is 100% and we sort them in the answer table by contest_id in ascending order.
Alice and Alex registered in contest 215 and the percentage is ((2/3) * 100) = 66.67%
Bob registered in contest 207 and the percentage is ((1/3) * 100) = 33.33%
```

- **Thoughts**

  首先我们观察这两个表以及问题，可以发现：

  1. 不同赛事是需要进行分别统计的
  2. 两个表虽然有共同的字段，但是并不需要连接。因为统计一门赛事注册的user_id在Register表中，统计user_id总数量在Users表中，两者都是可以独立查询出来的

  那么梳理一下做题步骤：

  1. 不同赛事进行分组
  2. 分别统计一门赛事中注册了的user_id数量、user_id总数量
  3. 保留两位小数
  4. 结果按percentage降序排序，相同按contest_id的升序排序

- **Solution**

  ```sql
  SELECT contest_id, 
      ROUND((COUNT(user_id) / (SELECT COUNT(user_id) FROM Users)) * 100, 2) AS percentage
  FROM Register
  GROUP BY contest_id
  ORDER BY percentage DESC, contest_id ASC
  ```

  <font color=red>[关键在于：需要从另一个表提取值的时候，一定要加上`SELECT`!切记`SELECT`和`FROM`缺一不可]</font>

## 8. 1193. Monthly Transactions I

Table: `Transactions`

```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| country       | varchar |
| state         | enum    |
| amount        | int     |
| trans_date    | date    |
+---------------+---------+
id is the primary key of this table.
The table has information about incoming transactions.
The state column is an enum of type ["approved", "declined"].
```

Write an SQL query to find for each month and country, the number of transactions and their total amount, the number of approved transactions and their total amount.

Return the result table in **any order**.

The query result format is in the following example.

**Example 1:**

```
Input: 
Transactions table:
+------+---------+----------+--------+------------+
| id   | country | state    | amount | trans_date |
+------+---------+----------+--------+------------+
| 121  | US      | approved | 1000   | 2018-12-18 |
| 122  | US      | declined | 2000   | 2018-12-19 |
| 123  | US      | approved | 2000   | 2019-01-01 |
| 124  | DE      | approved | 2000   | 2019-01-07 |
+------+---------+----------+--------+------------+
Output: 
+----------+---------+-------------+----------------+--------------------+-----------------------+
| month    | country | trans_count | approved_count | trans_total_amount | approved_total_amount |
+----------+---------+-------------+----------------+--------------------+-----------------------+
| 2018-12  | US      | 2           | 1              | 3000               | 1000                  |
| 2019-01  | US      | 1           | 1              | 2000               | 2000                  |
| 2019-01  | DE      | 1           | 1              | 2000               | 2000                  |
+----------+---------+-------------+----------------+--------------------+-----------------------+
```

- **Solution**

  ```sql
  SELECT DATE_FORMAT(trans_date, '%Y-%m') AS month, 
         country,
         COUNT(*) AS trans_count,
         SUM(state = 'approved') AS approved_count,
         SUM(amount) AS trans_total_amount,
         SUM(IF(state = 'approved', amount, 0)) AS approved_total_amount
  FROM Transactions
  GROUP BY month, country
  ```

  <font color=blue>**[Key Function]**</font>

  - `DATE_FORMAT(date, format)`: format a date into specified month format
  - `IF(condition, value_if_true, value_if_false)`: return the second parameter if a condition is TRUE, return the third parameter if a condition is FALSE

## 9. 1174. Immediate Food Delivery II

Table: `Delivery`

```
+-----------------------------+---------+
| Column Name                 | Type    |
+-----------------------------+---------+
| delivery_id                 | int     |
| customer_id                 | int     |
| order_date                  | date    |
| customer_pref_delivery_date | date    |
+-----------------------------+---------+
delivery_id is the primary key of this table.
The table holds information about food delivery to customers that make orders at some date and specify a preferred delivery date (on the same order date or after it).
```

If the customer's preferred delivery date is the same as the order date, then the order is called **immediate;** otherwise, it is called **scheduled**.

The **first order** of a customer is the order with the earliest order date that the customer made. It is guaranteed that a customer has precisely one first order.

Write an SQL query to find the percentage of immediate orders in the first orders of all customers, **rounded to 2 decimal places**.

The query result format is in the following example.

**Example 1:**

```
Input: 
Delivery table:
+-------------+-------------+------------+-----------------------------+
| delivery_id | customer_id | order_date | customer_pref_delivery_date |
+-------------+-------------+------------+-----------------------------+
| 1           | 1           | 2019-08-01 | 2019-08-02                  |
| 2           | 2           | 2019-08-02 | 2019-08-02                  |
| 3           | 1           | 2019-08-11 | 2019-08-12                  |
| 4           | 3           | 2019-08-24 | 2019-08-24                  |
| 5           | 3           | 2019-08-21 | 2019-08-22                  |
| 6           | 2           | 2019-08-11 | 2019-08-13                  |
| 7           | 4           | 2019-08-09 | 2019-08-09                  |
+-------------+-------------+------------+-----------------------------+
Output: 
+----------------------+
| immediate_percentage |
+----------------------+
| 50.00                |
+----------------------+
Explanation: 
The customer id 1 has a first order with delivery id 1 and it is scheduled.
The customer id 2 has a first order with delivery id 2 and it is immediate.
The customer id 3 has a first order with delivery id 5 and it is scheduled.
The customer id 4 has a first order with delivery id 7 and it is immediate.
Hence, half the customers have immediate first orders.
```

- **Solution**

  ```sql
  # 先计算所有的percentage，然后再筛选出来属于first orders的
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

## 10. 534. Game Play Analysis III

Table: `Activity`

```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| player_id    | int     |
| device_id    | int     |
| event_date   | date    |
| games_played | int     |
+--------------+---------+
(player_id, event_date) is the primary key of this table.
This table shows the activity of players of some games.
Each row is a record of a player who logged in and played a number of games (possibly 0) before logging out on someday using some device.
```

Write an SQL query to report for each player and date, how many games played **so far** by the player. That is, the total number of games played by the player until that date. Check the example for clarity.

Return the result table in **any order**.

The query result format is in the following example. 

**Example 1:**

```
Input: 
Activity table:
+-----------+-----------+------------+--------------+
| player_id | device_id | event_date | games_played |
+-----------+-----------+------------+--------------+
| 1         | 2         | 2016-03-01 | 5            |
| 1         | 2         | 2016-05-02 | 6            |
| 1         | 3         | 2017-06-25 | 1            |
| 3         | 1         | 2016-03-02 | 0            |
| 3         | 4         | 2018-07-03 | 5            |
+-----------+-----------+------------+--------------+
Output: 
+-----------+------------+---------------------+
| player_id | event_date | games_played_so_far |
+-----------+------------+---------------------+
| 1         | 2016-03-01 | 5                   |
| 1         | 2016-05-02 | 11                  |
| 1         | 2017-06-25 | 12                  |
| 3         | 2016-03-02 | 0                   |
| 3         | 2018-07-03 | 5                   |
+-----------+------------+---------------------+
Explanation: 
For the player with id 1, 5 + 6 = 11 games played by 2016-05-02, and 5 + 6 + 1 = 12 games played by 2017-06-25.
For the player with id 3, 0 + 5 = 5 games played by 2018-07-03.
Note that for each player we only care about the days when the player logged in.
```

- **Solution**

  - <u>Method 1: use correlated sub-query</u> --> time limit exceeded

    ```sql
    SELECT a.player_id, a.event_date, 
      (
        SELECT SUM(b.games_played)
        FROM Activity b
        WHERE a.player_id = b.player_id AND b.event_date <= a.event_date
      ) AS games_played_so_far
    FROM Activity a
    ```

  - <u>Method 2: use window function</u>

    当题目中出现滚动运算，比如running sum/running count/rolling average等，考虑使用window function。

    window function的语法为：`<function> OVER(PARTITION BY <> ORDER BY <> ROWS BETWEEN <>)`

    ```sql
    SELECT player_id, event_date,
      SUM(games_played) OVER(PARTITION BY player_id ORDER BY event_date) AS games_played_so_far # 根据player_id分为每个窗口，计算每个窗口里直到每个event_date的求和
    FROM Activity
    ```

## 11. 550. Game Play Analysis IV

## 12. 619. Biggest Single Number

Table: `MyNumbers`

```
+-------------+------+
| Column Name | Type |
+-------------+------+
| num         | int  |
+-------------+------+
There is no primary key for this table. It may contain duplicates.
Each row of this table contains an integer.
```

 A **single number** is a number that appeared only once in the `MyNumbers` table.

Write an SQL query to report the largest **single number**. If there is no **single number**, report `null`.

The query result format is in the following example. 

**Example 1:**

```
Input: 
MyNumbers table:
+-----+
| num |
+-----+
| 8   |
| 8   |
| 3   |
| 3   |
| 1   |
| 4   |
| 5   |
| 6   |
+-----+
Output: 
+-----+
| num |
+-----+
| 6   |
+-----+
Explanation: The single numbers are 1, 4, 5, and 6.
Since 6 is the largest single number, we return it.
```

**Example 2:**

```
Input: 
MyNumbers table:
+-----+
| num |
+-----+
| 8   |
| 8   |
| 7   |
| 7   |
| 3   |
| 3   |
| 3   |
+-----+
Output: 
+------+
| num  |
+------+
| null |
+------+
Explanation: There are no single numbers in the input table so we return null.
```

- **Thoughts**

  解题步骤：

  <img src="/Users/xinyuzhang/Library/Application%20Support/typora-user-images/image-20230725163931895.png" alt="image-20230725163931895" style="zoom:50%;" />

  特殊情况在于，如果不存在该值，则返回`NULL ` --> 使用`IFNULL`函数

- **Solution**

  ```sql
  SELECT IFNULL
  ((
    SELECT num
    FROM MyNumbers
    GROUP BY num
    HAVING COUNT(num) = 1
    ORDER BY num DESC
    LIMIT 1),
    NULL
  ) AS num
  ```

## 13. 1045. Customers Who Bought All Products

Table: `Customer`

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| customer_id | int     |
| product_key | int     |
+-------------+---------+
There is no primary key for this table. It may contain duplicates. customer_id is not NULL.
product_key is a foreign key to Product table.
```

Table: `Product`

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| product_key | int     |
+-------------+---------+
product_key is the primary key column for this table. 
```

Write an SQL query to report the customer ids from the `Customer` table that bought all the products in the `Product` table.

Return the result table in **any order**.

The query result format is in the following example.

**Example 1:**

```
Input: 
Customer table:
+-------------+-------------+
| customer_id | product_key |
+-------------+-------------+
| 1           | 5           |
| 2           | 6           |
| 3           | 5           |
| 3           | 6           |
| 1           | 6           |
+-------------+-------------+
Product table:
+-------------+
| product_key |
+-------------+
| 5           |
| 6           |
+-------------+
Output: 
+-------------+
| customer_id |
+-------------+
| 1           |
| 3           |
+-------------+
Explanation: 
The customers who bought all the products (5 and 6) are customers with IDs 1 and 3.
```

- **Thoughts**

  这道题目提供了两个表，分别是顾客表和所有产品的表，于是找到购买了所有产品的客户 --> 该客户拥有的distinct产品数量与所有产品表的数量相等

  <font color=blue>**[Notes]** </font>由于题目说明product_key 是 Product 表的主键，也是Customer的外键。**外键为某个表中的一列，它包含另一个表的主键值，定义了两个表之间的关系。**

  1. 这里是Customer表中的一列是 product_key,  product_key是Product表的主键
  2. 证明Customer表中的product_key 必定来自Product表, 
  3. 不存在Customer中的product_key 不是 Product 表中的情况

- **Solution**

  ```sql
  SELECT customer_id
  FROM Customer
  GROUP BY customer_id
  HAVING COUNT(DISTINCT product_key) = (SELECT COUNT(*) FROM Product)
  ```

## 14. 1731. The Number of Employees Which Report to Each Employee

Table: `Employees`

```
+-------------+----------+
| Column Name | Type     |
+-------------+----------+
| employee_id | int      |
| name        | varchar  |
| reports_to  | int      |
| age         | int      |
+-------------+----------+
employee_id is the primary key for this table.
This table contains information about the employees and the id of the manager they report to. Some employees do not report to anyone (reports_to is null). 
```

For this problem, we will consider a **manager** an employee who has at least 1 other employee reporting to them.

Write an SQL query to report the ids and the names of all **managers**, the number of employees who report **directly** to them, and the average age of the reports rounded to the nearest integer.

Return the result table ordered by `employee_id`.

The query result format is in the following example.

**Example 1:**

```
Input: 
Employees table:
+-------------+---------+------------+-----+
| employee_id | name    | reports_to | age |
+-------------+---------+------------+-----+
| 9           | Hercy   | null       | 43  |
| 6           | Alice   | 9          | 41  |
| 4           | Bob     | 9          | 36  |
| 2           | Winston | null       | 37  |
+-------------+---------+------------+-----+
Output: 
+-------------+-------+---------------+-------------+
| employee_id | name  | reports_count | average_age |
+-------------+-------+---------------+-------------+
| 9           | Hercy | 2             | 39          |
+-------------+-------+---------------+-------------+
Explanation: Hercy has 2 people report directly to him, Alice and Bob. Their average age is (41+36)/2 = 38.5, which is 39 after rounding it to the nearest integer.
```

- **Thoughts**

  这道题的关键在于如何连接两个表，筛选出为manager的人

  <img src="/Users/xinyuzhang//Library/Application%20Support/typora-user-images/image-20230804091554021.png" alt="image-20230804091554021" style="zoom:50%;" />

- **Solution**

  ```sql
  # To only show managers who have at least one report, you should use an INNER JOIN to only include rows where a match is found in both tables
  SELECT m.employee_id, m.name, 
      COUNT(*) AS reports_count,
      ROUND(AVG(e.age)) AS average_age
  FROM Employees e, Employees m     # m is to show managers table
  WHERE e.reports_to = m.employee_id
  GROUP BY m.employee_id  					# !!Key line
  ORDER BY employee_id
  ```

  <font color=red>**[Attention!]** 如果在没找到相关的row时，希望返回no rows而不是返回带有NULL的行，使用group by，这样表会只返回存在m.employee_id的时候</font>

## 15. 1789. Primary Department for Each Employee

Table: `Employee`

```
+---------------+---------+
| Column Name   |  Type   |
+---------------+---------+
| employee_id   | int     |
| department_id | int     |
| primary_flag  | varchar |
+---------------+---------+
(employee_id, department_id) is the primary key for this table.
employee_id is the id of the employee.
department_id is the id of the department to which the employee belongs.
primary_flag is an ENUM of type ('Y', 'N'). If the flag is 'Y', the department is the primary department for the employee. If the flag is 'N', the department is not the primary.
```

Employees can belong to multiple departments. When the employee joins other departments, they need to decide which department is their primary department. Note that when an employee belongs to only one department, their primary column is `'N'`.

Write an SQL query to report all the employees with their primary department. For employees who belong to one department, report their only department.

Return the result table in **any order**.

The query result format is in the following example. 

**Example 1:**

```
Input: 
Employee table:
+-------------+---------------+--------------+
| employee_id | department_id | primary_flag |
+-------------+---------------+--------------+
| 1           | 1             | N            |
| 2           | 1             | Y            |
| 2           | 2             | N            |
| 3           | 3             | N            |
| 4           | 2             | N            |
| 4           | 3             | Y            |
| 4           | 4             | N            |
+-------------+---------------+--------------+
Output: 
+-------------+---------------+
| employee_id | department_id |
+-------------+---------------+
| 1           | 1             |
| 2           | 1             |
| 3           | 3             |
| 4           | 3             |
+-------------+---------------+
Explanation: 
- The Primary department for employee 1 is 1.
- The Primary department for employee 2 is 1.
- The Primary department for employee 3 is 3.
- The Primary department for employee 4 is 3.
```

- **Thoughts**

  Two situations:

  1. only belong to one department, return the department

  2. belong to multiple departments, return department where primary_flag = 'Y'

  --> 需要两张表，在使用`UNION`联合查询并自动去重

- **Solution**

  ```sql
  SELECT employee_id, department_id
  FROM Employee
  GROUP BY employee_id
  HAVING COUNT(employee_id) = 1
  UNION
  SELECT employee_id, department_id
  FROM Employee
  WHERE primary_flag = 'Y'
  ```


## 16. 180. Consecutive Numbers

Table: `Logs`

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| num         | varchar |
+-------------+---------+
In SQL, id is the primary key for this table.
id is an autoincrement column.
```

Find all numbers that appear at least three times consecutively.

Return the result table in **any order**.

The result format is in the following example.

**Example 1:**

```
Input: 
Logs table:
+----+-----+
| id | num |
+----+-----+
| 1  | 1   |
| 2  | 1   |
| 3  | 1   |
| 4  | 2   |
| 5  | 1   |
| 6  | 2   |
| 7  | 2   |
+----+-----+
Output: 
+-----------------+
| ConsecutiveNums |
+-----------------+
| 1               |
+-----------------+
Explanation: 1 is the only number that appears consecutively for at least three times.
```

- **Solution**

  - <u>Method 1: 暴力解法</u>

    将表self join三次，找到连着出现了三次的

## 17. 1164. Product Price at a Given Date

Table: `Products`

```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| product_id    | int     |
| new_price     | int     |
| change_date   | date    |
+---------------+---------+
(product_id, change_date) is the primary key (combination of columns with unique values) of this table.
Each row of this table indicates that the price of some product was changed to a new price at some date.
```

Write a solution to find the prices of all products on `2019-08-16`. Assume the price of all products before any change is `10`.

Return the result table in **any order**.

The result format is in the following example.

**Example 1:**

```
Input: 
Products table:
+------------+-----------+-------------+
| product_id | new_price | change_date |
+------------+-----------+-------------+
| 1          | 20        | 2019-08-14  |
| 2          | 50        | 2019-08-14  |
| 1          | 30        | 2019-08-15  |
| 1          | 35        | 2019-08-16  |
| 2          | 65        | 2019-08-17  |
| 3          | 20        | 2019-08-18  |
+------------+-----------+-------------+
Output: 
+------------+-------+
| product_id | price |
+------------+-------+
| 2          | 50    |
| 1          | 35    |
| 3          | 10    |
+------------+-------+
```

- **Methods**

  要获得过滤的结果，步骤如下：

  1. 将每个产品的change_date进行排序，并选择<= '2019-08-16'的日期 --> 使用`DENSE_RANK()`窗口函数，窗口为`product_id`，根据`change_date`降序排雷
  2. 在排序过滤后的表格选择排序为1的记录，即为每个产品在2019-08-16日期前最新更新的价格
  3. 因为可能存在排序过滤后的表格不存在某个产品的情况，就要将原表和排序过滤后的表进行`LEFT JOIN`，并使用`IFNULL()`将价格改成10

- **Solution**

  ```sql
  SELECT DISTINCT p.product_id, IFNULL(n.new_price, 10) as price
  FROM Products p
  LEFT JOIN
  (
      SELECT product_id, new_price
      FROM (
          SELECT *,  DENSE_RANK() OVER(PARTITION BY product_id ORDER BY change_date DESC) AS rnk
          FROM Products
          WHERE change_date <= '2019-08-16 '
      ) a
      WHERE rnk = 1
  ) n
  on p.product_id = n.product_id

## 18. 1907. Count Salary Categories

Table: `Accounts`

```
+-------------+------+
| Column Name | Type |
+-------------+------+
| account_id  | int  |
| income      | int  |
+-------------+------+
account_id is the primary key (column with unique values) for this table.
Each row contains information about the monthly income for one bank account.
```

Write a solution to calculate the number of bank accounts for each salary category. The salary categories are:

- `"Low Salary"`: All the salaries **strictly less** than `$20000`.
- `"Average Salary"`: All the salaries in the **inclusive** range `[$20000, $50000]`.
- `"High Salary"`: All the salaries **strictly greater** than `$50000`.

The result table **must** contain all three categories. If there are no accounts in a category, return `0`.

Return the result table in **any order**.

The result format is in the following example.

**Example 1:**

```
Input: 
Accounts table:
+------------+--------+
| account_id | income |
+------------+--------+
| 3          | 108939 |
| 2          | 12747  |
| 8          | 87709  |
| 6          | 91796  |
+------------+--------+
Output: 
+----------------+----------------+
| category       | accounts_count |
+----------------+----------------+
| Low Salary     | 1              |
| Average Salary | 0              |
| High Salary    | 3              |
+----------------+----------------+
Explanation: 
Low Salary: Account 2.
Average Salary: No accounts.
High Salary: Accounts 3, 6, and 8.
```

- **Thoughts**

  - Step 1: define each category of the current income from Accounts
  - Step 2: select and filter the corresponding results
  - Step 3: left join with the original category table

  <font color=red>**[这道题的关键点在于，要建议初始category表，再进行left join!]**</font>

- **Solution**

  ```sql
  # build the category table
  WITH Category AS (
      SELECT "Low Salary" AS category
      UNION
      SELECT "Average Salary" AS category
      UNION
      SELECT "High Salary" AS category
  )
  
  SELECT c.category, IFNULL(COUNT(a.category), 0) AS accounts_count
  FROM Category c
  LEFT JOIN
  (
      SELECT *, 
          CASE 
              WHEN income < 20000 THEN "Low Salary"
              WHEN income >= 20000 AND income <= 50000 THEN "Average Salary"
              WHEN income > 50000 THEN "High Salary"
          END AS category
      FROM Accounts
  ) AS a
  ON c.category = a.category
  GROUP BY c.category 
  ```

## 19. 626. Exchange Seats

Table: `Seat`

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| student     | varchar |
+-------------+---------+
id is the primary key (unique value) column for this table.
Each row of this table indicates the name and the ID of a student.
id is a continuous increment.
```

Write a solution to swap the seat id of every two consecutive students. If the number of students is odd, the id of the last student is not swapped.

Return the result table ordered by `id` **in ascending order**.

The result format is in the following example. 

**Example 1:**

```
Input: 
Seat table:
+----+---------+
| id | student |
+----+---------+
| 1  | Abbot   |
| 2  | Doris   |
| 3  | Emerson |
| 4  | Green   |
| 5  | Jeames  |
+----+---------+
Output: 
+----+---------+
| id | student |
+----+---------+
| 1  | Doris   |
| 2  | Abbot   |
| 3  | Green   |
| 4  | Emerson |
| 5  | Jeames  |
+----+---------+
Explanation: 
Note that if the number of students is odd, there is no need to change the last one's seat.
```

- **Thoughts**

  题目要求swap，其实就是分别修改奇偶id，这种情况下即可以使用`CASE`来进行判断。此题有一个edge case，即当id为奇数且为最后一个时，id不变，这里可以使用`SELECT COUNT(*) FROM table`计算表格长度。

- **Solution**

  ```sql
  SELECT (
    CASE 
      WHEN id MOD 2 != 0 AND id != (SELECT COUNT(*) FROM Seat) THEN id + 1
      WHEN id MOD 2 != 0 AND id = (SELECT COUNT(*) FROM Seat) THEN id
      ELSE id - 1
    END
  ) AS id, student
  FROM Seat
  ORDER BY id
  ```

## 20. 1341. Movie Rating

Table: `Movies`

```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| movie_id      | int     |
| title         | varchar |
+---------------+---------+
movie_id is the primary key (column with unique values) for this table.
title is the name of the movie.
```

Table: `Users`

```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| user_id       | int     |
| name          | varchar |
+---------------+---------+
user_id is the primary key (column with unique values) for this table.
```

Table: `MovieRating`

```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| movie_id      | int     |
| user_id       | int     |
| rating        | int     |
| created_at    | date    |
+---------------+---------+
(movie_id, user_id) is the primary key (column with unique values) for this table.
This table contains the rating of a movie by a user in their review.
created_at is the user's review date. 
```

Write a solution to:

- Find the name of the user who has rated the greatest number of movies. In case of a tie, return the lexicographically smaller user name.
- Find the movie name with the **highest average** rating in `February 2020`. In case of a tie, return the lexicographically smaller movie name.

The result format is in the following example.

**Example 1:**

```
Input: 
Movies table:
+-------------+--------------+
| movie_id    |  title       |
+-------------+--------------+
| 1           | Avengers     |
| 2           | Frozen 2     |
| 3           | Joker        |
+-------------+--------------+
Users table:
+-------------+--------------+
| user_id     |  name        |
+-------------+--------------+
| 1           | Daniel       |
| 2           | Monica       |
| 3           | Maria        |
| 4           | James        |
+-------------+--------------+
MovieRating table:
+-------------+--------------+--------------+-------------+
| movie_id    | user_id      | rating       | created_at  |
+-------------+--------------+--------------+-------------+
| 1           | 1            | 3            | 2020-01-12  |
| 1           | 2            | 4            | 2020-02-11  |
| 1           | 3            | 2            | 2020-02-12  |
| 1           | 4            | 1            | 2020-01-01  |
| 2           | 1            | 5            | 2020-02-17  | 
| 2           | 2            | 2            | 2020-02-01  | 
| 2           | 3            | 2            | 2020-03-01  |
| 3           | 1            | 3            | 2020-02-22  | 
| 3           | 2            | 4            | 2020-02-25  | 
+-------------+--------------+--------------+-------------+
Output: 
+--------------+
| results      |
+--------------+
| Daniel       |
| Frozen 2     |
+--------------+
Explanation: 
Daniel and Monica have rated 3 movies ("Avengers", "Frozen 2" and "Joker") but Daniel is smaller lexicographically.
Frozen 2 and Joker have a rating average of 3.5 in February but Frozen 2 is smaller lexicographically.
```

- **Thoughts**

  这道题目提出了两个需求，并要求将两个答案连接成一个表格输出。可分别求出两个表格的答案，然后使用`UNION ALL`产生结果

- **Solution**

  ```sql
  # the user rating the greatest number of movies --> aggregate and count movie_id for each user_id [count + group by] then 
  (
  SELECT u.name AS results
  FROM MovieRating r LEFT JOIN Users u
  ON r.user_id = u.user_id
  GROUP BY r.user_id
  ORDER BY COUNT(r.movie_id) DESC, u.name
  LIMIT 1
  )
  UNION ALL     
  (
  # title with the highest average rating (AVG(rating))
  SELECT m.title
  FROM MovieRating r LEFT JOIN Movies m
  ON r.movie_id = m.movie_id
  WHERE r.created_at LIKE '2020-02%'
  GROUP BY r.movie_id
  ORDER BY AVG(r.rating) DESC, m.title
  LIMIT 1
  )
  ```

  - <font color=red>**[关键易错]:**</font>
    - 由于题目要求在tie时，返回更小的名字，所以在`OEDER BY`时要把条件都写上
    - 这里要用`UNION ALL`而不是`UNION`，这样当用户名和电影名重合时不会自动去除

## 21. 602. Friend Requests II: Who Has the Most Friends

Table: `RequestAccepted`

```
+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| requester_id   | int     |
| accepter_id    | int     |
| accept_date    | date    |
+----------------+---------+
(requester_id, accepter_id) is the primary key (combination of columns with unique values) for this table.
This table contains the ID of the user who sent the request, the ID of the user who received the request, and the date when the request was accepted.
```

Write a solution to find the people who have the most friends and the most friends number.

The test cases are generated so that only one person has the most friends.

The result format is in the following example.

**Example 1:**

```
Input: 
RequestAccepted table:
+--------------+-------------+-------------+
| requester_id | accepter_id | accept_date |
+--------------+-------------+-------------+
| 1            | 2           | 2016/06/03  |
| 1            | 3           | 2016/06/08  |
| 2            | 3           | 2016/06/08  |
| 3            | 4           | 2016/06/09  |
+--------------+-------------+-------------+
Output: 
+----+-----+
| id | num |
+----+-----+
| 3  | 3   |
+----+-----+
Explanation: 
The person with id 3 is a friend of people 1, 2, and 4, so he has three friends in total, which is the most number than any others.
```

**Follow up:** In the real world, multiple people could have the same most number of friends. Could you find all these people in this case?

- **Thoughts**

  As being friends is bidirectional, we can count the total number of both `requests _id` and `accepter_id` --> union two tables by rows together and count

- **Solution**

  ```sql
  SELECT a.requester_id AS id, COUNT(a.requester_id) AS num
  FROM (
    SELECT requester_id, accepter_id
    FROM RequestAccepted a
    UNION
    SELECT accepter_id, requester_id
    FROM RequestAccepted
  ) a
  GROUP BY id
  ORDER BY num DESC 
  LIMIT 1
  ```

## 22. 585. Investments in 2016

Table: `Insurance`

```
+-------------+-------+
| Column Name | Type  |
+-------------+-------+
| pid         | int   |
| tiv_2015    | float |
| tiv_2016    | float |
| lat         | float |
| lon         | float |
+-------------+-------+
pid is the primary key (column with unique values) for this table.
Each row of this table contains information about one policy where:
pid is the policyholder's policy ID.
tiv_2015 is the total investment value in 2015 and tiv_2016 is the total investment value in 2016.
lat is the latitude of the policy holder's city. It's guaranteed that lat is not NULL.
lon is the longitude of the policy holder's city. It's guaranteed that lon is not NULL.
```

Write a solution to report the sum of all total investment values in 2016 `tiv_2016`, for all policyholders who:

- have the same `tiv_2015` value as one or more other policyholders, and
- are not located in the same city as any other policyholder (i.e., the (`lat, lon`) attribute pairs must be unique).

Round `tiv_2016` to **two decimal places**.

The result format is in the following example.

**Example 1:**

```
Input: 
Insurance table:
+-----+----------+----------+-----+-----+
| pid | tiv_2015 | tiv_2016 | lat | lon |
+-----+----------+----------+-----+-----+
| 1   | 10       | 5        | 10  | 10  |
| 2   | 20       | 20       | 20  | 20  |
| 3   | 10       | 30       | 20  | 20  |
| 4   | 10       | 40       | 40  | 40  |
+-----+----------+----------+-----+-----+
Output: 
+----------+
| tiv_2016 |
+----------+
| 45.00    |
+----------+
Explanation: 
The first record in the table, like the last record, meets both of the two criteria.
The tiv_2015 value 10 is the same as the third and fourth records, and its location is unique.

The second record does not meet any of the two criteria. Its tiv_2015 is not like any other policyholders and its location is the same as the third record, which makes the third record fail, too.
So, the result is the sum of tiv_2016 of the first and last record, which is 45.
```

- **Thoughts**

  题目主要要求使用两个条件进行搜索，所以需要筛选出同时满足两个条件的记录。

  - <u>Method 1:</u> 使用`WHERE`合并两个条件，并最终在筛选后的表格进行计算
  - <u>Method 2:</u> 使用window function，分别计算满足每个条件的总数，然后根据数量进行筛选

- **Solution**

  - <u>Method 1:</u>

    ```sql
    SELECT ROUND(SUM(tiv_2016), 2) AS tiv_2016
    FROM Insurance
    WHERE tiv_2015 IN
    	(
        SELECT tiv_2015 FROM Insurance
        GROUP BY tiv_2015
        HAVING COUNT(*) > 1
      ) AND CONCAT(lat, lon) IN 
      (
        SELECT CONCAT(lat, lon) FROM Insurance
                GROUP BY CONCAT(lat, lon)
                HAVING COUNT(*) = 1
      )
    ```

    - 注意要使用`CONCAT`，因为sql不允许同时有两个变量与`IN`一起使用

  - <u>Method 2:</u>

    ```sql
    SELECT ROUND(SUM(tiv_2016 ), 2) AS tiv_2016 
    FROM
    (
        SELECT *, 
        COUNT(*) OVER(PARTITION BY tiv_2015) AS cnt_tiv,
        COUNT(*) OVER(PARTITION BY LAT, LON) AS cnt_latlon
        FROM INSURANCE
    ) a
    WHERE cnt_tiv > 1 AND cnt_latlon = 1
    ```

## 23. 1321. Restaurant Growth

Table: `Customer`

```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| customer_id   | int     |
| name          | varchar |
| visited_on    | date    |
| amount        | int     |
+---------------+---------+
In SQL,(customer_id, visited_on) is the primary key for this table.
This table contains data about customer transactions in a restaurant.
visited_on is the date on which the customer with ID (customer_id) has visited the restaurant.
amount is the total paid by a customer.
```

You are the restaurant owner and you want to analyze a possible expansion (there will be at least one customer every day).

Compute the moving average of how much the customer paid in a seven days window (i.e., current day + 6 days before). `average_amount` should be **rounded to two decimal places**.

Return the result table ordered by `visited_on` **in ascending order**.

The result format is in the following example.

**Example 1:**

```
Input: 
Customer table:
+-------------+--------------+--------------+-------------+
| customer_id | name         | visited_on   | amount      |
+-------------+--------------+--------------+-------------+
| 1           | Jhon         | 2019-01-01   | 100         |
| 2           | Daniel       | 2019-01-02   | 110         |
| 3           | Jade         | 2019-01-03   | 120         |
| 4           | Khaled       | 2019-01-04   | 130         |
| 5           | Winston      | 2019-01-05   | 110         | 
| 6           | Elvis        | 2019-01-06   | 140         | 
| 7           | Anna         | 2019-01-07   | 150         |
| 8           | Maria        | 2019-01-08   | 80          |
| 9           | Jaze         | 2019-01-09   | 110         | 
| 1           | Jhon         | 2019-01-10   | 130         | 
| 3           | Jade         | 2019-01-10   | 150         | 
+-------------+--------------+--------------+-------------+
Output: 
+--------------+--------------+----------------+
| visited_on   | amount       | average_amount |
+--------------+--------------+----------------+
| 2019-01-07   | 860          | 122.86         |
| 2019-01-08   | 840          | 120            |
| 2019-01-09   | 840          | 120            |
| 2019-01-10   | 1000         | 142.86         |
+--------------+--------------+----------------+
Explanation: 
1st moving average from 2019-01-01 to 2019-01-07 has an average_amount of (100 + 110 + 120 + 130 + 110 + 140 + 150)/7 = 122.86
2nd moving average from 2019-01-02 to 2019-01-08 has an average_amount of (110 + 120 + 130 + 110 + 140 + 150 + 80)/7 = 120
3rd moving average from 2019-01-03 to 2019-01-09 has an average_amount of (120 + 130 + 110 + 140 + 150 + 80 + 110)/7 = 120
4th moving average from 2019-01-04 to 2019-01-10 has an average_amount of (130 + 110 + 140 + 150 + 80 + 110 + 130 + 150)/7 = 142.86
```

- **Thoughts**

  这道题目要求计算7天窗口的滑动平均值，此功能可以通过`window_function`实现，对今天及前六天的记录进行求和和平均的计算。

- **Solution**

  ```sql
  # use window function: rows BETWEEN 6 preceding and current row --> AVG(amount) OVER (ROWS BETWEEN 6 PRECEDING AND CURRENT ROW)
  
  SELECT *
  FROM (
          SELECT visited_on, 
          SUM(total_amount) OVER (ORDER BY visited_on ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) AS amount,
          ROUND(AVG(total_amount) OVER (ORDER BY visited_on ROWS BETWEEN 6 PRECEDING AND CURRENT ROW), 2) AS average_amount
      FROM
          (
              SELECT visited_on, SUM(amount) AS total_amount
              FROM Customer
              GROUP BY visited_on
          ) a
  ) b
  WHERE DATEDIFF(visited_on, (SELECT MIN(visited_on) FROM Customer)) >= 6
  ```

  - <font color=red>**注意：**</font>对通过操作产生的表进行`WHERE`筛选出结果时，要另开新表进行`SELECT`！

## 24. Regular Expressions

1. A valid e-mail has a prefix name and a domain where:

   - **The prefix name** is a string that may contain letters (upper or lower case), digits, underscore `'_'`, period `'.'`, and/or dash `'-'`. The prefix name **must** start with a letter.

   - **The domain** is `'@leetcode.com'`.

   `Solution:` `WHERE mail REGEXP '^[A-Za-z][A-Za-z0-9_.-]*@leetcode[.]com$'`
