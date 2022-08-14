# 2-D Dynamic Programming

- **01背包问题🎒 (0-1 Knapsack Problem)**

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

       对于背包问题，有一种写法， 是使用二维数组，即**`dp[i][j]` 表示从下标为[0-i]的物品里任意取，放进容量为j的背包，价值总和最大是多少**。 --> 根据这个定义，我们想求的最终答案就是 `dp[n][w]`

       <img src="https://img-blog.csdnimg.cn/20210110103003361.png" alt="动态规划-背包问题1" style="zoom:50%;" />

    2. 确定递推公式

       再回顾一下`dp[i][j]`的含义：对于前 `i` 个物品，当前背包的容量为 `j`，这种情况下可以装的最大价值是 `dp[i][j]`。

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
      	dp = [[0] * (bagweight + 1) for i in range(len(weight))]
      	for j in range(weight[0], bagweight + 1):
        	dp[0][j] = value[0]
      
      	# 更新dp数组: 先遍历物品, 再遍历背包. 
      	for i in range(1, len(weight)):		  # 遍历物品; 从1开始，因为dp[x][0]已经为0了
        	for j in range(bagweight + 1):		# 遍历背包容量
          	if j < weight[i]:								# weight[i]:i物品的重量
            	dp[i][j] = dp[i - 1][j]				# 物品重量大于背包重量，物品放不进去 --> 不放物品
          	else:
            	dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - weight[i]] + value[i])
      ```

  - <u>一维dp数组（滚动数组）</u>

    对于背包问题其实空间是可以压缩的。在使用二维数组的时候，递推公式：`dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - weight[i]] + value[i])` **其实可以发现如果把dp[i - 1]那一层拷贝到dp[i]上，表达式完全可以是：`dp[i][j] = max(dp[i][j], dp[i][j - weight[i]] + value[i])`**

    **--> 与其把dp[i - 1]这一层拷贝到dp[i]上，不如只用一个一维数组了**，只用dp[j]（一维数组，也可以理解是一个滚动数组）。这就是滚动数组的由来，需要满足的条件是==上一层可以重复利用，直接拷贝到当前层==。

    <img src="https://pic.leetcode-cn.com/1622938646-fiOLSL-image.png" alt="image.png" style="zoom:33%;" />

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
    # weight = [1, 3, 4]
    # value = [15, 20, 30]
    # bag_weight = 4
    def test_1D_bag_problem():
        # 初始化: 全为0
        dp = [0] * (bag_weight + 1)
    
        # 先遍历物品, 再遍历背包容量
        for i in range(len(weight)):
            for j in range(bag_weight, weight[i] - 1, -1):    #背包重量: [weight[i], bag_weight]
                # 递归公式
                dp[j] = max(dp[j], dp[j - weight[i]] + value[i])
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

## 3. 416 [Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (46.47%) | [`dynamic-programming`](https://leetcode.com/tag/dynamic-programming) |

Given a **non-empty** array `nums` containing **only positive integers**, find if the array can be partitioned into two subsets such that the sum of elements in both subsets is equal. 

**Example 1:**

```
Input: nums = [1,5,11,5]
Output: true
Explanation: The array can be partitioned as [1, 5, 5] and [11].
```

**Example 2:**

```
Input: nums = [1,2,3,5]
Output: false
Explanation: The array cannot be partitioned into equal sum subsets.
```

- **Constraints:**

  - `1 <= nums.length <= 200`

  - `1 <= nums[i] <= 100`

- **Thoughts**

  <font color=blue>**[子集划分问题是一个典型的背包问题！]**</font>

  - 这道题目是要找是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。那么只要找到集合里能够出现 sum // 2 的子集总和，就算是可以分割成两个相同元素和子集了。

    我们可以先对集合求和，得出 `sum`，把问题转化为背包问题：**给一个可装载重量为 `sum / 2` 的背包和 `N` 个物品，每个物品的重量为 `nums[i]`。现在让你装物品，是否存在一种装法，能够恰好将背包装满**？
  
  - 动规五部曲分析如下：
  
    - definition: dp[j]: 容量为j的背包，所背的物品价值可以最大为dp[j]
    - function: dp[j] = max[dp[j], dp[j - nums[i]] + nums[i]]; return `dp[sum//2] = dp[target]` 
    - initialization: dp[0] = 0
    - traversal order: 物品遍历的for循环放在外层，遍历背包的for循环放在内层，且内层for循环倒序遍历
    - return: dp[j]的数值一定是小于等于j的。如果dp[j] == j说明，集合中的子集总和正好可以凑成总和j
  
- **Solution**

  - <u>Method 1: Dynamic Programming (01 Knapsack Problem)</u>

    ```python
    def canPartition(self, nums: List[int]) -> bool:
      # 和为奇数时，不可能划分成两个和相等的集合
      if sum(nums) % 2 == 1:
        return False
    
      target = sum(nums) // 2
      ## Optimization
      # if target in nums:
        # return True
      dp = [0] * (target + 1)
      for i in range(len(nums)):
        for j in range(target, nums[i] - 1, -1):  # bagweight: [nums[i], target]
          dp[j] = max(dp[j], dp[j - nums[i]] + nums[i])
    
      return dp[target] == target	# 看集合中的元素是否可以正好凑成总和target
    ```

    - Time complexity: $O(n \times target/sum(nums))$ --> 其中 n 是数组的长度，$\textit{target}$是整个数组的元素和的一半。需要计算出所有的状态，每个状态在进行转移时的时间复杂度为O(1)。

      Space complexity: $O(target)$

    - <font color=red>**关键点：需要注意的是 `j` 应该从后往前反向遍历，因为每个物品（或者说数字）只能用一次，以免之前的结果影响其他的结果**</font>

  - <u>Method 2: use set and calculate all possible sums</u>

    ```python
    def canPartition(self, nums: List[int]) -> bool:
      # 和为奇数时，不可能划分成两个和相等的集合
      # edge case: the sum(nums) is odd --> cannot be split equally
      if sum(nums) % 2 == 1:
        return False
    
      ## Important!!: Items of a set in python are immutable (unchangeable) and set size cannot be changed during iteration
      dp = set()
      dp.add(0)
      target = sum(nums) // 2
      for n in nums:
        nextDP = set(dp)
        for val in dp:
          nextDP.add(n + val)
        dp = nextDP
      return True if target in dp else False 
    ```

    - Time complexity: $O(n \times target/sum(nums))$ 

      Space complexity: $O(target)$

## 4. 1049 [Last Stone Weight II](https://leetcode.com/problems/last-stone-weight-ii/description/)

|  Category  |    Difficult    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (51.30%) | [`array`](https://leetcode.com/tag/array); [`greedy`](https://leetcode.com/tag/greedy) |

You are given an array of integers `stones` where `stones[i]` is the weight of the `ith` stone.

We are playing a game with the stones. On each turn, we choose any two stones and smash them together. Suppose the stones have weights `x` and `y` with `x <= y`. The result of this smash is:

- If `x == y`, both stones are destroyed, and
- If `x != y`, the stone of weight `x` is destroyed, and the stone of weight `y` has new weight `y - x`.

At the end of the game, there is **at most one** stone left.

Return *the smallest possible weight of the left stone*. If there are no stones left, return `0`.

**Example 1:**

```
Input: stones = [2,7,4,1,8,1]
Output: 1
Explanation:
We can combine 2 and 4 to get 2, so the array converts to [2,7,1,8,1] then,
we can combine 7 and 8 to get 1, so the array converts to [2,1,1,1] then,
we can combine 2 and 1 to get 1, so the array converts to [1,1,1] then,
we can combine 1 and 1 to get 0, so the array converts to [1], then that's the optimal value.
```

**Example 2:**

```
Input: stones = [31,26,33,21,40]
Output: 5
```

- **Constraints:**

  - `1 <= stones.length <= 30`

  - `1 <= stones[i] <= 100`

- **Thoughts**

  - 这道题目和1046. [Last Stone Weight](https://leetcode.com/problems/last-stone-weight/description/) 的区别在于：

    - 1046要求每次取出最重的两块石头smash，所以考虑用最大堆解决
    - 1049对每次smash的石头没有特殊要求，但是要使得最后的一块石头重量最小

  - 我们可以将石子划分为两个堆（正号堆/负号堆）：将每次操作中「重量较大」的石子放到「正号堆」，代表在这次操作中该石子重量在「最终运算结果」中应用 + 运算符；将每次操作中「重量较少/相等」的石子放到「负号堆」，代表在这次操作中该石子重量在「最终运算结果」中应用 − 运算符

    --> <font color=blue>**[任意选i块石头，使得他们的重量趋近于总重量的一半，因为这样和另一半抵消的差值就是最小的 --> 尽量让石头分成重量相同的两堆，相撞之后剩下的石头最小，这样就化解成01背包问题了]**</font>

  - 遵循动态规划五个步骤

    - Definition: dp[j]: 容量为j的背包

    - Function: 01 Knapsack: dp[j] = max(dp[j], dp[j - weight[i]] + value[i]) 

      ​				  formula: dp[j] = max(dp[j], dp[j - stones[i]] + stones[i]) -->  (dp[j - stones[i]]为 容量为j - stones[i]的背包最大所背重量)

      ​				 --> <font color=red>最后一堆石头总重量为`dp[target]`，另一堆为`sum - dp[target]`，两堆石子重量之差即为相撞后最小的石头重量 --> **在计算target的时候，target = sum // 2 因为是向下取整，所以sum - dp[target] 一定是大于等于dp[target]的** --> 为`sum - dp[target] - dp[target]`</font>

    - Initialization: dp[j] = 0, length = length of target (half of all possible weights)
    - Traversal order: 遍历背包时使用倒序遍历

- **Solution**

  ```python
  def lastStoneWeightII(self, stones: List[int]) -> int:
    total = sum(stones)
    target = total // 2
    dp = [0] * (target + 1)
    
    # first traverse through all items and then through all bag weights
    for i in range(len(stones)):
      for j in range(target, nums[i] - 1, -1):
        dp[j] = max(dp[j], dp[j - stones[i]] + stones[i])
    return total - 2 * dp[target]
  ```

  - Time complexity: $O(n \times target)$ 

    Space complexity: $O(target)$

  - 在分析了题目并确定这是背包问题时，可发现本题和416 [Partition Equal Subset Sum]的解法几乎一样，只是最后对`dp[target]`的处理方式不同 --> 416相当于是求背包是否正好装满，而本题是求背包最多能装多少
  - 不用考虑是否最后会留下石头，如果最后没有石头留下，则假设堆里有一个重量为0的石头即可

## 5. 494 Add [Target Sum](https://leetcode.com/problems/target-sum/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (45.41%) | [`dynamic-programming`](https://leetcode.com/tag/dynamic-programming); [`depth-first-search`](https://leetcode.com/tag/depth-first-search) |

You are given an integer array `nums` and an integer `target`.

You want to build an **expression** out of nums by adding one of the symbols `'+'` and `'-'` before each integer in nums and then concatenate all the integers.

- For example, if `nums = [2, 1]`, you can add a `'+'` before `2` and a `'-'` before `1` and concatenate them to build the expression `"+2-1"`.

Return the number of different **expressions** that you can build, which evaluates to `target`. 

**Example 1:**

```
Input: nums = [1,1,1,1,1], target = 3
Output: 5
Explanation: There are 5 ways to assign symbols to make the sum of nums be target 3.
-1 + 1 + 1 + 1 + 1 = 3
+1 - 1 + 1 + 1 + 1 = 3
+1 + 1 - 1 + 1 + 1 = 3
+1 + 1 + 1 - 1 + 1 = 3
+1 + 1 + 1 + 1 - 1 = 3
```

**Example 2:**

```
Input: nums = [1], target = 1
Output: 1
```

- **Constraints:**

  - `1 <= nums.length <= 20`

  - `0 <= nums[i] <= 1000`

  - `0 <= sum(nums[i]) <= 1000`

  - `-1000 <= target <= 1000`

- **Solution**

  - <u>Method 1: Backtracking</u> (Brute-force)

    <img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220812021424524.png" alt="image-20220812021424524" style="zoom: 20%;" />

    这道题目满足标准的回溯题目条件，**对于每个数字 `nums[i]`，我们可以选择给一个正号 `+` 或者一个负号 `-`**，然后利用回溯模板穷举出来所有可能的结果，数一数到底有几种组合能够凑出 `target` 

    回溯过程中维护一个计数器 ，当遇到一种表达式的结果等于目标数 $\textit{target}$时，将计数器的值加 1。

    ```python
    def findTargetSumWays(self, nums: List[int], target: int) -> int:
      n = len(nums)
      self.res = 0
      
      def backtrack(i, total):   # index
        if i == n and total == target:
          self.res += 1
          return
        
        if i < n:
          # +1
          total += nums[i]
          backtrack(i + 1, total)
          total -= nums[i]
    
          # -1
          total -= nums[i]
          backtrack(i + 1, total)
          total += nums[i]
      backtrack(0, 0)
      return self.res
    ```

    - Time complexity: $O(2^n)$ --> 回溯需要遍历所有不同的表达式，共有$2^n$种不同的表达式，每种表达式计算结果需要O(1)的时间，因此总时间复杂度为 $O(2^n)$ --> very low efficiency

      Space complexity: $O(n)$ --> stack memory for recursion function

  - <u>Method 2: Dynamic Programming with Memoziation</u>

    --> 从上面的decision tree可以看出，在回溯的过程中有可能存在多个重叠子问题影响计算效率 --> 使用一个memo记录对于每一个 `(index, total)` pair所包含的`number of expressions` --> `memo[(i, total)] = dp(i + 1, total + nums[i]) + dp(i + 1, total - nums[i])`

    ```python
    def findTargetSumWays(self, nums: List[int], target: int) -> int:
      memo = {}
      
      def dp(i, total):
        if i == len(nums):
          return 1 if total == target else 0
        if (i, total) in memo:
          return memo[(i, total)]
        
        memo[(i, total)] = dp(i + 1, total + nums[i]) + dp(i + 1, total - nums[i])
      	return memo[(i, total)]
      return dp(0, 0)
    ```

    Time complexity: $O(n * total)$

    Space complexity: $O(n)$ 

  - <u>==Method 3: 0-1 Knapsack Problem==</u>

    - 分析题目可知，该题目和1049. [Last Stone Weight II ](https://leetcode.com/problems/last-stone-weight-ii/description/)十分相似，都是分别将正号 `+` 或者负号 `-`分成两堆 --> 这个问题可以转化为一个子集划分问题，而子集划分问题又是一个典型的背包问题

      首先，如果我们把 `nums` 划分成两个子集 `A` 和 `B`，分别代表分配 `+` 的数和分配 `-` 的数，那么他们和 `target` 存在如下关系：

      ```python
      sum(A) - sum(B) = target
      sum(A) = target + sum(B)
      sum(A) + sum(A) = target + sum(B) + sum(A)
      2 * sum(A) = target + sum(nums)
      ```

      综上，可以推出 `sum(A) = (target + sum(nums)) / 2`，也就是把原问题转化成：**`nums` 中存在几个子集 `A`，使得 `A` 中元素的和为 `(target + sum(nums)) / 2`**？

      <font color=blue>**[Edge Cases]: **`(target + sum(nums)) / 2`如果为奇数，则此时无法找到方案，方案数为0; 2. 如果`abs(target) > sum(nums)`, 则即使全为正号或全为负号也无法构成target --> 这两种情况是edge cases!</font> --> 

    - 遵循动态规划五个步骤:

      - Definition：dp[j] 表示：填满j（包括j）这么大容积的包，有dp[j]种方法

      - Function: 

        如果不把 `nums[i]` 算入子集，**或者说你不把这第 `i` 个物品装入背包**，那么恰好装满背包的方法数就取决于上一个状态 `dp[i-1][j]`，继承之前的结果。

        如果把 `nums[i]` 算入子集，**或者说你把这第 `i` 个物品装入了背包**，那么只要看前 `i - 1` 个物品有几种方法可以装满 `j - nums[i-1]` 的重量就行了，所以取决于状态 `dp[i-1][j-nums[i-1]]`。

        例如：dp[j]，j 为5，

        - 已经有一个1（nums[i]） 的话，有 dp[4]种方法 凑成 dp[5]。
        - 已经有一个2（nums[i]） 的话，有 dp[3]种方法 凑成 dp[5]。
        - 已经有一个3（nums[i]） 的话，有 dp[2]中方法 凑成 dp[5]
        - 已经有一个4（nums[i]） 的话，有 dp[1]中方法 凑成 dp[5]
        - 已经有一个5 （nums[i]）的话，有 dp[0]中方法 凑成 dp[5]

        --> 公式：`dp[j] += dp[j - nums[i]]`

    ```python
    def findTargetSumWays(self, nums: List[int], target: int) -> int:
      total = sum(nums)
      # edge case
      if abs(target) > total or (target + total) % 2 == 1:
        return 0
    
      target_new = (target + total) // 2
      dp = [0] * (target_new + 1)
      dp[0] = 1
    
      for i in range(len(nums)):                      # 遍历所有物品
        for j in range(target_new, nums[i] - 1, -1):  # 遍历背包
          dp[j] += dp[j - nums[i]] # dp[j]: not add nums[i]; dp[j-nums[i]]: add nums[i] into the backpack
      return dp[target_new]
    ```

    - Time complexity: $O(n * targetNew)$

      Space complexity: $O(targetNew)$ 

## 6. 474 [Ones and Zeroes](https://leetcode.com/problems/ones-and-zeroes/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (46.52%) | [`dynamic-programming`](https://leetcode.com/tag/dynamic-programming) |

You are given an array of binary strings `strs` and two integers `m` and `n`.

Return *the size of the largest subset of `strs` such that there are **at most*** `m` `0`*'s and* `n` `1`*'s in the subset*.

A set `x` is a **subset** of a set `y` if all elements of `x` are also elements of `y`.

**Example 1:**

```
Input: strs = ["10","0001","111001","1","0"], m = 5, n = 3
Output: 4
Explanation: The largest subset with at most 5 0's and 3 1's is {"10", "0001", "1", "0"}, so the answer is 4.
Other valid but smaller subsets include {"0001", "1"} and {"10", "1", "0"}.
{"111001"} is an invalid subset because it contains 4 1's, greater than the maximum of 3.
```

**Example 2:**

```
Input: strs = ["10","0","1"], m = 1, n = 1
Output: 2
Explanation: The largest subset is {"0", "1"}, so the answer is 2.
```

- **Constraints:**

  - `1 <= strs.length <= 600`

  - `1 <= strs[i].length <= 100`

  - `strs[i]` consists only of digits `'0'` and `'1'`.

  - `1 <= m, n <= 100`

- **Thoughts**

  - 这道题和经典的背包问题非常相似，但是和经典的背包问题只有一种容量不同，这道题有两种容量，即选取的字符串子集中的 0 和 1 的数量上限。--> 即本题的背包重量有两维，需要同时考虑m和n；每一个元素（即字符串）都只有一个，所以该题为01背包问题

  - 遵循动态规划五部曲：

    - definition: `dp[i][j]`: the size of the largest subset with i 0's and j 1's

    - function: at each time, the object of the backpack is a string --> add the string --> add `zeroNum` 0's and `oneNum` 1's

      --> `dp[i][j] = max(dp[i][j], dp[i-zeroNum][j-oneNum] + 1)` + <font color=blue>[记得在遍历物品是记录每一个字符串0和1的数量]</font>font>

    - initialization: as each cell means the size --> `dp[i][j]` = 0

    - traversal order: 1. traverse object(each string) in order 2. traverse the two backpacks in reverse order

- **Solution**

  ```python
  def findMaxForm(self, strs: List[str], m: int, n: int) -> int:
          dp = [[0] * (n + 1) for _ in range(m + 1)]
  
          for s in strs:
              # for each string, count the number of 0 and 1
              zeroNum, oneNum = 0, 0
              for char in s:
                  if char == '0':
                      zeroNum += 1
                  else:
                      oneNum += 1
              
              for i in range(m, zeroNum - 1, -1): # i: [zeroNum, m]
                  for j in range(n, oneNum - 1, -1):
                      dp[i][j] = max(dp[i][j], dp[i - zeroNum][j - oneNum] + 1)
          
          return dp[m][n]
  ```

  - Time complexity: $O(len(strs) * m * n)$

    Space complexity: $O(m * n)$ 