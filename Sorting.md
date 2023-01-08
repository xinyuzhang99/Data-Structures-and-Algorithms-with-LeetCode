# Sorting 排序

- **归并排序笔记详见<u>Coding Template 11</u>📒**

## 1. ※※ 169 [Majority Element](https://leetcode.com/problems/majority-element/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Easy (62.97%) | [`array`](https://leetcode.com/tag/array); [`divide-and-conquer`](https://leetcode.com/tag/divide-and-conquer); [`bit-manipulation`](https://leetcode.com/tag/bit-manipulation) |

Given an array `nums` of size `n`, return *the majority element*.

The majority element is the element that appears more than `⌊n / 2⌋` times. You may assume that the ==majority element always exists== in the array.

**Example 1:**

```
Input: nums = [3,2,3]
Output: 3
```

**Example 2:**

```
Input: nums = [2,2,1,1,1,2,2]
Output: 2
```

**Constraints:**

- `n == nums.length`
- `1 <= n <= 5 * 104`
- `-109 <= nums[i] <= 109`

**Follow-up:** Could you solve the problem in linear time and in `O(1)` space?

- **Solution**

  - <u>Method 1: Hashing table/map</u>

    - S1: create a hashtable to store nums (key) and count(values)
    - S2: return the key whose counts > (n/2)

    ```python
    def majorityElement(self, nums: List[int]) -> int:
            
            # Solution 1:
            hashtable = dict()
            n = len(nums)
            
            for i in nums:
                if i in hashtable:
                    hashtable[i] += 1
                else:
                    hashtable[i] = 1
            
            for num, count in hashtable.items():
                if count > (n/2):
                    return num
            # Time complexity: O(N)
            # Space complexity: O(N) --> build a dictionary
    
            # Solution 2:
            # from collections import Counter
            counts = Counter(nums)   # automatically create a dictionary
            return max(counts.keys(), key=counts.get)
            # Time complexity: O(N)
            # Space complexity: O(N) --> build a dictionary
    
            # Solution 3:
            count = {}
            res, maxCount = 0, 0
    
            for n in nums:
                count[n] = 1 + count.get(n, 0) 
                res = n if count[n] > maxCount else res # find the element with the largest count
                maxCount = max(count[n], maxCount)   
            return res 
            # Time complexity: O(N)
            # Space complexity: O(N) --> build a dictionary
    ```

    - <font color=red>**注意点：**</font>

      - 建立哈希表时，可以直接使用 `counts = Counter(nums)`自动生成，记得引入库 `from collections import Counter`

      - `max(iterable, key)`函数中，`key`为一个function，体现具体根据iterable的什么功能进行max操作。

        例如:

        ```python
        # output the string with the longest length
        a_list = ["aaa", "bb", "c"]
        longest_string = max(a_list, key=len)
        ```

        在此题中，

        ```python
        max(counts.keys(), key=counts.get) 
        ```

        `max`返回`key`的值 --> 这里`key`为具体value，即为题目要输出的结果值，而该值是具有字典最大value值的key。字典中的`counts.get`函数可获得具体一个`key`的`value`

        ```python
        # get(): return the value for a specified key if it is in the dictionary
        # value: optional --> the value to be returned if the key is not found
        dict.get(key,value)
        myDict.get('age', 27)  # return 26
        myDict.get('city', 27) # return 27
        myDict.get('city')     # return None
        ```

      - `count[n] = 1 + count.get(n, 0)` --> 适用于哈希表计数，如果表中已有该值，则`count = 1 + 之前的count`，否则`count = 1`

  - <u>Method 2: Sorting</u>

    题目要求找出唯一具有最多count的数，则这个数为众数 --> 如果将数组 nums 中的所有元素按照单调递增或单调递减的顺序排序，那么下标为 $\lfloor \dfrac{n}{2} \rfloor$ 的元素（下标从 0 开始）一定是众数。

    ```python
    def majorityElement(self, nums: List[int]) -> int:
      nums.sort()
      return nums[len(nums) // 2]
    ```

    Time complexity: $O(nlogn)$ --> 将数组排序的时间复杂度 (Timesort)

    Space complexity: $O(logn)$ --> 如果使用语言自带的排序算法，需要使用 O*(log*n) 的栈空间

  - ==<u>Method 3: **Boyer-Moore**</u> Algorithm== --> **solve the follow-up question**

    https://www.youtube.com/watch?v=7pnhv842keE

    如果我们把众数记为 +1，把其他数记为 -1，将它们全部加起来，显然和大于 `0`，从结果本身我们可以看出众数比其他数多。

    我们首先给出 Boyer-Moore 算法的详细步骤：

    - 我们维护一个候选众数 candidate 和它出现的次数 count。初始时 candidate 可以为任意值，count 为 0；

    - 我们遍历数组 nums 中的所有元素，对于每个元素 x，在判断 x 之前，如果 count 的值为 0，我们先将 x 的值赋予 candidate，随后我们判断 x：

      - 如果 x 与 candidate 相等，那么计数器 count 的值增加 1；

      - 如果 x 与 candidate 不等，那么计数器 count 的值减少 1。

    - 在遍历完成后，candidate 即为整个数组的众数。

    举一个具体的例子，例如下面的这个数组：

    `[7, 7, 5, 7, 5, 1 | 5, 7 | 5, 5, 7, 7 | 7, 7, 7, 7]`
    在遍历到数组中的第一个元素以及每个在 `|` 之后的元素时，candidate 都会因为 count 的值变为 0 而发生改变。最后一次 candidate 的值从 5 变为 7，也就是这个数组中的众数。

    ```python
    def majorityElement(self, nums: List[int]) -> int:
      res, count = 0, 0
      
      for n in nums:
        if count == 0:
          res = n
        count += (1 if res == n else -1)
      return res
    ```

    - Time complexity: $O(N)$

      Space complexity: $O(1)$ --> not use any additional memory

## 2. 75 [Sort Colors](https://leetcode.com/problems/sort-colors/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (55.12%) | [`array`](https://leetcode.com/tag/array); [`two-pointers`](https://leetcode.com/tag/two-pointers); [`sort`](https://leetcode.com/tag/sort) |

Given an array `nums` with `n` objects colored red, white, or blue, sort them **[in-place](https://en.wikipedia.org/wiki/In-place_algorithm)** so that objects of the same color are adjacent, with the colors in the order red, white, and blue.

We will use the integers `0`, `1`, and `2` to represent the color red, white, and blue, respectively.

You must solve this problem without using the library's sort function.

**Example 1:**

```
Input: nums = [2,0,2,1,1,0]
Output: [0,0,1,1,2,2]
```

**Example 2:**

```
Input: nums = [2,0,1]
Output: [0,1,2]
```

**Constraints:**

- `n == nums.length`
- `1 <= n <= 300`
- `nums[i]` is either `0`, `1`, or `2`.

**Follow up:** Could you come up with a one-pass algorithm using only constant extra space?

- **Solution**

  - <u>Method 1: Counting Sort</u>

    由于数组里只包含 0、1、2，所以可以用hashtable对每一个值出现的次数进行计数，并直接修改原数组

    ```python
    def sortColors(self, nums: List[int]) -> None:
            """
            Do not return anything, modify nums in-place instead.
            """
            ## Method 1: BucketSort --> CountingSort
            hashtable = Counter(nums)
            i = 0
            num = 0
            
            while i < len(nums) and num <= 2:
                n = hashtable[num]
                nums[i:i+n] = [num] * n
                i += n
                num += 1
    ```

    - Time complexity: $O(N)$

      Space complexity: $O(3) = O(1)$ --> hashtable only has 3 elements

  - <u>Method 2: Two-Pointers</u> --> **<font color=blue>Follow-up</font>**

    --> use here three pointers to track the rightmost boundary of zeros, the leftmost boundary of twos and the current element under the consideration.

    <img src="https://leetcode.com/problems/sort-colors/solutions/271695/Figures/75/idx.png" alt="bla" style="zoom:50%;" />

    <img src="/Users/xinyuzhang/Downloads/IMG_726122EEECD9-1.jpeg" alt="IMG_726122EEECD9-1" style="zoom:50%;" />

    ```python
    def sortColors(self, nums: List[int]) -> None:
            """
            Do not return anything, modify nums in-place instead.
            """
        		## Dutch National Flag Problem
        		n = len(nums)
            l, r = 0, n - 1   # for all idx < p0 : nums[idx < p0] = 0; for all idx > p2 : nums[idx > p2] = 2
            curr = 0					# curr is an index of element under consideration		    
    
            while curr <= r:
                if nums[curr] == 2:
                    nums[curr], nums[r] = nums[r], nums[curr]
                    r -= 1
                elif nums[curr] == 0:
                    nums[curr], nums[l] = nums[l], nums[curr]
                    l += 1
                    curr += 1
                else:
                    curr += 1
    ```

    - Time complexity: $O(N)$

      Space complexity: $O(1)$ 
