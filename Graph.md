# Graph

- **图的逻辑结构和具体实现**

  - <u>理论上</u>：一幅图是由**节点**和**边**构成的，逻辑结构如下：

    <img src="https://labuladong.github.io/algo/images/%e5%9b%be/0.jpg" alt="img" style="zoom:50%;" />

    ```python
    # 图节点的逻辑结构
    class Vertex {
        id = 0
        neighbors = []
    } # 和多叉树节点几乎完全一样 --> 适用于树的 DFS/BFS 遍历算法，全部适用于图
    ```
  
  - <u>实际上</u>：用**邻接表 [Adjacency List] 和邻接矩阵**来实现图
  
    <img src="https://labuladong.github.io/algo/images/%e5%9b%be/2.jpeg" alt="img" style="zoom:50%;" />
  
    - 邻接表很直观，我把每个节点 `x` 的邻居都存到一个列表里，然后把 `x` 和这个列表关联起来，这样就可以通过一个节点 `x` 找到它的所有相邻节点。<font color=blue>**[More common: use hashmap!]**</font>
  
      - Pros: 占用的空间少。邻接矩阵里面空着那么多位置，肯定需要更多的存储空间。
  
      - Cons: 邻接表无法快速判断两个节点是否相邻。比如说我想判断节点 `1` 是否和节点 `3` 相邻，我要去邻接表里 `1` 对应的邻居列表里查找 `3` 是否存在。但对于邻接矩阵就简单了，只要看看 `matrix[1][3]` 就知道了，效率高。
  
        --> 使用哪一种方式实现图，要看具体情况。
  
    - 邻接矩阵则是一个二维布尔数组，我们权且称为 `matrix`，如果节点 `x` 和 `y` 是相连的，那么就把 `matrix[x][y]` 设为 `true`（上图中绿色的方格代表 `true`）。如果想找节点 `x` 的邻居，去扫一圈 `matrix[x][..]` 就行了。
  
    ```java
    // 邻接表
    // graph[x] 存储 x 的所有邻居节点
    List<Integer>[] graph;
    
    // 邻接矩阵
    // matrix[x][y] 记录 x 是否有一条指向 y 的边
    boolean[][] matrix;
    ```
  
    - **有向加权图怎么实现**？
  
      如果是邻接表，我们不仅仅存储某个节点 `x` 的所有邻居节点，还存储 `x` 到每个邻居的权重，不就实现加权有向图了吗？
  
      如果是邻接矩阵，`matrix[x][y]` 不再是布尔值，而是一个 int 值，0 表示没有连接，其他值表示权重，不就变成加权有向图了吗？
  
      ```java
      // 邻接表
      // graph[x] 存储 x 的所有邻居节点以及对应的权重
      List<int[]>[] graph;
      
      // 邻接矩阵
      // matrix[x][y] 记录 x 指向 y 的边的权重，0 表示不相邻
      int[][] matrix;
      ```
  
    - **无向图怎么实现**？也很简单，所谓的「无向」，是不是等同于==「双向」==？
  
      --> 如果连接无向图中的节点 `x` 和 `y`，把 `matrix[x][y]` 和 `matrix[y][x]` 都变成 `true` 不就行了；邻接表也是类似的操作，在 `x` 的邻居列表里添加 `y`，同时在 `y` 的邻居列表里添加 `x`。
  
  - <u>Degree（度）</u>：在无向图中，「度」就是每个节点相连的边的条数；由于有向图的边有方向，所以有向图中每个节点「度」被细分为**入度**（indegree: 指向该节点的边数）和**出度**（outdegree：指向别的节点的边数）
  
  - <u>建图：</u>
  
    ```python
    from collections import defaultdict
    class Graph:
      def __init__(self, numberofVertices):
            self.graph = defaultdict(list)
            self.numberofVertices = numberofVertices
        
        def addEdge(self, vertex, edge):     # graph implementation: key: vertex; value: edge
            self.graph[vertex].append(edge)
    ```
  

- **图的遍历**

  --> 参考多叉树的 DFS 遍历!

  ```python
  # 多叉树遍历框架
  def dfs(root):
    if not root: return
  	# 前序位置
  	for child in root.children:
      dfs(child)
    # 后序位置
  ```

  ```python
  result = []			# 保存符合条件的所有路径
  path = []				# 起点到终点的路径
  def dfs(参数):
      if (终止条件):
          存放结果
          return
  
      for (选择：本节点所连接的其他节点):
          处理节点
          dfs(图，选择的节点) # 递归
          回溯，撤销处理结果
  ```

  - 图和多叉树最大的区别是，图是可能包含环的，你从图的某一个节点开始遍历，有可能走了一圈又回到这个节点，而树不会出现这种情况，从某个节点出发必然走到叶子节点，绝不可能回到它自身。

    所以，==如果图包含环，遍历框架就要一个 `visited` 数组进行辅助==：

    ```python
    # 记录被遍历过的节点 --> visited 数组就是防止递归重复遍历同一个节点进入死循环的
    visited = set()		
    # 记录从起点到当前节点的路径 --> 用于判断是否成环 [记录当前遍历路径]
    path = set()
    
    # 图遍历框架
    def dfs(graph, s):
      if s in visited: 
        return
      # 经过节点 s，标记为已遍历
      visited.add(s)
      # 做选择：标记节点 s 在路径上
      path.add(s)
      for neighbor in graph.neighbors:
        dfs(graph, neighbor)
      # 撤销选择：节点 s 离开路径
      path.remove(s)
    ```

    - 这个 `Path` 数组的操作很像回溯算法中做「做选择」和「撤销选择」，区别在于位置：回溯算法的「做选择」和「撤销选择」在 for 循环里面，而对 `Path` 数组的操作在 for 循环外面。

      区别：<u>*DFS 算法，关注点在节点*；*回溯算法，关注点在树枝*</u>

    - 如果题目告诉你图中不含环，可以把 `visited` 数组都省掉，基本就是**多叉树的遍历**。

- **岛屿系列题目**

  **岛屿系列题目的核心考点就是用 <u>DFS/BFS 算法遍历二维数组</u>**。--> 如果你把二维矩阵中的每一个位置看做一个节点，这个节点的上下左右四个位置就是相邻节点，那么整个矩阵就可以抽象成一幅网状的「图」结构。

  - dfs第一个要素：**访问相邻节点** 

    <img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220715160740095.png" alt="image-20220715160740095" style="zoom:50%;" />

  - dfs第二个要素：判断Base case

    <img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220715174714014.png" alt="image-20220715174714014" style="zoom:50%;" />

  - 如何避免重复遍历
    网格结构的 DFS 与二叉树的 DFS 最大的不同之处在于，遍历中可能遇到遍历过的结点。这是因为，网格结构本质上是一个「图」，我们可以把每个格子看成图中的结点，每个结点有向上下左右的四条边。在图中遍历时，自然可能遇到重复遍历结点。

    如何避免这样的重复遍历呢？答案是标记已经遍历过的格子。以岛屿问题为例，我们需要在所有值为 1 的陆地格子上做 DFS 遍历。每走过一个陆地格子，就把格子的值改为 2，这样当我们遇到 2 的时候，就知道这是遍历过的格子了。也就是说，每个格子可能取三个值：

    - 0 —— 海洋格子
    - 1 —— 陆地格子（未遍历过）
    - 2 —— 陆地格子（已遍历过）

  ```python
  # 二维矩阵遍历框架
  # Version 1: cannot change the original values in the grid
  dir = [[1, 0], [-1, 0], [0, 1], [0, -1]]
  def dfs(grid, r, c, visited):
    m = len(grid)									# row
    n = len(grid[0])							# column
    
    # base case
    if r < 0 or c < 0 or r >= m or c >= n:		# 超出索引边界(r: row index; c: column index)
      return 
    
    if (r, c) in visited or grid[r][c] != '1':# 已遍历过 (r, c)；如果这个格子不是岛屿，直接返回
      return 
    
    # 进入节点 (r, c)
    visited.add((r, c))
    # 第一种写法
    dfs(grid, r - 1, c, visited)	# 上
    dfs(grid, r + 1, c, visited)	# 下
    dfs(grid, r, c - 1, visited)	# 左
    dfs(grid, r, c + 1, visited)	# 右
    
    # 第二种写法
    for d in dir:
      dfs(grid, r + d[0], c + d[1], visited)
      
  # Version 2: can change the original values in the grid
  dir = [[1, 0], [-1, 0], [0, 1], [0, -1]]
  def dfs(grid, r, c):
    m = len(grid)									# row
    n = len(grid[0])							# column
    
    # base case
    if (r not in range(row) or c not in range(col)):		# 超出索引边界(r: row index; c: column index)
      return 
    
    # 如果这个格子不是岛屿，直接返回
    if grid[r][c] != '1':
      return 
    
    grid[r][c] = '2'							# 将格子标记为「已遍历过」的陆地格子
    for d in dir:
      dfs(grid, r + d[0], c + d[1], visited)
  ```

## 1. 200 [Number of Islands](https://leetcode.com/problems/number-of-islands/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (54.16%) | [`depth-first-search`](https://leetcode.com/tag/depth-first-search); [`breadth-first-search`](https://leetcode.com/tag/breadth-first-search); [`union-find`](https://leetcode.com/tag/union-find) |

Given an `m x n` 2D binary grid `grid` which represents a map of `'1'`s (land) and `'0'`s (water), return *the number of islands*.

An **island** is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

**Example 1:**

```
Input: grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
Output: 1
```

**Example 2:**

```
Input: grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
Output: 3 
```

- **Constraints:**

  - `m == grid.length`

  - `n == grid[i].length`

  - `1 <= m, n <= 300`

  - `grid[i][j]` is `'0'` or `'1'`.

- **Solution**

  本道题目可以直接套用岛屿模板

  ```python
  def numIslands(self, grid: List[List[str]]) -> int:
    visited = set()
    row, col = len(grid), len(grid[0])
    dir = [[1, 0], [-1, 0], [0, 1], [0, -1]]
    
    def dfs(grid, r, c, visited):
      # base case
      if (r not in range(row) or c not in range(col)):
        return
      
      if (grid[r][c] != '1' or (r, c) in visited):
        return
      
      visited.add((r, c))
      # 访问上、下、左、右四个相邻结点
      for d in dir:
        dfs(grid, r + d[0], c + d[1], visited)  
    
    res = 0
    for r in range(row):
      for c in range(col):
        if (grid[r][c] == '1' and (r, c) not in visited):
          res += 1
          dfs(grid, r, c, visited)
    return res
  ```

  - Time complexity：$O(row \times col)$ --> two for-loops

    Space complexity: $O(row \times col)$ --> build a visited set with size of $row \times col$ + 在最坏情况下，整个网格均为陆地，深度优先搜索的深度达到 M**N*

  --> 如果可以修改grid的值

  ```python
  def numIslands(self, grid: List[List[str]]) -> int:
    row, col = len(grid), len(grid[0])
    dir = [[1, 0], [-1, 0], [0, 1], [0, -1]]
    
    def dfs(r, c):
      # base case
      if (r not in range(row) or c not in range(col)):
        return
      
      if (grid[r][c] != '1'):
        return
      
      grid[r][c] = '2'
      # 访问上、下、左、右四个相邻结点
      for d in dir:
        dfs(r + d[0], c + d[1])  
    
    res = 0
    for r in range(row):
      for c in range(col):
        if (grid[r][c] == '1'):
          res += 1
          dfs(r, c)
    return res
  ```

  - Time complexity：$O(row \times col)$ --> two for-loops

    Space complexity: $O(row \times col)$ --> 在最坏情况下，整个网格均为陆地，深度优先搜索的深度达到 $O(row \times col)$

## 2. 695 [Max Area of Island](https://leetcode.com/problems/max-area-of-island/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (69.90%) | [`array`](https://leetcode.com/tag/array); depth-first-search |

You are given an `m x n` binary matrix `grid`. An island is a group of `1`'s (representing land) connected **4-directionally** (horizontal or vertical.) You may assume all four edges of the grid are surrounded by water.

The **area** of an island is the number of cells with a value `1` in the island.

Return *the maximum **area** of an island in* `grid`. If there is no island, return `0`.

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2021/05/01/maxarea1-grid.jpg" alt="img" style="zoom:50%;" />

```
Input: grid = [[0,0,1,0,0,0,0,1,0,0,0,0,0],[0,0,0,0,0,0,0,1,1,1,0,0,0],[0,1,1,0,1,0,0,0,0,0,0,0,0],[0,1,0,0,1,1,0,0,1,0,1,0,0],[0,1,0,0,1,1,0,0,1,1,1,0,0],[0,0,0,0,0,0,0,0,0,0,1,0,0],[0,0,0,0,0,0,0,1,1,1,0,0,0],[0,0,0,0,0,0,0,1,1,0,0,0,0]]
Output: 6
Explanation: The answer is not 11, because the island must be connected 4-directionally.
```

**Example 2:**

```
Input: grid = [[0,0,0,0,0,0,0,0]]
Output: 0 
```

- **Constraints:**

  - `m == grid.length`

  - `n == grid[i].length`

  - `1 <= m, n <= 50`

  - `grid[i][j]` is either `0` or `1`.

- **Thoughts**

  - 这道题目只需要对每个岛屿做 DFS 遍历，求出每个岛屿的面积就可以了。求岛屿面积的方法也很简单，代码如下，每遍历到一个格子，就把面积加一。

    ```python
    return (1 + dfs(r + 1, c) +
            dfs(r - 1, c) +
            dfs(r, c + 1) +
            dfs(r, c - 1))
    ```

- **Solution**

  ```python
  # dfs: traverse through each adjacent cell and calculate the area; return area
  # main function: compare the current area of the island with maxAres
      def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
          maxArea = 0
          row, col = len(grid), len(grid[0])
          visited = set()  # store visited cells
  
          def dfs(r, c):
              # base case
              if (r not in range(row) or c not in range(col)):
                  return 0
              
              if (grid[r][c] != 1 or (r, c) in visited):
                  return 0
              
              visited.add((r, c))
              return (1 + dfs(r + 1, c) +
                          dfs(r - 1, c) +
                          dfs(r, c + 1) +
                          dfs(r, c - 1))
  
          for r in range(row):
              for c in range(col):
                  if grid[r][c] == 1:
                      maxArea = max(maxArea, dfs(r, c))
          return maxArea
  ```

  - Time complexity：$O(row \times col)$ --> two for-loops

    Space complexity: $O(row \times col)$ 

## 3. 1254 [Number of Closed Islands](https://leetcode.com/problems/number-of-closed-islands/description/)

|  Category  |   Difficulty    |                     Tags                      |
| :--------: | :-------------: | :-------------------------------------------: |
| algorithms | Medium (63.47%) | [`Unknown`](https://leetcode.com/tag/Unknown) |

Given a 2D `grid` consists of `0s` (land) and `1s` (water). An *island* is a maximal 4-directionally connected group of `0s` and a *closed island* is an island **totally** (all left, top, right, bottom) surrounded by `1s.`

Return the number of *closed islands*.

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2019/10/31/sample_3_1610.png" alt="img" style="zoom: 67%;" />

```
Input: grid = [[1,1,1,1,1,1,1,0],[1,0,0,0,0,1,1,0],[1,0,1,0,1,1,1,0],[1,0,0,0,0,1,0,1],[1,1,1,1,1,1,1,0]]
Output: 2
Explanation: 
Islands in gray are closed because they are completely surrounded by water (group of 1s).
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2019/10/31/sample_4_1610.png)

```
Input: grid = [[0,0,1,0,0],[0,1,0,1,0],[0,1,1,1,0]]
Output: 1
```

**Example 3:**

```
Input: grid = [[1,1,1,1,1,1,1],
               [1,0,0,0,0,0,1],
               [1,0,1,1,1,0,1],
               [1,0,1,0,1,0,1],
               [1,0,1,1,1,0,1],
               [1,0,0,0,0,0,1],
               [1,1,1,1,1,1,1]]
Output: 2
```

- **Constraints:**

  - `1 <= grid.length, grid[0].length <= 100`

  - `0 <= grid[i][j] <=1`

- **Thoughts**

  - 力扣第 1254 题「 [统计封闭岛屿的数目](https://leetcode.cn/problems/number-of-closed-islands/)」和之前的题有两点不同：

    1、用 `0` 表示陆地，用 `1` 表示海水。

    2、让你计算「封闭岛屿」的数目。所谓「封闭岛屿」就是上下左右全部被 `1` 包围的 `0`，也就是说**靠边的陆地不算作「封闭岛屿」**。

    --> **判断「封闭岛屿」：*<u>把那些靠边的岛屿排除掉</u>*，剩下的就是「封闭岛屿」了**

- **Solution**

  ```python
  # 0: land; 1: water: closed island: 0 surrounded by 1
      # S1: add islands on top and bottom, left and right to visited --> not closed islands
      # S2: for the rest, traverse and each island is a closed island
      def closedIsland(self, grid: List[List[int]]) -> int:
          visited = set()
          row, col = len(grid), len(grid[0])
          dir = [[1, 0], [-1, 0], [0, 1], [0, -1]]  # up, down, right, left
  
          # traverse through the four directions to find an island
          def dfs(r, c):
              # base case
              if (r not in range(row) or c not in range(col)):
                  return
              
              if (grid[r][c] != 0 or (r, c) in visited):
                  return
              
              visited.add((r, c))
              for d in dir:
                  dfs(r + d[0], c + d[1])
              
          # dfs(r, c)
          # remove left and right islands on each row
          for r in range(row):
              dfs(r, 0)
              dfs(r, col - 1)
          
          # remove top and bottom islands on each column
          for c in range(col):
              dfs(0, c)
              dfs(row - 1, c)
          
          res = 0
          for r in range(row):
              for c in range(col):
                  if (grid[r][c] == 0 and (r, c) not in visited):
                      res += 1
                      dfs(r, c)
          return res
  ```

  - Time complexity：$O(row \times col)$ --> two for-loops

    Space complexity: $O(row \times col)$ 

## 4. 1020 [Number of Enclaves](https://leetcode.com/problems/number-of-enclaves/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (63.11%) | [`array`](https://leetcode.com/tag/array);  [`dynamic-programming`](https://leetcode.com/tag/dynamic-programming); [`sliding-window`](https://leetcode.com/tag/sliding-window) |

You are given an `m x n` binary matrix `grid`, where `0` represents a sea cell and `1` represents a land cell.

A **move** consists of walking from one land cell to another adjacent (**4-directionally**) land cell or walking off the boundary of the `grid`.

Return *the number of land cells in* `grid` *for which we cannot walk off the boundary of the grid in any number of **moves***. 

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2021/02/18/enclaves1.jpg" alt="img" style="zoom:50%;" />

```
Input: grid = [[0,0,0,0],[1,0,1,0],[0,1,1,0],[0,0,0,0]]
Output: 3
Explanation: There are three 1s that are enclosed by 0s, and one 1 that is not enclosed because its on the boundary.
```

**Example 2:**

<img src="https://assets.leetcode.com/uploads/2021/02/18/enclaves2.jpg" alt="img" style="zoom:50%;" />

```
Input: grid = [[0,1,1,0],[0,0,1,0],[0,0,1,0],[0,0,0,0]]
Output: 0
Explanation: All 1s are either on the boundary or can reach the boundary.
```

- **Constraints:**

  - `m == grid.length`

  - `n == grid[i].length`

  - `1 <= m, n <= 500`

  - `grid[i][j]` is either `0` or `1`.

- **Thoughts**

  - 从题意可得，该题目是求 the sum of area of closed islands（封闭岛屿的面积总和）--> combine 695. and 1254.

- **Python**

  ```python
  def numEnclaves(self, grid: List[List[int]]) -> int:
          visited = set()
          row, col = len(grid), len(grid[0])
  
          # traverse and return the area of the closed islands
          def dfs(r, c):
              if (r not in range(row) or c not in range(col)):
                  return 0
              
              if (grid[r][c] != 1 or (r, c) in visited):
                  return 0
              
              visited.add((r, c))
              return (1 + dfs(r + 1, c) +
                          dfs(r - 1, c) +
                          dfs(r, c + 1) + 
                          dfs(r, c - 1))            
  
          for r in range(row):
              dfs(r, 0)
              dfs(r, col - 1)
          
          for c in range(col):
              dfs(0, c)
              dfs(row - 1, c)
          
          res = 0
          for r in range(row):
              for c in range(col):
                  if (grid[r][c] == 1 and (r, c) not in visited):
                      res += dfs(r, c)
          return res
  ```

  - Time complexity：$O(row \times col)$ --> two for-loops

    Space complexity: $O(row \times col)$ 

## 5. 133 [Clone Graph](https://leetcode.com/problems/clone-graph/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (48.09%) | [`depth-first-search`](https://leetcode.com/tag/depth-first-search); [`breadth-first-search`](https://leetcode.com/tag/breadth-first-search); [`graph`](https://leetcode.com/tag/graph) |

Given a reference of a node in a **[connected](https://en.wikipedia.org/wiki/Connectivity_(graph_theory)#Connected_graph)** undirected graph.

Return a [**deep copy**](https://en.wikipedia.org/wiki/Object_copying#Deep_copy) (clone) of the graph.

Each node in the graph contains a value (`int`) and a list (`List[Node]`) of its neighbors.

```python
class Node {
    public int val;
    public List<Node> neighbors;
}
```

**Test case format:**

For simplicity, each node's value is the same as the node's index (1-indexed). For example, the first node with `val == 1`, the second node with `val == 2`, and so on. The graph is represented in the test case using an adjacency list.

**An adjacency list** is a collection of unordered **lists** used to represent a finite graph. Each list describes the set of neighbors of a node in the graph.

The given node will always be the first node with `val = 1`. You must return the **copy of the given node** as a reference to the cloned graph.

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2019/11/04/133_clone_graph_question.png" alt="img" style="zoom: 15%;" />

```
Input: adjList = [[2,4],[1,3],[2,4],[1,3]]
Output: [[2,4],[1,3],[2,4],[1,3]]
Explanation: There are 4 nodes in the graph.
1st node (val = 1)'s neighbors are 2nd node (val = 2) and 4th node (val = 4).
2nd node (val = 2)'s neighbors are 1st node (val = 1) and 3rd node (val = 3).
3rd node (val = 3)'s neighbors are 2nd node (val = 2) and 4th node (val = 4).
4th node (val = 4)'s neighbors are 1st node (val = 1) and 3rd node (val = 3).
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2020/01/07/graph.png)

```
Input: adjList = [[]]
Output: [[]]
Explanation: Note that the input contains one empty list. The graph consists of only one node with val = 1 and it does not have any neighbors.
```

**Example 3:**

```
Input: adjList = []
Output: []
Explanation: This an empty graph, it does not have any nodes.
```

- **Constraints:**

  - The number of nodes in the graph is in the range `[0, 100]`.

  - `1 <= Node.val <= 100`

  - `Node.val` is unique for each node.

  - There are no repeated edges and no self-loops in the graph.

  - The Graph is connected and all nodes can be visited starting from the given node.

- **Thoughts**

  - 我们需要明确图的深拷贝是在做什么 --> 

    <u>Deep copy:</u>，对于一张图而言，它的深拷贝即构建一张与原图结构，值均一样的图，但是其中的节点不再是原来图节点的引用。因此，为了深拷贝出整张图，我们需要知道整张图的结构以及对应节点的值。由于题目只给了我们一个节点的引用，因此为了知道整张图的结构以及对应节点的值，我们需要**从给定的节点出发，进行「图的遍历」，并在遍历的过程中完成图的深拷贝**。--> ==**遍历图 --> depth-first-search**==

  - 为了避免在深拷贝时陷入死循环，我们需要理解图的结构。对于一张无向图，任何给定的无向边都可以表示为两个有向边，即如果节点 A 和节点 B 之间存在无向边，则表示该图具有从节点 A 到节点 B 的有向边和从节点 B 到节点 A 的有向边。

    <img src="https://assets.leetcode-cn.com/solution-static/133/1.png" alt="fig1" style="zoom: 33%;" />

    为了防止多次遍历同一个节点，陷入死循环，我们需要用一种数据结构记录已经被克隆过的节点。--> ==**Hashmap --> key: old node; val: new copy node**==

    <font color=red>**Hashmap and dfs are used almost in every graph problem!!**</font>

  - <u>Procedures:</u>
    - S1: build a hashmap to map from old nodes to new nodes
    - S2: for each old node, make a copy (add `val`)
    - S3: run the function recursively to copy all neighbor nodes (add 'neighbors')

- **Solution**

  ```python
  class Node:
      def __init__(self, val = 0, neighbors = None):
          self.val = val
          self.neighbors = neighbors if neighbors is not None else []
          
  def cloneGraph(self, node: 'Node') -> 'Node':
          if not node:
              return 
          oldToNew = {}
  
          def dfs(node):
              #  如果该节点已经被访问过了，则直接从哈希表中取出对应的克隆节点返回
              if node in oldToNew:  # already make a clone of the node --> return the copy node
                  return oldToNew[node]
              
              # if the node hasn't been cloned
              # 克隆节点，注意到为了深拷贝我们不会克隆它的邻居的列表
              copy = Node(node.val)
              oldToNew[node] = copy
              # 遍历该节点的邻居并更新克隆节点的邻居列表
              for neighbor in node.neighbors:
                  copy.neighbors.append(dfs(neighbor))   # append all cloned new nodes in neighbors
              return copy
  
          return dfs(node)
  ```
  
  - Time complexity：O(N) --> 其中 N 表示节点数量。深度优先搜索遍历图的过程中每个节点只会被访问一次。
  
    Space complexity: O(N) --> 存储克隆节点和原节点的哈希表需要 O(N) 的空间，递归调用栈需要 O(H) 的空间，其中 H 是图的深度，经过放缩可以得到 O(H) = O(N)，因此总体空间复杂度为O(N)。

## 6. 417 [Pacific Atlantic Water Flow](https://leetcode.com/problems/pacific-atlantic-water-flow/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (49.58%) | [`depth-first-search`](https://leetcode.com/tag/depth-first-search); [`breadth-first-search`](https://leetcode.com/tag/breadth-first-search) |

There is an `m x n` rectangular island that borders both the **Pacific Ocean** and **Atlantic Ocean**. The **Pacific Ocean** touches the island's left and top edges, and the **Atlantic Ocean** touches the island's right and bottom edges.

The island is partitioned into a grid of square cells. You are given an `m x n` integer matrix `heights` where `heights[r][c]` represents the **height above sea level** of the cell at coordinate `(r, c)`.

The island receives a lot of rain, and the rain water can flow to neighboring cells directly north, south, east, and west if the neighboring cell's height is **less than or equal to** the current cell's height. Water can flow from any cell adjacent to an ocean into the ocean.

Return *a **2D list** of grid coordinates* `result` *where* `result[i] = [ri, ci]` *denotes that rain water can flow from cell* `(ri, ci)` *to **both** the Pacific and Atlantic oceans*.

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2021/06/08/waterflow-grid.jpg" alt="img" style="zoom:50%;" />

```
Input: heights = [[1,2,2,3,5],[3,2,3,4,4],[2,4,5,3,1],[6,7,1,4,5],[5,1,1,2,4]]
Output: [[0,4],[1,3],[1,4],[2,2],[3,0],[3,1],[4,0]]
```

**Example 2:**

```
Input: heights = [[2,1],[1,2]]
Output: [[0,0],[0,1],[1,0],[1,1]]
```

- **Constraints:**

  - `m == heights.length`

  - `n == heights[r].length`

  - `1 <= m, n <= 200`

  - `0 <= heights[r][c] <= 105`

- **Thoughts**

  - 雨水的流动方向是从高到低，每个单元格上的雨水只能流到高度小于等于当前单元格的相邻单元格。如果直接以每个单元格作为起点模拟雨水的流动，则会重复遍历每个单元格，导致时间复杂度过高。为了降低时间复杂度，可以==从矩阵的边界开始反向搜索寻找雨水流向边界的单元格==，反向搜索时，每次只能移动到高度相同或更大的单元格。--> 对于一个点它能流动两边的大洋，那么反过来，两边大洋的水反着流就能达到这个点

  - 由于矩阵的左边界和上边界是太平洋，矩阵的右边界和下边界是大西洋，因此从矩阵的左边界和上边界开始反向搜索即可找到雨水流向太平洋的单元格，从矩阵的右边界和下边界开始反向搜索即可找到雨水流向大西洋的单元格。

    判断是否能达到点的条件: heights[r][c] >= prevHeights

  - S1: find all cells reachable from Pacific Ocean (left and top)

    S2: find all cells reachable from Atlantic Ocean (right and bottom)

    S3: find the unions of the two results

- **Solution**

  ```python
  def pacificAtlantic(self, heights: List[List[int]]) -> List[List[int]]:
          row, col = len(heights), len(heights[0])
          pac, atl = set(), set()   # store valid (ri, ci) 
          dir = [[1, 0], [-1, 0], [0, 1], [0, -1]]
  
          def dfs(r, c, ocean, prevHeight):
              # base case
              if r < 0 or c < 0 or r >= row or c >= col:
                  return
              
              if (heights[r][c] < prevHeight or (r, c) in ocean):
                  return
              
              ocean.add((r, c))
              for d in dir:
                  dfs(r + d[0], c + d[1], ocean, heights[r][c])
          
          # check available cells based on row
          for r in range(row):
              dfs(r, 0, pac, heights[r][0])							 # left: Pacific Ocean
              dfs(r, col - 1, atl, heights[r][col - 1])	 # right: Atlantic Ocean
          
          for c in range(col):	
              dfs(0, c, pac, heights[0][c])								# top: Pacific Ocean
              dfs(row - 1, c, atl, heights[row - 1][c])		# botoom: Atlantic Ocean
          
          res = []
          for r in range(row):
              for c in range(col):
                  if (r, c) in pac and (r, c) in atl:
                      res.append((r, c))
          return res
  ```

  - Time complexity：$O(row \times col)$ --> two for-loops

    Space complexity: $O(row \times col)$ --> set memory + stack memory for recursion

## 7. 130 [Surrounded Regions](https://leetcode.com/problems/surrounded-regions/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (34.23%) | [Surrounded Regions](https://leetcode.com/problems/surrounded-regions/description/); [`breadth-first-search`](https://leetcode.com/tag/breadth-first-search); [`union-find`](https://leetcode.com/tag/union-find) |

Given an `m x n` matrix `board` containing `'X'` and `'O'`, *capture all regions that are 4-directionally surrounded by* `'X'`.

A region is **captured** by flipping all `'O'`s into `'X'`s in that surrounded region.

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2021/02/19/xogrid.jpg" alt="img" style="zoom:50%;" />

```
Input: board = [["X","X","X","X"],["X","O","O","X"],["X","X","O","X"],["X","O","X","X"]]
Output: [["X","X","X","X"],["X","X","X","X"],["X","X","X","X"],["X","O","X","X"]]
Explanation: Notice that an 'O' should not be flipped if:
- It is on the border, or
- It is adjacent to an 'O' that should not be flipped.
The bottom 'O' is on the border, so it is not flipped.
The other three 'O' form a surrounded region, so they are flipped.
```

**Example 2:**

```
Input: board = [["X"]]
Output: [["X"]] 
```

- **Constraints:**

  - `m == board.length`

  - `n == board[i].length`

  - `1 <= m, n <= 200`

  - `board[i][j]` is `'X'` or `'O'`.

- **Thoughts**

  - 这道题思想类似于找“封闭岛屿”问题，本质需要我们将内部的封闭“岛屿”全部修改，而处于边界或者与边界有“上下左右联系”的模块保持‘O‘不变

- **Solution**

  ```python
  def solve(self, board: List[List[str]]) -> None:
          """
          Do not return anything, modify board in-place instead.
          """
          visited = set()
          row, col = len(board), len(board[0])
          dir = [[1, 0], [-1, 0], [0, 1], [0, -1]]
  
          def dfs(r, c):
              if r < 0 or c < 0 or r >= row or c >= col:
                  return
              
              if board[r][c] == 'X' or (r, c) in visited:
                  return
              
              visited.add((r, c))
              for d in dir:
                  dfs(r + d[0], c + d[1])
  
          # remove boarder examples
          for r in range(row):    
              dfs(r, 0)           # left border
              dfs(r, col - 1)     # right border
          
          for c in range(col):
              dfs(0, c)           # top border
              dfs(row - 1, c)     # bottom border
          
          for r in range(row):
              for c in range(col):
                  if board[r][c] == 'O' and (r, c) not in visited:
                      board[r][c] = 'X'				# 注意是在main function里进行修改而不是在dfs里！
                      dfs(r, c)
  ```

  - Time complexity：$O(row \times col)$ --> two for-loops

    Space complexity: $O(row \times col)$ --> set memory + stack memory for recursion

## 8. 797 [All Paths From Source to Target](https://leetcode.com/problems/all-paths-from-source-to-target/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (80.93%) | [`hash-table`](https://leetcode.com/tag/hash-table); [`math`](https://leetcode.com/tag/math) |

Given a directed acyclic graph (**DAG**) of `n` nodes labeled from `0` to `n - 1`, find all possible paths from node `0` to node `n - 1` and return them in **any order**.

The graph is given as follows: `graph[i]` is a list of all nodes you can visit from node `i` (i.e., there is a directed edge from node `i` to node `graph[i][j]`).

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2020/09/28/all_1.jpg" alt="img" style="zoom:50%;" />

```
Input: graph = [[1,2],[3],[3],[]]
Output: [[0,1,3],[0,2,3]]
Explanation: There are two paths: 0 -> 1 -> 3 and 0 -> 2 -> 3.
```

**Example 2:**

<img src="https://assets.leetcode.com/uploads/2020/09/28/all_2.jpg" alt="img" style="zoom:50%;" />

```
Input: graph = [[4,3,1],[3,2,4],[3],[4],[]]
Output: [[0,4],[0,3,4],[0,1,3,4],[0,1,2,3,4],[0,1,4]]
```

- **Constraints:**

  - `n == graph.length`

  - `2 <= n <= 15`

  - `0 <= graph[i][j] < n`

  - `graph[i][j] != i` (i.e., there will be no self-loops).

  - All the elements of `graph[i]` are **unique**.

  - The input graph is **guaranteed** to be a **DAG**.

- **Thoughts**

  - 由于题目确定了该图不包含环，所以这道题可以用多叉树的dfs遍历解决 --> **以 `0` 为起点遍历图，同时记录遍历过的路径，当遍历到终点 (n - 1) 时将路径记录下来即可**
  - 这道题和Backtracking笔记里的排列题和17 [Letter Combinations of a Phone Number](https://leetcode.com/problems/letter-combinations-of-a-phone-number/description/)接近。

- **Solution**

  ```python
  def allPathsSourceTarget(self, graph: List[List[int]]) -> List[List[int]]:
    res = []
    path = []
  
    def dfs(node):          # node: the current node for traversal
      # base case: 当目前遍历的节点为最后一个节点的时候，就找到了一条从出发点到终止点的路径
      if node == len(graph) - 1: # 要求从节点0到节点n-1的路径并输出，所以是len(graph)-1
        res.append(path[:])
        return
  
      for i in range(len(graph[node])): # 遍历节点n连接的所有邻居节点
        path.append(graph[node][i])
        dfs(graph[node][i])
        path.pop()
  
     path.append(0)           # 无论什么路径都是从0节点出发
     dfs(0)                   # 开始遍历
     return res
  ```

  - Time complexity：$O(n \times 2^n)$ --> each node has two conditions: in the path/not in the path, the total possible number of paths is $2^n$; the length of each path is n

    Space complexity: $O(n)$ --> stack memory for recursion + path

## 9. 207 [Course Schedule](https://leetcode.com/problems/course-schedule/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (45.08%) | [`depth-first-search`](https://leetcode.com/tag/depth-first-search); [`breadth-first-search`](https://leetcode.com/tag/breadth-first-search); [`graph`](https://leetcode.com/tag/graph); [`topological-sort`](https://leetcode.com/tag/topological-sort) |

There are a total of `numCourses` courses you have to take, labeled from `0` to `numCourses - 1`. You are given an array `prerequisites` where `prerequisites[i] = [ai, bi]` indicates that you **must** take course `bi` first if you want to take course `ai`.

- For example, the pair `[0, 1]`, indicates that to take course `0` you have to first take course `1`.

Return `true` if you can finish all courses. Otherwise, return `false`.

**Example 1:**

```
Input: numCourses = 2, prerequisites = [[1,0]]
Output: true
Explanation: There are a total of 2 courses to take. 
To take course 1 you should have finished course 0. So it is possible.
```

**Example 2:**

```
Input: numCourses = 2, prerequisites = [[1,0],[0,1]]
Output: false
Explanation: There are a total of 2 courses to take. 
To take course 1 you should have finished course 0, and to take course 0 you should also have finished course 1. So it is impossible.
```

- **Constraints:**

  - `1 <= numCourses <= 2000`

  - `0 <= prerequisites.length <= 5000`

  - `prerequisites[i].length == 2`

  - `0 <= ai, bi < numCourses`

  - All the pairs prerequisites[i] are **unique**.

- **Thoughts**

  - This problem is equivalent to finding if a cycle exists in a directed graph. If a cycle exists, no topological ordering exists and therefore it will be impossible to take all courses. --> <font color=blue>**Topological Sort**</font>
  - <u>Procedures:</u>
    - S1: create a graph --> key: each course; value: prerequisites; finish mapping
    - S2: create a visited set to track the nodes; create a path set to track the path (start on current node)
    - S3: dfs --> base case: detect a loop 
    - S4: conduct backtracking in graphs (each prerequisite is like a neighbor node)
    - S5: run dfs on each course

- **Solution**

  ```python
  from collections import defaultdict
  def canFinish(self, numCourses: int, prerequisites: List[List[int]]) -> bool:
    graph = defaultdict(list)
    for course, pre in prerequisites:	# point from pre to course
      graph[pre].append(course)
    
    visited = set()										# avoid visiting a node repeatedly	
    path = set()											# check if the graph has a cycle
    self.hasCycle = False
    def dfs(pre):
      if pre in visited or self.hasCycle:
        return
      if pre in path:
        hasCycle = True
      visited.add(pre)
      path.add(pre)
      for crs in graph[pre]:
        dfs(crs)
      path.remove(pre)								  
    
    # As not all nodes in the graph are connected --> treat every node as a start to use DFS
    for pre in range(numCourses):     # O(V + E)
      dfs(pre)
    return not self.hasCycle
  ```

  - Time complexity：$O(V + E)$ --> V为先修课程的要求数，E为课程数

    Space complexity: $O(V + E)$ --> the two lists created will insert all vertices and edges + stack memory for recursion (O(V))

## 10. 210 [Course Schedule II](https://leetcode.com/problems/course-schedule-ii/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (46.74%) | [`depth-first-search`](https://leetcode.com/tag/depth-first-search); [`breadth-first-search`](https://leetcode.com/tag/breadth-first-search); [`graph`](https://leetcode.com/tag/graph); [`topological-sort`](https://leetcode.com/tag/topological-sort) |

There are a total of `numCourses` courses you have to take, labeled from `0` to `numCourses - 1`. You are given an array `prerequisites` where `prerequisites[i] = [ai, bi]` indicates that you **must** take course `bi` first if you want to take course `ai`.

- For example, the pair `[0, 1]`, indicates that to take course `0` you have to first take course `1`.

Return *the ordering of courses you should take to finish all courses*. If there are many valid answers, return **any** of them. If it is impossible to finish all courses, return **an empty array**.

**Example 1:**

```
Input: numCourses = 2, prerequisites = [[1,0]]
Output: [0,1]
Explanation: There are a total of 2 courses to take. To take course 1 you should have finished course 0. So the correct course order is [0,1].
```

**Example 2:**

```
Input: numCourses = 4, prerequisites = [[1,0],[2,0],[3,1],[3,2]]
Output: [0,2,1,3]
Explanation: There are a total of 4 courses to take. To take course 3 you should have finished both courses 1 and 2. Both courses 1 and 2 should be taken after you finished course 0.
So one correct course order is [0,1,2,3]. Another correct ordering is [0,2,1,3].
```

**Example 3:**

```
Input: numCourses = 1, prerequisites = []
Output: [0]
```

- **Constraints:**

  - `1 <= numCourses <= 2000`

  - `0 <= prerequisites.length <= numCourses * (numCourses - 1)`

  - `prerequisites[i].length == 2`

  - `0 <= ai, bi < numCourses`

  - `ai != bi`

  - All the pairs `[ai, bi]` are **distinct**.

- **Thoughts**

  - 这道题目和207 [Course Schedule]基本一样，区别在于207是判断是否有环，210是先判断是否有环，无环的话遍历图
  - <font color=blue>**关键点：**</font>在topologial sort里按顺序遍历：`res.insert(0, pre)`

- **Solution**

  ```python
  # main goal: traverse through a graph with no cycles
  def findOrder(self, numCourses: int, prerequisites: List[List[int]]) -> List[int]:
          graph = defaultdict(list)
          for crs, pre in prerequisites:						
              graph[pre].append(crs)
          
          visited = set()
          path = set()
          self.hasCycle = False
          res = []
          def dfs(pre):
              if pre in path:
                  self.hasCycle = True 
              if pre in visited or self.hasCycle:
                  return
              visited.add(pre)
              path.add(pre)
              for course in graph[pre]:
                  dfs(course)
              res.insert(0, pre)
              path.remove(pre)
          
          for node in range(numCourses):
              dfs(node)
          
          if self.hasCycle:
              return []
          return res
  ```

  - Time complexity：$O(V + E)$ --> V为先修课程的要求数，E为课程数

    Space complexity: $O(V + E)$ --> the two lists created will insert all vertices and edges + stack memory for recursion (O(V))
