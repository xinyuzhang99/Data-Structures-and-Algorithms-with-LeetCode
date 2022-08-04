# 2-D Dynamic Programming

- **01背包问题🎒**

  ![416.分割等和子集1](https://img-blog.csdnimg.cn/20210117171307407.png)

  01背包和完全背包就够用了。而完全背包又是也是01背包稍作变化而来，即：完全背包的物品数量是无限的。<font color=red>**所以背包问题的理论基础重中之重是01背包，一定要理解透！**</font>

  有n件物品和一个最多能背重量为w的背包。第i件物品的重量是weight[i]，得到的价值是value[i] 。**每件物品只能用一次**，求解将哪些物品装入背包里物品价值总和最大。

  <img src="https://img-blog.csdnimg.cn/20210117175428387.jpg" alt="动态规划-背包问题" style="zoom: 33%;" />

  - 暴力解法：每一件物品其实只有两个状态，取或者不取，所以可以使用回溯法搜索出所有的情况，那么时间复杂度就是$o(2^n)$，这里的n表示物品数量。--> **所以暴力的解法是指数级别的时间复杂度。进而才需要动态规划的解法来进行优化！**

  - 例子：背包最大重量为4。物品为：

    |       | 重量 | 价值 |
    | :---: | :--: | :--: |
    | 物品0 |  1   |  15  |
    | 物品1 |  3   |  20  |
    | 物品2 |  4   |  30  |

    问背包能背的物品最大价值是多少？

  - <u>二维dp数组</u>

    --> 使用动态规划五部曲进行分析

    1. 确定dp数组以及下标的含义

       对于背包问题，有一种写法， 是使用二维数组，即**`dp[i][j]` 表示从下标为[0-i]的物品里任意取，放进容量为j的背包，价值总和最大是多少**。

       <img src="https://img-blog.csdnimg.cn/20210110103003361.png" alt="动态规划-背包问题1" style="zoom:50%;" />

    2. 确定递推公式

       再回顾一下`dp[i][j]`的含义：从下标为[0-i]的物品里任意取，放进容量为j的背包，价值总和最大是多少。

       那么可以有两个方向推出来`dp[i][j]`，

       - **不放物品i**：由`dp[i - 1][j]`推出，即背包容量为j，里面不放物品i的最大价值，此时`dp[i][j]`就是`dp[i - 1][j]`。(其实就是当物品i的重量大于背包j的重量时，物品i无法放进背包中，所以被背包内的价值依然和前面相同。)
       - **放物品i**：由`dp[i - 1][j - weight[i]`推出，`dp[i - 1][j - weight[i]` 为背包容量为`j - weight[i]`的时候不放物品i的最大价值，那么`dp[i - 1][j - weight[i] + value[i]`（物品i的价值），就是背包放物品i得到的最大价值

       所以递归公式： `dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - weight[i]] + value[i])`;

    3. dp数组如何初始化

       **关于初始化，一定要和dp数组的定义吻合，否则到递推公式的时候就会越来越乱**。

       - 首先从`dp[i][j]`的定义出发，如果背包容量j为0的话，即`dp[i][0]`，无论是选取哪些物品，背包价值总和一定为0。

       - 状态转移方程 `dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - weight[i]] + value[i])`; 可以看出i是由 i-1 推导出来，那么i为0的时候就一定要初始化。

         `dp[0][j]`，即：i为0，存放编号0的物品的时候，各个容量的背包所能存放的最大价值。

         那么很明显当` j < weight[0]`的时候，`dp[0][j]` 应该是 0，因为背包容量比编号0的物品重量还小。

         当`j >= weight[0]`时，`dp[0][j]` 应该是value[0]，因为背包容量放足够放编号0物品。

       <img src="https://code-thinking.cdn.bcebos.com/pics/%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92-%E8%83%8C%E5%8C%85%E9%97%AE%E9%A2%9810.jpg" alt="动态规划-背包问题10" style="zoom:50%;" />

       ```python
       # for j in range(weight[0]):
         # dp[0][j] = 0
       
       # weight数组的大小就是物品个数, bagweight是背包容量
       dp = [[0] * (bagweight + 1) for i in range(len(weight))]
       for j in range(weight[0], bagweight + 1):
         dp[0][j] = value[0]
       ```

    4. 确定遍历顺序

       **先遍历物品还是先遍历背包重量呢？** **其实都可以！！但是<font color=red>先遍历物品</font>更好理解**。

       ```python
       # weight数组的大小就是物品个数
       for i in range(1, len(weight)):		  # 遍历物品; 从1开始，因为dp[x][0]已经为0了
         for j in range(bagweight + 1):				# 遍历背包容量
           if j < weight[i]:								# weight[i]:i物品的重量
             dp[i][j] = dp[i - 1][j]				# 物品重量大于背包重量，物品放不进去 --> 不放物品
           else:
             dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - weight[i]] + value[i])
       ```

       `dp[i-1][j]`和`dp[i - 1][j - weight[i]` 都在`dp[i][j]`的左上角方向（包括正上方向），那么先遍历物品，再遍历背包的过程如图所示：

       <img src="https://img-blog.csdnimg.cn/202101101032124.png" alt="动态规划-背包问题5" style="zoom:50%;" />

    - ```python
      def test_2D_bag_problem1(bag_size, weight, value) -> int: 
      	rows, cols = len(weight), bag_size + 1
      	dp = [[0 for _ in range(cols)] for _ in range(rows)]
          
      	# 初始化dp数组. 
      	for i in range(rows): 
      		dp[i][0] = 0
      	first_item_weight, first_item_value = weight[0], value[0]
      	for j in range(1, cols): 	
      		if first_item_weight <= j: 
      			dp[0][j] = first_item_value
      
      	# 更新dp数组: 先遍历物品, 再遍历背包. 
      	for i in range(1, len(weight)): 
      		cur_weight, cur_val = weight[i], value[i]
      		for j in range(1, cols): 
      			if cur_weight > j: 					# 说明背包装不下当前物品. 
      				dp[i][j] = dp[i - 1][j]  # 所以不装当前物品. 
      			else: 
      				# 定义dp数组: dp[i][j] 前i个物品里，放进容量为j的背包，价值总和最大是多少。
      				dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - cur_weight]+ cur_val)
      ```

  - <u>一维dp数组（滚动数组）</u>

    对于背包问题其实状态都是可以压缩的。

    在使用二维数组的时候，递推公式：`dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - weight[i]] + value[i])`

    **其实可以发现如果把dp[i - 1]那一层拷贝到dp[i]上，表达式完全可以是：`dp[i][j] = max(dp[i][j], dp[i][j - weight[i]] + value[i])`**

    **--> 与其把dp[i - 1]这一层拷贝到dp[i]上，不如只用一个一维数组了**，只用dp[j]（一维数组，也可以理解是一个滚动数组）。这就是滚动数组的由来，需要满足的条件是==上一层可以重复利用，直接拷贝到当前层==。

    1. 确定dp数组的定义

       在一维dp数组中，dp[j]表示：一维dp数组的递推公式最大为dp[j]。

    2. 一维dp数组的递推公式

       dp[j]为容量为j的背包所背的最大价值，那么如何推导dp[j]呢？dp[j]可以通过`dp[j - weight[i]]`推导出来，`dp[j - weight[i]]`表示容量为`j - weight[i]`的背包所背的最大价值。

       `dp[j - weight[i]] + value[i]` 表示 容量为 `j - 物品i重量的背包加上物品i的价值`。（也就是容量为j的背包，放入物品i了之后的价值即：dp[j]）

       此时dp[j]有两个选择，一个是取自己dp[j] 相当于二维dp数组中的`dp[i-1][j]`，即不放物品i，一个是取`dp[j - weight[i]] + value[i]`，即放物品i，指定是取最大的，毕竟是求最大价值，

       ==递归公式：`dp[j] = max(dp[j], dp[j - weight[i]] + value[i])`== --> 看出相对于二维dp数组的写法，就是把`dp[i][j]`中i的维度去掉了

    3. 一维dp数组如何初始化

       dp[j]表示：容量为j的背包，所背的物品价值可以最大为dp[j]，那么dp[0]就应该是0，因为背包容量为0所背的物品的最大价值就是0。假设物品价值都是大于0的，所以dp数组初始化的时候，都初始为0就可以了

    4. 一维dp数组遍历顺序

       ```python
       for i in range(len(weight)):		  # 遍历物品; 从1开始，因为dp[0]已经为0了
         for j in range(bagweight, weight[i] - 1, -1):		# 遍历背包容量
           dp[j] = max(dp[j], dp[j - weight[i]] + value[i])
       ```

       - 二维dp遍历的时候，背包容量是从小到大，而一维dp遍历的时候，背包是从大到小。

         --> <font color=blue>**倒序遍历是为了保证物品i只被放入一次！**</font>。但如果一旦正序遍历了，那么物品0就会被重复加入多次！

         举一个例子：物品0的重量weight[0] = 1，价值value[0] = 15

         如果正序遍历:

         dp[1] = dp[1 - weight[0]] + value[0] = 15

         dp[2] = dp[2 - weight[0]] + value[0] = 30

         此时dp[2]就已经是30了，意味着物品0，被放入了两次，所以不能正序遍历。

         为什么倒序遍历，就可以保证物品只放入一次呢？

         倒序就是先算dp[2]

         dp[2] = dp[2 - weight[0]] + value[0] = 15 （dp数组已经都初始化为0）

         dp[1] = dp[1 - weight[0]] + value[0] = 15

         所以从后往前循环，每次取得状态不会和之前取得状态重合，这样每种物品就只取一次了。

         **那么问题又来了，为什么二维dp数组历的时候不用倒序呢？**

         因为对于二维dp，`dp[i][j]`都是通过上一层即`dp[i - 1][j]`计算而来，本层的`dp[i][j]`并不会被覆盖！

       - **看两个嵌套for循环的顺序，代码中是先遍历物品嵌套遍历背包容量，那可不可以先遍历背包容量嵌套遍历物品呢？**

         不可以！

         因为一维dp的写法，背包容量一定是要倒序遍历（原因上面已经讲了），如果遍历背包容量放在上一层，那么每个dp[j]就只会放入一个物品，即：背包里只放入了一个物品。

         倒序遍历的原因是，本质上还是一个对二维数组的遍历，并且右下角的值依赖上一层左上角的值，因此需要保证左边的值仍然是上一层的，从右向左覆盖。

    5. 举例推导dp数组

       <img src="https://img-blog.csdnimg.cn/20210110103614769.png" alt="动态规划-背包问题9" style="zoom:50%;" />

    ```python
    def test_1D_bag_problem():
        weight = [1, 3, 4]
        value = [15, 20, 30]
        bag_weight = 4
        # 初始化: 全为0
        dp = [0] * (bag_weight + 1)
    
        # 先遍历物品, 再遍历背包容量
        for i in range(len(weight)):
            for j in range(bag_weight, weight[i] - 1, -1):
                # 递归公式
                dp[j] = max(dp[j], dp[j - weight[i]] + value[i])
    
        print(dp)
    
    test_1_wei_bag_problem()
    ```

## 1. 62 [Unique Paths](https://leetcode.com/problems/unique-paths/description/)

|  Category  |   Difficulty    | Likes |                             Tags                             |
| :--------: | :-------------: | :---: | :----------------------------------------------------------: |
| algorithms | Medium (60.24%) | 10113 | [`array`](https://leetcode.com/tag/array); [`dynamic-programming`](https://leetcode.com/tag/dynamic-programming) |

There is a robot on an `m x n` grid. The robot is initially located at the **top-left corner** (i.e., `grid[0][0]`). The robot tries to move to the **bottom-right corner** (i.e., `grid[m - 1][n - 1]`). The robot can only move either down or right at any point in time.

Given the two integers `m` and `n`, return *the number of possible unique paths that the robot can take to reach the bottom-right corner*.

The test cases are generated so that the answer will be less than or equal to `2 * 109`.

**Example 1:**

![img](https://assets.leetcode.com/uploads/2018/10/22/robot_maze.png)

```
Input: m = 3, n = 7
Output: 28
```

**Example 2:**

```
Input: m = 3, n = 2
Output: 3
Explanation: From the top-left corner, there are a total of 3 ways to reach the bottom-right corner:
1. Right -> Down -> Down
2. Down -> Down -> Right
3. Down -> Right -> Down
```

- **Constraints:**

  - `1 <= m, n <= 100`

- **Thoughts**

  - 从题目可得，机器人只能往下和右的方向走，对于每一个格子而言，该格子的独有路径数为上面格子和左边格子路径数的和 --> 状态转移，使用自底向上动态规划方法 (Bottom-Up with Tabulations)

    ![IMG_904E053C688A-1](/Users/xinyuzhang/Downloads/IMG_904E053C688A-1.jpeg)

- **Solution**

  ```python
  # formula: F(r, c) = F(r - 1, c) + F(r, c - 1) (F is the number of paths at (r, c))
  # initial state: F(0, 0) = 1
  # end state: r = m - 1, c = n - 1
  def uniquePaths(self, m: int, n: int) -> int:
          # m: row, n: col
          # dp = [[0] * col for i in range(row)]
          dp = [[0] * n for _ in range(m)]
          dp[0][0] = 1
          for r in range(m):
              for c in range(n):
                  # the upper cell is valid
                  if r - 1 >= 0:
                      dp[r][c] += dp[r - 1][c]
                  # the left cell is valid
                  if c - 1 >= 0:
                      dp[r][c] += dp[r][c - 1]
          return dp[m - 1][n - 1]
  ```

  - Time complexity: $O(mn)$

    Space complexity: $O(mn)$

## 2. 63 [Unique Paths II](https://leetcode.com/problems/unique-paths-ii/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (38.46%) | [`array`](https://leetcode.com/tag/array); [`dynamic-programming`](https://leetcode.com/tag/dynamic-programming) |

You are given an `m x n` integer array `grid`. There is a robot initially located at the **top-left corner** (i.e., `grid[0][0]`). The robot tries to move to the **bottom-right corner** (i.e., `grid[m-1][n-1]`). The robot can only move either down or right at any point in time.

An obstacle and space are marked as `1` or `0` respectively in `grid`. A path that the robot takes cannot include **any** square that is an obstacle.

Return *the number of possible unique paths that the robot can take to reach the bottom-right corner*.

The testcases are generated so that the answer will be less than or equal to `2 * 109`.

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/11/04/robot1.jpg)

```
Input: obstacleGrid = [[0,0,0],[0,1,0],[0,0,0]]
Output: 2
Explanation: There is one obstacle in the middle of the 3x3 grid above.
There are two ways to reach the bottom-right corner:
1. Right -> Right -> Down -> Down
2. Down -> Down -> Right -> Right
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2020/11/04/robot2.jpg)

```
Input: obstacleGrid = [[0,1],[0,0]]
Output: 1
```

- **Constraints:**

  - `m == obstacleGrid.length`

  - `n == obstacleGrid[i].length`

  - `1 <= m, n <= 100`

  - `obstacleGrid[i][j]` is `0` or `1`.

- **Thoughts**

  根据动态规划的五个步骤：procedure: definition; function; initialization; traversal order; example

  - procedure: definition; function; initialization, traversal order, example
  - definition: `dp[r][c]` is the number of unique path to grid[r][c]
  - function: `dp[r][c] = dp[r - 1][c] + dp[r][c - 1]`; if `grid[r][c] == 1`, `dp[r][c] = 0`, continue
  - initialization: `dp[0][0] = 1`

  --> 和62题不同的关键点在于：有障碍的话，需要标记对应的dp table（dp数组）保持初始值0

- **Solution**

  ```python
  def uniquePathsWithObstacles(self, obstacleGrid: List[List[int]]) -> int:
          row, col = len(obstacleGrid), len(obstacleGrid[0])
          dp = [[0] * col for _ in range(row)]   # build an empth grid
  
          dp[0][0] = 1
          for r in range(row):
              for c in range(col):
                  if obstacleGrid[r][c] == 1:
                      dp[r][c] = 0
                      continue
                  if r - 1 >= 0:
                      dp[r][c] += dp[r - 1][c]
                  if c - 1 >= 0:
                      dp[r][c] += dp[r][c - 1]
          return dp[row - 1][col - 1]
  ```

  - Time complexity: $O(mn)$

    Space complexity: $O(mn)$