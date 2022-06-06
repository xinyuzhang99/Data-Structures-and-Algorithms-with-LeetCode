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
  - 对于找top-k的题目，使用Heap进行排序
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

  

  