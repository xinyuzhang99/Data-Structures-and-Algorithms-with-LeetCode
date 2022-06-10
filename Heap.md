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

    ```python
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
    ```

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

    - Time complexity: $O(n)$ --> n 表示数组的长度。首先，遍历一遍数组统计元素的频率，这一系列操作的时间复杂度是 O(n)；桶的数量为 n + 1n+1，所以桶排序的时间复杂度为 O(n)
    - Space complexity: O(n)