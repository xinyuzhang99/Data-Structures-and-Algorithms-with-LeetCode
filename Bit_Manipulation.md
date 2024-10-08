# Bit Manipulation 位操作

<img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220824164628325.png" alt="image-20220824164628325" style="zoom:50%;" />

<img src="https://pic.leetcode-cn.com/1615691379-TPWDJp-image.png" alt="image.png" style="zoom: 33%;" />

**Note:** Python bitwise operators work only on integers.

- **常见的位操作**

  ```python
  # 1. 利用或操作 | 和空格将英文字符转换为小写
  ('a' | '') = 'a'
  ('A' | '') = 'a'
  
  # 2. 利用与操作 & 和下划线将英文字符转换为大写
  ('b' & '_') = 'B'
  ('B' & '_') = 'B'
  
  # 3. 利用异或操作 ^ 和空格进行英文字符大小写互换 (XOR: exclusive or)
  ('d' ^ '') = 'D'
  ('D' ^ ' ') = 'd'
  
  # 4. 判断两个数是否异号：这个技巧还是很实用的，利用的是补码编码的符号位。如果不用位运算来判断是否异号，需要使用 if else 分支，还挺麻烦的。读者可能想利用乘积或者商来判断两个数是否异号，但是这种处理方式可能造成溢出，从而出现错误。
  x, y = -1, 2
  f = ((x ^ y) < 0)     # True
  
  x, y = 3, 2
  f = ((x ^ y) < 0)     # False
  
  # 5. 不用临时变量交换两个数
  a = 1, b = 2;
  a ^= b  # a = a ^ b
  b ^= a
  a ^= b
  # 现在 a = 2, b = 1
  
  # 6. 加一
  n = 1
  n = -~n
  # 现在 n = 2
  
  # 7. 减一
  n = 2
  n = ~-n
  # 现在 n = 1
  
  # 8. extract the rightmost bit 
  n % 2
  n & 1
  
  # 整数集合set位运算
  # 整数集合做标志时，比如回溯时的visited标志数组
  vstd 访问 i ：vstd | (1 << i)
  vstd 离开 i ：vstd & ~(1 << i)
  vstd 不包含 i : not vstd & (1 << i)
  
  并集 ：A | B
  交集 ：A & B
  全集 ：(1 << n) - 1
  补集 ：((1 << n) - 1) ^ A
  子集 ：(A & B) == B
  判断是否是 2 的幂 ：A & (A - 1) == 0
  最低位的 1 变为 0 ：n &= (n - 1)
  最低位的 1：A & (-A)，最低位的 1 一般记为 lowbit(A)
  ```

- **`n & (n-1)`的运用**

  **`n & (n-1)` 这个操作是算法中常见的，作用是消除数字 `n` 的二进制表示中的最后一个 1**。

  <img src="https://labuladong.github.io/algo/images/%e4%bd%8d%e6%93%8d%e4%bd%9c/1.png" alt="img" style="zoom:50%;" />

  其核心逻辑就是，`n - 1` 一定可以消除最后一个 1，同时把其后的 0 都变成 1，这样再和 `n` 做一次 `&` 运算，就可以仅仅把最后一个 1 变成 0 了。 

  - 1、计算汉明权重（Hamming Weight） 191. [Number of 1 Bits](https://leetcode.com/problems/number-of-1-bits/description/)
  - 2、判断一个数是不是 2 的指数

- **`a ^ a = 0` 的运用**

  异或运算的性质是需要我们牢记的：一个数和它本身做异或运算结果为 0，即 `a ^ a = 0`；一个数和 0 做异或运算的结果为它本身，即 `a ^ 0 = a`。


## 1. 191 [Number of 1 Bits](https://leetcode.com/problems/number-of-1-bits/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Easy (62.05%) | [`bit-manipulation`](https://leetcode.com/tag/bit-manipulation) |

Write a function that takes an unsigned integer and returns the number of '1' bits it has (also known as the [Hamming weight](http://en.wikipedia.org/wiki/Hamming_weight)).

**Note:**

- Note that in some languages, such as Java, there is no unsigned integer type. In this case, the input will be given as a signed integer type. It should not affect your implementation, as the integer's internal binary representation is the same, whether it is signed or unsigned.
- In Java, the compiler represents the signed integers using [2's complement notation](https://en.wikipedia.org/wiki/Two's_complement). Therefore, in **Example 3**, the input represents the signed integer. `-3`.

**Example 1:**

```
Input: n = 00000000000000000000000000001011
Output: 3
Explanation: The input binary string 00000000000000000000000000001011 has a total of three '1' bits.
```

**Example 2:**

```
Input: n = 00000000000000000000000010000000
Output: 1
Explanation: The input binary string 00000000000000000000000010000000 has a total of one '1' bit.
```

**Example 3:**

```
Input: n = 11111111111111111111111111111101
Output: 31
Explanation: The input binary string 11111111111111111111111111111101 has a total of thirty one '1' bits.
```

**Constraints:**

- The input must be a **binary string** of length `32`.

**Follow up:** If this function is called many times, how would you optimize it?

- **Solution**

  - <u>Method 1: using remainder to check if each digit is 1</u>

    --> 直接循环检查给定整数 n 的二进制位的每一位是否为 1。具体代码中，当检查第 i 位时，我们可以让 n 与 $2^i$ 进行与运算，当且仅当 n 的第 i 位为 1 时，运算结果不为 0。

    ```python
    def hammingWeight(self, n: int) -> int:
      res = 0
      while n:
        # res += 1 if n % 2 == 1 else 0
        res += n % 2
        # n = n >> 1
        n >>= 1
      return res
    ```

    - Time complexity: O(32) = O(1) --> every string is of length 32

      Space complexity: O(1)

  - <u>Method 2: Optimization `n & (n - 1)`</u> --> <font color=blue>**[Follow-up]**</font>

    **`n & (n-1)` 这个操作是算法中常见的，作用是消除数字 `n` 的二进制表示中的最后一个 1**。--> `n - 1` makes sure the rightmost 1 bit changes to 0 bit; `n & (n - 1)` makes other bits of the string remain the same

    这样我们可以利用这个位运算的性质加速我们的检查过程，在实际代码中，我们不断让当前的 n 与 n−1 做与运算，直到 n 变为 0 即可。因为每次运算会使得 n 的最低位的 1 被翻转，因此运算次数就等于 n 的二进制位中 1 的个数。

    --> <font color=blue>**repeatedly flip the least-significant 1-bit of the number to 0**</font>, and add 1 to the sum. As soon as the number becomes 0, we know that it does not have any more 11-bits, and we return the sum
  
    ```python
    def hammingWeight(self, n: int) -> int:
      res = 0
      while n:
        # n = n & (n - 1)
        n &= n - 1
        res += 1
      return res
    ```

    - Time complexity: O(32) = O(1) --> every string is of length 32, <font color=red>but no need to implement division calculations each time</font>

      Space complexity: O(1)

  - <u>Method 3: apply Python library functions</u>

    在 Python 语言中，使用 `bin()` 函数可以得到一个整数的二进制字符串
  
    ```python
    def hammingWeight(self, n: int) -> int:
      return bin(n).count('1')
    ```

    <font color=red>需要注意的是，二进制字符串是以 "0b" 开头，所以如果题目要问的是二进制中 0 的个数，需要注意答案是 `bin(n).count("0") - 1`</font>

    - Time complexity: O(32) = O(1) --> every string is of length 32
  
      Space complexity: O(32)

### 1.1 1356 [Sort Integers by The Number of 1 Bits](https://leetcode.com/problems/sort-integers-by-the-number-of-1-bits/description/)

|  Category  |  Difficulty   |                Tags                 |
| :--------: | :-----------: | :---------------------------------: |
| algorithms | Easy (71.43%) | Bit Manipulation; Sorting; Counting |

You are given an integer array `arr`. Sort the integers in the array in ascending order by the number of `1`'s in their binary representation and in case of two or more integers have the same number of `1`'s you have to sort them in ascending order.

Return *the array after sorting it*. 

**Example 1:**

```
Input: arr = [0,1,2,3,4,5,6,7,8]
Output: [0,1,2,4,8,3,5,6,7]
Explantion: [0] is the only integer with 0 bits.
[1,2,4,8] all have 1 bit.
[3,5,6] have 2 bits.
[7] has 3 bits.
The sorted array by bits is [0,1,2,4,8,3,5,6,7]
```

**Example 2:**

```
Input: arr = [1024,512,256,128,64,32,16,8,4,2,1]
Output: [1,2,4,8,16,32,64,128,256,512,1024]
Explantion: All integers have 1 bit in the binary representation, you should just sort them in ascending order. 
```

- **Constraints:**

  - `1 <= arr.length <= 500`

  - `0 <= arr[i] <= 10^4`

- **Thoughts**

  这道题是191的变形题，要根据计数得到的么一个整数1 bit的个数进行排序 --> 在191的基础上进行排序操作

  - S1: count the number of 1 bits of each integer (use a helper function)
  - S2: sort the array based on the helper function as a key

- **Solution**

  ```python
  def sortByBits(self, arr: List[int]) -> List[int]:
    def count1bits(n: int):
      count = 0
      while n:
        n &= n - 1
        count += 1
    	return count
          
    return sorted(arr, key=lambda x: (count1bits(x), x))  # complex sorts: sort the array firstly by count, then by value
  ```

  - Time complexity: O(nlogn), 其中 n 为整数数组 *arr* 的长度，由于`count1bits`的时间复杂度为O(1)，所以整体的时间复杂度为排序的时间复杂度

    Space complexity: O(1)

### 1.2 383 [Counting Bits](https://leetcode.com/problems/counting-bits/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Easy (74.39%) | [`dynamic-programming`](https://leetcode.com/tag/dynamic-programming); [`bit-manipulation`](https://leetcode.com/tag/bit-manipulation) |

Given an integer `n`, return *an array* `ans` *of length* `n + 1` *such that for each* `i` (`0 <= i <= n`)*,* `ans[i]` *is the **number of*** `1`***'s** in the binary representation of* `i`.

**Example 1:**

```
Input: n = 2
Output: [0,1,1]
Explanation:
0 --> 0
1 --> 1
2 --> 10
```

**Example 2:**

```
Input: n = 5
Output: [0,1,1,2,1,2]
Explanation:
0 --> 0
1 --> 1
2 --> 10
3 --> 11
4 --> 100
5 --> 101
```

- **Constraints:**
  - `0 <= n <= 105`

- **Follow up:**

  - It is very easy to come up with a solution with a runtime of `O(n log n)`. Can you do it in linear time `O(n)` and possibly in a single pass?

  - Can you do it without using any built-in function (i.e., like `__builtin_popcount` in C++)?

- **Solution**

  - <u>Method 1: Bit Manipulation</u>

    这道题实际也是求从0-n所有数字二进制含有的1比特数，其实与LeetCode 191完全一致 --> 使用`n = n & (n - 1)`

    ```python
    def countBits(self, n: int) -> List[int]:
            res = []
            for i in range(n + 1):  # O(N)
                num = 0
                while i:     # The number of iterations in the inner loop depends on the number of set bits in i, which is usually much smaller than n --> average time complexity of the inner loop: O(logN)      
                    i &= i - 1  
                    num += 1
                res.append(num)
            return res
    ```

    - Time complexity: $O(NlogN)$ --> 需要对从 0 到 *n* 的每个整数使用计算「一比特数」，对于每个整数计算「一比特数」的时间都不会超过 O*(log*n)。

      Space complexity: O(1)

  - <u>Method 2: Pattern based on Odd and Even Numbers</u> ==**Follow-up**==

    上述Method 1固然可以解决该问题，但是没有利用题目的特殊情况：题目要求的是从0到n的二进制一比特数，这样可以根据先前的一比特数推断后面数的一比特数 --> 这里可以从奇偶找到规律

    - **奇数**：二进制表示中，奇数一定比前面那个偶数多一个 1，因为多的就是最低位的 1。
               举例： 
               0 = 0       1 = 1
               2 = 10      3 = 11
    - **偶数**：二进制表示中，偶数中 1 的个数一定和除以 2 之后的那个数一样多。因为最低位是 0，除以 2 就是右移一位，也就是把那个 0 抹掉而已，所以 1 的个数是不变的。
                举例：
                2 = 10       4 = 100       8 = 1000
                3 = 11       6 = 110       12 = 1100

    ```python
    def countBits(self, n: int) -> List[int]:
            res = [0]
            for i in range(1, n + 1):
                if i % 2 == 0:      # 偶数时，与除以2的数的一比特数一样多
                    res.append(res[i//2])
                else:								# 奇数时，与前一个数（偶数）的一比特数一样
                    res.append(res[i - 1] + 1)
            return res
    ```

    - Time complexity: O(N)

      Space complexity: O(N)

## 2. 231 [Power of Two](https://leetcode.com/problems/power-of-two/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Easy (45.06%) | [`math`](https://leetcode.com/tag/math); [`bit-manipulation`](https://leetcode.com/tag/bit-manipulation) |

Given an integer `n`, return *`true` if it is a power of two. Otherwise, return `false`*.

An integer `n` is a power of two, if there exists an integer `x` such that `n == 2x`. 

**Example 1:**

```
Input: n = 1
Output: true
Explanation: 20 = 1
```

**Example 2:**

```
Input: n = 16
Output: true
Explanation: 24 = 16
```

**Example 3:**

```
Input: n = 3
Output: false
```

**Constraints:**

- `-231 <= n <= 231 - 1`

**Follow up:** Could you solve it without loops/recursion?

- **Solution**

  <font color=red>**注意：0和负数都不是2的幂**</font>

  - <u>Method 1: common sense</u>

    --> if a value is a power of two --> at each time, n % 2 == 0 until n = 1

    ```python
    def isPowerOfTwo(self, n: int) -> bool:
      if n < 1:
        return False
      while n > 1:
        if n % 2 == 1:
          return False
        n /= 2
      return True
    
    ## Simplified Version
    def isPowerOfTwo(self, n: int) -> bool:
      while n > 1:
        if n % 2 != 0:
          return False
        n /= 2
      return n >= 1 and True
    ```

    - Time complexity: O(n)

      Space complexity: O(1)

  - <u>Method 2: bit manipulation</u> --> <font color=blue>**[Follow-up]**</font>

    一个数 *n* 是 2 的幂，当且仅当 *n* 是正整数，并且 <font color=red>***n* 的二进制表示中仅包含 1 个 1**</font>。

    ```
    2^0 = 1 = 0b0001
    2^1 = 2 = 0b0010
    2^2 = 4 = 0b0100
    ```

    <img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220824222810906.png" alt="image-20220824222810906" style="zoom:40%;" />

    ```python
    def isPowerOfTwo(self, n: int) -> bool:
      if n <= 0:
        return False
    
      return (n & (n - 1)) == 0
    
    ## Simplified Version
    def isPowerOfTwo(self, n: int) -> bool:
      return n > 0 and (n & (n - 1)) == 0
    ```

    - Time complexity: O(1)

      Space complexity: O(1)

## 3. 136 [Single Number](https://leetcode.com/problems/single-number/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Easy (69.46%) | [`hash-table`](https://leetcode.com/tag/hash-table); [`bit-manipulation`](https://leetcode.com/tag/bit-manipulation) |

Given a **non-empty** array of integers `nums`, every element appears *twice* except for one. Find that single one.

You must implement a solution with a linear runtime complexity and use only constant extra space.

**Example 1:**

```
Input: nums = [2,2,1]
Output: 1
```

**Example 2:**

```
Input: nums = [4,1,2,1,2]
Output: 4
```

**Example 3:**

```
Input: nums = [1]
Output: 1
```

- **Constraints:**

  - `1 <= nums.length <= 3 * 104`

  - `-3 * 104 <= nums[i] <= 3 * 104`

  - Each element in the array appears twice except for one element which appears only once.

- **Thoughts**

  - 如果这道题没有对时间和空间复杂度的要求，第一个想法是使用hashmap或者hashset解决，步骤如下：

    - S1: add each element in a hashset
    - S2: if encountered a repeated element, remove it from the hashset 
    - S3: return the last element in the hashset

    这样的解法时间复杂度为O(N)，空间复杂度为O(N)

  - 通过题目中的every element appears *twice* except for one，可以发现题目明确说每个元素出现两次而不是多次，于是可以考虑使用异或运算解决！

    关键：`n ^ n = 0; n ^ 0 = n`

    <img src="/Users/xinyuzhang/Downloads/IMG_A76A8D03BAD8-1.jpeg" alt="IMG_A76A8D03BAD8-1" style="zoom:40%;" />

- **Solution**

  ```python
  def singleNumber(self, nums: List[int]) -> int:
    res = 0
    for n in nums:
      res ^= n
    return res
  ```

  - Time complexity: O(N)

    Space complexity: O(1)

## 4. 268 [Missing Number](https://leetcode.com/problems/missing-number/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Easy (59.75%) | [`array`](https://leetcode.com/tag/array); [`math`](https://leetcode.com/tag/math); [`bit-manipulation`](https://leetcode.com/tag/bit-manipulation) |

Given an array `nums` containing `n` distinct numbers in the range `[0, n]`, return *the only number in the range that is missing from the array.*

**Example 1:**

```
Input: nums = [3,0,1]
Output: 2
Explanation: n = 3 since there are 3 numbers, so all numbers are in the range [0,3]. 2 is the missing number in the range since it does not appear in nums.
```

**Example 2:**

```
Input: nums = [0,1]
Output: 2
Explanation: n = 2 since there are 2 numbers, so all numbers are in the range [0,2]. 2 is the missing number in the range since it does not appear in nums.
```

**Example 3:**

```
Input: nums = [9,6,4,2,3,5,7,0,1]
Output: 8
Explanation: n = 9 since there are 9 numbers, so all numbers are in the range [0,9]. 8 is the missing number in the range since it does not appear in nums.
```

**Constraints:**

- `n == nums.length`
- `1 <= n <= 104`
- `0 <= nums[i] <= n`
- All the numbers of `nums` are **unique**.

**Follow up:** Could you implement a solution using only `O(1)` extra space complexity and `O(n)` runtime complexity?

- **Solution**

  - <u>Method 3: Bit Manipulation</u>

    因为异或满足交换律，所以 `1^2^3 = 3^2^1 = 1^3^2`，顺序无所谓的，把数字和下标都摆在一行,然后两个数值相等的一组抹成0,你就会发现最终就只会有那个丢失的数字的索引孤零零的留在原地,然后0异或它作为结果返回即可。

    <img src="/Users/xinyuzhang/Downloads/IMG_A12472AC7852-1.jpeg" alt="IMG_A12472AC7852-1" style="zoom: 10%;" />

    <font color=red>**注意index的取值范围为[0, n-1]，所以还要单独和n异或一次**</font>

  ```python
  def missingNumber(self, nums: List[int]) -> int:
    ## Method 1: hashset --> time complexity: O(n); space complexity: O(n)
    full = set(range(len(nums) + 1))
    for n in nums:
      full.remove(n)
    return list(full)[0] 								# 要提取set里的元素时，需要先将set转换成list! 
  	# Time complexity: O(n)
    # Space complexity: O(n)
  
    ## Method 2: Sum of arithmetic sequency
    totalSum = sum(range(len(nums) + 1)) # 求和公式：(首项 + 末项) * 项数 / 2: ((0 + n) * (n + 1)) / 2
    partialSum = sum(nums)
    return totalSum - partialSum   
  	# Time complexity: O(n)
    # Space complexity: O(1)
  
  	# Simplified version
  	# add each value in [0, n] and substrace each value in nums --> get the result
    res = len(nums)
    for i in range(len(nums)):
      res += i - nums[i]
    return res 
  	# Time complexity: O(n)
    # Space complexity: O(1)
  
    ## Method 3: Bit manipulation (XOR) --> Commutative property
    # 5 ^ 5 ^ 3 = 3
    # [0, 1, 2, 3] ^ [3, 0, 1] = [2]
    # 只要把所有的元素和索引做异或运算，成对儿的数字都会消为 0，只有这个落单的元素会剩下
    n = len(nums)
    res = 0
    for i in range(n):
      # res = res ^ i ^ nums[i]
      res ^= i ^ nums[i] # i:[0, n - 1], nums[i]: [0, n] with a missing value
    return res ^= n
    # Time complexity: O(n)
    # Space complexity: O(n)
  ```

## 5. 7 [Reverse Integer](https://leetcode.com/problems/reverse-integer/description/)

|  Category  |   Difficulty    |                  Tags                   |
| :--------: | :-------------: | :-------------------------------------: |
| algorithms | Medium (26.76%) | [`math`](https://leetcode.com/tag/math) |

Given a signed 32-bit integer `x`, return `x` *with its digits reversed*. If reversing `x` causes the value to go outside the signed 32-bit integer range `[-2^31, 2^31 - 1]`, then return `0`.

**Assume the environment does not allow you to store 64-bit integers (signed or unsigned).** 

**Example 1:**

```
Input: x = 123
Output: 321
```

**Example 2:**

```
Input: x = -123
Output: -321
```

**Example 3:**

```
Input: x = 120
Output: 21
```

- **Constraints:**
  - `-231 <= x <= 231 - 1`
- **Thoughts**
  - 这道题如果只是反转整数较为简单，有两种思路：1. 将整数转换为字符串，再将反转后的字符串转换为整数 2. 将每一个digit通过取模操作计算出来, 再重新组成新的整数。但是题目说**Assume the environment does not allow you to store 64-bit integers (signed or unsigned). **要求不允许使用 64 位整数，即运算过程中的数字必须在 32 位有符号整数的范围内 --> <font color=red>**不能直接算出答案再进行大小比较，要提前一位进行判断**</font>
  - 关键点：
    - x可能是负数，所以需要对绝对值反转字符串再加上负号；负数取模使用`fmod`函数，整除要向下整除: `digit = int(math.fmod(x, 10)); x = int(x / 10)`
    - 反转后的结果有大小限制，这就需要在返回结果之前先筛选出有效答案

- **Solution**

  ```python
  ## Method 1: Convert to reversing string <Wrong method!! -->  not satisfy requirements>
  def reverse(self, x: int) -> int:
    res = int(str(abs(x))[::-1])
    res = -res if x < 0 else res
    return res if -2**31 <= res <= 2**31 + 1 else 0
  ## Time complexity: O(N)
  ## Space complexity: O(1)
  
  ## Method 2: Digit calculation <Correct method!!>
  def reverse(self, x: int) -> int:
    INT_MAX = 2**31 - 1
    INT_MIN = -2**31
    res = 0
    
    # 循环的判断条件应该是 while(x!=0)，无论正数还是负数，按照上面不断的/10这样的操作，最后都会变成0
    # 如果 x 是正整数，则循环的判断条件应该是 while (x > 0):
    while x:
      digit = int(math.fmod(x, 10))				# x % 10
      x = int(x / 10)											# x // 10
      
      # Detect if there is data overflow
      # 1. reversed x is larger than max_value
      if res > INT_MAX//10 or (res == INT_MAX//10 and digit >= INT_MAX%10):
        return 0
      # 2. reversed x is smaller than min_value
      if res < int(INT_MIN//10) or (res == int(INT_MIN//10) and digit <= math.fmod(INT_MIN%10)):
        return 0   
      res = res * 10 + digit
    return res 
  ## Time complexity: O(log_10(x)) 翻转的次数即 x 十进制的位数 --> x 中大约有 log10(x) 位数字: 由于x是32位二进制数字，而显示在输入中的是十进制，翻转的也是通过十进制翻转
  ## Space complexity: O(1)
  ```
