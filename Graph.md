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
  visited = set()
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

- **Union Find 并查集**

  --> 主要是解决图论中「动态连通性」问题 (可以抽象成给一幅图连线)

  - Union-Find 算法主要需要实现这几个 API：

    ```python
    class UF:
      # 将 p 和 q 连接 --> 合并两个元素为同一个根节点 **
      def union(p, q): 
      # 判断 p 和 q 是否连通 
      def connected(p, q):
      # 返回图中有多少个连通分量 (Connected Component)
      def count():
      # 找到某个元素的根节点，返回根节点的值 **
      def find(p):
    ```

    这里所说的「连通」是一种等价关系，也就是说具有如下三个性质：

    1、自反性：节点 `p` 和 `p` 是连通的。

    2、对称性：如果节点 `p` 和 `q` 连通，那么 `q` 和 `p` 也连通。

    3、传递性：如果节点 `p` 和 `q` 连通，`q` 和 `r` 连通，那么 `p` 和 `r` 也连通。

    判断这种「等价关系」非常实用，比如说编译器判断同一个变量的不同引用，比如社交网络中的朋友圈计算等等

    - 并查集可以解决什么问题呢？

      主要就是集合问题，两个节点在不在一个集合，也可以将两个节点添加到一个集合中

  - <u>基本思路：</u>使用森林（若干棵树）来表示图的动态连通性，用数组来具体实现这个森林

    --> 我们设定树的每个节点有一个指针指向其父节点，如果是根节点的话，这个指针指向自己（也就是没有相互连通时）。

    <img src="https://labuladong.github.io/algo/images/unionfind/3.jpg" alt="img" style="zoom:50%;" />

    **如果某两个节点被连通，则让其中的（任意）一个节点的根节点接到另一个节点的根节点上**：

    <img src="https://labuladong.github.io/algo/images/unionfind/4.jpg" alt="img" style="zoom:50%;" />

    **这样，如果节点 `p` 和 `q` 连通的话，它们一定拥有相同的根节点**：

    <img src="https://labuladong.github.io/algo/images/unionfind/5.jpg" alt="img" style="zoom:50%;" />

    - 数组存放的是对应数的根节点，也代表对应的数: `root = [0, 1, 2, 3, 4, 5]`

    ```python
    class UnionFind:
      def __init__(self, grid):
        row, col = len(grid), len(grid[0])
        n = row * col									# 构造函数，n 为图的节点总数
        self.count = n								# number of connected components
        # self.parent = [-1] * n
        # for i in range(n):    				# initialization --> 父节点指针初始指向自己
          # self.parent[i] = i
        self.parent = list(range(n))
          
      # find the root of x
      def find(self, x):
        # Original version
        # while (self.parent[x] != x):
          # x = self.parent[x]
        # return x
      
      	# Optimized version
        if x != self.parent[x]:
          self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
      
      # S1: find the roots of the two nodes
      # S2: if the roots are different, make x's root be y's root --> connect
      def union(x, y):
        rootX = self.find(x)
        rootY = self.find(y)
        if rootX != rootY:
          # 将两棵树合并为一棵
          self.parent[rootX] = rootY	   # or: self.root[rootY] = rootX
          self.count -= 1							 # 两个分量合二为一
          
      def connected(x, y):
        rootX = self.find(x)
        rootY = self.find(y)
        return rootX == rootY
    ```

    - 主要 API `connected` 和 `union` 中的复杂度都是 `find` 函数造成的，所以说它们的复杂度和 `find` 一样。`find` 主要功能就是<font color=blue>**从某个节点向上遍历到树根**</font>，其时间复杂度就是树的高度。我们可能习惯性地认为树的高度就是 `logN`，但这并不一定。`logN` 的高度只存在于平衡二叉树，对于一般的树可能出现极端不平衡的情况，使得「树」几乎退化成「链表」，树的高度最坏情况下可能变成 `N`。--> `find` , `union` , `connected` 的时间复杂度都是 O(N)

  - <u>平衡性优化</u>：  `union` 过程可能出现不平衡现象 --> 简单粗暴地把 `p` 所在的树接到 `q` 所在的树的根节点下面，那么这里就可能出现「头重脚轻」的不平衡状况 <font color=blue>**[Quick Union]**</font>

    <img src="https://labuladong.github.io/algo/images/unionfind/7.jpg" alt="img" style="zoom:50%;" />

    --> 长此以往，树可能生长得很不平衡。**我们其实是希望，小一些的树接到大一些的树下面，这样就能避免头重脚轻，更平衡一些**。解决方法是额外使用一个 `rank` 数组，记录每棵树包含的节点数，我们不妨称为「重量」：

    ```python
    # [Quick union] 权重法
    def __init__(self, grid):
        row, col = len(grid), len(grid[0])
        n = row * col									# 构造函数，n 为图的节点总数
        self.parent = [-1] * n
        self.rank = [1] * n						# 记录每一组树的高度
        self.count = n
        for i in range(n):    				# initialization --> 父节点指针初始指向自己
          self.parent[i] = i
          self.size[i] = 1						# 最初每棵树只有一个节点，重量应该初始化为1
    
    def union(x, y):
        rootX = self.find(x)
        rootY = self.find(y)
        if rootX != rootY:
          # 将两棵树合并为一棵, 小树接到大树下面，较平衡
          if self.rank[rootX] > self.rank[rootY]:			# 此时Y是小树，将y所在的小树接到x所在大树的根节点下
            self.parent[rootY] = rootX
            self.rank[rootX] += self.rank[rootY]
          else:																				# 此时X是小树
            self.parent[rootX] = rootY
            self.rank[rootY] += self.rank[rootX]
          self.count -= 1							 # 两个分量合二为一
    ```

    这样，通过比较树的重量，就可以保证树的生长相对平衡，树的高度大致在 `logN` 这个数量级，极大提升执行效率。此时，`find` , `union` , `connected` 的时间复杂度都下降为 O(logN)，即便数据规模上亿，所需时间也非常少。

    - <u>路径压缩</u>: 进一步压缩每棵树的高度，使树高始终保持为常数 --> 这样每个节点的父节点就是整棵树的根节点，`find` 就能以 O(1) 的时间找到某一节点的根节点，相应的，`connected` 和 `union` 复杂度都下降为 $O(\alpha(n))$。<font color=blue>**[Quick Find]**</font>

  
    第一种方法：
  
    <img src="https://labuladong.github.io/algo/images/unionfind/9.gif" alt="img" style="zoom:50%;" />
  
    第二种方法：
  
    <img src="https://labuladong.github.io/algo/images/unionfind/10.jpeg" alt="img" style="zoom:50%;" />
  
    ```python
    def find(self, x):
        while (self.parent[x] != x):
          self.parent[x] = self.parent[self.parent[x]]			# 路径压缩：每次 while 循环都会把一对儿父子节点改到同一层，这样每次调用 find 函数向树根遍历的同时，顺手就将树高缩短了
          x = self.parent[x]
        return x
    
    # 先找到根节点，然后把 x 到根节点之间的所有节点直接接到根节点下面
    ## [Quick Find]
    def find(self, x):
        if x != self.parent[x]:
          self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
    ```
  
  - <u>Summary Coding Template:</u>
  
    ```python
    class UnionFind:
      def __init__(self, grid):
        row, col = len(grid), len(grid[0])
        n = row * col									# 构造函数，n 为图的节点总数
        self.count = n								# number of connected components
        parent = list(range(n))				# initialization --> 父节点指针初始指向自己
          
      # [Quick find]: path compression --> flatten the tree
      # find the root of x
      def find(self, x):
        if x != self.parent[x]:
          self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
      
      # [Quick union --> union by rank]
      # S1: find the roots of the two nodes
      # S2: if the roots are different, make x's root be y's root --> connect
      def union(x, y):
        rootX = self.find(x)
        rootY = self.find(y)
        if rootX != rootY:
          # 将两棵树合并为一棵
          self.parent[rootX] = rootY	   # or: self.root[rootY] = rootX
          self.count -= 1							 # 两个分量合二为一
          
      def connected(x, y):
        rootX = self.find(x)
        rootY = self.find(y)
        return rootX == rootY
    ```
  
    - <img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220725155049695.png" alt="image-20220725155049695" style="zoom:50%;" />
  
    - 构造函数初始化数据结构需要 O(N) 的时间和空间复杂度；连通两个节点 `union`、判断两个节点的连通性 `connected`、计算连通分量 `count` 所需的时间复杂度均为 O(1)。
  
    - 总结一下我们优化算法的过程：
  
      1、用 `root` 数组记录每个节点的父节点，相当于指向父节点的指针，所以 `root` 数组内实际存储着一个森林（若干棵多叉树）。
  
      2、用 `size` 数组记录着每棵树的重量，目的是让 `union` 后树依然拥有平衡性，保证各个 API 时间复杂度为 O(logN)，而不会退化成链表影响操作效率。
  
      3、在 `find` 函数中进行路径压缩，保证任意树的高度保持在常数，使得各个 API 时间复杂度为 O(1)。使用了路径压缩之后，可以不使用 `size` 数组的平衡优化。

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
      for crs in graph[pre]:					# for each course, conduct dfs
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

## 11. 547 [Number of Provinces](https://leetcode.com/problems/number-of-provinces/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (62.63%) | [`depth-first-search`](https://leetcode.com/tag/depth-first-search); [`union-find`](https://leetcode.com/tag/union-find) |

There are `n` cities. Some of them are connected, while some are not. If city `a` is connected directly with city `b`, and city `b` is connected directly with city `c`, then city `a` is connected indirectly with city `c`.

A **province** is a group of directly or indirectly connected cities and no other cities outside of the group.

You are given an `n x n` matrix `isConnected` where `isConnected[i][j] = 1` if the `ith` city and the `jth` city are directly connected, and `isConnected[i][j] = 0` otherwise.

Return *the total number of **provinces***.

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/12/24/graph1.jpg)

```
Input: isConnected = [[1,1,0],[1,1,0],[0,0,1]]
Output: 2
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2020/12/24/graph2.jpg)

```
Input: isConnected = [[1,0,0],[0,1,0],[0,0,1]]
Output: 3
```

- **Constraints:**

  - `1 <= n <= 200`

  - `n == isConnected.length`

  - `n == isConnected[i].length`

  - `isConnected[i][j]` is `1` or `0`.

  - `isConnected[i][i] == 1`

  - `isConnected[i][j] == isConnected[j][i]`

- **Solution**

  - <u>Method 1: Union-find</u> 

    - n 个城市和它们之间的相连关系看成图，城市是图中的节点，相连关系是图中的边，给定的矩阵 $\textit{isConnected}$ 即为图的邻接矩阵，省份即为图中的连通分量。province: cities connect together (have a common root) --> 使用并查集 (Union-find!)

      Goal: return the number of connected components

    - S1: initialize the graph and all components

      S2: write find and union functions to connect all edges in the input

    ```python
    def findCircleNum(self, isConnected: List[List[int]]) -> int:
            n = len(isConnected)
            self.count = n       # number of collected components
            self.rank = [1] * n
            parent = list(range(n))
            
            # find the root of current node
            # Quick find
            def find(node):
                if parent[node] != node:
                    parent[node] = find(parent[node])
                return parent[node]
            
            def union(node1, node2):
                root1 = find(node1)
                root2 = find(node2)
                # if root1 != root2:
                #     parent[root1] = root2
                #     self.count -= 1
                if root1 != root2:
                    if self.rank[root1] < self.rank[root2]:
                        parent[root1] = root2
                        self.rank[root1] += self.rank[root2]
                    else:
                        parent[root2] = root1
                        self.rank[root2] += self.rank[root1]
                    self.count -= 1
        
            # connect based on the input
            for i in range(n):
                for j in range(i + 1, n):
                    if isConnected[i][j] == 1:
                        union(i, j)
            return self.count
    ```

    - Time complexity: 

      - n is the number of cities and traverse through each element in `isConnected` --> $O(n^2)$
      - For each `union` function, need to implement `find` twice and `union` once --> in total, at most $2n^2$ `find` + $n^2$ `union` --> $O(2n^2log(n^2)) = O(n^2logn)$ 

      Space complexity: O(n)

   - <u>Method 2: DFS</u>

     - 深度优先搜索的思路是很直观的。遍历所有城市，对于每个城市，如果该城市尚未被访问过，则从该城市开始深度优先搜索，通过矩阵 $\textit{isConnected}$ 得到与该城市直接相连的城市有哪些，这些城市和该城市属于同一个连通分量，然后对这些城市继续深度优先搜索，直到同一个连通分量的所有城市都被访问到，即可得到一个省份。遍历完全部城市以后，即可得到连通分量的总数，即省份的总数。

       ```python
       def findCircleNum(self, isConnected: List[List[int]]) -> int:
               n = len(isConnected)
               visited = set()
               province = 0
       
               def dfs(i):
                   for j in range(n):
                       if isConnected[i][j] == 1 and j not in visited:
                           visited.add(j)
                           dfs(j)
       
               for i in range(n):
                   if i not in visited:
                       dfs(i)
                       province += 1
               return province
       ```

       - Time complexity：$O(n^2)$ --> 其中 n 是城市的数量。需要遍历矩阵 n 中的每个元素。

         Space complexity: $O(n)$ --> `visited` set and stack memory for recursion

## 12. 684 [Redundant Connection](https://leetcode.com/problems/redundant-connection/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (61.26%) | [`tree`](https://leetcode.com/tag/tree); [`union-find`](https://leetcode.com/tag/union-find); [`graph`](https://leetcode.com/tag/graph) |

In this problem, a tree is an **undirected graph** that is ==connected== and has no cycles.

You are given a graph that started as a tree with `n` nodes labeled from `1` to `n`, with one additional edge added. The added edge has two **different** vertices chosen from `1` to `n`, and was not an edge that already existed. The graph is represented as an array `edges` of length `n` where `edges[i] = [ai, bi]` indicates that there is an edge between nodes `ai` and `bi` in the graph.

Return *an edge that can be removed so that the resulting graph is a tree of* `n` *nodes*. If there are multiple answers, return the answer that occurs last in the input. 

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2021/05/02/reduntant1-1-graph.jpg" alt="img" style="zoom: 67%;" />

```
Input: edges = [[1,2],[1,3],[2,3]]
Output: [2,3]
```

**Example 2:**

<img src="https://assets.leetcode.com/uploads/2021/05/02/reduntant1-2-graph.jpg" alt="img" style="zoom:67%;" />

```
Input: edges = [[1,2],[2,3],[3,4],[1,4],[1,5]]
Output: [1,4]
```

- **Constraints:**

  - `n == edges.length`

  - `3 <= n <= 1000`

  - `edges[i].length == 2`

  - `1 <= ai < bi <= edges.length`

  - `ai != bi`

  - There are no repeated edges.

  - The given graph is connected.

- **Thoughts**

  - 在图的问题中看到`connected` --> 想到并查集方法！

  - 可以通过并查集寻找附加的边。初始时，每个节点都属于不同的连通分量。遍历每一条边，判断这条边连接的两个顶点是否属于相同的连通分量。

    - 如果两个顶点属于不同的连通分量，则说明在遍历到当前的边之前，这两个顶点之间不连通，因此当前的边不会导致环出现，合并这两个顶点的连通分量。

    - 如果两个顶点属于相同的连通分量，则说明在遍历到当前的边之前，这两个顶点之间已经连通，因此当前的边导致环出现，为附加的边，将当前的边作为答案返回。

- **Solution**

  ```python
  def findRedundantConnection(self, edges: List[List[int]]) -> List[int]:
          n = len(edges)
          parent = list(range(n + 1))
          rank = [1] * (n + 1)
  
          # path compression
          def find(node):
              if node != parent[node]:
                  parent[node] = find(parent[node])
              return parent[node]
          
          # union by rank
          def union(node1, node2):
              root1 = find(node1)
              root2 = find(node2)
              # parent[root1] = root2
              if root1 != root2:
                  if rank[root1] <= rank[root2]:      # root1 is the smaller tree
                      parent[root1] = root2           # connect root2 to root1
                      rank[root1] += rank[root2]
                  else:
                      parent[root2] = root1
                      rank[root2] += rank[root1]
  
          for node1, node2 in edges:          
              if find(node1) != find(node2):
                  union(node1, node2)
              else: 
                  return [node1, node2]
          return []
  ```

  - Time complexity: 

    - If not use fast-union, only use fast-find: $O(nlogn)$ --> 其中 n 是图中的节点个数。需要遍历图中的 n 条边，对于每条边，需要对两个节点查找祖先，如果两个节点的祖先不同则需要进行合并，需要进行 2 次查找和最多 1 次合并。一共需要进行 2n 次查找和最多 n 次合并，因此总时间复杂度是 $O(2n \log n)=O(n \log n)$。
    - If use fast-union and fast-find: $O(n \alpha (n))$ --> $\alpha$ is the inverse of Ackermanns function where $\alpha (n)$ can be considered as a really small constant --> $\approx O(n)$

    Space complexity: O(n) by `parent` and `rank`

## 13. LeetCode 261 / LintCode 178 Graph Valid Tree

`Algorithms Medium Accepted Rate35%`

**Description**

Given `n` nodes labeled from `0` to `n - 1` and a list of `undirected` edges (each edge is a pair of nodes), write a function to check whether these edges make up a valid tree.

You can assume that no duplicate edges will appear in edges. Since all edges are `undirected`, `[0, 1]` is the same as `[1, 0]` and thus will not appear together in edges.

Example

**Example 1:**

```
Input: n = 5 edges = [[0, 1], [0, 2], [0, 3], [1, 4]]
Output: true.
```

**Example 2:**

```
Input: n = 5 edges = [[0, 1], [1, 2], [2, 3], [1, 3], [1, 4]]
Output: false.
```

- **Thoughts**

  - Valid tree: have no cycles + <font color=red>**len(edges) should equal to (n - 1)! (the tree is fully connected!)**</font> --> 由于题目同时给了 $n$ 和 $edges$，所以要考虑到两者比较关系对结果的影响
  
- **Solution**

  - <u>Method 1: Depth-first-search</u>
  
    - method: DFS --> create an adjacency list using hashmap and a visted set to track all visited nodes
  
    - fully connected: `len(visited) = n`
  
    - no cycle: check the visited set --> <font color=red>**Important!: because this is an undirected graph, so trivial 'cycles' might be detected (false positive)**</font> --> created another variable `prev` to denote the previous node 
  
      注意在这道题中，图是无向的，所以在创建adjacency list时，对于每条edge两个node都要加。在detect loop时也要注意要跳过该节点的上一个节点，以防错误检测loop
  
    ```python
    def validTree(self, n: int, edges: List[List[int]]) -> bool:
            if len(edges) != n - 1:
                return False
            
            # S1: create an adjacency list using a hashmap {key (node value): value (neighbor value)}
            adj = {i:[] for i in range(n)}					
            for node1, node2 in edges:							# O(E)
                adj[node1].append(node2)	
                adj[node2].append(node1)
            
            # S2: implement DFS to visit all nodes and check if there is a loop
            visited = set()
            def dfs(node, prev):
                if node in visited:
                    return False
                
                visited.add(node)
                for neighbor in adj[node]:						# O(N)
                    if neighbor == prev:
                        continue
                    if not dfs(neighbor, node):
                        return False
                return True
            
            return dfs(0, -1) and len(visited) == n
    ```
  
    - Time complexity: $O(N + E)$ --> traverse through each node and each edge
  
      Space complexity: $O(N + E)$ --> The adjacency list is a list of length N, with inner lists with lengths that add to a total of E. This gives a total of O(N + E) space.
  
  - <u>Method 2: Union-find</u>
  
    - 和684. Redundant Connection题目很类似，可以使用union-find算法模板
  
      ```python
      def valid_tree(self, n: int, edges: List[List[int]]) -> bool:
              # write your code here
              if len(edges) != n - 1:
                  return False
      
              parent = list(range(n))
      
              def find(node):
                  if node != parent[node]:
                      parent[node] = find(parent[node])
                  return parent[node]
              
              def union(node1, node2):
                  root1 = find(node1)
                  root2 = find(node2)
                  parent[root1] = root2
              
              for node1, node2 in edges:
                  if find(node1) != find(node2):
                      union(node1, node2)
                  else:
                      return False
              return True
      ```
  
    - Time complexity: $O(2nlogn) = O(nlogn)$ (no optimization of union-find)
  
      Space complexity: O(n) by `parent` 
  

## 14. 332 [Reconstruct Itinerary](https://leetcode.com/problems/reconstruct-itinerary/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Hard (40.27%) | [`depth-first-search`](https://leetcode.com/tag/depth-first-search); [`graph`](https://leetcode.com/tag/graph) |

You are given a list of airline `tickets` where `tickets[i] = [fromi, toi]` represent the departure and the arrival airports of one flight. Reconstruct the itinerary in order and return it.

All of the tickets belong to a man who departs from `"JFK"`, thus, the itinerary must begin with `"JFK"`. If there are multiple valid itineraries, you should return the itinerary that has the smallest lexical order when read as a single string.

- For example, the itinerary `["JFK", "LGA"]` has a smaller lexical order than `["JFK", "LGB"]`.

You may assume all tickets form at least one valid itinerary. You must use all the tickets once and only once. 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/03/14/itinerary1-graph.jpg)

```
Input: tickets = [["MUC","LHR"],["JFK","MUC"],["SFO","SJC"],["LHR","SFO"]]
Output: ["JFK","MUC","LHR","SFO","SJC"]
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2021/03/14/itinerary2-graph.jpg)

```
Input: tickets = [["JFK","SFO"],["JFK","ATL"],["SFO","ATL"],["ATL","JFK"],["ATL","SFO"]]
Output: ["JFK","ATL","JFK","SFO","ATL","SFO"]
Explanation: Another possible reconstruction is ["JFK","SFO","ATL","JFK","ATL","SFO"] but it is larger in lexical order.
```

- **Constraints:**

  - `1 <= tickets.length <= 300`

  - `tickets[i].length == 2`

  - `fromi.length == 3`

  - `toi.length == 3`

  - `fromi` and `toi` consist of uppercase English letters.

  - `fromi != toi`

- **Thoughts**

  - 这道题的飞行具有dependency --> topological sort --> 只是topological sort里每个点只能走一次, 但是这里可以走回头路, 所以只有在所有边都用完的情况下才放进结果数组里面.

  - Idea: 从JFK开始尝试全部可能的路径，返回第一个用完ticket的路径

    - 把input转换为图的结构并以lexical顺序排序: build a hashmap/adjacency list {from: to} based on lexical order (sorted)

    - 在构造的图上从JFK开始dfs, remove掉访问过的边，backtrack时再加回来

      <img src="https://camo.githubusercontent.com/97fc71990118675e1736bf595445afc0e4593fa13d02ea9a85df16151c8df01f/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f323032303131313531383036353535352e706e67" alt="332.重新安排行程1" style="zoom:50%;" />

- **Solution**

  ```python
  from collections import defaultdict
  def findItinerary(self, tickets: List[List[str]]) -> List[str]:
          ticketMap = defaultdict(list)
          tickets.sort()
          for dep, arr in tickets:
              ticketMap[dep].append(arr)
          '''
          tickets_dict里面的内容是这样的
           {'JFK': ['SFO', 'ATL'], 'SFO': ['ATL'], 'ATL': ['JFK', 'SFO']})
          '''
          
          res = ['JFK']   # JFK is the start
          def dfs(cur):
              if len(res) == len(tickets) + 1: # find a valid itenary
                  return True
              
              arrival = list(ticketMap[cur])   # the list of ticket arrivals
              for arr in arrival:
                  ticketMap[cur].pop(0)        # use this ticket (from cur to arr)
                  res.append(arr)
                  if dfs(arr):								 # backtrack
                      return res
                  res.pop()
                  ticketMap[cur].append(arr)
              return False
          
          dfs('JFK')
          return res
  ```

  - Time complexity: $O(nlogn + n + n!)$; $O(n!)$: dfs时，从一个机场出发最多有n张票可用，到达一个新地点后还有(n-1)张票可以用 --> $n!$

    Space complexity: O(n)--> create a ticketMap
