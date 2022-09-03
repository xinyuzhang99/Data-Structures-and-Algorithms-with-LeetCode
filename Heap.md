# Heap 堆

## Python中堆的常用操作

```python
import heapq

class Test:
  def test(self):
    # Create minheap
    minheap = []
    heapq.heapify(minheap)
    
    # Add element
    heapq.heappush(minheap, 10)
    heapq.heappush(minheap, 8)
    heapq.heappush(minheap, 9)
    heapq.heappush(minheap, 2)
    heapq.heappush(minheap, 1)
    heapq.heappush(minheap, 11)
    print(minheap)
    # [1, 2, 9, 10, 8, 11] 按照minheap插入元素
   
    # peek
    minheap[0]
    
    # Delete and return
    heapq.heappop(minheap)
    
    # Size
    len(minheap)
    
    # Iteration
    while len(minheap) != 0:
      print(heapq.heappop(minheap))
```

Python里没有直接生成`maxheap`的函数，故把数组所有元素取成负数（乘-1），再按照`minheap`插入元素

Procedures of adding elements:

<img src="/Users/xinyuzhang/Downloads/IMG_13B36B9593AF-1.jpeg" alt="IMG_13B36B9593AF-1" style="zoom:33%;" />

## 1. 215 [Kth Largest Element in an Array](https://leetcode.com/problems/kth-largest-element-in-an-array/description/)

|  Category  |   Difficulty    |           Tag            |
| :--------: | :-------------: | :----------------------: |
| algorithms | Medium (63.56%) | divide-and-conquer; heap |

Given an integer array `nums` and an integer `k`, return *the* `kth` *largest element in the array*.

Note that it is the `kth` largest element in the sorted order, not the `kth` distinct element.

**Example 1:**

```
Input: nums = [3,2,1,5,6,4], k = 2
Output: 5
```

**Example 2:**

```
Input: nums = [3,2,3,1,2,4,5,5,6], k = 4
Output: 4
```

- **Solution**

  - <u>Method 1: Sort</u>

    ```python
    def findKthLargest(self, nums: List[int], k: int) -> int:
            nums.sort(reverse=True)
            return nums[k-1]   # or: return sorted(nums)[-k]
    ```

    Time Complexity: O(nlogn)

    **sort 与 sorted 区别：**

    sort 是<u>应用在 list 上</u>的方法，sorted 可以对<u>所有可迭代的对象</u>进行排序操作。

    list 的 sort 方法返回的是对已经存在的列表进行操作，无返回值，而内建函数 sorted 方法返回的是一个<u>新的 list</u>，而不是在原来的基础上进行的操作。

  - <u>Method 2: Heap</u>

    - S1: Create a heap and define it as a maxheap
    - S2: heapq.heappush --> insert all $-1\times values$ to the heap
    - S3: heapq.heappop for k-1 times --> return $-1\times maxheap[0]$

    <font color=red>**kth largest = (len(nums) - k)th smallest!!**</font>
    
    ```python
    # Method 1: use max-heap
    import heapq
    class Solution:
        def findKthLargest(self, nums: List[int], k: int) -> int:
            maxheap = []
            heapq.heapify(maxheap)
    
            for n in nums:
                heapq.heappush(maxheap, -1 * n)
            
            while k > 1:
                heapq.heappop(maxheap)
                k -= 1
            return -1 * maxheap[0]
    # Time complexity: O(nlogn)
    # Space complexity: O(n)
    
    # Method 2: use min-heap
    import heapq
    class Solution:
        def findKthLargest(self, nums: List[int], k: int) -> int:
            minHeap = nums
            heapq.heapify(minHeap)
            
            while len(minHeap) > k:
                heapq.heappop(minHeap)
            return minHeap[0]
    ```

​			    Time complexity: O(nlogn)

​				Space complexity: O(n)

## 2. 692 [Top K Frequent Words](https://leetcode.com/problems/top-k-frequent-words/description/)

|  Category  |   Difficulty    |          Tags          |
| :--------: | :-------------: | :--------------------: |
| algorithms | Medium (54.39%) | Hash-table; heap; trie |

Given an array of strings `words` and an integer `k`, return *the* `k` *most frequent strings*.

Return the answer **sorted** by **the frequency** from highest to lowest. Sort the words with the same frequency by their **lexicographical order**.

**Example 1:**

```
Input: words = ["i","love","leetcode","i","love","coding"], k = 2
Output: ["i","love"]
Explanation: "i" and "love" are the two most frequent words.
Note that "i" comes before "love" due to a lower alphabetical order.
```

**Example 2:**

```
Input: words = ["the","day","is","sunny","the","the","the","sunny","is","is"], k = 4
Output: ["the","is","sunny","day"]
Explanation: "the", "is", "sunny" and "day" are the four most frequent words, with the number of occurrence being 4, 3, 2 and 1 respectively. 
```

- **Constraints:**

  - `1 <= words.length <= 500`

  - `1 <= words[i] <= 10`

  - `words[i]` consists of lowercase English letters.

  - `k` is in the range `[1, The number of **unique** words[i]]`

- **Thoughts**
  - 对于找==top-k==的题目，使用Heap进行排序
  - 题目要求的顺序为：输出<u>频率大</u>的字符串，如果频率相同输出<u>字母顺序小</u>的字符串 --> 考虑使用堆结构进行排序 --> 由于python自带的是min-heap，故条件改为：<font color=blue>**频率小的元素放在堆顶，字母顺序大的元素放在堆顶** </font> --> 频率小的元素先出堆，字母顺序大的元素先出堆 --> 最后按照顺序取出来后<font color=blue>**反转顺序**</font>，即为正确顺序
  - <font color=red>**难点：**</font> write the <font color=red>**comparator function**</font>

- **Solution**

  ==Heap + Hash-table==

  - S1: Create a hash table to store the strings (keys) and their frequencies (values)
  - S2: Create a min-heap where 频率小的元素放在堆顶，字母顺序大的元素放在堆顶
  - S3: Create a result list and append the keys --> then reverse the list to get  the result list

  ```python
  import heapq
  from typing import List
  # The updated comparator function
  class Node:
      def __init__(self, key, value):
          self.key = key
          self.value = value
  
      # customize a comparator (return true/false)
      def __lt__(self, nxt):               # Return True: self比next先弹出
          if self.value > nxt.value:
              return False
          elif self.value < nxt.value:     # 出现频率小的先弹出
              return True
          elif self.value == nxt.value:
              return self.key > nxt.key    # 字典序大的先弹出
  
  class Solution:
      def topKFrequent(self, words: List[str], k: int) -> List[str]:
          hashtable = dict()
  
          for word in words:
              if word in hashtable:
                  hashtable[word] += 1
              else:
                  hashtable[word] = 1
  
          minheap = []
          heapq.heapify(minheap)
          for key, value in hashtable.items():
              heapq.heappush(minheap, Node(key, value)) 
              if len(minheap) > k:   		   # 去掉频率小，在选择范围外的元素
                  heapq.heappop(minheap) 
                  
          res = []
          while len(minheap) > 0:
              res.append(heapq.heappop(minheap).key)
          res.reverse()
  
          return res
  ```

​		- Time complexity: O(n + nlogk + n) = O(nlogk)

​		  Space complexity: O(k)

## 3. 347 [Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (65.02%) | [`hash-table`](https://leetcode.com/tag/hash-table); [`heap`](https://leetcode.com/tag/heap) |

Given an integer array `nums` and an integer `k`, return *the* `k` *most frequent elements*. You may return the answer in **any order**.

**Example 1:**

```
Input: nums = [1,1,1,2,2,3], k = 2
Output: [1,2]
```

**Example 2:**

```
Input: nums = [1], k = 1
Output: [1]
```

**Constraints:**

- `1 <= nums.length <= 105`
- `k` is in the range `[1, the number of unique elements in the array]`.
- It is **guaranteed** that the answer is **unique**.

**Follow up:** Your algorithm's time complexity must be better than `O(nlogn)`, where n is the array's size.

- **Thoughts**

  - 对于找==top-k==的题目，使用Heap进行排序
  - 由于minheap是根据count的大小进行排序，但是最后我们要输出的是对应的value值 --> 在heap里放的值是 (count, value) pair

- **Solution**

  - <u>Method 1: Heap</u>

    - S1: build a hashtable which contains values and counts
    - S2: build a minheap and insert values
    - S3: append the most frequent element and reverse the list

    ```python
    from collections import Counter
    import heapq
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
      hashtable = Counter(nums)
      minheap = []
      heapq.heapify(minheap)
      
      for num, count in hashtable.items():
        element = [count, num]
        heapq.heappush(minheap, element)
        if len(minheap) > k:
          heapq.heappop(minheap)
      
      res = []
      while k > 0:
        res.append(heapq.heappop(minheap)[1])
        k -= 1
      res.reverse()
      return res
    ```

    - Time complexity: $O(N + Nlogk + N) = O(Nlogk)$ --> $N$为数组的长度，$k$为堆的大小；第一个$O(N)$是遍历哈希表的时间，$Nlogk$是每次堆操作需要$O(logk)$的时间，一共有N个元素，故为$O(Nlogk)$；最后一个$O(N)$为`res.reverse()`的时间

    - Space complexity: $O(N)+O(k)=O(N)$

  - <u>Method 2: Bucket Sort</u>

    由于每一个元素出现的频率都不可能大于数组长度，于是可以创建bucket，令count为key, 实际数组值为value

    <img src="https://pic.leetcode-cn.com/ad27531bbe762c0cf408a1e80f6468800d3e4ee2d6318963276b9ed923dd2c54-file_1561712388097" alt="img" style="zoom: 33%;" />

    -  S1: create a hashtable to store each element and its counts
    - S2: create a frequency bucket to store count and element --> len(freq) = len(nums) (Create list of empty lists for bucktes: for frequencies 1, 2, ..., n)
    - S3: traverse backwards through the dictionary and append elements

    ```python
    from collections import Counter
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
      count = {}
      freq = [[] for i in range(len(nums) + 1)] # 0, 1, 2, ..., n
      
      # S1
      for n in nums:
        count[n] = 1 + count.get(n, 0)
      # S2
      for n, c in count.items():
        freq[c].append(n)
      
      # S3
      res = []
      for i in range(len(freq) - 1, 0, -1):
        for n in freq[i]:												# for each count, append each element in the bucket
          res.append(n)
          if len(res) == k:
            return res 
    ```

    - Time complexity: $O(n)$ --> n 表示数组的长度。首先，遍历一遍数组统计元素的频率，这一系列操作的时间复杂度是 O(n)；桶的数量为 n+1，所以桶排序的时间复杂度为 O(n)

      Space complexity: O(n)

## 4. 703 [Kth Largest Element in a Stream](https://leetcode.com/problems/kth-largest-element-in-a-stream/description/)

|  Category  |  Difficulty   |                     Tags                      |
| :--------: | :-----------: | :-------------------------------------------: |
| algorithms | Easy (54.87%) | [`Unknown`](https://leetcode.com/tag/Unknown) |

Design a class to find the `kth` largest element in a stream. Note that it is the `kth` largest element in the sorted order, not the `kth` distinct element.

Implement `KthLargest` class:

- `KthLargest(int k, int[] nums)` Initializes the object with the integer `k` and the stream of integers `nums`.
- `int add(int val)` Appends the integer `val` to the stream and returns the element representing the `kth` largest element in the stream.

**Example 1:**

```java
Input
["KthLargest", "add", "add", "add", "add", "add"]
[[3, [4, 5, 8, 2]], [3], [5], [10], [9], [4]]
Output
[null, 4, 5, 5, 8, 8]

Explanation
KthLargest kthLargest = new KthLargest(3, [4, 5, 8, 2]);
kthLargest.add(3);   // return 4
kthLargest.add(5);   // return 5
kthLargest.add(10);  // return 5
kthLargest.add(9);   // return 8
kthLargest.add(4);   // return 8 
```

- **Constraints:**

  - `1 <= k <= 104`

  - `0 <= nums.length <= 104`

  - `-104 <= nums[i] <= 104`

  - `-104 <= val <= 104`

  - At most `104` calls will be made to `add`.

  - It is guaranteed that there will be at least `k` elements in the array when you search for the `kth` element.

- **Thoughts**

  - 这题考察堆的应用，和 [215. 数组中的第 K 个最大元素](https://leetcode.com/problems/kth-largest-element-in-an-array)的主要实现步骤基本一样
  - --> 使用一个大小为 k 的优先队列来存储前 k 大的元素，其中优先队列的**队头为队列中最小的元素，也就是第 k 大的元素**。
  - - 为什么使用小根堆？
      因为我们需要在堆中保留数据流中的前 K 大元素，使用小根堆能保证每次调用堆的 pop() 函数时，从堆中删除的是堆中的最小的元素（堆顶）。
    - 为什么能保证堆顶元素是第 K 大元素？
      因为小根堆中保留的一直是堆中的前 K 大的元素，堆的大小是 K，所以堆顶元素是第 K 大元素。
    - 每次 add() 的时间复杂度是多少？
      每次 add() 时，调用了堆的 push() 和 pop() 方法，两个操作的时间复杂度都是 log(K).。

- **Solution**

  ```python
  class KthLargest:
      def __init__(self, k: int, nums: List[int]):
          self.k = k
          self.minHeap = nums
          heapq.heapify(self.minHeap)
          while len(self.minHeap) > k:
              heapq.heappop(self.minHeap)
  
      def add(self, val: int) -> int:
          heapq.heappush(self.minHeap, val)
          while len(self.minHeap) > self.k:
              heapq.heappop(self.minHeap)
          return self.minHeap[0]
  ```

  - Time complexity: $O(nlogk)$ --> 时间复杂度主要花费在初始化上，n为初始化时nums的长度

    Space complexity: O(k) --> build a heap to store k largest values

## 5. 1046. [Last Stone Weight](https://leetcode.com/problems/last-stone-weight/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Easy (64.41%) | [`two-pointers`](https://leetcode.com/tag/two-pointers); [`sliding-window`](https://leetcode.com/tag/sliding-window) |

You are given an array of integers `stones` where `stones[i]` is the weight of the `ith` stone.

We are playing a game with the stones. On each turn, we choose the **heaviest two stones** and smash them together. Suppose the heaviest two stones have weights `x` and `y` with `x <= y`. The result of this smash is:

- If `x == y`, both stones are destroyed, and
- If `x != y`, the stone of weight `x` is destroyed, and the stone of weight `y` has new weight `y - x`.

At the end of the game, there is **at most one** stone left.

Return *the weight of the last remaining stone*. If there are no stones left, return `0`.

**Example 1:**

```
Input: stones = [2,7,4,1,8,1]
Output: 1
Explanation: 
We combine 7 and 8 to get 1 so the array converts to [2,4,1,1,1] then,
we combine 2 and 4 to get 2 so the array converts to [2,1,1,1] then,
we combine 2 and 1 to get 1 so the array converts to [1,1,1] then,
we combine 1 and 1 to get 0 so the array converts to [1] then that's the value of the last stone.
```

**Example 2:**

```
Input: stones = [1]
Output: 1
```

- **Constraints:**

  - `1 <= stones.length <= 30`

  - `1 <= stones[i] <= 1000`

- **Thoughts**

  - Procedures:
    - S1: store all stones into a max-heap
    - S2: while len(heap) **> 1**, use max-heap to pop two stones each time (first(x) <= second(y))
    - S3: if x == y: continue; else: push(y - x)
    - S4: return heap[0] if heap else 0

- **Solution**

  ```python
  def lastStoneWeight(self, stones: List[int]) -> int:
    # maxHeap = []
    # heapq.heapify(maxHeap)
    # for s in stones:
    #     heapq.heappush(maxHeap, -1 * s)
  
    # A simple version of writing max-heap!
    maxHeap = [-s for s in stones]
    heapq.heapify(maxHeap)
  
    while len(maxHeap) > 1:
      x = heapq.heappop(maxHeap)
      y = heapq.heappop(maxHeap)
      if x == y:
        continue
      else:
        heapq.heappush(maxHeap, x - y)
    return -1 * maxHeap[0] if maxHeap else 0
  ```

  - Time complexity: $O(nlogn)$ --> 时间复杂度主要花费在初始化上，n为stones的长度

    Space complexity: O(n) --> build a heap to store most n values

# 6. 973 [K Closest Points to Origin](https://leetcode.com/problems/k-closest-points-to-origin/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (65.94%) | [`string`](https://leetcode.com/tag/string); [`greedy`](https://leetcode.com/tag/greedy) |

Given an array of `points` where `points[i] = [xi, yi]` represents a point on the **X-Y** plane and an integer `k`, return the `k` closest points to the origin `(0, 0)`.

The distance between two points on the **X-Y** plane is the Euclidean distance (i.e., `√(x1 - x2)2 + (y1 - y2)2`).

You may return the answer in **any order**. The answer is **guaranteed** to be **unique** (except for the order that it is in).

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2021/03/03/closestplane1.jpg" alt="img" style="zoom:33%;" />

```
Input: points = [[1,3],[-2,2]], k = 1
Output: [[-2,2]]
Explanation:
The distance between (1, 3) and the origin is sqrt(10).
The distance between (-2, 2) and the origin is sqrt(8).
Since sqrt(8) < sqrt(10), (-2, 2) is closer to the origin.
We only want the closest k = 1 points from the origin, so the answer is just [[-2,2]].
```

**Example 2:**

```
Input: points = [[3,3],[5,-1],[-2,4]], k = 2
Output: [[3,3],[-2,4]]
Explanation: The answer [[-2,4],[3,3]] would also be accepted.
```

- **Constraints:**

  - `1 <= k <= points.length <= 104`

  - `-104 < xi, yi < 104`

- **Thoughts**

  - k closest points to the origin --> minheap; closest distance to (0, 0) --> $(x1 - x2)^2 + (y1 - y2)^2 -> x^2 + y^2$

    --> main idea: use min-heap based on distance and return the first k elements

  - S1: create a new array to record each point's distance

    S2: establish a min-heap (distance, point) <font color=red>重点！由于最小堆是对距离进行排序，然而最后结果要求返回的是points，所以最小堆输入的应该是一个tuple</font>

    S3: return the first k elements using index (pop out)

  - Optimization: 使用堆实时维护前k个最小的距离平方，这样的话能将时间复杂度从 $O(nlogn)$ 降成 $O(nlogk)$。

- **Solution**

  ```python
  # Version 1: Non-optimized + non-simplified
  import heapq
  import math     # important!!
  def kClosest(self, points: List[List[int]], k: int) -> List[List[int]]:
    # base case:
    if not points:
      return 
  
    n = len(points)
    distance = [None] * n
    # for i in range(n):
      # x = points[i][0]
      # y = points[i][1]
      # distance[i] = math.sqrt((math.pow(x, 2) + math.pow(y, 2)))
    for x, y in points:
      distance[i] = math.sqrt(x**2 + y**2)
  
    minHeap = []
    heapq.heapify(minHeap)
    for i in range(n):
      heapq.heappush(minHeap, (distance[i], points[i]))
  
    res = []
    while k > 0:
      val = heapq.heappop(minHeap)
      res.append(val[1])
      k -= 1
    return res
  # Time complexity: $O(nlogn)$
  # Space complexity: O(n) --> build a heap to store most n values
    
  # Version 2: Non-optimized + simplified 使用大根堆！！
  # Optimization: to ensure the heap has k closest points, so we use max-heap and eliminate unsatisfactory points (order is unnecessary)
  def kClosest(self, points: List[List[int]], k: int) -> List[List[int]]:
    if not points:
      return []
  
    maxHeap = []
    for x, y in points[:k]:   # the k-size heap
      maxHeap.append((-math.sqrt(x**2 + y**2), x, y))
      heapq.heapify(maxHeap)
  
    for x, y in points[k:]:
      dist = -math.sqrt(x**2 + y**2)
      heapq.heappushpop(maxHeap, (dist, x, y))
    return [[x, y] for (dist, x, y) in maxHeap] 
  
  # Time complexity: O(nlogk)
  # Inserting an item to a heap of size k take `O(logK)` time. And we do this for each item points. So runtime is `O(N * logK)` where N is the length of `points`.
  # Space complexity: O(k)
  ```
  

## 7. 621 [Task Scheduler](https://leetcode.com/problems/task-scheduler/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (54.62%) | [`array`](https://leetcode.com/tag/array); [`greedy`](https://leetcode.com/tag/greedy); [`queue`](https://leetcode.com/tag/queue) |

Given a characters array `tasks`, representing the tasks a CPU needs to do, where each letter represents a different task. Tasks could be done in any order. Each task is done in one unit of time. For each unit of time, the CPU could complete either one task or just be idle.

However, there is a non-negative integer `n` that represents the cooldown period between two **same tasks** (the same letter in the array), that is that there must be at least `n` units of time between any two same tasks.

Return *the least number of units of times that the CPU will take to finish all the given tasks*.

**Example 1:**

```
Input: tasks = ["A","A","A","B","B","B"], n = 2
Output: 8
Explanation: 
A -> B -> idle -> A -> B -> idle -> A -> B
There is at least 2 units of time between any two same tasks.
```

**Example 2:**

```
Input: tasks = ["A","A","A","B","B","B"], n = 0
Output: 6
Explanation: On this case any permutation of size 6 would work since n = 0.
["A","A","A","B","B","B"]
["A","B","A","B","A","B"]
["B","B","B","A","A","A"]
...
And so on.
```

**Example 3:**

```
Input: tasks = ["A","A","A","A","A","A","B","C","D","E","F","G"], n = 2
Output: 16
Explanation: 
One possible solution is
A -> B -> C -> A -> D -> E -> A -> F -> G -> A -> idle -> idle -> A -> idle -> idle -> A
```

- **Constraints:**

  - `1 <= task.length <= 104`

  - `tasks[i]` is upper-case English letter.

  - The integer `n` is in the range `[0, 100]`.

- **Thoughts**

  - 从题目可得，完成所有任务的最短时间取决于**出现次数最多的任务数量**。--> start with the most frequent task --> use a **max-heap** to find

  - <u>Procedures:</u>

    - S1: build a hashmap to count the frequency of each task

    - S2: create a max-heap based on frequency

    - S3: build a queue which has [-newCount, idleTime] (idleTime = time + n)

    - S4: at each time, pop a task from max-heap and add the list to the queue

    - S5: when currentTime == idleTime, pop from the queue and add (-cnt) back to the maxHeap

    - S6: repeat steps until maxHeap and queue are not empty

      <img src="/Users/xinyuzhang/Downloads/IMG_CD947FD0F439-1.jpeg" alt="IMG_CD947FD0F439-1" style="zoom: 33%;" />

- **Solution**

  ```python
  from collections import Counter, deque
  import heapq
  def leastInterval(self, tasks: List[str], n: int) -> int:
    count = Counter(tasks)
    maxHeap = [-c for c in count.values()]
    heapq.heapify(maxHeap)
    
    time = 0
    q = deque()
    while maxHeap or q:
      time += 1
      if maxHeap:										# add the pair (cnt, time + n) to the queue						
        cnt = heapq.heappop(maxHeap)
        newCnt = cnt + 1
        if newCnt:
          q.append((newCnt, time + n))
      else:
        time = q[0][1]							# if maxHeap is empty, the current time is the idleTime in q
      
      if q and q[0][1] == time:			# add the task back to the maxHeap
        heapq.heappush(maxHeap, q.popleft()[0])
    return time
  ```

  Time complexity: $O(nlogn)$ --> 但由于题目说明task[i]一定是uppercase letter，所以一共是O(log26) --> O(Nlog26) = O(N) 

  Space complexity: $O(2 \times N) = O(N)$ --> build a heap to store most n values

## 8. 295 [Find Median from Data Stream](https://leetcode.com/problems/find-median-from-data-stream/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Hard (50.42%) | [`heap`](https://leetcode.com/tag/heap); [`design`](https://leetcode.com/tag/design) |

The **median** is the middle value in an ordered integer list. If the size of the list is even, there is no middle value and the median is the mean of the two middle values.

- For example, for `arr = [2,3,4]`, the median is `3`.
- For example, for `arr = [2,3]`, the median is `(2 + 3) / 2 = 2.5`.

Implement the MedianFinder class:

- `MedianFinder()` initializes the `MedianFinder` object.
- `void addNum(int num)` adds the integer `num` from the data stream to the data structure.
- `double findMedian()` returns the median of all elements so far. Answers within `10-5` of the actual answer will be accepted.

**Example 1:**

```java
Input
["MedianFinder", "addNum", "addNum", "findMedian", "addNum", "findMedian"]
[[], [1], [2], [], [3], []]
Output
[null, null, null, 1.5, null, 2.0]

Explanation
MedianFinder medianFinder = new MedianFinder();
medianFinder.addNum(1);    // arr = [1]
medianFinder.addNum(2);    // arr = [1, 2]
medianFinder.findMedian(); // return 1.5 (i.e., (1 + 2) / 2)
medianFinder.addNum(3);    // arr[1, 2, 3]
medianFinder.findMedian(); // return 2.0
```

- **Constraints:**

  - `-105 <= num <= 105`

  - There will be at least one element in the data structure before calling `findMedian`.

  - At most `5 * 104` calls will be made to `addNum` and `findMedian`. 

- **Follow up:**

  - If all integer numbers from the stream are in the range `[0, 100]`, how would you optimize your solution?

  - If `99%` of all integer numbers from the stream are in the range `[0, 100]`, how would you optimize your solution?

- **Thoughts**

  - 看到求中位数的题目，首先想到的方法是将数组排序，如果数组长度是奇数，最中间的一个元素就是中位数，如果数组长度是偶数，最中间两个元素的平均数作为中位数。--> 然而如果数据规模非常巨大，排序所需时间复杂度过高

  - **核心思路：使用两个优先级队列**

    - 中位数是有序数组最中间的元素算出来的，我们可以把「有序数组」抽象成一个倒三角形，宽度可以视为元素的大小，那么这个倒三角的中部就是计算中位数的元素 --> 把这个大的倒三角形从正中间切成两半，变成一个小倒三角和一个梯形，这个小倒三角形相当于一个从小到大的有序数组，这个梯形相当于一个从大到小的有序数组。

    - 梯形虽然是小顶堆，但其中的元素是较大的，我们称其为`large`，倒三角虽然是大顶堆，但是其中元素较小，我们称其为`small`。

    - 梯形中的最小宽度要大于等于小倒三角的最大宽度，这样它俩才能拼成一个大的倒三角 --> **要维护`large`和`small`的元素个数之差不超过 1，还要维护`large`堆的堆顶元素要大于等于`small`堆的堆顶元素** --> 技巧：**想要往`large`里添加元素，不能直接添加，而是要先往`small`里添加，然后再把`small`的堆顶元素加到`large`中；向`small`中添加元素同理**

      假设我们准备向`large`中插入元素：

      如果插入的`num`小于`small`的堆顶元素，那么`num`就会留在`small`堆里，为了保证两个堆的元素数量之差不大于 1，作为交换，把`small`堆顶部的元素再插到`large`堆里。

    <img src="https://mmbiz.qpic.cn/sz_mmbiz_jpg/gibkIz0MVqdEqmDektgFAZh0j0hW5oRYgWchxg0mXJDZI4ycjoM2BTBGesicmkmmg4VgnLA9YLcJM0EuicIy8aA7w/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1" alt="Image" style="zoom:50%;" />

- **Solution**

  ```python
  import heapq
  class MedianFinder:
  
      # Build two heaps, `maxHeap for small values and `minHeap for large values
      def __init__(self):
          self.smallVal = []  # for maxHeap
          self.largeVal = []  # for minHeap  
          heapq.heapify(self.smallVal)
          heapq.heapify(self.largeVal)
  
      # key: 两个堆中的元素数量之差不能超过 1 + large堆的堆顶元素要大于等于small堆的堆顶元素 --> 构成一个三角形
      # S1: check which heap has less values
      # S2: for the heap with less values, first add num to the other heap, then pop the other heap and add the value to the heap
      def addNum(self, num: int) -> None:
          if len(self.smallVal) <= len(self.largeVal):  # add value to the small heap
              heapq.heappush(self.largeVal, num)
              heapq.heappush(self.smallVal, -1 * heapq.heappop(self.largeVal))
          else:
              heapq.heappush(self.smallVal, -1 * num)
              heapq.heappush(self.largeVal, -1 * heapq.heappop(self.smallVal))
           
      # S1: if length is even --> calculate the median with the two top values from the two heaps
      # S2: if length is odd --> check which heap has a longer length; the longer one has the median on top
      def findMedian(self) -> float:
          nS = len(self.smallVal)
          nL = len(self.largeVal)
          if nS == nL:
              return (-1 * self.smallVal[0] + self.largeVal[0]) / 2
          elif nS > nL:
              return -1 * self.smallVal[0]
          elif nL > nS:
              return self.largeVal[0]
  ```

  - Time complexity: $addNum$: O(logn)，其中 n 为累计添加的数的数量。$findMedian$: O(1)。

    Space complexity: O(n) --> for heap
