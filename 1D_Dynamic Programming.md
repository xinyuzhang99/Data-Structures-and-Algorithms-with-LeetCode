# 1-D Dynamic Programming

**动态规划问题的一般形式就是求<font color=red>最值</font>**; **求解动态规划的核心问题是<font color=red>穷举</font>**。

穷举所有可行解其实并不是一件容易的事，需要你熟练掌握递归思维，只有列出**正确的「状态转移方程」**，才能正确地穷举。而且，你需要判断算法问题是否**具备「最优子结构」(Optimal Substructure)**，是否能够通过子问题的最值得到原问题的最值 (**要符合「最优子结构」，子问题间必须互相<font color=red>独立</font>**)。另外，动态规划问题**存在「重叠子问题」(Overlapping Subproblem)**，如果暴力穷举的话效率会很低，所以需要你使用*<u>「备忘录」或者「DP table」</u>*来优化穷举过程，避免不必要的计算。

==[重叠子问题、最优子结构 (***Optimal Substructure***)、状态转移方程 (State Transition Equation)]==就是动态规划三要素！

求解过程：==[初始状态、终止状态、状态转移方程式]== --> 动态规划：通过方程式求出初始状态到终止状态中所有中间状态的值（存到一个数组里） ，以此求得终止状态

- **动态规划五步骤: Definition --> Equation --> Initialization --> Traversal Order --> Example**

  1. 确定dp数组（dp table）以及下标的含义
  2. 确定递推公式/***Optimal Substructure***
  3. dp数组如何初始化
  4. 确定遍历顺序
  5. 举例推导dp数组

- **动态规划debug: 找问题的最好方式就是把dp数组打印出来，看看究竟是不是按照自己思路推导的！**

  --> **写代码之前一定要把状态转移在dp数组的上具体情况模拟一遍，心中有数，确定最后推出的是想要的结果**。然后再写代码，如果代码没通过就打印dp数组，看看是不是和自己预先推导的哪里不一样。如果打印出来和自己预先模拟推导是一样的，那么就是自己的递归公式、初始化或者遍历顺序有问题了。如果和自己预先模拟推导的不一样，那么就是代码实现细节有问题。

- **Example**

  机器人从 (0, 0)走到终点，求一共有多少路径

  ![IMG_904E053C688A-1](/Users/xinyuzhang/Downloads/IMG_904E053C688A-1.jpeg)

  - 方程式：F(r, c) = F(r-1, c) + F(r, c-1)
  - 初始状态：F(0, 0) = 1
  - 终止状态：到达最后一个格子

- 「状态转移方程」这个名词，实际上就是描述问题结构的数学形式（直接代表着暴力解法）:

  <img src="https://labuladong.github.io/algo/images/%e5%8a%a8%e6%80%81%e8%a7%84%e5%88%92%e8%af%a6%e8%a7%a3%e8%bf%9b%e9%98%b6/fib.png" alt="img" style="zoom: 50%;" />

  状态转移方程思维框架：**明确 base case -> 明确「状态」-> 明确「选择」 -> 定义 `dp` 数组/函数的含义**。
  
  ```python
  # 自顶向下递归的动态规划: Top-Down with Memoization
  def dp(状态1, 状态2, ...):
      for 选择 in 所有可能的选择:
          # 此时的状态已经因为做了选择而改变
          result = 求最值(result, dp(状态1, 状态2, ...))
      return result
  
  # 自底向上迭代的动态规划: Bottom-Up with Tabulations
  # 初始化 base case
  dp[0][0][...] = base case
  # 进行状态转移
  for 状态1 in 状态1的所有取值：
      for 状态2 in 状态2的所有取值：
          for ...
              dp[状态1][状态2][...] = 求最值(选择1，选择2...)
  ```
  
  - 在01背包问题中，**状态有两个，就是「背包的容量」和「可选择的物品」**； 选择也有两个，**「装进背包」或者「不装进背包」**

- **带备忘录自顶向下的递归解法 (Top-Down with Memoization)**

  ```python
  def fib(self, n: int) -> int:
          def helper(n, memo):
              if (n == 0 or n == 1):
                  return n
              if n not in memo:
                  memo[n] = helper(n - 1, memo) + helper(n - 2, memo)
              return memo[n]
          
          memo = {}
          return helper(n, memo)
  ```

  即然耗时的原因是重复计算，那么可以造一个「备忘录」，每次算出某个子问题的答案后别急着返回，先记到「备忘录」里再返回；每次遇到一个子问题先去「备忘录」里查一查，如果发现之前已经解决过这个问题了，直接把答案拿出来用，不要再耗时去计算了。一般使用一个**数组**充当这个「备忘录」，当然也可以使用哈希表（字典），思想都是一样的。

  <img src="https://labuladong.github.io/algo/images/%e5%8a%a8%e6%80%81%e8%a7%84%e5%88%92%e8%af%a6%e8%a7%a3%e8%bf%9b%e9%98%b6/2.jpg" alt="img" style="zoom:50%;" />

  实际上，带「备忘录」的递归算法，把一棵存在巨量冗余的递归树通过「剪枝」，改造成了一幅不存在冗余的递归图，极大减少了子问题（即递归图中节点）的个数。

  <img src="https://labuladong.github.io/algo/images/%e5%8a%a8%e6%80%81%e8%a7%84%e5%88%92%e8%af%a6%e8%a7%a3%e8%bf%9b%e9%98%b6/3.jpg" alt="img" style="zoom:50%;" />

  - **递归算法的时间复杂度就是用子问题个数乘以解决一个子问题需要的时间**。

    子问题个数，即图中节点的总数，由于本算法不存在冗余计算，子问题就是 `f(1)`, `f(2)`, `f(3)` … `f(20)`，数量和输入规模 n = 20 成正比，所以子问题个数为 O(n)。

    解决一个子问题的时间，同上，没有什么循环，时间为 O(1)。

    --> <font color=blue>**算法时间复杂度：O(N)**</font>

- **`dp` 数组的迭代（递推）解法 (Bottom-Up with Tabulations)**

  --> 把上一步「备忘录」独立出来成为一张表，通常叫做 DP table，在这张表上完成「自底向上」的推算

  ```python
  def fib(N):
    if N == 0 or N == 1:
      return N
    # base case
    dp = [0, 1]
    for i in range(2, N + 1):
      dp.append(dp[i - 1] + dp[i - 2])    # the total length is N + 1
    return dp[N]
  ```

  <img src="https://labuladong.github.io/algo/images/%e5%8a%a8%e6%80%81%e8%a7%84%e5%88%92%e8%af%a6%e8%a7%a3%e8%bf%9b%e9%98%b6/4.jpg" alt="img" style="zoom:50%;" />

  - <u>细节优化</u>

    根据斐波那契数列的状态转移方程，当前状态只和之前的两个状态有关，其实并不需要那么长的一个 DP table 来存储所有的状态，只要想办法存储之前的两个状态就行了。所以，可以进一步优化，把空间复杂度降为 O(1)。这也就是我们最常见的计算斐波那契数的算法：

    ```python
    def fib(N):
      if N == 0 or N == 1:
        return N
      dp0, dp1 = 0, 1
      for i in range(2, N + 1):
        dp = dp0 + dp1
        dp0 = dp1
        dp1 = dp
      return dp1
    ```

    这一般是动态规划问题的最后一步优化，如果我们发现<font color=blue>**每次状态转移只需要 DP table 中的一部分**</font>，那么可以尝试缩小 DP table 的大小，只记录必要的数据，从而降低空间复杂度。--> <font color=blue>**一般来说是把一个二维的 DP table 压缩成一维，即把空间复杂度从 $O(n^2)$ 压缩到 $O(n)$**</font>

- **应用：**

  1. <u>计数</u>：有多少种方式/方法（机器人从左上角到右下角有多少条路径）
  2. <u>求最值</u>：最大值/最小值（机器人从左到右路径的最大数字和）
  3. <u>求存在性</u>：是否存在某种可能（是否存在机器人从左到右的路径）

- **题型总结：**

  ![img](https://camo.githubusercontent.com/624ae48228610285917d87d92baba28f9e2132199c3e266dbfb82f59acccf302/68747470733a2f2f636f64652d7468696e6b696e672e63646e2e626365626f732e636f6d2f706963732fe58aa8e68081e8a784e588922de680bbe7bb93e5a4a7e7bab2312e6a7067)

## 1. 322 [Coin Change](https://leetcode.com/problems/coin-change/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (40.64%) | [`dynamic-programming`](https://leetcode.com/tag/dynamic-programming) |

You are given an integer array `coins` representing coins of different denominations and an integer `amount` representing a total amount of money.

Return *the fewest number of coins that you need to make up that amount*. If that amount of money cannot be made up by any combination of the coins, return `-1`.

You may assume that you have an infinite number of each kind of coin.

**Example 1:**

```
Input: coins = [1,2,5], amount = 11
Output: 3
Explanation: 11 = 5 + 5 + 1
```

**Example 2:**

```
Input: coins = [2], amount = 3
Output: -1
```

**Example 3:**

```
Input: coins = [1], amount = 0
Output: 0
```

- **Constraints:**

  - `1 <= coins.length <= 12`

  - `1 <= coins[i] <= 231 - 1`

  - `0 <= amount <= 104`

- **Thoughts**

  - Follow the five procedures of dynamic programming:

    1. 确定dp数组以及下标的含义：**dp[j]：凑足总额为j所需钱币的最少个数为dp[j]**

    2. 确定递推公式：`dp[j] = min(dp[j - coins[i]] + 1, dp[j])` --> 前提：`j - coins[i] >= 0`

    3. dp数组如何初始化

       首先凑足总金额为0所需钱币的个数一定是0，那么dp[0] = 0；

       其他下标对应的数值呢？考虑到递推公式的特性，dp[j]必须初始化为一个最大的数，否则就会在`min(dp[j - coins[i]] + 1, dp[j])`比较的过程中被初始值覆盖。--> 下标非0的元素都是应该是最大值

    4. 确定遍历顺序

       本题求钱币最小个数，**那么钱币有顺序和没有顺序都可以，都不影响钱币的最小个数**。所以本题并不强调集合是组合还是排列。

       **如果求组合数就是外层for循环遍历物品，内层for遍历背包**。

       **如果求排列数就是外层for遍历背包，内层for循环遍历物品**。

    5. 举例推导dp数组

       以输入：coins = [1, 2, 5], amount = 5为例

       <img src="https://camo.githubusercontent.com/1f86436773a97d299934ccae62025735d7c68958d4313139c75a88dcd2b6fc22/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f32303231303230313131313833333930362e6a7067" alt="322.零钱兑换" style="zoom:50%;" />

       --> dp[amount]为最终结果

- **Solution**

  ```python
  class Solution:
      # dynamic programming: optimized substructures + overlapping subproblems
      # initial state: dp[0] = 0
      # end state: i = amount
      # formula: dp[i] = 1 + dp[i - coin] and find the minimum
      def coinChange(self, coins: List[int], amount: int) -> int:
          dp = [float('inf')] * (amount + 1)   # the maximum length of dp：[0, amount]
          dp[0] = 0
  
          for i in range(1, amount + 1):
              for c in coins:
                  if i - c >= 0:
                      dp[i] = min(dp[i], 1 + dp[i - c])
          return dp[amount] if dp[amount] != float('inf') else -1
  ```

  - Time complexity: $O(amount \times len(coins))$ 

    Space complexity: $O(amount + 1)$

## 2. 70 [Climbing Stairs](https://leetcode.com/problems/climbing-stairs/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Easy (51.14%) | [`dynamic-programming`](https://leetcode.com/tag/dynamic-programming) |

You are climbing a staircase. It takes `n` steps to reach the top.

Each time you can either climb `1` or `2` steps. In how many distinct ways can you climb to the top?

**Example 1:**

```
Input: n = 2
Output: 2
Explanation: There are two ways to climb to the top.
1. 1 step + 1 step
2. 2 steps
```

**Example 2:**

```
Input: n = 3
Output: 3
Explanation: There are three ways to climb to the top.
1. 1 step + 1 step + 1 step
2. 1 step + 2 steps
3. 2 steps + 1 step
```

- **Constraints:**
  - `1 <= n <= 45`
- **Thoughts**
  - 根据动态规划五个步骤：确定dp数组以及下标的含义 --> 确定递推公式 --> dp数组如何初始化 --> 确定遍历顺序 --> 举例推导dp数组

- **Solution**

  ```python
  def climbStairs(self, n: int) -> int:
    if n == 1:									# 因为下面直接对dp[2]操作了，防止空指针
      return 1
  
    dp = [0] * (n + 1)
    dp[1], dp[2] = 1, 2	
  
    for i in range(3, n + 1):
      dp[i] = dp[i - 1] + dp[i - 2]
    return dp[n]
  ```

  - Time complexity: O(N)

    Space complexity: O(N)

  - <font color=red>**优化：减少空间复杂度，不用数组而是动态改变数值**</font>

    ```python
    def climbStairs(self, n: int) -> int:
      if n == 1:									# 因为下面直接对dp[2]操作了，防止空指针
        return 1
    
      dp1, dp2 = 1, 2	
    
      for i in range(3, n + 1):
        temp = dp1 + dp2
        dp1 = dp2
        dp2 = temp
      return dp2
    ```

    - Time complexity: O(N)

      Space complexity: O(1)

  - **<font color=red>拓展：</font>**

    这道题目还可以继续深化，就是一步一个台阶，两个台阶，三个台阶，直到 m个台阶，有多少种方法爬到n阶楼顶。

    ```python
    # Complete Knapsack 完全背包
    def climbStairs(self, n: int) -> int:
      dp = [0] * (n + 1)
      dp[0] = 1
    
      for i in range(n):
        for j in range(m):
          if i - j >= 0:
            dp[i] += dp[i - j]
      return dp[n]
    ```
    
    --> convert to 2-D dynamic programming (similar to 62. [Unique Paths](https://leetcode.com/problems/unique-paths/description/))

## 3. 746 [Min Cost Climbing Stairs](https://leetcode.com/problems/min-cost-climbing-stairs/description/)

|  Category  |  Difficulty   |                  Tags                   |
| :--------: | :-----------: | :-------------------------------------: |
| algorithms | Easy (59.05%) | [`trie`](https://leetcode.com/tag/trie) |

You are given an integer array `cost` where `cost[i]` is the cost of `ith` step on a staircase. Once you pay the cost, you can either climb one or two steps.

You can either start from the step with index `0`, or the step with index `1`.

Return *the minimum cost to reach the top of the floor*.

**Example 1:**

```
Input: cost = [10,15,20]
Output: 15
Explanation: You will start at index 1.
- Pay 15 and climb two steps to reach the top.
The total cost is 15.
```

**Example 2:**

```
Input: cost = [1,100,1,1,1,100,1,1,100,1]
Output: 6
Explanation: You will start at index 0.
- Pay 1 and climb two steps to reach index 2.
- Pay 1 and climb two steps to reach index 4.
- Pay 1 and climb two steps to reach index 6.
- Pay 1 and climb one step to reach index 7.
- Pay 1 and climb two steps to reach index 9.
- Pay 1 and climb one step to reach the top.
The total cost is 6. 
```

- **Constraints:**

  - `2 <= cost.length <= 1000`

  - `0 <= cost[i] <= 999`

- **Thoughts**

  根据动态规划的五个步骤：procedure: definition; function; initialization; traversal order; example

  - definition: dp[i]: the minimum cost to climb to the top starting from the ith staircase. --> 表示将正整数 i 拆分成至少两个正整数的和之后，这些正整数的最大乘积
  - function: `dp[i] = cost[i] + min(dp[i-1], dp[i-2])`
  - initialization: dp[0] = cost[0], dp[1] = cost[1]; dp[n] = 0
  - example: 得出return smaller number of dp[-1], dp[-2]

- **Solution**

  ```python
  def minCostClimbingStairs(self, cost: List[int]) -> int:
    n = len(cost)
    dp = [float('inf')] * n
  
    dp[0] = cost[0]
    dp[1] = cost[1]
    for i in range(2, n):
      dp[i] = cost[i] + min(dp[i-1], dp[i-2])  
    return min(dp[-1], dp[-2])
  ```

  - Time complexity: O(N)

    Space complexity: O(N)

## 4. 343 [Integer Break](https://leetcode.com/problems/integer-break/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (54.27%) | [`math`](https://leetcode.com/tag/math); [`dynamic-programming`](https://leetcode.com/tag/dynamic-programming) |

Given an integer `n`, break it into the sum of `k` **positive integers**, where `k >= 2`, and maximize the product of those integers.

Return *the maximum product you can get*.

**Example 1:**

```
Input: n = 2
Output: 1
Explanation: 2 = 1 + 1, 1 × 1 = 1.
```

**Example 2:**

```
Input: n = 10
Output: 36
Explanation: 10 = 3 + 3 + 4, 3 × 3 × 4 = 36.
```

- **Constraints:**

  - `2 <= n <= 58`

- **Thoughts**

  - 根据动态规划的五个步骤：definition; function; initialization; traversal order; example

    - definition: dp[i]: the maximum product when integer is n
    - function: valid val: [1, i]; `dp[i] = max(dp[i], val * dp[i - val], val * (i - val))`
    - initialization: dp[2] = 1

  - <font color=blue>**关键点：确定状态转移过程 --> 整数拆分有两种方案：拆成两个整数相加；拆成多个整数相加**</font>

    <img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220731190456346.png" alt="image-20220731190456346" style="zoom:50%;" />

- **Solution**

  ```python
  def integerBreak(self, n: int) -> int:
    dp = [0] * (n + 1)
    dp[2] = 1
  
    for i in range(3, n + 1):
      for val in range(1, i - 1):
        dp[i] = max(dp[i], val * dp[i - val], val * (i - val))
    return dp[n]
  ```

  - Time complexity: $O(N^2)$

    Space complexity: O(N)

## 4. 96 [Unique Binary Search Trees](https://leetcode.com/problems/unique-binary-search-trees/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (58.43%) | [`dynamic-programming`](https://leetcode.com/tag/dynamic-programming); [`tree`](https://leetcode.com/tag/tree) |

Given an integer `n`, return *the number of structurally unique **BST'**s (binary search trees) which has exactly* `n` *nodes of unique values from* `1` *to* `n`.

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2021/01/18/uniquebstn3.jpg" alt="img" style="zoom:50%;" />

```
Input: n = 3
Output: 5
```

**Example 2:**

```
Input: n = 1
Output: 1
```

- **Constraints:**

  - `1 <= n <= 19`

- **Thoughts**

  - 根据题目举例。比如给算法输入`n = 5`，也就是说用`{1,2,3,4,5}`这些数字去构造 BST。首先，这棵 BST 的根节点总共有几种情况？显然有 5 种情况对吧，因为每个数字都可以作为根节点。比如说我们固定`3`作为根节点，这个前提下能有几种不同的 BST 呢？根据 BST 的特性，根节点的左子树都比根节点的值小，右子树的值都比根节点的值大。所以如果固定`3`作为根节点，左子树节点就是`{1,2}`的组合，右子树就是`{4,5}`的组合。

    --> **左子树的组合数和右子树的组合数乘积**就是`3`作为根节点时的 BST 个数。

    <img src="https://img-blog.csdnimg.cn/20210107093226241.png" alt="96.不同的二叉搜索树2" style="zoom: 33%;" />

    --> 对于[1, n]的每一个值，将每一个值都定为根节点，再将每一个根节点能产生的BST数量相加，最后即为答案

    <font color=blue>**[关键点]需要举例，画图，分析，才能找到递推的关系，由此想到以动态规划的方法解决**</font>

  - 根据动态规划的五个步骤：definition; function; initialization; traversal order; example
    - definition: dp[i]: the number of BST with nodes [1, i]
    - function: dp[i] += dp[j - 1] * dp[i - j]; return dp[n] 
    - initialization: dp[0] = 1 --> 从定义上来讲，空节点也是一棵二叉树，也是一棵二叉搜索树，这是可以说得通的。从递归公式上来讲，`dp[以j为头结点左子树节点数量] * dp[以j为头结点右子树节点数量] `中以j为头结点左子树节点数量为0，也需要`dp[以j为头结点左子树节点数量] = 1`， 否则乘法的结果就都变成0了。所以初始化dp[0] = 1

- **Solution**

  ```python
  def numTrees(self, n: int) -> int:
    if n == 1 or n == 2:
      return n
  
    dp = [0] * (n + 1)
    dp[0] = 1
    for i in range(1, n + 1):
      for j in range(1, i+ 1):
        dp[i] += dp[j - 1] * dp[i - j]
    return dp[n]
  ```

  - Time complexity: $O(N^2)$

    Space complexity: O(N)

## 5. 139 [Word Break](https://leetcode.com/problems/word-break/description/)

|  Category  |   Difficulty    |        Tags         |
| :--------: | :-------------: | :-----------------: |
| algorithms | Medium (44.66%) | dynamic-programming |

Given a string `s` and a dictionary of strings `wordDict`, return `true` if `s` can be segmented into a space-separated sequence of one or more dictionary words.

**Note** that the same word in the dictionary may be reused multiple times in the segmentation. 

**Example 1:**

```
Input: s = "leetcode", wordDict = ["leet","code"]
Output: true
Explanation: Return true because "leetcode" can be segmented as "leet code".
```

**Example 2:**

```
Input: s = "applepenapple", wordDict = ["apple","pen"]
Output: true
Explanation: Return true because "applepenapple" can be segmented as "apple pen apple".
Note that you are allowed to reuse a dictionary word.
```

**Example 3:**

```
Input: s = "catsandog", wordDict = ["cats","dog","sand","and","cat"]
Output: false
```

- **Constraints:**

  - `1 <= s.length <= 300`

  - `1 <= wordDict.length <= 1000`

  - `1 <= wordDict[i].length <= 20`

  - `s` and `wordDict[i]` consist of only lowercase English letters.

  - All the strings of `wordDict` are **unique**.

- **Thoughts**

  - 这道题目和<u>Backtracking.md</u>里的131.[Palindrome Partitioning](https://leetcode.com/problems/palindrome-partitioning/description/)很类似，就是枚举字符串所有的分割情况。139是枚举分割后的所有子串，判断是否回文；本道是枚举分割所有字符串，判断是否在字典里出现过。然而使用枚举分割进行回溯，时间复杂度为$O(2^n)$ --> 每一个单词都有两个状态（切割和不切割）--> 使用动态规划！

  - 单词就是物品，字符串s就是背包，单词能否组成字符串s，就是问物品能不能把背包装满。拆分时可以重复使用字典中的单词，说明就是一个完全背包！

    s 串能否分解为单词表的单词（前 s.length 个字符的 s 串能否分解为单词表单词）--> 将大问题分解为规模小一点的子问题：

    - 前 i 个字符的子串，能否分解成单词
    - 剩余子串，是否为单个单词。

  - 遵循动态规划5个步骤：

    - Definition：`dp[i]`：长度为`i`的`s[0:i-1]`子串是否能拆分成单词。题目求:`dp[s.length]`

      ![image.png](https://pic.leetcode-cn.com/70b0957d0086f43cd56b9e311e03deed4e9a77be0ae40ccbaa2f2b006d7caeb5-image.png)

    - Equation: 我们用指针 j 去划分s[0:i] 子串，s[0:i] 子串对应 dp[i+1] ，它是否为 true（s[0:i]能否 break），取决于两点：

      - 它的<u>前缀子串</u> s[0:j-1] 的 dp[j]，是否为 true。
      - 剩余子串 s[j:i]，是否是单词表的单词

      --> if([j, i] 这个区间的子串出现在字典里 && dp[j]是true) 那么 dp[i] = true

      <img src="https://pic.leetcode-cn.com/bcef185f09c72fb525855bd56155f4658793d86b0dc4f3de31cace6bd9398c5b-image.png" alt="image.png" style="zoom: 33%;" />

    - Initialization: 最开始dp数列的值均为`False`; base case 为`dp[0] = true`。即，长度为 0 的`s[0:-1]`**能**拆分成单词表单词。这看似荒谬，但这**只是为了**让边界情况也能套用状态转移方程，而已。

- **Solution**

  ```python
  def wordBreak(self, s: str, wordDict: List[str]) -> bool:
          dp = [False] * (len(s) + 1)
          dp[0] = True
  
          for i in range(1, len(s) + 1):
              for j in range(i):
                  # Further Optimization
                  # 如果发现dp[i] == true ，直接break
                	if dp[i] == True:
                    break
                  # 如果dp[j] == false，dp[i]没有为 true 的可能，continue，考察下一个 j
                  if dp[j] == False:
                    continue
                    
                  word = s[j: i]# s[j:i]
                  if dp[j] and word in wordDict:
                      dp[i] = True
                      break     # dp[i] = true了，i长度的子串已经可以拆成单词了，不需要j继续划分子串了
  
          return dp[len(s)]
  ```

  - Time complexity: $O(N^2)$

    Space complexity: O(N)

## 6. 198 [House Robber](https://leetcode.com/problems/house-robber/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (47.38%) | [`dynamic-programming`](https://leetcode.com/tag/dynamic-programming) |

You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed, the only constraint stopping you from robbing each of them is that adjacent houses have security systems connected and **it will automatically contact the police if two adjacent houses were broken into on the same night**.

Given an integer array `nums` representing the amount of money of each house, return *the maximum amount of money you can rob tonight **without alerting the police***.

**Example 1:**

```
Input: nums = [1,2,3,1]
Output: 4
Explanation: Rob house 1 (money = 1) and then rob house 3 (money = 3).
Total amount you can rob = 1 + 3 = 4.
```

**Example 2:**

```
Input: nums = [2,7,9,3,1]
Output: 12
Explanation: Rob house 1 (money = 2), rob house 3 (money = 9) and rob house 5 (money = 1).
Total amount you can rob = 2 + 9 + 1 = 12 
```

- **Constraints:**

  - `1 <= nums.length <= 100`

  - `0 <= nums[i] <= 400`

- **Thoughts**

  - 这道题目是动态规划的经典题目，与746 [Min Cost Climbing Stairs](https://leetcode.com/problems/min-cost-climbing-stairs/description/)基本一致，只不过这道题要求不能抢劫相邻房屋 --> 一次迈两步

  - 遵循动态规划五个步骤：

    - definition: dp[i]: maximum amount of money at previous i houses (including not robbed houses) **考虑下标i（包括i）以内的房屋，最多可以偷窃的金额为dp[i]**; return dp[n - 1]

    - equation: `dp[i] = max(dp[i - 1], dp[i - 2] + nums[i])`

      - 如果偷第i房间，那么`dp[i] = dp[i - 2] + nums[i] `，即：第i-1房一定是不考虑的，找出 下标i-2（包括i-2）以内的房屋，最多可以偷窃的金额为dp[i-2] 加上第i房间偷到的钱。

      - 如果不偷第i房间，那么`dp[i] = dp[i - 1]`，即考虑i-1房，（**注意这里是考虑，并不是一定要偷i-1房，这是很多同学容易混淆的点**）

    - initialization: dp[i] = 0; dp[0] = nums[0]; dp[1] = max(nums[0], nums[1])

    - traversal order: in order --> dp[i] 是根据dp[i - 2] 和 dp[i - 1] 推导出来的，那么一定是从前到后遍历！

  - <font color=blue>**注意edge cases!**</font>

- **Solution**

  ```python
  def rob(self, nums: List[int]) -> int:
    n = len(nums)
    # edge cases
    if n == 1:
      return nums[0]
    
    dp = [0] * n
    dp[0] = nums[0]
    dp[1] = max(nums[0], nums[1])
    for i in range(2, n):
      dp[i] = max(dp[i - 1], dp[i - 2] + nums[i])
    return dp[n - 1]
  
  ## Simple Version
  def rob(self, nums: List[int]) -> int:
    if len(nums) == 1:
      return nums[0]
    
    rob1, rob2 = 0, 0
    for n in nums:
      newRob = max(rob1 + n, rob2)
      rob1 = rob2
      rob2 = newRob
    return rob2
  ```

  - Time complexity: $O(N)$

    Space complexity: O(n)

  - Further Optimization： 使用滚动数组将空间复杂度进行进一步降维

    ```python
    # Optimization: 滚动数组
    dp0 = nums[0]
    dp1 = max(nums[0], nums[1])
    
    for i in range(2, n):
      temp = dp1
      dp1 = max(temp, dp0 + nums[i])
      dp0 = temp
    return dp1
    ```

    - Time complexity: $O(N)$

      Space complexity: O(1)

## 7. 213 [House Robber II](https://leetcode.com/problems/house-robber-ii/description/)

|  Category  |   Difficulty    |        Tags         |
| :--------: | :-------------: | :-----------------: |
| algorithms | Medium (39.98%) | dynamic-programming |

You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed. All houses at this place are **arranged in a circle.** That means the first house is the neighbor of the last one. Meanwhile, adjacent houses have a security system connected, and **it will automatically contact the police if two adjacent houses were broken into on the same night**.

Given an integer array `nums` representing the amount of money of each house, return *the maximum amount of money you can rob tonight **without alerting the police***. 

**Example 1:**

```
Input: nums = [2,3,2]
Output: 3
Explanation: You cannot rob house 1 (money = 2) and then rob house 3 (money = 2), because they are adjacent houses.
```

**Example 2:**

```
Input: nums = [1,2,3,1]
Output: 4
Explanation: Rob house 1 (money = 1) and then rob house 3 (money = 3).
Total amount you can rob = 1 + 3 = 4.
```

**Example 3:**

```
Input: nums = [1,2,3]
Output: 3
```

- **Constraints:**

  - `1 <= nums.length <= 100`

  - `0 <= nums[i] <= 1000`

- **Thoughts**

  - 这道题目和第一道描述基本一样，强盗依然不能抢劫相邻的房子，输入依然是一个数组，但是告诉你这些房子不是一排，而是围成了一个圈。也就是说，现在**第一间房子和最后一间房子也相当于是相邻的，不能同时抢**。

  - 首先，首尾房间不能同时被抢，那么只可能有三种不同情况：要么都不被抢；要么第一间房子被抢最后一间不抢；要么最后一间房子被抢第一间不抢。这三种情况，哪种的结果最大，就是最终答案！不过，其实我们不需要比较三种情况，**只要比较情况二和情况三就行了，**因为这两种情况对于房子的选择余地比情况一大呀，房子里的钱数都是非负数，所以选择余地大，最优决策结果肯定不会小。

    <img src="https://mmbiz.qpic.cn/sz_mmbiz_jpg/gibkIz0MVqdG9kDIzE6qfsOcugRP3xn8nlATHI4e9ib8SUiar0s2OR8zQdvficwknUKDwfcKWV0sc3WwL1lC0Cw5GQ/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1" alt="Image" style="zoom:50%;" />

    --> 可以将198. House Robber的解法写成一个辅助函数，对情况二和情况三分别使用辅助函数，返回最大值

- **Solution**

  ```python
  def rob(self, nums: List[int]) -> int:
    if len(nums) == 1:
      return nums[0]
    
    def helper(houses):
      rob1, rob2 = 0, 0
      for h in houses:
        newRob = max(rob1 + h, rob2)
        rob1 = rob2
        rob2 = newRob
      return rob2
    
    return max(helper(nums[1:]), helper(nums[:-1]))
  ```

  - Time complexity: $O(N)$

    Space complexity: O(1)

## 子序列/子数组类型问题

## 1. 300 [Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (49.55%) | [`binary-search`](https://leetcode.com/tag/binary-search); [`dynamic-programming`](https://leetcode.com/tag/dynamic-programming) |

Given an integer array `nums`, return the length of the longest strictly increasing subsequence.

A **subsequence** is a sequence that can be derived from an array by deleting some or no elements without changing the order of the remaining elements. For example, `[3,6,2,7]` is a subsequence of the array `[0,3,1,6,2,2,7]`. 

**Example 1:**

```
Input: nums = [10,9,2,5,3,7,101,18]
Output: 4
Explanation: The longest increasing subsequence is [2,3,7,101], therefore the length is 4.
```

**Example 2:**

```
Input: nums = [0,1,0,3,2,3]
Output: 4
```

**Example 3:**

```
Input: nums = [7,7,7,7,7,7,7]
Output: 1
```

**Constraints:**

- `1 <= nums.length <= 2500`
- `-104 <= nums[i] <= 104`

**Follow up:** Can you come up with an algorithm that runs in `O(n log(n))` time complexity?

- **Thoughts**

  - <u>Decision Tree:</u>

    ```
    Input  : arr[] = {3, 10, 2, 11}
    f(i): Denotes LIS of subarray ending at index 'i'
    
    (LIS(1)=1)
    
          f(4)  {f(4) = 1 + max(f(1), f(2), f(3))}
      /    |    \
    f(1)  f(2)  f(3) {f(3) = 1, f(2) and f(1) are > f(3)}
           |      |  \
          f(1)  f(2)  f(1) {f(2) = 1 + max(f(1)}
                  |
                f(1) {f(1) = 1}
    ```

  - 最长上升子序列是动规的经典题目，这里dp[i]是可以根据dp[j] （j < i）推导出来的，那么依然用动规五部曲来分析：

    - definition: dp[i]: the length of the longest strictly increasing subsequence **end at nums[i]** <font color=blue>(**i之前包括i的以nums[i]结尾最长上升子序列的长度**) </font>

    - equation: 

      位置i的最长升序子序列等于j从0到i-1各个位置的最长升序子序列 + 1 的最大值。

      `for j < i`, `if nums[i] > nums[j], dp[i] = max(dp[i], dp[j] + 1)`

      ![img](https://labuladong.github.io/algo/images/%e6%9c%80%e9%95%bf%e9%80%92%e5%a2%9e%e5%ad%90%e5%ba%8f%e5%88%97/gif1.gif)

      <font color=red>根据 `dp` 数组的定义，运用**数学归纳法**的思想，假设 `dp[0...i-1]` 都已知，想办法求出 `dp[i]`</font>

    - initialization: dp[i] = 1 --> 因为以 nums[i] 结尾的最长递增子序列起码要包含它自己

    - traversal order: in order

    - return value: `return max(dp)` --> 最终结果（子序列的最大长度）应该是 **dp 数组中的最大值**

  - <font color=blue>**变种题目：674. Longest Continuous Increasing Subsequence**</font>

    Given an unsorted array of integers `nums`, return *the length of the longest **continuous increasing subsequence** (i.e. subarray)*. The subsequence must be **strictly** increasing.

    A **continuous increasing subsequence** is defined by two indices `l` and `r` (`l < r`) such that it is `[nums[l], nums[l + 1], ..., nums[r - 1], nums[r]]` and for each `l <= i < r`, `nums[i] < nums[i + 1]`.

    - 674和这道题最大的区别在于“连续”。本题要求的是最长**连续**递增序列 --> 修改一下递推公式即可：`if nums[i] > nums[i -  1], dp[i] = dp[i - 1] + 1`

    - Time complexity: O(N)
    - Space complexity: O(N)

- **Solution**

  ```python
  def lengthOfLIS(self, nums: List[int]) -> int:
    n = len(nums)
    if n == 1:
      return 1
    dp = [1] * n
    
    for i in range(n):
      for j in range(i):
        if nums[i] > nums[j]:					# 如果要求非严格递增，将此行 '<' 改为 '<=' 即可。
          dp[i] = max(dp[i], dp[j] + 1)
    return max(dp)
  ```

  - Time complexity: $O(N^2)$

    Space complexity: O(N)

- **Follow-up: Dynamic Programming + Binary Search**

  <font color=red>**空缺！**</font>

## 2. 354 [Russian Doll Envelopes](https://leetcode.com/problems/russian-doll-envelopes/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Hard (38.92%) | [`binary-search`](https://leetcode.com/tag/binary-search); [`dynamic-programming`](https://leetcode.com/tag/dynamic-programming) |

You are given a 2D array of integers `envelopes` where `envelopes[i] = [wi, hi]` represents the width and the height of an envelope.

One envelope can fit into another if and only if both the width and height of one envelope are greater than the other envelope's width and height.

Return *the maximum number of envelopes you can Russian doll (i.e., put one inside the other)*.

**Note:** You cannot rotate an envelope. 

**Example 1:**

```
Input: envelopes = [[5,4],[6,4],[6,7],[2,3]]
Output: 3
Explanation: The maximum number of envelopes you can Russian doll is 3 ([2,3] => [5,4] => [6,7]).
```

**Example 2:**

```
Input: envelopes = [[1,1],[1,1],[1,1]]
Output: 1
```

- **Constraints:**

  - `1 <= envelopes.length <= 105`

  - `envelopes[i].length == 2`

  - `1 <= wi, hi <= 105`

- **Solution**

  **这道题目其实是300. 最长递增子序列的一个变种，因为每次合法的嵌套是大的套小的，相当于在二维平面中找一个最长递增的子序列，其长度就是最多能嵌套的信封个数**。

  - <u>Method 1: Dynamic Programming (Brute-force)</u>

    ```python
    def maxEnvelopes(self, envelopes: List[List[int]]) -> int:
            n = len(envelopes)
            if n == 1:
                return 1
            
            # envelopes.sort()
            envelopes.sort(key=lambda x:(x[0], -x[1]))
            dp = [1] * n
            for i in range(n):
                for j in range(i):
                    # if envelopes[i][0] > envelopes[j][0] and envelopes[i][1] > envelopes[j][1]:
                    if envelopes[i][1] > envelopes[j][1]:
                        dp[i] = max(dp[i], dp[j] + 1)
            return max(dp)
    ```

    - Time complexity: $O(N^2)$

      Space complexity: O(N)

## 3. 53 [Maximum Subarray](https://leetcode.com/problems/maximum-subarray/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Easy (49.53%) | [`array`](https://leetcode.com/tag/array); [`divide-and-conquer`](https://leetcode.com/tag/divide-and-conquer); [`dynamic-programming`](https://leetcode.com/tag/dynamic-programming) |

Given an integer array `nums`, find the contiguous subarray (containing at least one number) which has the largest sum and return *its sum*.

A **subarray** is a **contiguous** part of an array. 

**Example 1:**

```
Input: nums = [-2,1,-3,4,-1,2,1,-5,4]
Output: 6
Explanation: [4,-1,2,1] has the largest sum = 6.
```

**Example 2:**

```
Input: nums = [1]
Output: 1
```

**Example 3:**

```
Input: nums = [5,4,-1,7,8]
Output: 23
```

**Constraints:**

- `1 <= nums.length <= 105`
- `-104 <= nums[i] <= 104`

**Follow up:** If you have figured out the `O(n)` solution, try coding another solution using the **divide and conquer** approach, which is more subtle.

- **Solution**

  - <u>Method 1: Dynamic Programming</u>

    1. definition: dp[i]: 以 nums[i] 为结尾的「最大子数组和」为 dp[i]
    2. function: `dp[i] = max(nums[i], nums[i] + dp[i - 1]) ` （最大值可能为这个数字本身，也可能是和之前的最大子数组和相加）
    3. initialization: `dp[0] = nums[0]`
    4. traversal order: in order
    5. return value: `res = max(dp)`

    ```python
    def maxSubArray(self, nums: List[int]) -> int:
    # 1. Original version
            n = len(nums)
            dp = [0] * n
            dp[0] = nums[0]
    
            for i in range(1, n):
                # dp[i] 有两种「选择」，要么与前面的相邻子数组连接，形成一个和更大的子数组；要么不与前面的子数组连接，自成一派，自己作为一个子数组
                dp[i] = max(nums[i], nums[i] + dp[i - 1])
            return max(dp)
    # Time complexity: O(N)
    # Space complexity: O(N)
            
    # 2. Optimization: as dp[i] only relates to dp[i - 1] --> 滚动数组
            n = len(nums)
            dp0, dp1 = nums[0], 0
            res = dp0
            for i in range(1, n):
                dp1 = max(nums[i], nums[i] + dp0)
                dp0 = dp1
                res = max(res, dp1)
            return res
    # Time complexity: O(N)
    # Space complexity: O(1)
    ```

  - <u>Method 2: Greedy / Sliding Window</u>

    ```python
    def maxSubArray(self, nums: List[int]) -> int:
      			## 1. Original Version
            # for each element two situations: add the element/start a new subarray
            res = nums[0]
            preSum = 0
    
            for n in nums:
                # Situation 1: start a new subarray
                if preSum < 0:
                    preSum = 0    # start a new subarray
                
                # Situation 2: add the current element and compare with previous res
                preSum += n
                res = max(res, preSum)
            return res
          
            ## 2. Greedy --> 直接修改原数组，令nums[i]为包含nums[i]结尾的最大数组和，所以在遍历的过程中
             for i in range(1, len(nums)):
                if nums[i - 1] >= 0:
                    nums[i] += nums[i - 1]
            return max(nums)
    ```

    - Time complexity: $O(N)$

      Space complexity: O(1)

## 子数组问题





