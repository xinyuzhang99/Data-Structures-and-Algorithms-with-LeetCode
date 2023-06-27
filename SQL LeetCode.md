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

  当求的表