# 2-D Dynamic Programming

<img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220816150918375.png" alt="image-20220816150918375" style="zoom:50%;" />

- ==**01背包问题🎒 (0-1 Knapsack Problem)**==

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

       <img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20221031165016552.png" alt="image-20221031165016552" style="zoom:50%;" />

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

       <img src="/Users/xinyuzhang/Downloads/202101101032124.png" alt="202101101032124" style="zoom:50%;" />

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

    <img src="https://labuladong.github.io/algo/images/%e7%8a%b6%e6%80%81%e5%8e%8b%e7%bc%a9/1.jpeg" alt="img" style="zoom:33%;" />

    <img src="https://labuladong.github.io/algo/images/%e7%8a%b6%e6%80%81%e5%8e%8b%e7%bc%a9/2.jpeg" alt="img" style="zoom:33%;" />

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
       for i in range(1, len(weight)):		  # 遍历物品; 从1开始，因为dp[0]已经为0了
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

       <img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20221031165152709.png" alt="image-20221031165152709" style="zoom:50%;" />

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
  
- ==**完全背包问题🎒 (Complete Knapsack Problem)**==

  有N件物品和一个最多能背重量为W的背包。第i件物品的重量是weight[i]，得到的价值是value[i] 。**每件物品都有无限个（也就是可以放入背包多次）**，求解将哪些物品装入背包里物品价值总和最大。--> <font color=blue>**完全背包和01背包问题唯一不同的地方就是，每种物品有无限件，在代码里体现在遍历顺序上的不同**。</font>

  01背包内嵌的循环是从大到小遍历，为了保证每个物品仅被添加一次。而完全背包的物品是可以添加多次的，所以要从小到大去遍历，即：

  ```python
  # 先遍历物品，再遍历背包
  for i in range(len(weight)):
    for j in range(weight[i], bagweight + 1):
      dp[j] = max(dp[j], dp[j - weight[i]] + value[i])
  ```

- ==**多重背包问题🎒**==

  有N种物品和一个容量为V 的背包。第i种物品最多有Mi件可用，每件耗费的空间是Ci ，价值是Wi 。求解将哪些物品装入背包可使这些物品的耗费的空间 总和不超过背包容量，且价值总和最大。多重背包和01背包是非常像的， 为什么和01背包像呢？

  --> 每件物品最多有Mi件可用，把Mi件摊开，其实就是一个01背包问题了。

  例如：

  背包最大重量为10。

  物品为：

  |       | 重量 | 价值 | 数量 |
  | ----- | ---- | ---- | ---- |
  | 物品0 | 1    | 15   | 2    |
  | 物品1 | 3    | 20   | 3    |
  | 物品2 | 4    | 30   | 2    |

  问背包能背的物品最大价值是多少？

  和如下情况有区别么？

  |       | 重量 | 价值 | 数量 |
  | ----- | ---- | ---- | ---- |
  | 物品0 | 1    | 15   | 1    |
  | 物品0 | 1    | 15   | 1    |
  | 物品1 | 3    | 20   | 1    |
  | 物品1 | 3    | 20   | 1    |
  | 物品1 | 3    | 20   | 1    |
  | 物品2 | 4    | 30   | 1    |
  | 物品2 | 4    | 30   | 1    |

```python
weight = [1, 3, 4]
value = [15, 20, 30]
nums = [2, 3, 2]
bag_weight = 10
def test_multi_pack1():
  # S1: 现将物品展开数量为1 --> 转换成01背包
    for i in range(len(nums)):
        while nums[i] > 1:
            weight.append(weight[i])
            value.append(value[i])
            nums[i] -= 1
    
    dp = [0]*(bag_weight + 1)
    # 遍历物品
    for i in range(len(weight)):
        # 遍历背包
        for j in range(bag_weight, weight[i] - 1, -1):
            dp[j] = max(dp[j], dp[j - weight[i]] + value[i])
```

- ==**股票买卖问题**==

  - <u>穷举框架</u>

    动态规划算法本质上就是穷举「状态」，然后在「选择」中选择最优解。那么对于这道题，我们具体到每一天，看看总共有几种可能的「状态」，再找出每个「状态」对应的「选择」。我们要穷举所有「状态」，穷举的目的是根据对应的「选择」更新状态。

    **每天都有三种「选择」**：买入、卖出、无操作，我们用 `buy`, `sell`, `rest` 表示这三种选择。但问题是，并不是每天都可以任意选择这三种选择的，因为 `sell` 必须在 `buy` 之后，`buy` 必须在 `sell` 之前。那么 `rest` 操作还应该分两种状态，一种是 `buy` 之后的 `rest`（持有了股票），一种是 `sell` 之后的 `rest`（没有持有股票）。而且别忘了，我们还有交易次数 `k` 的限制，就是说你 `buy` 还只能在 `k > 0` 的前提下操作。

    **这个问题的「状态」有三个**，第一个是<font color=blue>**天数**</font>，第二个是<font color=blue>**允许交易的最大次数**</font>，第三个是<font color=blue>**当前的持有状态**</font>（即之前说的 `rest` 的状态，我们不妨用 1 表示持有，0 表示没有持有）。然后我们用一个三维数组就可以装下这几种状态的全部组合：

    ```python
    dp[i][k][0 or 1]    # i: 天数; k: 允许交易的最大次数; 0 or 1: 当前持有状态
    # 0 <= i <= n - 1, 1 <= k <= K
    # n 为天数，大 K 为交易数的上限，0 和 1 代表是否持有股票。
    # 此问题共 n × K × 2 种状态，全部穷举就能搞定。
    #  dp[3][2][1] 的含义就是：今天是第三天，我现在手上持有着股票，至今最多进行 2 次交易。再比如 dp[2][3][0] 的含义：今天是第二天，我现在手上没有持有股票，至今最多进行 3 次交易。
    
    for i in range(n):
      for k in range(1, K + 1):
        for s in {0, 1}:
          dp[i][k][s] = max(buy, sell, rest)
          
    return dp[n - 1][K][0]   # 想求的最终答案，即最后一天，最多允许 K 次交易，最多获得多少利润。（最后一天不应该还持有股票，因为最后一天卖出股票肯定利益更大）
    ```

  - <u>状态转移框架</u>

    --> 完成了「状态」的穷举，开始思考每种「状态」有哪些「选择」，应该如何更新「状态」

    <img src="https://labuladong.github.io/algo/images/%e8%82%a1%e7%a5%a8%e9%97%ae%e9%a2%98/1.png" alt="img" style="zoom:50%;" />

    State transition function:

    ```python
    # 状态转移1
    # 解释：今天我没有持有股票，有两种可能，我从这两种可能中求最大利润：
    # 1、我昨天就没有持有，且截至昨天最大交易次数限制为 k；然后我今天选择 rest，所以我今天还是没有持有，最大交易次数限制依然为 k。
    # 2、我昨天持有股票，且截至昨天最大交易次数限制为 k；但是今天我 sell 了，所以我今天没有持有股票了，最大交易次数限制依然为 k。
    
    dp[i][k][0] = max(dp[i-1][k][0], dp[i-1][k][1] + prices[i])
    # 						max( 今天选择 rest, 今天选择 sell，前一天利润加上今天卖出的价格)
    
    # 状态转移2
    # 解释：今天我持有着股票，最大交易次数限制为 k，那么对于昨天来说，有两种可能，我从这两种可能中求最大利润：
    # 1、我昨天就持有着股票，且截至昨天最大交易次数限制为 k；然后今天选择 rest，所以我今天还持有着股票，最大交易次数限制依然为 k。
    # 2、我昨天本没有持有，且截至昨天最大交易次数限制为 k - 1；但今天我选择 buy，所以今天我就持有股票了，最大交易次数限制为 k。
    
    dp[i][k][1] = max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i])
    # 						max( 今天选择 rest, 今天选择 buy，前一天利润减去今天买入的价格)
    # 注意 k 的限制，在选择 buy 的时候相当于开启了一次交易，那么对于昨天来说，交易次数的上限 k 应该减小 1。
    # ！！交易是从buy开始的，所以buy的选择能改变交易次数
    ```

    这里着重提醒一下，**时刻牢记「状态」的定义**，状态 `k` 的定义并不是「已进行的交易次数」，而是「最大交易次数的上限限制」。如果确定今天进行一次交易，且要保证截至今天最大交易次数上限为 `k`，那么昨天的最大交易次数上限必须是 `k - 1`。

    - Base Case

      ```python
      dp[-1][...][0] = 0
      解释：因为 i 是从 0 开始的，所以 i = -1 意味着还没有开始，这时候的利润当然是 0。
      
      dp[-1][...][1] = -infinity
      解释：还没开始的时候，是不可能持有股票的。
      因为我们的算法要求一个最大值，所以初始值设为一个最小值，方便取最大值。
      
      dp[...][0][0] = 0
      解释：因为 k 是从 1 开始的，所以 k = 0 意味着根本不允许交易，这时候利润当然是 0。
      
      dp[...][0][1] = -infinity
      解释：不允许交易的情况下，是不可能持有股票的。
      因为我们的算法要求一个最大值，所以初始值设为一个最小值，方便取最大值。
      ```

    - Summary

      ```python
      base case：
      dp[-1][...][0] = dp[...][0][0] = 0
      dp[-1][...][1] = dp[...][0][1] = -infinity
      
      状态转移方程：
      dp[i][k][0] = max(dp[i-1][k][0], dp[i-1][k][1] + prices[i])
      dp[i][k][1] = max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i])
      ```

     - <u>空间优化</u>

       观察发现，`dp[i][*][*] `的状态与上一时刻 `dp[i-1][*][*]` 有关，因此可考虑省去第一维度。⚠️ ⚠️ ⚠️ 同时又要注意到，如果先更新 `dp[j][1]` 的话，`dp[j][1]` 又会立马被用于更新 `dp[j][0]`，而先更新 `dp[j][0] `再更新 `dp[j][1] `则可避免这种时效问题。

- **子序列问题**

  子序列问题比子串、子数组更难一些，因为子序列问题是不连续的。子序列问题一般会**涉及到两个字符串**。**一般这类问题都是求最长子序列**，因为最短子序列就是一个字符 --> <font color=blue>**一旦涉及到子序列和最值 --> 考察的是动态规划，时间复杂度一般都是$O(N^2)$**</font>

  - <u>两种思路</u>

    1. 一维的dp数组 --> <font color=blue>**最长递增子序列，最大子数组和**</font>

       ```python
       n = len(array)
       dp = [0] * n
       
       for i in range(1, n):
         for j in range(i):
           dp[i] = max/min(dp[i], dp[j] + ...)
       ```

       在这个思路中dp数组的定义是：**在子数组`arr[0...i]`中，我们要求的子序列长度是`dp[i]`**

    2. 二维的dp数组 --> <font color=blue>**最长公共子序列，编辑距离 （<u>涉及两个字符串/数组的子序列</u>）+ 回文子序列问题 （只涉及一个字符串/数组）**</font>

       ```python
       n = len(arr)
       dp = [[0] * n for _ in range(n + 1)]
       
       for i in range(n):
         for j in range(n):
           if arr[i-1] == arr[j-1]:
             dp[i][j] = dp[i-1][j-1] + ...
           else:
             dp[i][j] = max/min(...)
       ```

       - 涉及两个字符串/数组的子序列：在子数组`arr1[0...i]`和子数组`arr2[0...j]`中，我们要求的子序列长度为`dp[i][j]`
       - 只涉及一个字符串/数组：在子数组`array[i...j]`中，我们要求的子序列长度为`dp[i][j]`


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

#### 1.1 64 [Minimum Path Sum](https://leetcode.com/problems/minimum-path-sum/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (59.51%) | [`array`](https://leetcode.com/tag/array); [`dynamic-programming`](https://leetcode.com/tag/dynamic-programming) |

Given a `m x n` `grid` filled with non-negative numbers, find a path from top left to bottom right, which minimizes the sum of all numbers along its path.

**Note:** You can only move either down or right at any point in time.

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2020/11/05/minpath.jpg" alt="img" style="zoom: 50%;" />

```
Input: grid = [[1,3,1],[1,5,1],[4,2,1]]
Output: 7
Explanation: Because the path 1 → 3 → 1 → 1 → 1 minimizes the sum.
```

**Example 2:**

```
Input: grid = [[1,2,3],[4,5,6]]
Output: 12
```

- **Constraints:**

  - `m == grid.length`

  - `n == grid[i].length`

  - `1 <= m, n <= 200`

  - `0 <= grid[i][j] <= 100`

- **Thoughts**

  - 看到题目求最值，即想到使用动态规划解法 --> 这道题和62题的思路基本一模一样，根据题意修改即可

    根据动态规划步骤：

    - definiiton: `dp[i][j]`: minimum path sum to `grid[i][j]`
    - formula: `dp[i][j] = min(dp[i][j], dp[i - 1][j] + grid[i][j], dp[i][j - 1] + grid[i][j])`
    - initialization: `dp = [[float('inf')] * col for _ in range(row)]`; `dp[0][0] = grid[0][0]`

  - <font color=red>**易错点：**</font>由于这道题求的是最小值，所以初始化的dp table的值应该都是`float('inf')`

- **Solution**

  ```python
  def minPathSum(self, grid: List[List[int]]) -> int:
          row, col = len(grid), len(grid[0])
          dp = [[float('inf')] * col for _ in range(row)]
          dp[0][0] = grid[0][0]
  
          for i in range(row):
              for j in range(col):
                  if i - 1 >= 0:
                      dp[i][j] = min(dp[i][j], dp[i - 1][j] + grid[i][j])
                  if j - 1 >= 0:
                      dp[i][j] = min(dp[i][j], dp[i][j - 1] + grid[i][j])
          return dp[-1][-1]      
  ```

  - Time complexity: $O(row * col)$

    Space complexity: $O(row * col)$ for the `dp` array

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
  - definition: `dp[r][c]` is the number of unique path to `grid[r][c]`
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

## 7. 518 [Coin Change 2](https://leetcode.com/problems/coin-change-2/description/)

|  Category  |   Difficulty    |                     Tags                      |
| :--------: | :-------------: | :-------------------------------------------: |
| algorithms | Medium (57.79%) | [`Unknown`](https://leetcode.com/tag/Unknown) |

You are given an integer array `coins` representing coins of different denominations and an integer `amount` representing a total amount of money.

Return *the number of combinations that make up that amount*. If that amount of money cannot be made up by any combination of the coins, return `0`.

You may assume that you have an infinite number of each kind of coin.

The answer is **guaranteed** to fit into a signed **32-bit** integer.

**Example 1:**

```
Input: amount = 5, coins = [1,2,5]
Output: 4
Explanation: there are four ways to make up the amount:
5=5
5=2+2+1
5=2+1+1+1
5=1+1+1+1+1
```

**Example 2:**

```
Input: amount = 3, coins = [2]
Output: 0
Explanation: the amount of 3 cannot be made up just with coins of 2.
```

**Example 3:**

```
Input: amount = 10, coins = [10]
Output: 1
```

- **Constraints:**

  - `1 <= coins.length <= 300`

  - `1 <= coins[i] <= 5000`

  - All the values of `coins` are **unique**.

  - `0 <= amount <= 5000`

- **Thoughts**

  遵循动态规划五部曲：

  - definition: dp[j]: number of combinations to make amout j

  - function: 如果没使用这个面值的硬币，凑出面额 j 的方法数 dp[j] 应该等于 dp[j]，继承之前的结果

    ​                如果使用这个面值的硬币，那么就应该关注如何凑出金额 j - coins[i-1]

    --> `dp[j] = dp[j] + dp[j - coins[i]]` 将两种情况的数量相加（一共应该是n中方法）

  - initialization: dp[0] = 1 --> <font color=red>初始化为0大小的背包, 当然是不装任何东西了, 就是1种方法</font>

  - traversal order: both in order --> 由于这道题组合不强调顺序，所以**如果求组合数就是外层for循环遍历物品，内层for遍历背包**。

- **Solution**

  ```python
  def change(self, amount: int, coins: List[int]) -> int:
    dp = [0] * (amount + 1)
    dp[0] = 1
  
    for i in range(len(coins)):
      for j in range(coins[i], amount + 1):
        dp[j] = dp[j] + dp[j - coins[i]]
    return dp[amount]
  ```

  - Time complexity: $O(n \times amount)$

    Space complexity: $O(amount)$ 

## 8. 377 [Combination Sum IV](https://leetcode.com/problems/combination-sum-iv/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (49.55%) | [`dynamic-programming`](https://leetcode.com/tag/dynamic-programming) |

Given an array of **distinct** integers `nums` and a target integer `target`, return *the number of possible combinations that add up to* `target`.

The test cases are generated so that the answer can fit in a **32-bit** integer.

**Example 1:**

```
Input: nums = [1,2,3], target = 4
Output: 7
Explanation:
The possible combination ways are:
(1, 1, 1, 1)
(1, 1, 2)
(1, 2, 1)
(1, 3)
(2, 1, 1)
(2, 2)
(3, 1)
<Important!>Note that different sequences are counted as different combinations.
```

**Example 2:**

```
Input: nums = [9], target = 3
Output: 0
```

- **Constraints:**

  - `1 <= nums.length <= 200`

  - `1 <= nums[i] <= 1000`

  - All the elements of `nums` are **unique**.

  - `1 <= target <= 1000`

**Follow up:** What if negative numbers are allowed in the given array? How does it change the problem? What limitation we need to add to the question to allow negative numbers?

- **Thoughts**

  - 题目显示不限制每个数字所取的次数，所以该题目为完全背包。从`Note that different sequences are counted as different combinations.`可以看出本题题目描述说是求组合，但又说是可以元素相同顺序不同的组合算两个组合，**其实就是求排列！**

    组合不强调顺序，(1,5)和(5,1)是同一个组合。排列强调顺序，(1,5)和(5,1)是两个不同的排列。

    这道题目和<u>Backtrack.md</u>的39/40很接近，但其本质是本题求的是排列总和，而且仅仅是求排列总和的个数，并不是把所有的排列都列出来，可以使用完全背包理论解决。**如果本题要把排列都列出来的话，只能使用回溯算法爆搜**。（Brute-force!）

  - 遵循动态规划五部曲：

    - definition: dp[j]: the number of possible combinations that add up to j (**凑成目标正整数为j的排列个数为dp[j]**) 

    - function: dp[j] = dp[j] + dp[j - nums[i]]

    - initialization: dp[0] = 1(target >= 1 --> dp[0] must be 1 for future calculations)

    - traversal order: 如果把遍历nums（物品）放在外循环，遍历target的作为内循环的话，举一个例子：计算dp[4]的时候，结果集只有 {1,3} 这样的集合，不会有{3,1}这样的集合，因为nums遍历放在外层，3只能出现在1后面！

      所以本题遍历顺序最终遍历顺序：**target（背包）放在外循环，将nums（物品）放在内循环，内循环从前到后遍历**。

- **Solution**

  ```python
  def combinationSum4(self, nums: List[int], target: int) -> int:
    dp = [0] * (target + 1)
    dp[0] = 1
  
    # traverse backpack weight first and then traverse object
    for j in range(target + 1):
      for i in range(len(nums)):
        if j - nums[i] >= 0:
          dp[j] += dp[j - nums[i]]
    return dp[target]
  ```

  - Time complexity: $O(n \times target)$

    Space complexity: $O(target)$ 

## 9. 279 [Perfect Squares](https://leetcode.com/problems/perfect-squares/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (51.92%) | [`math`](https://leetcode.com/tag/math); [`dynamic-programming`](https://leetcode.com/tag/dynamic-programming); [`breadth-first-search`](https://leetcode.com/tag/breadth-first-search) |

Given an integer `n`, return *the least number of perfect square numbers that sum to* `n`.

A **perfect square** is an integer that is the square of an integer; in other words, it is the product of some integer with itself. For example, `1`, `4`, `9`, and `16` are perfect squares while `3` and `11` are not. 

**Example 1:**

```
Input: n = 12
Output: 3
Explanation: 12 = 4 + 4 + 4.
```

**Example 2:**

```
Input: n = 13
Output: 2
Explanation: 13 = 4 + 9.
```

- **Constraints:**

  - `1 <= n <= 104`

- **Thoughts**

  - 分析题目后可得知，题目可转换为完全背包问题：**完全平方数就是物品（可以无限件使用），凑个正整数n就是背包，问凑满这个背包最少有多少物品？** --> 由于不考虑每个平方完全数的顺序，所以该题是求组合数

    `target = n`; `nums = [1, 4, 9, 16...]`

  - 遵循动态规划五部曲:

    - definition: dp[j]: the least number of perfect square numbers that sum to n --> <font color=red>每个元素代表的是最小值，所以初始化时要将每个元素的值初始化为最大值`float('inf')`</font> --> **非0下标的dp[j]一定要初始为最大值，这样dp[j]在递推的时候才不会被初始值覆盖**。
    - function: `dp[j] = min(dp[j], dp[j - i * i] + 1)` --> <font color=blue>**因为这道题的物品是平方数，所以可以先创建一个数组包含小于target的所有平方数，即为所有可取的物品**</font>
    - initialization: dp[0]表示和为0的完全平方数的最小数量，并且题目没有提到从0开始的平方数，那么dp[0]一定是0。
    - traversal order: 由于这道题求的是组合数，所以两种遍历顺序都可以！

  - 从二维状态转移到一维空间的优化：

    <img src="https://pic.leetcode-cn.com/1618579990-nKBWBw-640.png" alt="640.png" style="zoom: 67%;" />

- **Solution**

  ```python
  def numSquares(self, n: int) -> int:
          nums = [i ** 2 for i in range(1, n + 1) if i ** 2 <= n]
          dp = [float('inf')] * (n + 1)
          dp[0] = 0
          
          for i in range(len(nums)):
              for j in range(nums[i], n + 1):  # weight: [nums[i], n]
                  dp[j] = min(dp[j], dp[j - nums[i]] + 1)
          return dp[n]
  ```

  - Time complexity: $O(n \times \sqrt n)$ --> 物品`nums[i]`的最大值为$\sqrt n$

    Space complexity: $O(n)$  --> store dp array

## 10. 121 [Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Easy (54.14%) | [`array`](https://leetcode.com/tag/array); [`dynamic-programming`](https://leetcode.com/tag/dynamic-programming) |

You are given an array `prices` where `prices[i]` is the price of a given stock on the `ith` day.

You want to maximize your profit by choosing a **single day** to buy one stock and choosing a **different day in the future** to sell that stock.

Return *the maximum profit you can achieve from this transaction*. If you cannot achieve any profit, return `0`.

**Example 1:**

```
Input: prices = [7,1,5,3,6,4]
Output: 5
Explanation: Buy on day 2 (price = 1) and sell on day 5 (price = 6), profit = 6-1 = 5.
Note that buying on day 2 and selling on day 1 is not allowed because you must buy before you sell.
```

**Example 2:**

```
Input: prices = [7,6,4,3,1]
Output: 0
Explanation: In this case, no transactions are done and the max profit = 0.
```

- **Constraints:**

  - `1 <= prices.length <= 105`

  - `0 <= prices[i] <= 104`

- **Solution**

  - <u>Method 1: Brute-force</u> (超时)

    ```python
    def maxProfit(self, prices: List[int]) -> int:
      res = 0
      for i in range(len(prices)):
        for j in range(i + 1, len(prices)):
          res = max(res, prices[j] - prices[i])
      return res
    ```

    - Time complexity: $O(N^2)$

      Space complexity: $O(1)$

  - <u>Method 2: Two-Pointers</u>（Brute-force解法的升级版，使用双指针减小时间复杂度）

    ```python
    def maxProfit(self, prices: List[int]) -> int:
      l, r = 0, 1      # l: to buy (low); r: to sell (high)
      res = 0
      while r < len(prices):
        if prices[r] > prices[l]:   # profitable
          profit = prices[r] - prices[l]
          res = max(res, profit)
        else:
          # if prices[r] < prices[l] --> not profitable --> set l to the low price --> l = r
          l = r
        r += 1
     return res
    ```

    - Time complexity: $O(N)$

      Space complexity: $O(1)$

  - <u>Method 3: Dynamic Programming</u>

    - Definition: `dp[i][0]` 表示第i天不持有股票所得最多现金 ，由于一开始现金是0，所以加入第i天买入股票现金就是 -prices[i]。`dp[i][1]`表示第i天持有股票所得最多现金; `return dp[n-1][0]`
    - Equation: `dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])`; `dp[i][1] = max(dp[i-1][1], -prices[i])`
    - Initialization: 从递归公式可以看出，基础都是从`dp[0][0]`和`dp[0][1]`推导出来的 --> `dp[0][0]`表示第0天持有股票，此时的持有股票就一定是买入股票了，因为不可能有前一天推出来，所以`dp[0][0] -= prices[0]`; `dp[0][1]`表示第0天不持有股票，不持有股票那么现金就是0，所以`dp[0][1]` = 0
    - Traversal Order: 从递推公式可以看出dp[i]都是有dp[i - 1]推导出来的，那么一定是从前向后遍历。

    ```python
    def maxProfit(self, prices: List[int]) -> int:
      n = len(prices)
      dp = [[0] * 2 for _ in range(n)]
      # initialization
      dp[0][0], dp[0][1] = 0, -prices[i]
      
      for i in range(1, n):
        # dp[i][0]: not have a stock at day i --> rest; sell the stock
        dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
        # dp[i][1]: have a stock at day i --> rest; buy the stock (profit becomes -prices[i])
        dp[i][1] = max(dp[i-1][1], -prices[i])
      return dp[n-1][0]
    # Time complecity: O(N)
    # Space complexity: O(2N) = O(N)
    
    ## Optimization --> reduce space complexity
    def maxProfit(self, prices: List[int]) -> int:
      n = len(prices)
      dp0, dp1 = 0, -prices[i]			# dp0: dp[-1][0]; dp1: dp[-1][1]
      for i in range(1, n):
        dp0 = max(dp0, dp1 + prices[i])
        dp1 = max(dp1, -prices[i])
      return dp0
    # Time complecity: O(N)
    # Space complexity: O(1)
    ```

  - <u>Method 4: Greedy</u>

    <font color=red>**空缺！！**</font>

## 11. 122 [Best Time to Buy and Sell Stock II](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/description/)

|  Category  |   Difficulty    |                       Tags                        |
| :--------: | :-------------: | :-----------------------------------------------: |
| algorithms | Medium (62.17%) | [`array`](https://leetcode.com/tag/array); greedy |

You are given an integer array `prices` where `prices[i]` is the price of a given stock on the `ith` day.

On each day, you may decide to buy and/or sell the stock. You can only hold **at most one** share of the stock at any time. However, you can buy it then immediately sell it on the **same day**.

Find and return *the **maximum** profit you can achieve*. 

**Example 1:**

```
Input: prices = [7,1,5,3,6,4]
Output: 7
Explanation: Buy on day 2 (price = 1) and sell on day 3 (price = 5), profit = 5-1 = 4.
Then buy on day 4 (price = 3) and sell on day 5 (price = 6), profit = 6-3 = 3.
Total profit is 4 + 3 = 7.
```

**Example 2:**

```
Input: prices = [1,2,3,4,5]
Output: 4
Explanation: Buy on day 1 (price = 1) and sell on day 5 (price = 5), profit = 5-1 = 4.
Total profit is 4.
```

**Example 3:**

```
Input: prices = [7,6,4,3,1]
Output: 0
Explanation: There is no way to make a positive profit, so we never buy the stock to achieve the maximum profit of 0.
```

- **Constraints:**

  - `1 <= prices.length <= 3 * 104`

  - `0 <= prices[i] <= 104`

- **Thoughts**

  - 题目强调可以在同一天出售，但这个条件纯属多余，如果当天买当天卖，那利润当然就是 0，这不是和没有进行交易是一样的吗？这道题和上一道题121非常类似，但不同点在于没有给出交易总数 `k` 的限制，也就相当于 `k` 为**正无穷 --> 因为本题的股票可以买卖多次！** 。<font color=blue>**如果 `k` 为正无穷，那么就可以认为 `k` 和 `k - 1` 是一样的。**</font>可以这样改写框架：

    ```python
    dp[i][k][0] = max(dp[i-1][k][0], dp[i-1][k][1] + prices[i])
    dp[i][k][1] = max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i])
                = max(dp[i-1][k][1], dp[i-1][k][0] - prices[i])
    
    我们发现数组中的 k 已经不会改变了，也就是说不需要记录 k 这个状态了：
    dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
    dp[i][1] = max(dp[i-1][1], dp[i-1][0] - prices[i])
    ```

- **Solution**

  ```python
  def maxProfit(self, prices: List[int]) -> int:
    n = len(prices)
    dp = [[0] * 2 for _ in range(n)]
    dp[0][0], dp[0][1] = 0, -prices[0]
  
    for i in range(1, n):
      dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
      dp[i][1] = max(dp[i-1][1], dp[i-1][0] - prices[i])
    return dp[n-1][0]
  ```

  - Time complexity: $O(N)$

    Space complexity: $O(N)$

  - <u>Optimization: reduce space complexity</u>

    ```python
    def maxProfit(self, prices: List[int]) -> int:
      n = len(prices)
      dp0, dp1 = 0, -prices[0]
      for i in range(1, n):
        temp = dp0
        dp0 = max(dp0, dp1 + prices[i])
        dp1 = max(dp1, temp - prices[i])
      return dp0
    ```

    - Time complexity: $O(N)$

      Space complexity: $O(1)$

## 12. 309 [Best Time to Buy and Sell Stock with Cooldown](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (52.67%) | [`dynamic-programming`](https://leetcode.com/tag/dynamic-programming) |

You are given an array `prices` where `prices[i]` is the price of a given stock on the `ith` day.

Find the maximum profit you can achieve. You may complete as many transactions as you like (i.e., buy one and sell one share of the stock multiple times) with the following restrictions:

- After you sell your stock, you cannot buy stock on the next day (i.e., cooldown one day).

**Note:** You may not engage in multiple transactions simultaneously (i.e., you must sell the stock before you buy again). 

**Example 1:**

```
Input: prices = [1,2,3,0,2]
Output: 3
Explanation: transactions = [buy, sell, cooldown, buy, sell]
```

**Example 2:**

```
Input: prices = [1]
Output: 0
```

- **Constraints:**

  - `1 <= prices.length <= 5000`

  - `0 <= prices[i] <= 1000`

- **Thoughts**

  这道题和上一道题一样，k为正无穷，只不过每次 `sell` 之后要等一天才能继续交易，只要把这个特点融入上一题的状态转移方程即可：

  ```python
  dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
  dp[i][1] = max(dp[i-1][1], dp[i-2][0] - prices[i])
  解释：第 i 天选择 buy 的时候，要从 i-2 的状态转移，而不是 i-1 。
  ```

- **Solution**

  ```python
  def maxProfit(self, prices: List[int]) -> int:
    n = len(prices)
    if n == 1:
      return 0
    dp = [[0] * 2 for _ in range(n)]
    # base case
    dp[0][0], dp[0][1] = 0, -prices[0]
    # for dp[i-2][0] and for dp[i-2][1]
    dp[1][0] = max(dp[0][0], dp[0][1] + prices[1])   
    dp[1][1] = max(dp[0][1], -prices[1])
  
    for i in range(2, n):
      dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
      dp[i][1] = max(dp[i-1][1], dp[i-2][0] - prices[i])
    return dp[n-1][0]
  ```

  - Time complexity: $O(N)$

    Space complexity: $O(N)$

  - <u>Optimization</u>

    ```python
    def maxProfit(self, prices: List[int]) -> int:
      n = len(prices)
      dp0, dp1 = 0, -prices
      dp_pre0 = 0						# 代表dp[i-2][0]
      
      for i in range(1, n):
        temp = dp0
        dp0 = max(dp0, dp1 + prices[i])
        dp1 = max(dp1, dp_pre0 - prices[i])
        dp_pre0 = temp
      return dp0
    ```

    - Time complexity: $O(N)$

      Space complexity: $O(1)$

## 13. 714 [Best Time to Buy and Sell Stock with Transaction Fee](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (62.44%) | [`array`](https://leetcode.com/tag/array); [`dynamic-programming`](https://leetcode.com/tag/dynamic-programming); [`greedy`](https://leetcode.com/tag/greedy) |

You are given an array `prices` where `prices[i]` is the price of a given stock on the `ith` day, and an integer `fee` representing a transaction fee.

Find the maximum profit you can achieve. You may complete as many transactions as you like, but you need to pay the transaction fee for each transaction.

**Note:** You may not engage in multiple transactions simultaneously (i.e., you must sell the stock before you buy again).

**Example 1:**

```
Input: prices = [1,3,2,8,4,9], fee = 2
Output: 8
Explanation: The maximum profit can be achieved by:
- Buying at prices[0] = 1
- Selling at prices[3] = 8
- Buying at prices[4] = 4
- Selling at prices[5] = 9
The total profit is ((8 - 1) - 2) + ((9 - 4) - 2) = 8.
```

**Example 2:**

```
Input: prices = [1,3,7,5,10,3], fee = 3
Output: 6 
```

- **Constraints:**

  - `1 <= prices.length <= 5 * 104`

  - `1 <= prices[i] < 5 * 104`

  - `0 <= fee < 5 * 104`

- **Thoughts**

  这道题和11. 122 [Best Time to Buy and Sell Stock II](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/description/)基本一致，唯一区别在于当买入股票时还要计算手续费

- **Solution**

  ```python
  def maxProfit(self, prices: List[int], fee: int) -> int:
    n = len(prices)
    dp = [[0] * 2 for _ in range(n)]
    dp[0][0], dp[0][1] = 0, -prices[0] - fee
  
    for i in range(1, n):
      dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
      dp[i][1] = max(dp[i-1][1], dp[i-1][0] - prices[i] - fee) # buy the stock
    return dp[n-1][0]
  # Time complexity: O(N); Space complexity: O(1)
  
  ## Opyimization
  def maxProfit(self, prices: List[int], fee: int) -> int:
    n = len(prices)
    dp0, dp1 = 0, -prices[0] - fee
  
    for i in range(1, n):
      temp = dp0
      dp0 = max(dp0, dp1 + prices[i])
      dp1 = max(dp1, temp - prices[i] - fee)
    return dp0
  # Time complexity: O(1); Space complexity: O(1)
  ```

## 14. 123 [Best Time to Buy and Sell Stock III](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Hard (43.35%) | [`array`](https://leetcode.com/tag/array); [`dynamic-programming`](https://leetcode.com/tag/dynamic-programming) |

You are given an array `prices` where `prices[i]` is the price of a given stock on the `ith` day.

Find the maximum profit you can achieve. You may complete **at most two transactions**.

**Note:** You may not engage in multiple transactions simultaneously (i.e., you must sell the stock before you buy again). 

**Example 1:**

```
Input: prices = [3,3,5,0,0,3,1,4]
Output: 6
Explanation: Buy on day 4 (price = 0) and sell on day 6 (price = 3), profit = 3-0 = 3.
Then buy on day 7 (price = 1) and sell on day 8 (price = 4), profit = 4-1 = 3.
```

**Example 2:**

```
Input: prices = [1,2,3,4,5]
Output: 4
Explanation: Buy on day 1 (price = 1) and sell on day 5 (price = 5), profit = 5-1 = 4.
Note that you cannot buy on day 1, buy on day 2 and sell them later, as you are engaging multiple transactions at the same time. You must sell before buying again.
```

**Example 3:**

```
Input: prices = [7,6,4,3,1]
Output: 0
Explanation: In this case, no transaction is done, i.e. max profit = 0.
```

- **Constraints:**

  - `1 <= prices.length <= 105`

  - `0 <= prices[i] <= 105`

- **Thoughts**

  这道题和原版模板十分接近了，即为k = 2的情况。只是需要注意如何在Python构建三维数组以及二三维数组的slicing

- **Solution**

  ```python
  # dp[i][k][0] = max(dp[i-1][k][0], dp[i-1][k][1] + prices[i])
  # dp[i][k][1] = max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i])
  def maxProfit(self, prices: List[int]) -> int:
    n = len(prices)
    if n == 1:
      return 0
    dp = [[[0] * 2 for _ in range(3)] for _ in range(n)]			# dp: n * (k+1) * 2
    
    # initialization
    # dp[0][:][0] = 0; dp[0][:][1] = -prices[0]
    for i in range(3):
      dp[0][i][0], dp[0][i][1] = 0, -prices[0]
      
    for i in range(1, n):
      for k in range(1, 3):
        dp[i][k][0] = max(dp[i-1][k][0], dp[i-1][k][1] + prices[i])
        dp[i][k][1] = max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i])
    return dp[n-1][2][0]
  ```

  - Time complexity: $O(k \times N) = O(2N) = O(N)$

    Space complexity: $O(N)$

  - <u>Optimization</u>

    ```python
    # reduce dp table to 2-dimensional with no i
    # dp[i][k][0] = max(dp[i-1][k][0], dp[i-1][k][1] + prices[i])
    # dp[i][k][1] = max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i])
    def maxProfit(self, prices: List[int]) -> int:
      n = len(prices)
      if n == 1:
        return 0
      dp = [[0] * 2 for _ in range(3)]  # dp: (k + 1) * 2
      
      # initialization
      # dp[:][0] = 0; dp[:][1] = -prices[0]
      for i in range(3):
        dp[i][0], dp[i][1] = 0, -prices[0]
        
      for i in range(1, n):
        for k in range(1, 3):
          dp[k][0] = max(dp[k][0], dp[k][1] + prices[i])
          dp[k][1] = max(dp[k][1], dp[k-1][0] - prices[i])
      return dp[2][0]
    ```

    - Time complexity: $O(k \times N) = O(2N) = O(N)$

      Space complexity: $O(1)$ --> create a dp table with size 6 ($(k + 1) * 2$ is a constant)

## 15. 188 [Best Time to Buy and Sell Stock III](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Hard (43.35%) | [`array`](https://leetcode.com/tag/array); [`dynamic-programming`](https://leetcode.com/tag/dynamic-programming) |

You are given an array `prices` where `prices[i]` is the price of a given stock on the `ith` day.

Find the maximum profit you can achieve. You may complete **at most two transactions**.

**Note:** You may not engage in multiple transactions simultaneously (i.e., you must sell the stock before you buy again).

**Example 1:**

```
Input: prices = [3,3,5,0,0,3,1,4]
Output: 6
Explanation: Buy on day 4 (price = 0) and sell on day 6 (price = 3), profit = 3-0 = 3.
Then buy on day 7 (price = 1) and sell on day 8 (price = 4), profit = 4-1 = 3.
```

**Example 2:**

```
Input: prices = [1,2,3,4,5]
Output: 4
Explanation: Buy on day 1 (price = 1) and sell on day 5 (price = 5), profit = 5-1 = 4.
Note that you cannot buy on day 1, buy on day 2 and sell them later, as you are engaging multiple transactions at the same time. You must sell before buying again.
```

**Example 3:**

```
Input: prices = [7,6,4,3,1]
Output: 0
Explanation: In this case, no transaction is done, i.e. max profit = 0.
```

- **Constraints:**

  - `1 <= prices.length <= 105`

  - `0 <= prices[i] <= 105`

- **Thoughts**

  这道题可以直接套用股票买卖的模板

- **Solution**

  ```python
  # dp: n * (k + 1) * 2 --> 3 conditions: day, at most transactions, have/not have a stock
      def maxProfit(self, k: int, prices: List[int]) -> int:  
          n = len(prices)
          # base case
          if n == 0 or n == 1 or k == 0:
              return 0
          
          # initialization
          dp = [[[0] * 2 for _ in range(k + 1)] for _ in range(n)]    # dimension:(n, k, 2)
          for i in range(k + 1):
              dp[0][i][0] = 0
              dp[0][i][1] = -prices[0]
  
          for i in range(1, n):
              for j in range(1, k + 1):
                  dp[i][j][0] = max(dp[i-1][j][0], dp[i-1][j][1] + prices[i])
                  dp[i][j][1] = max(dp[i-1][j][1], dp[i-1][j-1][0] - prices[i])
          return dp[n-1][k][0] 
  # Time complexity: O(k * N)
  # Space complexity: O(k * N * 2)
  
  ## Optimization
  def maxProfit(self, k: int, prices: List[int]) -> int:
          n = len(prices)
          # base case
          if n == 0 or n == 1 or k == 0:
              return 0
          
          # initialization
          dp = [[[0] * 2 for _ in range(k + 1)] for _ in range(n)]    # dimension:(n, k, 2)
          for i in range(k + 1):
              dp[0][i][0] = 0
              dp[0][i][1] = -prices[0]
  
          for i in range(1, n):
              for j in range(1, k + 1):
                  dp[i][j][0] = max(dp[i-1][j][0], dp[i-1][j][1] + prices[i])
                  dp[i][j][1] = max(dp[i-1][j][1], dp[i-1][j-1][0] - prices[i])
          return dp[n-1][k][0]  
  # Time complexity: O(k * N)
  # Space complexity: O(k * 2) = O(1)
  ```

  - Further Optimization:

    如果传入的 `k` 值会非常大，`dp` 数组会太大。那么现在想想，交易次数 `k` 最多有多大呢？一次交易由买入和卖出构成，至少需要两天。所以说有效的限制 `k` 应该不超过 `n/2` -->  <font color=blue>**n 天最多只能进行 n/2 笔交易**</font> `k = min(k, n//2)`

## 子序列系列

## 1. 718 [Maximum Length of Repeated Subarray](https://leetcode.com/problems/maximum-length-of-repeated-subarray/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (51.37%) | [`array`](https://leetcode.com/tag/array); [`hash-table`](https://leetcode.com/tag/hash-table); [`binary-search`](https://leetcode.com/tag/binary-search); [`dynamic-programming`](https://leetcode.com/tag/dynamic-programming) |

Given two integer arrays `nums1` and `nums2`, return *the maximum length of a subarray that appears in **both** arrays*.

**Example 1:**

```
Input: nums1 = [1,2,3,2,1], nums2 = [3,2,1,4,7]
Output: 3
Explanation: The repeated subarray with maximum length is [3,2,1].
```

**Example 2:**

```
Input: nums1 = [0,0,0,0,0], nums2 = [0,0,0,0,0]
Output: 5 
```

- **Constraints:**

  - `1 <= nums1.length, nums2.length <= 1000`

  - `0 <= nums1[i], nums2[i] <= 100`

- **Thoughts**

  - 这道题和📒<u>1D_Dynamic Programming.md</u>的300. [Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/description/)和变种题目674的区别主要在于这里有两个`nums`，所以对应的dp数组也是二维的
  - 遵循动态规划五个步骤：
    - definition: `dp[i][j]`: the maximum length of a subarray to nums1[i] and nums2[j]; `return max(dp)`
    - equation: `if nums1[i - 1] == nums2[j - 1]: dp[i][j] = dp[i - 1][j - 1] + 1`
    - initialization: `dp[i][j] = 0`; dp: (i + 1) * (j + 1) --> <font color=red>设置dp数组为(i + 1) * (j + 1)是因为num1[0]和nums2[0]也要进行是否相等的比较，所以dp数组值不确定为0或1，要增加一行一列初始化</font>

- **Solution**

  ```python
  def findLength(self, nums1: List[int], nums2: List[int]) -> int:        
      n1, n2 = len(nums1), len(nums2)
      dp = [[0] * (n2 + 1) for _ in range(n1 + 1)]
      res = 0
      for i in range(1, n1 + 1):
        for j in range(1, n2 + 1):
          if nums1[i - 1] == nums2[j - 1]:
            dp[i][j] = dp[i - 1][j - 1] + 1
            res = max(res, dp[i][j])
      return res
  ```

  - Time complexity: $O(N1 \times N2)$

    Space complexity: $O(N1 \times N2)$

  - <u>Optimization: 滚动数组</u>

    `dp[i][j]都是由dp[i - 1][j - 1]`推出。那么压缩为一维数组，也就是dp[j]都是由dp[j - 1]推出。也就是相当于可以把上一层`dp[i - 1][j]`拷贝到下一层`dp[i`][j]来继续用。

    ```python
    def findLength(self, nums1: List[int], nums2: List[int]) -> int:     
      n1, n2 = len(nums1), len(nums2)
      dp = [0] * (n2 + 1)
      res = 0
      for i in range(1, n1 + 1):
        for j in range(n2, 0, -1):
          if nums1[i - 1] == nums2[j - 1]:
            dp[j] = dp[j - 1] + 1
          else:
            dp[j] = 0
            res = max(res, dp[j])
      return res
    ```

    <font color=red>**易错点：在从二维数组转换为一维数组时一定要注意遍历第二维时要从后向前遍历，避免重复覆盖！！**</font>


## 2. 1143 Longest Common Subsequence

|  Category  |   Difficulty    |                     Tags                      |
| :--------: | :-------------: | :-------------------------------------------: |
| algorithms | Medium (58.89%) | [`Unknown`](https://leetcode.com/tag/Unknown) |

Given two strings `text1` and `text2`, return *the length of their longest **common subsequence**.* If there is no **common subsequence**, return `0`.

A **subsequence** of a string is a new string generated from the original string with some characters (can be none) deleted without changing the relative order of the remaining characters.

- For example, `"ace"` is a subsequence of `"abcde"`.

A **common subsequence** of two strings is a subsequence that is common to both strings.

**Example 1:**

```
Input: text1 = "abcde", text2 = "ace" 
Output: 3  
Explanation: The longest common subsequence is "ace" and its length is 3.
```

**Example 2:**

```
Input: text1 = "abc", text2 = "abc"
Output: 3
Explanation: The longest common subsequence is "abc" and its length is 3.
```

**Example 3:**

```
Input: text1 = "abc", text2 = "def"
Output: 0
Explanation: There is no such common subsequence, so the result is 0. 
```

- **Constraints:**

  - `1 <= text1.length, text2.length <= 1000`

  - `text1` and `text2` consist of only lowercase English characters.

- **Thoughts**

  - 这道题目和718比较相似，因为有两个字符串来求共同字符串，所以使用二维dp模板

  - 1. Definition: `dp[i][j]`：长度为<u>[0, i - 1]</u>的字符串text1与长度为<u>[0, j - 1]</u>的字符串text2的最长公共子序列为`dp[i][j]`

    2. Function: 

       主要就是两大情况： text1[i - 1] 与 text2[j - 1]相同，text1[i - 1] 与 text2[j - 1]不相同

       如果text1[i - 1] 与 text2[j - 1]相同，那么找到了一个公共元素，所以`dp[i][j]` = `dp[i - 1`][j - 1] + 1;

       如果text1[i - 1] 与 text2[j - 1]不相同，那就看看text1[0, i - 2]与text2[0, j - 1]的最长公共子序列 和 text1[0, i - 1]与text2[0, j - 2]的最长公共子序列，取最大的。

       即：`dp[i][j] `= max(`dp[i - 1][j]`, `dp[i][j - 1]`);

    3. Initialization: `dp[i][j] = 0`; dp: (i + 1) * (j + 1) --> <font color=red>设置dp数组为(i + 1) * (j + 1)是因为num1[0]和nums2[0]也要进行是否相等的比较，所以dp数组值不确定为0或1，要增加一行一列初始化</font>

    4. Traversal order: 

       <img src="https://code-thinking-1253855093.file.myqcloud.com/pics/20210204115139616.jpg" alt="1143.最长公共子序列" style="zoom: 67%;" />

       --> in-order

    4. Return value: return `dp[-1][-1]`

- **Solution**

  ```python
  class Solution:
      def longestCommonSubsequence(self, text1: str, text2: str) -> int:
          len1 = len(text1)
          len2 = len(text2)
          dp = [[0] * (len2 + 1) for _ in range(len1 + 1)]
  
          for i in range(1, len1 + 1):
              for j in range(1, len2 + 1):
                  if text1[i-1] == text2[j-1]:
                      dp[i][j] = dp[i-1][j-1] + 1
                  else:
                      dp[i][j] = max(dp[i-1][j], dp[i][j-1])
          return dp[-1][-1]
  ```

  - Time complexity: O(m * n)

    Space complexity: O(m * n) 

### 3. [Edit Distance](https://leetcode.com/problems/edit-distance/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Hard (51.03%) | [`string`](https://leetcode.com/tag/string); [`dynamic-programming`](https://leetcode.com/tag/dynamic-programming) |

Given two strings `word1` and `word2`, return *the minimum number of operations required to convert `word1` to `word2`*.

You have the following three operations permitted on a word:

- Insert a character
- Delete a character
- Replace a character

**Example 1:**

```
Input: word1 = "horse", word2 = "ros"
Output: 3
Explanation: 
horse -> rorse (replace 'h' with 'r')
rorse -> rose (remove 'r')
rose -> ros (remove 'e')
```

**Example 2:**

```
Input: word1 = "intention", word2 = "execution"
Output: 5
Explanation: 
intention -> inention (remove 't')
inention -> enention (replace 'i' with 'e')
enention -> exention (replace 'n' with 'x')
exention -> exection (replace 'n' with 'c')
exection -> execution (insert 'u')
```

- **Constraints:**

  - `0 <= word1.length, word2.length <= 500`

  - `word1` and `word2` consist of lowercase English letters.

- **Thoughts**

  这道题目分析得知，所求的最少操作数量也实则求两个字符串最长子序列长度 --> 解法与前两题较为相似，使用二维dp模板

  - 解题步骤：
    1. Definition: `dp[i][j]`表示以下标i-1为结尾的字符串word1，和以下标j-1为结尾的字符串word2，最近编辑距离为`dp[i][j]`

  