# Greedy - 贪心算法

<img src="https://code-thinking-1253855093.file.myqcloud.com/pics/20210917104315.png" alt="贪心算法大纲" style="zoom: 50%;" />

- **定义**

  **贪心的本质是选择每一阶段的局部最优，从而达到全局最优**。<font color=blue>**每一步做出的选择都是<u>当前</u>看起来最好的选择，只是局部最优解而非全局最优解**</font> --> 贪心算法可以理解为一种<font color=blue>**特殊的动态规划问题，拥有贪心选择性质**</font>，可以进一步降低动态规划算法的时间复杂度。

  例如，有一堆钞票，你可以拿走十张，如果想达到最大的金额，你要怎么拿？

  指定每次拿最大的，最终结果就是拿走最大数额的钱。每次拿最大的就是局部最优，最后拿走最大数额的钱就是推出全局最优。

- **贪心套路（什么用贪心）**

  刷题或者面试的时候，手动模拟一下感觉可以局部最优推出整体最优，而且想不到反例，那么就试一试贪心。例如刚刚举的拿钞票的例子，就是模拟一下每次拿做大的，最后就能拿到最多的钱，这还要数学证明的话，其实就不在算法面试的范围内了，可以看看专业的数学书籍！所以这也是为什么很多同学通过（accept）了贪心的题目，但都不知道自己用了贪心算法，**因为贪心有时候就是常识性的推导，所以会认为本应该就这么做！**--> **贪心没有套路，说白了就是常识性推导加上举反例**

- **贪心解题四步骤**

  <img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220823112108583.png" alt="image-20220823112108583" style="zoom: 25%;" />

  1. 将问题分解为若干个子问题
  2. 找出适合的贪心策略 
  3. 求解每一个子问题的最优解
  4. 将局部最优解堆叠成全局最优解

## 1. 455 [Assign Cookies](https://leetcode.com/problems/assign-cookies/description/)

|  Category  |  Difficulty   |                    Tags                     |
| :--------: | :-----------: | :-----------------------------------------: |
| algorithms | Easy (50.74%) | [`greedy`](https://leetcode.com/tag/greedy) |

Assume you are an awesome parent and want to give your children some cookies. But, you should give each child at most one cookie.

Each child `i` has a greed factor `g[i]`, which is the minimum size of a cookie that the child will be content with; and each cookie `j` has a size `s[j]`. If `s[j] >= g[i]`, we can assign the cookie `j` to the child `i`, and the child `i` will be content. Your goal is to maximize the number of your content children and output the maximum number.

**Example 1:**

```
Input: g = [1,2,3], s = [1,1]
Output: 1
Explanation: You have 3 children and 2 cookies. The greed factors of 3 children are 1, 2, 3. 
And even though you have 2 cookies, since their size is both 1, you could only make the child whose greed factor is 1 content.
You need to output 1.
```

**Example 2:**

```
Input: g = [1,2], s = [1,2,3]
Output: 2
Explanation: You have 2 children and 3 cookies. The greed factors of 2 children are 1, 2. 
You have 3 cookies and their sizes are big enough to gratify all of the children, 
You need to output 2. 
```

- **Constraints:**

  - `1 <= g.length <= 3 * 104`

  - `0 <= s.length <= 3 * 104`

  - `1 <= g[i], s[j] <= 231 - 1`

- **Thoughts**

  - 为了满足更多的小孩，就不要造成饼干尺寸的浪费。大尺寸的饼干既可以满足胃口大的孩子也可以满足胃口小的孩子，那么就应该优先满足胃口大的。**这里的局部最优就是大饼干喂给胃口大的，充分利用饼干尺寸喂饱一个，全局最优就是喂饱尽可能多的小孩**。可以尝试使用贪心策略，先将饼干数组和小孩数组排序。

  - 一共有两种考虑思路：

    1. 优先考虑胃口g，先喂饱大胃口 --> traverse through g first: 从后向前遍历小孩数组，用大饼干优先满足胃口大的，并统计满足小孩数量。

    <img src="https://pic.leetcode-cn.com/1654417025-INpLdL-BF963F4D-2A3E-401A-A112-274E18C76A86_1_201_a.jpeg" alt="BF963F4D-2A3E-401A-A112-274E18C76A86_1_201_a.jpeg" style="zoom: 33%;" />

    2. 优先考虑饼干s🍪，用小饼干先喂饱小胃口 --> traverse through s first

- **Solution**

  ```python
  def findContentChildren(self, g: List[int], s: List[int]) -> int:
    ## Method: greedy + two-pointers 
    # 1. 优先考虑胃口g, 先喂饱大胃口
    if not s:
      return 0
    
    g.sort()
    s.sort()
    res = 0
    for i in range(len(g) - 1, -1, 0):
        if j >= 0 and s[j] >= g[i]:
          res += 1
          j -= 1
    return res
  
    # 2. 优先考虑饼干s，小饼干先喂给小胃口
    if not s:
      return 0
    
    g.sort()
    s.sort()
    res = 0
    for i in range(len(s)):
      if res < len(g) and s[i] >= g[res]:
        res += 1
    return res
  ```

  - Time complexity: $O(nlogn + mlogm + m)$
  
    Space complexity: $O(logm + logn)$


## 2. [Maximum Subarray](https://leetcode.com/problems/maximum-subarray/description/)

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

## 3. 55 [Jump Game](https://leetcode.com/problems/jump-game/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (37.90%) | [`array`](https://leetcode.com/tag/array), [`greedy`](https://leetcode.com/tag/greedy) |

You are given an integer array `nums`. You are initially positioned at the array's **first index**, and each element in the array represents your maximum jump length at that position.

Return `true` *if you can reach the last index, or* `false` *otherwise*.

**Example 1:**

```
Input: nums = [2,3,1,1,4]
Output: true
Explanation: Jump 1 step from index 0 to 1, then 3 steps to the last index.
```

**Example 2:**

```
Input: nums = [3,2,1,0,4]
Output: false
Explanation: You will always arrive at index 3 no matter what. Its maximum jump length is 0, which makes it impossible to reach the last index.
```

- **Constraints:**

  - `1 <= nums.length <= 104`

  - `0 <= nums[i] <= 105`

- **Solution**

  解题思路：尽可能到达最远位置（贪心）。如果能到达某个位置，那一定能到达它前面的所有位置。

  - <u>Approach 1: </u>

    如何确保能一个位置，取决于前一个位置加上step能否到达该位置 --> 可以从最后开始往前移动goal，如果最后goal能移到最初点，则代表能reach

    ![image-20221230021742706](/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20221230021742706.png)

    ```python
    def canJump(self, nums: List[int]) -> bool:
      goal = len(nums) - 1
      
      for i in range(goal - 1, -1, -1):    # [0, goal - 1]
        if i + nums[i] >= goal:
          goal = i
      
      return goal == 0   # or: return True if goal == 0 else None
    ```

    - Time complexity: O(N)

      Space complexity: O(1)

  - <u>Approach 2: </u>

    刚看到本题一开始可能想：当前位置元素如果是3，我究竟是跳一步呢，还是两步呢，还是三步呢，究竟跳几步才是最优呢？

    其实跳几步无所谓，关键在于可跳的覆盖范围！不一定非要明确一次究竟跳几步，每次取最大的跳跃步数，这个就是可以跳跃的覆盖范围。这个范围内，别管是怎么跳的，反正一定可以跳过来。

    **那么这个问题就转化为<u>跳跃覆盖范围究竟可不可以覆盖到终点</u>！** 每次移动取最大跳跃步数（得到最大的覆盖范围），每移动一个单位，就更新最大覆盖范围。

    **贪心算法局部最优解：每次取最大跳跃步数（取最大覆盖范围），整体最优解：最后得到整体最大覆盖范围，看是否能到终点**。

    局部最优推出全局最优，找不出反例，试试贪心！

    <img src="https://img-blog.csdnimg.cn/20201124154758229.png" alt="55.跳跃游戏" style="zoom:50%;" />

    i每次移动只能在cover的范围内移动，每移动一个元素，cover得到该元素数值（新的覆盖范围）的补充，让i继续移动下去。而cover每次只取 max(该元素数值补充后的范围, cover本身范围)。如果cover大于等于了终点下标，直接return true就可以了。

    <font color=red>这道题目关键点在于：不用拘泥于每次究竟跳几步，而是看覆盖范围，覆盖范围内一定是可以跳过来的，不用管是怎么跳的。</font>

    ```python
    def canJump(self, nums: List[int]) -> bool:
      n = len(nums)
      rightmost = 0
      for i in range(n):
        if i <= rightmost:
          rightmost = max(rightmost, i + nums[i])
        if rightmost >= n - 1:
          return True
      return False
    ```

    - Time complexity: O(N)

      Space complexity: O(1)

## 4. 45 [Jump Game II](https://leetcode.com/problems/jump-game-ii/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (37.33%) | [`array`](https://leetcode.com/tag/array), [`greedy`](https://leetcode.com/tag/greedy) |

You are given a **0-indexed** array of integers `nums` of length `n`. You are initially positioned at `nums[0]`.

Each element `nums[i]` represents the maximum length of a forward jump from index `i`. In other words, if you are at `nums[i]`, you can jump to any `nums[i + j]` where:

- `0 <= j <= nums[i]` and
- `i + j < n`

Return *the minimum number of jumps to reach* `nums[n - 1]`. The test cases are generated such that you can reach `nums[n - 1]`. 

**Example 1:**

```
Input: nums = [2,3,1,1,4]
Output: 2
Explanation: The minimum number of jumps to reach the last index is 2. Jump 1 step from index 0 to 1, then 3 steps to the last index.
```

**Example 2:**

```
Input: nums = [2,3,0,1,4]
Output: 2
```

- **Constraints:**

  - `1 <= nums.length <= 104`

  - `0 <= nums[i] <= 1000`

- **Thoughts**

  - 这道题首先看到是求最值问题 --> 考虑使用动态规划，根据动态规划步骤：
    1. Definition: dp[i]: the minimum number of jumps to index i --> return `dp[-1]`
    2. Formula: 每一个dp[i]的值都由前面所有选择组成，所以可以额外生成一个`subproblems`数组 --> `dp[i] = min(subproblems)`，而每一个选择的结果为:`subproblems.append(dp[j] + 1) `
    3. Initialization: `dp = [0] * len(nums)`
    4. Traversal order: in-order traversal
  - 然而动态规划的解法超出时间限制，所以考虑使用优化的动态规划：贪心算法（**贪心算法比动态规划多了一个性质：贪心选择性质**）如果我们「贪心」地进行正向查找，每次找到可到达的最远位置，就可以在线性时间内得到最少的跳跃次数。

- **Solution**

  - <u>Method 1: Dynamic Programming (Time Limit Exceeded)</u>

    ```python
    def jump(self, nums: List[int]) -> int:
      n = len(nums)
      dp = [0] * n
      
      for i in range(1, n):
        subproblems = []
        for j in range(i):			# 穷举每一个选择并计算每一个选择的结果
          if i - j > nums[j]:   # cannot jump from j to i
            continue
          # can jump from j to i --> add 1 step to dp[j]
          subproblems.append(dp[j] + 1)
      	dp[i] = min(subproblems)
      
      return dp[-1]   
    ```

    - Time complexity: $O(N^2)$ --> two for-loops where i and j both traverse through the whole array

      Space complexity: O(N) --> create `dp` matrix

  - <u>Method 2: Greedy (Implicit BFS)</u>

    动态规划的时间复杂度高的根本原因在于：for 循环中会陷入递归计算子问题，**运用贪心选择性质，我们不需要「递归地」计算出所有选择的具体结果然后比较求最值，而只需要做出那个最有「潜力」，看起来最优的选择即可**

    例如，对于数组 [2,3,1,2,4,2,3]，初始位置是下标 0，从下标 0 出发，最远可到达下标 2。下标 0 可到达的位置中，下标 1 的值是 3，从下标 1 出发可以达到更远的位置，因此第一步到达下标 1。从下标 1 出发，最远可到达下标 4。下标 1 可到达的位置中，下标 4 的值是 4 ，从下标 4 出发可以达到更远的位置，因此第二步到达下标 4。

    --> 使用两个指针`left`和`right`分别指代每一次跳跃后的左边界和右边界以显示range

    <img src="https://assets.leetcode-cn.com/solution-static/45/45_fig1.png" alt="fig1" style="zoom:50%;" />

    <font color=blue>这道题的贪心算法解法也运用了BFS思想 --> 每一层即为跳同样的步骤的结果</font>

    <img src="/Users/xinyuzhang/Downloads/IMG_85C0E187F078-1.jpeg" alt="IMG_85C0E187F078-1" style="zoom:50%;" />

    ```python
    def jump(self, nums: List[int]) -> int:
      n = len(nums)
      l, r = 0, 0
      res = 0
      
      while r < n - 1:     # r does not reach the last value; if r == n - 1, res += 1, add another 1
        farthest = 0
        for i in range(l, r + 1):
          farthest = max(farthest, i + nums[i])   # the farthest destination
        l = r + 1
        r = farthest
        res += 1		# move to the next level --> jump a step
      
      return res
    ```

    - Time complexity: $O(N)$ --> 虽然有while和for两个loop，但其实只是让l, r遍历了一次数组

      Space complexity: O(1)

