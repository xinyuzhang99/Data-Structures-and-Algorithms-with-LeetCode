# Advanced Graphs

- **最小生成树算法 Minimum Spanning Tree**

  最小生成树算法主要有 **Prim 算法（普里姆算法）**和 **Kruskal 算法（克鲁斯卡尔算法）**两种，这两种算法虽然都运用了贪心思想，但从实现上来说差异还是蛮大的。

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

  - Time complexity: $O(ElogE + N + E * \alpha(N))$
  - Space complexity: O(N), space required by `parents`.

