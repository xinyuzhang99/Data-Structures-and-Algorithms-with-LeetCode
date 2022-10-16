# Advanced Graphs

- **最小生成树算法 Minimum Spanning Tree**

  最小生成树算法主要有 **Prim 算法（普里姆算法）**和 **Kruskal 算法（克鲁斯卡尔算法）**两种，这两种算法虽然都运用了贪心思想，但从实现上来说差异还是蛮大的。[Kruskal's algorithm](https://leetcode.com/explore/featured/card/graph/621/algorithms-to-construct-minimum-spanning-tree/3856/) and Prim's Algorithm are two **greedy algorithms** for building a minimum spanning tree in a ***weighted* and *undirected*** graph.

  图的「生成树」：就是在图中找一棵包含图中的所有节点的树。专业点说，生成树是含有图中所有顶点的「无环连通子图」。

  **最小生成树：所有可能的生成树中，权重和最小的那棵生成树就叫「最小生成树」**

  - A Minimum Spanning Tree (MST) is a subset of the edges of connected, weighted and undirected graph which :

    1. Connects all vertices together
    2. No cycle
    3. **Minimum total edge**

    - 这里就用到了贪心思路：

      **将所有边按照权重从小到大排序，从权重最小的边开始遍历，如果这条边和`mst`中的其它边不会形成环，则这条边是最小生成树的一部分，将它加入`mst`集合；否则，这条边不是最小生成树的一部分，不要把它加入`mst`集合**。

    --> 最小生成树是含有图中所有顶点且权重和最小的「无环连通子图」

  - <u>Kruskal算法</u>

    --> use **union-find** for the first two conditions and **sort by weights** for the third condition

    树的判定算法加上按权重排序的逻辑就变成了 Kruskal 算法

  - <u>==Prim算法: BFS + Visted set + Min-heap==</u>

    - **首先，Prim 算法也使用贪心思想来让生成树的权重尽可能小**，也就是「切分定理」。Prim 算法不需要事先对所有边排序，而是利用<font color=blue>**优先级队列**</font>动态实现排序的效果，所以 Prim 算法类似于 Kruskal 的动态过程 --> A **min-heap** is a tree-like data structure that always **stores the minimum valued element (edge weight here)** at the root and where insertion and removal of elements (edges) take logarithmic time. --> <font color=blue>A min-heap to pick minimum weight edge, each element of heap is a pair of `(edge weight, node)`.</font>

      **其次，Prim 算法使用 [BFS 算法思想](https://labuladong.github.io/algo/4/31/111/) 和 `visited` 布尔数组避免成环**，来保证选出来的边最终形成的一定是一棵树。

      --> In this algorithm, we include an arbitrary node in the MST and keep on adding the **lowest-weighted** edges of the nodes present in the MST until all nodes are included in the MST and **no cycles** are formed.

    - 切分定理：**对于任意一种「切分」，其中权重最小的那条「横切边」一定是构成最小生成树的一条边**。

      **每次切分都能找到最小生成树的一条边，然后又可以进行新一轮切分，直到找到最小生成树的所有边为止**。

## 1. 1135 [Connecting Cities With Minimum Cost](https://leetcode.com/problems/connecting-cities-with-minimum-cost/)

There are `n` cities labeled from `1` to `n`. You are given the integer `n` and an array `connections` where `connections[i] = [xi, yi, costi]` indicates that the cost of connecting city `xi` and city `yi` (bidirectional connection) is `costi`.

Return *the minimum **cost** to connect all the* `n` *cities such that there is at least one path between each pair of cities*. If it is impossible to connect all the `n` cities, return `-1`,

The **cost** is the sum of the connections' costs used.

**Example 1:**

![img](https://assets.leetcode.com/uploads/2019/04/20/1314_ex2.png)

```
Input: n = 3, connections = [[1,2,5],[1,3,6],[2,3,1]]
Output: 6
Explanation: Choosing any 2 edges will connect all cities so we choose the minimum 2.
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2019/04/20/1314_ex1.png)

```
Input: n = 4, connections = [[1,2,3],[3,4,4]]
Output: -1
Explanation: There is no way to connect all cities even if all edges are used.
```

- **Constraints:**

  - `1 <= n <= 104`

  - `1 <= connections.length <= 104`

  - `connections[i].length == 3`

  - `1 <= xi, yi <= n`

  - `xi != yi`

  - `0 <= costi <= 105`

- **Thoughts**

  - Intuition: 如果我们将城市和连接建模为图，则每个连接都是一条边（无向），每个城市都是图的一个节点。 我们需要找到一个边的子集，它以最小的总权重连接图的所有节点。 根据定义，这是图的最小生成树或 MST。--> model the cities and connections as a graph --> **Minimum Spanning Tree + Greedy**
  - Procedures:
    - S1: sort all the connections (edges) present in the graph based on their weights (in increasing order)
    - S2: each time find a valid edge using union-find, add the cost to the result
  - **Key: Kruskal algorithm = sort() + union-find**, where `sort()` is used to sort weight/cost of each edge and `union-find` is used to check if the garph is fully-connected and has no loop

- **Solution**

  ```python
  def minimumCost(self, n: int, connections: List[List[int]]) -> int:
          
          connections.sort(key = lambda x: x[2])    # sort all edges by cost: O(ElogE)
          weight = 0
          total = 0  # keep track of the edge number
          
          # 城市编号为 1...n，所以初始化大小为 n + 1
          parent = list(range(n + 1))								# space complexity: O(N)
          def find(node):
              if parent[node] != node:
                  parent[node] = find(parent[node])  # path compression
              return parent[node]
          
          def union(node1, node2):
              root1 = find(node1)
              root2 = find(node2)
              if root1 == root2:
                  return
              parent[root1] = root2
          
          for x, y, cost in connections:
              if find(x) == find(y):
                  continue
              else:
                  union(x, y)												# each time: alpha(N); for E edges: O(E*alpha(N)) = O(E)
                  weight += cost
                  total += 1
          
          return weight if total == n - 1 else -1   # check if the graph is fully-connected (result edge = n - 1)
  ```

  - Time complexity: $O(ElogE + E * \alpha(N))$
  - Space complexity: O(N), space required by `parents`.

## 2. 1584 [Min Cost to Connect All Points](https://leetcode.com/problems/min-cost-to-connect-all-points/description/)

|  Category  |   Difficulty    |                     Tags                      |
| :--------: | :-------------: | :-------------------------------------------: |
| algorithms | Medium (64.34%) | [`Unknown`](https://leetcode.com/tag/Unknown) |

You are given an array `points` representing integer coordinates of some points on a 2D-plane, where `points[i] = [xi, yi]`.

The cost of connecting two points `[xi, yi]` and `[xj, yj]` is the **manhattan distance** between them: `|xi - xj| + |yi - yj|`, where `|val|` denotes the absolute value of `val`.

Return *the minimum cost to make all points connected.* All points are connected if there is **exactly one** simple path between any two points.

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/08/26/d.png)

```
Input: points = [[0,0],[2,2],[3,10],[5,2],[7,0]]
Output: 20
Explanation: 

We can connect the points as shown above to get the minimum cost of 20.
Notice that there is a unique path between every pair of points.
```

**Example 2:**

```
Input: points = [[3,12],[-2,5],[-4,1]]
Output: 18
```

- **Constraints:**

  - `1 <= points.length <= 1000`

  - `-106 <= xi, yi <= 106`

  - All pairs `(xi, yi)` are distinct.

- **Solution**

  - 这道题和1135. Connecting Cities With Minimum Cost基本一致，都是使用Minimum Spanning Tree算法，区别在于这道题每两个点之间的cost需要先计算出来

    这道题做了一个小的变通：每个坐标点是一个二元组，那么按理说应该用五元组表示一条带权重的边，但这样的话不便执行 Union-Find 算法；所以我们用 `points` 数组中的索引代表每个坐标点，这样就可以直接复用之前的 Kruskal 算法逻辑了。

  - <u>Method 1: Kruskal Algorithm</u>

    --> sort distance + union find

    - S1: build edge list to represent distance between a pair of points --> adjacency list
    - S2: sort distance in ascending order
    - S3: use union-find

    ```python
     n = len(points)
            # S1: create an edge list to represent tuple(edgeweight, node1, node2): 生成所有边及权重
            edges = []																				# Space complexity: O(E) to cover all edges
            for i in range(n):            # current node
                for j in range(i + 1, n): # next node
                    distance = abs(points[i][0] - points[j][0]) + abs(points[i][1] - points[j][1])
                    edges.append((distance, i, j))
    
            # S2: sort distance in ascending order
            edges.sort()																			# Time complexity: O(ElogE)
    
            # S3: use union-find (by rank + path compression) # Time complexity: O(E * alpha(V))
            parent = list(range(n))														# Space complexity: O(V)
            # rank = [1] * n
            self.count = n   # initial there are n connected components
            
            # Find the root of the node
            def find(node):
                if parent[node] != node:
                    parent[node] = find(parent[node])
                return parent[node]
            
            # Connect two nodes
            def union(node1, node2):
                root1 = find(node1)
                root2 = find(node2)
                if root1 == root2:
                    return
                parent[root1] = root2
            
            cost = 0
            for distance, node1, node2 in edges:						 # Time complexity: O(E * alpha(V))
                if find(node1) == find(node2):
                    continue
                else:
                    union(node1, node2)
                    cost += distance
                    self.count -= 1
                    if self.count == 1:
                        break
            return cost
    ```

    - Time complexity: $O(ElogE + E * \alpha(V))$, in the worst case: $E = N^2$
    - Space complexity: $O(E + V)$, space required by `edges` and `parents`.

  - <u>Method 2: Prim's Algorithm</u>

    --> **BFS思想（遍历附近的所有点） + Visited set + min-heap**

    visited set: keep track of visited nodes (used edges); min-heap: with least weight edge on top (edge weight, node)

    - Procedures:

      - <u>push all adjacent edges of node</u> in `heap `with their respective `weights` using a for-loop

      - pop elements from the `heap` and attempt to add them to the tree until `edgesUsed` becomes equal to `n`. We initially added one temporary edge, thus we stop when `n` edges are added in the MST.

        --> get the minimum weighted edge and the node from the top of `heap` and pop it.

        <img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20221015171436405.png" alt="image-20221015171436405" style="zoom:50%;" />

      <img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20221015171353709.png" alt="image-20221015171353709" style="zoom:50%;" />

    ```python
    # Prim's: BFS + visited set + min-heap to dynamically determine the minimum cost edge (cost, node)
    import heapq
    def minCostConnectPoints(self, points: List[List[int]]) -> int:
      n = len(points)
      visited = set()
      cost = 0
      currEdges = 0
      
      minHeap = [(0, 0)]    # heap: (cost/weight, node)
      heapq.heapify(minHeap)
      
      # BFS traversal --> stop traversal condition: currEdges == n - 1
      # 1. pop out the top edge and update the information
      # 2. push all neighboring nodes and their weights to the min-heap
      while currEdges < n:
        weight, node = heapq.heappop(minHeap)				# Time complexity: O(logn) for each pop/push operation
        if node in visited:
          continue
        
        visited.add(node)
        cost += weight
        currEdges += 1
        
        for neighbor in range(n):    # traverse through all unvisited nodes 
          if neighbor in visited:
            continue
          
          next_weight = abs(points[node][0] - points[neighbor][0]) + abs(points[node][1] - points[neighbor][1])
          heapq.heappush(minHeap, (next_weight, neighbor))	# Time complexity: O(logn)
      return cost
    ```

    - Time complexity: $O(N^2logN)$
    - Space complexity: $O(E) = O(N^2)$, in the worst case, push/pop $N^2$ edges in the heap

    