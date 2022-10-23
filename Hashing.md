# Hashing

## 1. 242 [Valid Anagram](https://leetcode.com/problems/valid-anagram/description/)

|  Category  |  Difficulty   |                            Likes                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Easy (61.56%) | [`hash-table`](https://leetcode.com/tag/hash-table); [`sort`](https://leetcode.com/tag/sort) |

Given two strings `s` and `t`, return `true` *if* `t` *is an anagram of* `s`*, and* `false` *otherwise*.

An **Anagram** is a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once.

**Example 1:**

```
Input: s = "anagram", t = "nagaram"
Output: true
```

**Example 2:**

```
Input: s = "rat", t = "car"
Output: false
```

**Constraints:**

- `1 <= s.length, t.length <= 5 * 104`
- `s` and `t` consist of lowercase English letters.

- **Follow up:** What if the inputs contain Unicode characters? How would you adapt your solution to such a case? 

  Unicode 是为了解决传统字符编码的局限性而产生的方案，它为每个语言中的字符规定了一个唯一的二进制编码。而 Unicode 中可能存在一个字符对应多个字节的问题，为了让计算机知道多少字节表示一个字符，面向传输的编码方式的 UTF−8 和 UTF−16 也随之诞生逐渐广泛使用，具体相关的知识读者可以继续查阅相关资料拓展视野，这里不再展开。


- **Solution**

  - <u>Method 1: Sort</u> 

    --> t 是 s 的异位词等价于<font color=blue>「**两个字符串排序后相等」**</font>。因此我们可以对字符串 s 和 t 分别排序，看排序后的字符串是否相等即可判断。此外，如果 s 和 t 的长度不同，t 必然不是 s 的异位词。

    ```python
    def isAnagram(self, s: str, t: str) -> bool:
      # Method 1
      return sorted(s) == sorted(t)
    	# Method 2
      return Counter(s) == Counter(t)
    ```

    Time complexity: $O(nlogn + n) = O(nlogn)$ --> where n is the length of s --> Sort: $O(nlogn)$; 比较两个字符串是否相等： O(n)

    Space complexity: $O(logn)$ --> 排序需要 $O(logn)$ 的空间复杂度

  - <u>Method 2: Hashing</u>

    ```python
    def isAnagram(self, s: str, t: str) -> bool:
            hashmap = Counter(s)
    
            if len(s) != len(t):
                return False
            for c in t:
                if c not in hashmap:
                    return False
                else:
                    hashmap[c] -= 1
            
            for c in s:
                if hashmap[c] != 0:
                    return False
            return True    
    ```

    Time complexity: O(n + n) = O(n)

    Space complexity: O(n) --> create a hashmap with n elements


## 2. 49 [Group Anagrams](https://leetcode.com/problems/group-anagrams/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (64.40%) | [`hash-table`](https://leetcode.com/tag/hash-table); [`string`](https://leetcode.com/tag/string) |

Given an array of strings `strs`, group **the anagrams** together. You can return the answer in **any order**.

An **Anagram** is a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once.

**Example 1:**

```
Input: strs = ["eat","tea","tan","ate","nat","bat"]
Output: [["bat"],["nat","tan"],["ate","eat","tea"]]
```

**Example 2:**

```
Input: strs = [""]
Output: [[""]]
```

**Example 3:**

```
Input: strs = ["a"]
Output: [["a"]]
```

**Constraints:**

- `1 <= strs.length <= 104`
- `0 <= strs[i].length <= 100`
- `strs[i]` consists of lowercase English letters.

- **Thoughts**

  - 一旦需要==根据特征进行分类==，就应该利用hash table

  - ```python
     strs = ["eat","tea","tan","ate","nat","bat"]
     ans = {"aet": ["ate","eat","tea"],
            "ant": ["nat","tan"],
            "abt": ["bat"]}
    ```

- **Solution**

  - <u>Method 1: Use sorted string as key</u>

    --> 由于互为字母异位词的两个字符串包含的字母相同，因此对两个字符串分别进行排序之后得到的字符串一定是相同的，故可以将排序之后的字符串作为哈希表的键。

    - S1: create a hash table `res`
    - S2: keys are combinations of letters (sorted), values are according strings
    - S3: return res.values()

    ```python
    from collections import defaultdict
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
      res = defaultdict(list)
      for s in strs:
        key = "".join(sorted(s))
        res[key].append(s)
      return res.values()
    ```

    Time complexity: $O(m*nlogn)$ --> where $n$ is the largest length of each string, $m$ is the number of strings in `strs`

    Space complexity: $O(m*n)$ --> the hash table records every string

  - <u>Method 2: Use counts of each letter (每个字母出现的字数) as key</u>

    --> 由于互为字母异位词的两个字符串包含的字母相同，因此两个字符串中的相同字母出现的次数一定是相同的，故可以将每个字母出现的次数使用字符串表示，作为哈希表的键。由于字符串只包含小写字母，因此对于每个字符串，可以使用长度为 26 的数组记录每个字母出现的次数。

    ```python
    # for each string: count = [0] * 26 --> [a, b, c...,z]
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
      res = defaultdict(list)
      for s in strs:
        count = [0] * 26
        for char in s:
          count[ord(char) - ord('a')] += 1
        res[tuple(count)].append(s)
      return res.values()
    ```

    Time complexity: $O(m*(n+26)$ --> where $n$ is the largest length of each string, $m$ is the number of strings in `strs` --> 需要遍历 m 个字符串，对于每个字符串，需要 O(n) 的时间计算每个字母出现的次数，O(26) 的时间生成哈希表的键

    Space complexity: $O(m*(n+26)$ --> the hash table records every string


## 3. 1010 [Pairs of Songs With Total Durations Divisible by 60](https://leetcode.com/problems/pairs-of-songs-with-total-durations-divisible-by-60/description/)

|  Category  |   Difficulty    |    Tags    |
| :--------: | :-------------: | :--------: |
| algorithms | Medium (53.53%) | Hash Table |

You are given a list of songs where the `ith` song has a duration of `time[i]` seconds.

Return *the number of pairs of songs for which their total duration in seconds is divisible by* `60`. Formally, we want the number of indices `i`, `j` such that `i < j` with `(time[i] + time[j]) % 60 == 0`.

**Example 1:**

```
Input: time = [30,20,150,100,40]
Output: 3
Explanation: Three pairs have a total duration divisible by 60:
(time[0] = 30, time[2] = 150): total duration 180
(time[1] = 20, time[3] = 100): total duration 120
(time[1] = 20, time[4] = 40): total duration 60
```

**Example 2:**

```
Input: time = [60,60,60]
Output: 3
Explanation: All three pairs have a total duration of 120, which is divisible by 60.
```

- **Constraints:**

  - `1 <= time.length <= 6 * 104`

  - `1 <= time[i] <= 500`

- **Thoughts**

  这道题首先会使用暴力解法，用两个for循环，但是会超时。观察的话可得知，(a + b) % 60 == 0 --> (a % 60 + b % 60) % 60 == 0

  Two situations: 1. a % 60 + b % 60 == 60  2. a % 60 = 0 and b % 60 = 0

  这样的话这道题可以转换成 2 Sum类似，可以创建一个hashmap记录所有余数的数量

- **Solution**

  ```python
  def numPairsDivisibleBy60(self, time: List[int]) -> int:
          ## Method 1: brute-force
          n = len(time)
          res = 0
          for i in range(n):
              for j in range(i + 1, n):
                  if (time[i] + time[j]) % 60 == 0:
                      res += 1
          return res
          # Time complexity: O(N^2); Space complexity: O(1)
  
          ## Method 2: hashing
          # Similar to two-sum, use a hashmap to record all remainders and find according pair counts
          # (a + b) % 60 == 0 --> (a % 60 + b % 60) % 60 == 0
          # Two situations: 1. a % 60 + b % 60 == 60  2. a % 60 = 0 and b % 60 = 0
          remainder = defaultdict(int)
          res = 0
          for t in time:
              if t % 60 == 0:					        # situation 1: a % 60 = 0 and b % 60 = 0
                  res += remainder[0]
              else:														# situation 2: a % 60 + b % 60 == 60
                  res += remainder[60 - t%60]
              remainder[t % 60] += 1
          return res   
          # Time complexity: O(N); Space complexity: O(N)
  ```

  

