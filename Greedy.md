# Greedy - 贪心算法

<img src="https://code-thinking-1253855093.file.myqcloud.com/pics/20210917104315.png" alt="贪心算法大纲" style="zoom: 50%;" />

- **定义**

  **贪心的本质是选择每一阶段的局部最优，从而达到全局最优**。<font color=blue>**每一步做出的选择都是<u>当前</u>看起来最好的选择，只是局部最优解而非全局最优解**</font>

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
  - Space complexity: $O(logm + logn)$

  