# Linked List 链表

## 1. ==链表+双指针技巧==

### 1. 21 [Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/description/)

|  Category  |  Difficulty   |    Tags     |
| :--------: | :-----------: | :---------: |
| algorithms | Easy (60.38%) | Linked-list |

You are given the heads of two sorted linked lists `list1` and `list2`.

Merge the two lists in a one **sorted** list. The list should be made by splicing together the nodes of the first two lists.

Return *the head of the merged linked list*.



**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/10/03/merge_ex1.jpg)

```
Input: list1 = [1,2,4], list2 = [1,3,4]
Output: [1,1,2,3,4,4]
```

**Example 2:**

```
Input: list1 = [], list2 = []
Output: []
```

**Example 3:**

```
Input: list1 = [], list2 = [0]
Output: [0]
```

**Constraints:**

- The number of nodes in both lists is in the range `[0, 50]`.
- `-100 <= Node.val <= 100`
- Both `list1` and `list2` are sorted in **non-decreasing** order.

- **Thoughts：**

  - 创建一个dummy虚拟节点，有了 `dummy` 节点这个占位符，可以==避免处理空指针的情况，降低代码的复杂性==。

  - 使用两个双指针分别遍历两个链表，比较每次两个指针所指向的值大小，将小值插入进结果链表里，并让指针向前走一步，然后重复之前步骤 --> 当有一个指针遍历完链表所有元素后，将另一个链表剩下元素全部插入结果链表

    <img src="https://labuladong.github.io/algo/images/%e9%93%be%e8%a1%a8%e6%8a%80%e5%b7%a7/1.gif" alt="img" style="zoom:50%;" />

- **Solution**

  ```python
  from typing import Optional
  class ListNode:
      def __init__(self, val=0, next=None):
          self.val = val
          self.next = next
  class Solution:
      def mergeTwoLists(self, list1: Optional[ListNode], list2: Optional[ListNode]) -> Optional[ListNode]:
          dummyhead = ListNode(0)
          curr = dummyhead
  
          while list1 and list2:            # 当两个链表都没遍历完时
              if list1.val <= list2.val:
                  curr.next = list1
                  curr = curr.next
                  list1 = list1.next
              else:
                  curr.next = list2
                  curr = curr.next
                  list2 = list2.next
          
          if list1:           
              curr.next = list1
          elif list2:
              curr.next = list2
  
          return dummyhead.next
  ```
  
  - Time Complexity: O(N) = O(n + m) --> worst case: traverse through all nodes in the two lists
  
    Space Complexity: O(1)

### 2. 19 [Remove Nth Node From End of List](https://leetcode.com/problems/remove-nth-node-from-end-of-list/description/)

|  Category  |   Difficulty    | Likes | Dislikes |
| :--------: | :-------------: | :---: | :------: |
| algorithms | Medium (38.35%) | 10038 |   479    |

<details open="" style="color: rgb(212, 212, 212); font-family: -apple-system, &quot;system-ui&quot;, &quot;Segoe WPC&quot;, &quot;Segoe UI&quot;, system-ui, Ubuntu, &quot;Droid Sans&quot;, sans-serif; font-size: 14px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><summary><strong>Tags</strong></summary><p style="margin-top: 0px; margin-bottom: 0.7em;"><a href="https://leetcode.com/tag/linked-list" title="https://leetcode.com/tag/linked-list" style="color: var(--vscode-textLink-foreground); text-decoration: none;"><code style="color: var(--vscode-textLink-foreground); font-family: var(--vscode-editor-font-family, &quot;SF Mono&quot;, Monaco, Menlo, Consolas, &quot;Ubuntu Mono&quot;, &quot;Liberation Mono&quot;, &quot;DejaVu Sans Mono&quot;, &quot;Courier New&quot;, monospace); font-size: 1em; line-height: 1.357em; white-space: pre-wrap;">linked-list</code></a><span>&nbsp;</span>|<span>&nbsp;</span><a href="https://leetcode.com/tag/two-pointers" title="https://leetcode.com/tag/two-pointers" style="color: var(--vscode-textLink-foreground); text-decoration: none;"><code style="color: var(--vscode-textLink-foreground); font-family: var(--vscode-editor-font-family, &quot;SF Mono&quot;, Monaco, Menlo, Consolas, &quot;Ubuntu Mono&quot;, &quot;Liberation Mono&quot;, &quot;DejaVu Sans Mono&quot;, &quot;Courier New&quot;, monospace); font-size: 1em; line-height: 1.357em; white-space: pre-wrap;">two-pointers</code></a></p></details>

<details open="" style="color: rgb(212, 212, 212); font-family: -apple-system, &quot;system-ui&quot;, &quot;Segoe WPC&quot;, &quot;Segoe UI&quot;, system-ui, Ubuntu, &quot;Droid Sans&quot;, sans-serif; font-size: 14px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><summary><strong>Companies</strong></summary><p style="margin-top: 0px; margin-bottom: 0.7em;"><code style="color: var(--vscode-textPreformat-foreground); font-family: var(--vscode-editor-font-family, &quot;SF Mono&quot;, Monaco, Menlo, Consolas, &quot;Ubuntu Mono&quot;, &quot;Liberation Mono&quot;, &quot;DejaVu Sans Mono&quot;, &quot;Courier New&quot;, monospace); font-size: 1em; line-height: 1.357em; white-space: pre-wrap;">Unknown</code></p></details>

Given the `head` of a linked list, remove the `nth` node from the end of the list and return its head.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/10/03/remove_ex1.jpg)

```
Input: head = [1,2,3,4,5], n = 2
Output: [1,2,3,5]
```

**Example 2:**

```
Input: head = [1], n = 1
Output: []
```

**Example 3:**

```
Input: head = [1,2], n = 1
Output: [1]
```

**Constraints:**

- The number of nodes in the list is `sz`.
- `1 <= sz <= 30`
- `0 <= Node.val <= 100`
- `1 <= n <= sz`

- **Thoughts**
  - 假设链表有 `n` 个节点，倒数第 `k` 个节点就是正数第 `n - k + 1` 个节点 --> 先让一个指针 `p1` 指向链表的头节点 `head`，然后走 `k` 步， 再用一个指针 `p2` 指向链表头节点 `head` --> 让 `p1` 和 `p2` 同时向前走，`p1` 走到链表末尾的空指针时前进了 `n - k` 步，`p2` 也从 `head` 开始前进了 `n - k` 步，停留在第 `n - k + 1` 个节点上，即恰好停链表的倒数第 `k` 个节点上

- **Solution**

  ```python
  from typing import Optional
  class ListNode:
      def __init__(self, val=0, next=None):
          self.val = val
          self.next = next
  class Solution:
      def removeNthFromEnd(self, head: Optional[ListNode], n: int) -> Optional[ListNode]:
          dummyhead = ListNode() 
          dummyhead.next = head # dummyhead points to the list
  
          slow = dummyhead			# !!注意不是slow = fast = dummyhead.next而是指向空节点
          fast = dummyhead
  
          while n != 0:          # 快指针先走n步
              fast = fast.next
              n -= 1
          
          while fast.next:       # the loop stops when fast points to the last element
              slow = slow.next
              fast = fast.next
          slow.next = slow.next.next   # delete the nth node from the end
          
          return dummyhead.next
  ```

### 3. 876 [Middle of the Linked List](https://leetcode.com/problems/middle-of-the-linked-list/description/)

|  Category  |  Difficulty   | Likes | Dislikes |
| :--------: | :-----------: | :---: | :------: |
| algorithms | Easy (72.70%) | 5393  |   135    |

<details open="" style="color: rgb(212, 212, 212); font-family: -apple-system, &quot;system-ui&quot;, &quot;Segoe WPC&quot;, &quot;Segoe UI&quot;, system-ui, Ubuntu, &quot;Droid Sans&quot;, sans-serif; font-size: 14px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><summary><strong>Tags</strong></summary><p style="margin-top: 0px; margin-bottom: 0.7em;"><a href="https://leetcode.com/tag/ordered-map" title="https://leetcode.com/tag/ordered-map" style="color: var(--vscode-textLink-foreground); text-decoration: none;"><code style="color: var(--vscode-textLink-foreground); font-family: var(--vscode-editor-font-family, &quot;SF Mono&quot;, Monaco, Menlo, Consolas, &quot;Ubuntu Mono&quot;, &quot;Liberation Mono&quot;, &quot;DejaVu Sans Mono&quot;, &quot;Courier New&quot;, monospace); font-size: 1em; line-height: 1.357em; white-space: pre-wrap;">ordered-map</code></a></p></details>

<details open="" style="color: rgb(212, 212, 212); font-family: -apple-system, &quot;system-ui&quot;, &quot;Segoe WPC&quot;, &quot;Segoe UI&quot;, system-ui, Ubuntu, &quot;Droid Sans&quot;, sans-serif; font-size: 14px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"><summary><strong>Companies</strong></summary><p style="margin-top: 0px; margin-bottom: 0.7em;"><code style="color: var(--vscode-textPreformat-foreground); font-family: var(--vscode-editor-font-family, &quot;SF Mono&quot;, Monaco, Menlo, Consolas, &quot;Ubuntu Mono&quot;, &quot;Liberation Mono&quot;, &quot;DejaVu Sans Mono&quot;, &quot;Courier New&quot;, monospace); font-size: 1em; line-height: 1.357em; white-space: pre-wrap;">Unknown</code></p></details>

Given the `head` of a singly linked list, return *the middle node of the linked list*.

If there are two middle nodes, return **the second middle** node.

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/07/23/lc-midlist1.jpg)

```
Input: head = [1,2,3,4,5]
Output: [3,4,5]
Explanation: The middle node of the list is node 3.
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2021/07/23/lc-midlist2.jpg)

```
Input: head = [1,2,3,4,5,6]
Output: [4,5,6]
Explanation: Since the list has two middle nodes with values 3 and 4, we return the second one.
```

- **Constraints:**

  - The number of nodes in the list is in the range `[1, 100]`.

  - `1 <= Node.val <= 100`

- **Thoughts**

  - 由于无法直接得到单链表的长度 `n`，想一次遍历就得到中间节点，使用==「快慢指针」==的技巧：

    我们让两个指针 `slow` 和 `fast` 分别指向链表头结点 `head`。

    **每当慢指针 `slow` 前进一步，快指针 `fast` 就前进两步，这样，当 `fast` 走到链表末尾时，`slow` 就指向了链表中点**。

- **Solution**

  ```python
  from typing import Optional, List
  class ListNode:
      def __init__(self, val=0, next=None):
          self.val = val
          self.next = next
  class Solution:
      def middleNode(self, head: Optional[ListNode]) -> Optional[ListNode]:
          if head is None:
              return []
          slow = head
          fast = head
          while fast and fast.next:    # no need to write (while slow)! 因为慢指针肯定在快指针之后，所以只需定义快指针的临界条件
              slow = slow.next
              fast = fast.next.next
          return slow
  ```

​       Time Complexity: O(N)

​		Space Complexity: O(1)

### 4. 141 [Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Easy (45.92%) | [`linked-list`](https://leetcode.com/tag/linked-list); [`two-pointers`](https://leetcode.com/tag/two-pointers) |

Given `head`, the head of a linked list, determine if the linked list has a cycle in it.

There is a cycle in a linked list if there is some node in the list that can be reached again by continuously following the `next` pointer. Internally, `pos` is used to denote the index of the node that tail's `next` pointer is connected to. **Note that `pos` is not passed as a parameter**.

Return `true` *if there is a cycle in the linked list*. Otherwise, return `false`.

**Example 1:**

![img](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist.png)

```
Input: head = [3,2,0,-4], pos = 1
Output: true
Explanation: There is a cycle in the linked list, where the tail connects to the 1st node (0-indexed).
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist_test2.png)

```
Input: head = [1,2], pos = 0
Output: true
Explanation: There is a cycle in the linked list, where the tail connects to the 0th node.
```

**Example 3:**

![img](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist_test3.png)

```
Input: head = [1], pos = -1
Output: false
Explanation: There is no cycle in the linked list.
```

- **Constraints:**

  - The number of the nodes in the list is in the range `[0, 104]`.

  - `-105 <= Node.val <= 105`

  - `pos` is `-1` or a **valid index** in the linked-list.

- **Thoughts**

  - 解决方案也是用<font color=blue>**快慢指针**</font>：

    每当慢指针 `slow` 前进一步，快指针 `fast` 就前进两步。(similar to 876)

    如果 `fast` 最终遇到空指针，说明链表中没有环；如果 `fast` 最终和 `slow` 相遇，那肯定是 `fast` 超过了 `slow` 一圈，说明链表中含有环 ==若有环，两指针一定会相遇。因为每走 1 轮，`fast` 与 `slow` 的间距 +1，`fast` 终会追上 `slow`== --> if there is a cycle, for each time, the distance between `fast` and `slow` increases by 1, so `fast` will eventually meets `slow`

- **Solution**

  ```python
  from typing import Optional
  class ListNode:
      def __init__(self, x):
          self.val = x
          self.next = None
  
  class Solution:
      def hasCycle(self, head: Optional[ListNode]) -> bool:
          if head is None:
              return False
          slow = head
          fast = head
          while (fast != None and fast.next != None):
              slow = slow.next
              fast = fast.next.next
              if slow == fast:
                  return True
          return False
  ```

  - Time complexity: O(N)

    Space complexity: O(1)

- **Follow-up**

  - If there is a cycle in the linked list, where is the start point of the cycle?

    --> 当快慢指针相遇时，证明有环，这时把slow指针重新指向head，让slow和fast指针同时前进，相遇的时候即是环的起点

    ```python
    def hasCycle(self, head: Optional[ListNode]) -> bool:
            if head is None:
                return None
            slow = head
            fast = head
            while (fast != None and fast.next != None):
                slow = slow.next
                fast = fast.next.next
                if slow == fast:
                    break
            
            if (fast is None or fast.next is None):
              return None
            
            # *Additional code
            slow = head
            while (slow != fast):
              slow = slow.next
              fast = fast.next
            return slow
    ```

## 5. 160 [Intersection of Two Linked Lists](https://leetcode.com/problems/intersection-of-two-linked-lists/description/)

|  Category  |  Difficulty   |                         Tags                          |
| :--------: | :-----------: | :---------------------------------------------------: |
| algorithms | Easy (50.04%) | [`linked-list`](https://leetcode.com/tag/linked-list) |

Given the heads of two singly linked-lists `headA` and `headB`, return *the node at which the two lists intersect*. If the two linked lists have no intersection at all, return `null`.

For example, the following two linked lists begin to intersect at node `c1`:

![img](https://assets.leetcode.com/uploads/2021/03/05/160_statement.png)

The test cases are generated such that there are no cycles anywhere in the entire linked structure.

**Note** that the linked lists must **retain their original structure** after the function returns.

**Custom Judge:**

The inputs to the **judge** are given as follows (your program is **not** given these inputs):

- `intersectVal` - The value of the node where the intersection occurs. This is `0` if there is no intersected node.
- `listA` - The first linked list.
- `listB` - The second linked list.
- `skipA` - The number of nodes to skip ahead in `listA` (starting from the head) to get to the intersected node.
- `skipB` - The number of nodes to skip ahead in `listB` (starting from the head) to get to the intersected node.

The judge will then create the linked structure based on these inputs and pass the two heads, `headA` and `headB` to your program. If you correctly return the intersected node, then your solution will be **accepted**.

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/03/05/160_example_1_1.png)

```
Input: intersectVal = 8, listA = [4,1,8,4,5], listB = [5,6,1,8,4,5], skipA = 2, skipB = 3
Output: Intersected at '8'
Explanation: The intersected node's value is 8 (note that this must not be 0 if the two lists intersect).
From the head of A, it reads as [4,1,8,4,5]. From the head of B, it reads as [5,6,1,8,4,5]. There are 2 nodes before the intersected node in A; There are 3 nodes before the intersected node in B.
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2021/03/05/160_example_2.png)

```
Input: intersectVal = 2, listA = [1,9,1,2,4], listB = [3,2,4], skipA = 3, skipB = 1
Output: Intersected at '2'
Explanation: The intersected node's value is 2 (note that this must not be 0 if the two lists intersect).
From the head of A, it reads as [1,9,1,2,4]. From the head of B, it reads as [3,2,4]. There are 3 nodes before the intersected node in A; There are 1 node before the intersected node in B.
```

**Example 3:**

![img](https://assets.leetcode.com/uploads/2021/03/05/160_example_3.png)

```
Input: intersectVal = 0, listA = [2,6,4], listB = [1,5], skipA = 3, skipB = 2
Output: No intersection
Explanation: From the head of A, it reads as [2,6,4]. From the head of B, it reads as [1,5]. Since the two lists do not intersect, intersectVal must be 0, while skipA and skipB can be arbitrary values.
Explanation: The two lists do not intersect, so return null.
```

- **Constraints:**

  - The number of nodes of `listA` is in the `m`.

  - The number of nodes of `listB` is in the `n`.

  - `1 <= m, n <= 3 * 104`

  - `1 <= Node.val <= 105`

  - `0 <= skipA < m`

  - `0 <= skipB < n`

  - `intersectVal` is `0` if `listA` and `listB` do not intersect.

  - `intersectVal == listA[skipA] == listB[skipB]` if `listA` and `listB` intersect.

- **Thoughts**

  - 这道题的关键点在于如何两个链表的长度可能不等，如何让指针能同时到达相交点

    - Method 1: 先遍历两个链表求出链表长度，再根据差值将指针更新到一致，让 `p1` 和 `p2` 两个指针能够同时到达相交节点 `c1`

    - Method 2: 让 `p1` 遍历完链表 `A` 之后开始遍历链表 `B`，让 `p2` 遍历完链表 `B` 之后开始遍历链表 `A`，这样相当于「逻辑上」两条链表接在了一起。如果这样进行拼接，就可以让 `p1` 和 `p2` 同时进入公共部分，也就是同时到达相交节点 `c1`：

      <img src="https://labuladong.github.io/algo/images/%e9%93%be%e8%a1%a8%e6%8a%80%e5%b7%a7/6.jpeg" alt="img" style="zoom:33%;" />

- **Solution**

  - <u>Method 1:</u> 先遍历两个链表求出链表长度，再根据差值将指针更新到一致，让 `p1` 和 `p2` 两个指针能够同时到达相交节点 `c1`

    ```python
    class Solution:
        def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> Optional[ListNode]:
            p1 = headA
            p2 = headB
            lenA = 0
            lenB = 0
    				
            # Calculate the lengths of the two linked lists
            while p1:
                p1 = p1.next
                lenA += 1
            while p2:
                p2 = p2.next
                lenB += 1
            
            # 让 p1 和 p2 到达尾部的距离相同
            p1 = headA
            p2 = headB
            if lenA > lenB:
                n = lenA - lenB
                while n >= 1:
                    p1 = p1.next
                    n -= 1
            elif lenA < lenB:
                n = lenB - lenA
                while n >= 1:
                    p2 = p2.next
                    n -= 1
            
            # 看两个指针是否会相同，p1 == p2 时有两种情况：
        		# 1、要么是两条链表不相交，他俩同时走到尾部空指针 (此时p1 = p2 = None, return None)
        		# 2、要么是两条链表相交，他俩走到两条链表的相交点
            while (p1 != p2):
                p1 = p1.next
                p2 = p2.next
            return p1
    ```

    Time Complexity: O(N)

    Space Complexity: O(1)

  - <u>Method 2:</u>让 `p1` 遍历完链表 `A` 之后开始遍历链表 `B`，让 `p2` 遍历完链表 `B` 之后开始遍历链表 `A`, 直到`p1=p2`时，返回相应值

    ```python
    class Solution:
        def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> Optional[ListNode]:
            p1 = headA
            p2 = headB
    
            while (p1 != p2):
                # p1 走一步，如果走到 A 链表末尾，转到 B 链表
                if (p1 == None):    
                    p1 = headB
                else:
                    p1 = p1.next
                # p2 走一步，如果走到 B 链表末尾，转到 A 链表
                if (p2 == None):
                    p2 = headA
                else:
                    p2 = p2.next
            return p1
    ```

    Time Complexity: O(N)

    Space Complexity: O(1)

## 6. 83 [Remove Duplicates from Sorted List](https://leetcode.com/problems/remove-duplicates-from-sorted-list/description/)

|  Category  |  Difficulty   |                         Tags                          |
| :--------: | :-----------: | :---------------------------------------------------: |
| algorithms | Easy (49.05%) | [`linked-list`](https://leetcode.com/tag/linked-list) |

Given the `head` of a sorted linked list, *delete all duplicates such that each element appears only once*. Return *the linked list **sorted** as well*. 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/01/04/list1.jpg)

```
Input: head = [1,1,2]
Output: [1,2]
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2021/01/04/list2.jpg)

```
Input: head = [1,1,2,3,3]
Output: [1,2,3] 
```

- **Constraints:**

  - The number of nodes in the list is in the range `[0, 300]`.

  - `-100 <= Node.val <= 100`

  - The list is guaranteed to be **sorted** in ascending order.

- **Thoughts**

  这道题目有两种方法：

  - Method 1: **一次遍历**

    由于给定的链表是排好序的，因此**重复的元素在链表中出现的位置是连续的**，因此我们只需要对链表进行一次遍历，就可以删除重复的元素。

    **细节:** 当我们遍历到链表的最后一个节点时，*cur.next* 为空节点，如果不加以判断，访问 *cur.next* 对应的元素会产生运行错误。因此我们只需要遍历到链表的最后一个节点，而不需要遍历完整个链表。

    ```python
    class Solution:
        def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
            cur = head
            if not head:
                return head
            while cur.next:
                if cur.val == cur.next.val:
                    cur.next = cur.next.next
                else:
                    cur = cur.next
            return head
    ```

    - Time complexity: O(N)

      Space complexity: O(1)

  - Method 2: **快慢指针** (Similar to 26: remove duplicates from an array)

    <img src="https://labuladong.github.io/algo/images/%e6%95%b0%e7%bb%84%e5%8e%bb%e9%87%8d/2.gif" alt="img" style="zoom:33%;" />

    ```python
    class Solution:
        def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
            slow = head
            fast = head
    
            if head is None:
                return head
    
            while fast:
                if (slow.val != fast.val):
                    slow.next = fast
                    slow = slow.next
                fast = fast.next
            
            slow.next = None    #  断开与结尾重复元素的连接
            return head  
    ```

    - Time complexity: O(N)

      Space complexity: O(1)     


## 6.1 **1836 Remove Duplicates From an Unsorted Linked List**

Given the `head` of a linked list, find all the values that appear **more than once** in the list and delete the nodes that have any of those values.

Return *the linked list after the deletions.*

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/04/21/tmp-linked-list.jpg)

```
Input: head = [1,2,3,2]
Output: [1,3]
Explanation: 2 appears twice in the linked list, so all 2's should be deleted. After deleting all 2's, we are left with [1,3].
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2021/04/21/tmp-linked-list-1.jpg)

```
Input: head = [2,1,1,2]
Output: []
Explanation: 2 and 1 both appear twice. All the elements should be deleted.
```

**Example 3:**

![img](https://assets.leetcode.com/uploads/2021/04/21/tmp-linked-list-2.jpg)

```
Input: head = [3,2,2,1,3,2,4]
Output: [1,4]
Explanation: 3 appears twice and 2 appears three times. After deleting all 3's and 2's, we are left with [1,4].
```

- **Constraints:**

  - The number of nodes in the list is in the range `[1, 105]`

  - `1 <= Node.val <= 105`

- **Thoughts**

  - 这道题和83题的区别在于，由于链表是无序的，所以在一次遍历删除节点之前就需要知道哪个节点是需要被删除的。可以使用一个hashmap先记录每个节点值的count，然后再一次遍历删除相应节点

- **Solution**

  ```python
  # S1: use a hashmap to count appearances of each node's value --> {key: node.val; val: counts}
  # S2: traverse through the whole linked list, if a node with the same value shows, delete
  from collections import defaultdict
  class Solution:
      def deleteDuplicatesUnsorted(self, head: ListNode) -> ListNode:
          dummyHead = ListNode(-1)
          dummyHead.next = head    
          pre = dummyHead
          cur = head
          
          hashmap = defaultdict(int)
          while cur:
              hashmap[cur.val] += 1
              cur = cur.next
          
          cur = head     # initialize the current pointer back to the head
          while cur:
              if hashmap[cur.val] > 1:
                  pre.next = cur.next
                  # cur = pre.next
              else:
                  pre = pre.next
                  # cur = cur.next
              cur = cur.next
          return dummyHead.next
  ```

  - Time complexity: O(2N) = O(N)

    Space complexity: O(1)

### 7. 2 [Add Two Numbers](https://leetcode.com/problems/add-two-numbers/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (38.74%) | [`linked-list`](https://leetcode.com/tag/linked-list); [`math`](https://leetcode.com/tag/math) |

You are given two **non-empty** linked lists representing two non-negative integers. The digits are stored in **reverse order**, and each of their nodes contains a single digit. Add the two numbers and return the sum as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2020/10/02/addtwonumber1.jpg" alt="img" style="zoom:50%;" />

```
Input: l1 = [2,4,3], l2 = [5,6,4]
Output: [7,0,8]
Explanation: 342 + 465 = 807.
```

**Example 2:**

```
Input: l1 = [0], l2 = [0]
Output: [0]
```

**Example 3:**

```
Input: l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]
Output: [8,9,9,9,0,0,0,1]
```

- **Constraints:**

  - The number of nodes in each linked list is in the range `[1, 100]`.

  - `0 <= Node.val <= 9`

  - It is guaranteed that the list represents a number that does not have leading zeros.

- **Thoughts**

  - 由于输入的两个链表都是逆序存储数字的位数的，因此两个链表中同一位置的数字可以直接相加。我们同时遍历两个链表，逐位计算它们的和，并与当前位置的进位值相加。具体而言，如果当前两个链表处相应位置的数字为 n1,n2，进位值为 carry，则它们的和为 n1+n2+carry；其中，答案链表处相应位置的数字为(n1+n2+carry)mod10，而新的进位值为 $\lfloor\frac{n1+n2+\textit{carry}}{10}\rfloor $

  - 如果两个链表的长度不同，则可以认为长度短的链表的后面有若干个 0 。

  - 如果链表遍历结束后，有 `carry > 0`，还需要在答案链表的后面附加一个节点，节点的值为carry。

- **Solution**

  ```python
  def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
    dummy = ListNode(-1)
    cur = dummy
    carry = 0
    
    while l1 or l2:
      sum = 0
      if l1:
        sum += l1.val
        l1 = l1.next
      if l2:
        sum += l2.val
        l2 = l2.next
      sum += carry
      cur.next = ListNode(sum % 10)
      carry = sum // 10
      cur = cur.next
    
    if carry != 0:
      cur.next = ListNode(carry)
    return dummy.next
  ```

  - Time Complexity: $O(max(m, n))$ --> 其中 m 和 n 分别为两个链表的长度。我们要遍历两个链表的全部位置，而处理每个位置只需要 O(1) 的时间。

    Space Complexity: O(1)

### 8. 142 [Linked List Cycle II](https://leetcode.com/problems/linked-list-cycle-ii/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (44.41%) | [`linked-list`](https://leetcode.com/tag/linked-list); [`two-pointers`](https://leetcode.com/tag/two-pointers) |

Given the `head` of a linked list, return *the node where the cycle begins. If there is no cycle, return* `null`.

There is a cycle in a linked list if there is some node in the list that can be reached again by continuously following the `next` pointer. Internally, `pos` is used to denote the index of the node that tail's `next` pointer is connected to (**0-indexed**). It is `-1` if there is no cycle. **Note that** `pos` **is not passed as a parameter**.

**Do not modify** the linked list.

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist.png" alt="img" style="zoom:67%;" />

```
Input: head = [3,2,0,-4], pos = 1
Output: tail connects to node index 1
Explanation: There is a cycle in the linked list, where tail connects to the second node.
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist_test2.png)

```
Input: head = [1,2], pos = 0
Output: tail connects to node index 0
Explanation: There is a cycle in the linked list, where tail connects to the first node.
```

**Example 3:**

![img](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist_test3.png)

```
Input: head = [1], pos = -1
Output: no cycle
Explanation: There is no cycle in the linked list.
```

- **Constraints:**

  - The number of the nodes in the list is in the range `[0, 104]`.

  - `-105 <= Node.val <= 105`

  - `pos` is `-1` or a **valid index** in the linked-list. 

**Follow up:** Can you solve it using `O(1)` (i.e. constant) memory? --> ==see Method 2==

- **Thoughts**

  - 这道题目，不仅考察对链表的操作，而且还需要一些数学运算。

    主要考察两知识点：

    - 判断链表是否环
    - 如果有环，如何找到这个环的入口

- **Solution**

  - <u>Method 1: Hashset</u>

    一个非常直观的思路是：我们遍历链表中的每个节点，并将它记录下来；一旦遇到了此前遍历过的节点，就可以判定链表中存在环。借助哈希表可以很方便地实现。

    ```python
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
      if not head:
        return head
      
      hashset = set()
      while head:
        if head in hashset:
          return head
        hashset.add(head)
        head = head.next
      return None
    ```

    - Time Complexity: O(N)

      Space Complexity: O(N) --> build a set

  - <u>Method 2: Two-Pointers</u>

    我们使用两个指针，$\textit{fast}$ 与 $\textit{slow}$。它们起始都位于链表的头部。随后，$\textit{slow}$每次向后移动一个位置，而 $\textit{fast}$ 指针向后移动两个位置。如果链表中存在环，则 $\textit{fast}$ 指针最终将再次与 $\textit{slow}$ 指针在环中相遇。由于任意时刻，$\textit{fast}$ 指针走过的距离都为 $\textit{slow}$ 指针的 2 倍 --> `fast = 2 * slow`

    设链表中环外部分的长度为 a。slow 指针进入环后，又走了 b 的距离与 fast 相遇。此时，fast 指针已经走完了环的 n 圈，因此它走过的总距离为 `a+n(b+c)+b=a+(n+1)b+nc`。

    <img src="/Users/xinyuzhang/Downloads/IMG_1700D2C619A3-1.jpeg" alt="IMG_1700D2C619A3-1" style="zoom: 33%;" />

    --> **<u>从相遇点到入环点的距离加上 n-1 圈的环长，恰好等于从链表头部到入环点的距离。</u>** --> 当发现 slow 与 fast 相遇时，我们再额外使用一个指针 ptr。起始，它指向链表头部；随后，它和 slow 每次向后移动一个位置。最终，它们会在入环点相遇

    ```python
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
      if not head:
        return head
      
      slow, fast = head, head
      while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
          ptr = head
          while ptr != slow:
            ptr = ptr.next
            slow = slow.next
          return slow
      return None 
    ```

    - Time Complexity: O(N)

      Space Complexity: O(1) --> only use three pointers (slow, fast, ptr)

### 9. 287 [Find the Duplicate Number](https://leetcode.com/problems/find-the-duplicate-number/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (58.84%) | [`array`](https://leetcode.com/tag/array);  [`two-pointers`](https://leetcode.com/tag/two-pointers); [`binary-search`](https://leetcode.com/tag/binary-search) |

Given an array of integers `nums` containing `n + 1` integers where ==each integer is in the range `[1, n]` inclusive.==

There is only **one repeated number** in `nums`, return *this repeated number*.

==You must solve the problem **without** modifying the array `nums` and uses only constant extra space.==

**Example 1:**

```
Input: nums = [1,3,4,2,2]
Output: 2
```

**Example 2:**

```
Input: nums = [3,1,3,4,2]
Output: 3
```

- **Constraints:**

  - `1 <= n <= 105`

  - `nums.length == n + 1`

  - `1 <= nums[i] <= n`

  - All the integers in `nums` appear only **once** except for **precisely one integer** which appears **two or more** times.

- **Follow up:**

  - How can we prove that at least one duplicate number must exist in `nums`?

  - Can you solve the problem in linear runtime complexity?

- **Thoughts**

  - 该题的难点在于题目要求的without modifying the array and use only constant extra space，不然可以将数列排序或者使用哈希集合来做

  - 题目表明：each integer is in the range `[1, n]` inclusive --> 所有元素的值都不超过该数组的长度 --> 我们对 nums 数组建图，每个位置 ii 连一条 $i\rightarrow \textit{nums}[i]$ 的边。由于存在的重复的数字 target，因此 target 这个位置一定有起码两条指向它的边，因此整张图一定存在环，且我们要找到的 target 就是这个环的入口，那么整个问题就等价于 **142. 环形链表 II**。使用双指针找出环，并找到环的起点 --> ==Floyd’s Cycle Finding Algorithm（Floyd判圈算法）==

    <img src="/Users/xinyuzhang/Downloads/IMG_8BD7623244E9-1.jpeg" alt="IMG_8BD7623244E9-1" style="zoom: 33%;" />

    

- <u>**Method 1: Two-Pointers; Floyd's cycle finding algorithm**</u>

  ```python
  def findDuplicate(self, nums: List[int]) -> int:
    ## Method: Linked List Cycle (Floyd's Algorithm)
    # --> see each element as a pointer --> point to the according index
    slow, fast = 0, 0
    while True:
      slow = nums[slow]
      fast = nums[nums[fast]]			# advance fast twice
      if slow == fast:
        ptr = 0
        while slow != ptr:
          slow = nums[slow]
          ptr = nums[ptr]
          if slow == ptr:
            return slow
  ```

  - Time Complexity: O(N)

    Space Complexity: O(1) --> only use three pointers (slow, fast, ptr)

  - <font color=red>**注意点：**</font>
    - 这道题能使用Floyd判圈算法解决是因为，它以节点0而不是nums[0]作为遍历链表唯一的入口，并且链表中不存在某个节点能重新回到链表入口结点0 --> 由于数字从1开始计数，0这个位置不可能被其它位置的数字指向，所以0位置不可能在环内，所以从0开始遍历一定会指向某个包含重复数字的环
    - [Floyd’s cycle finding algorithm](https://www.geeksforgeeks.org/detect-loop-in-a-linked-list/) or Hare-Tortoise algorithm is a pointer algorithm that uses only two pointers, moving through the sequence at different speeds. This algorithm is used to find a loop in a linked list. It uses two pointers **one moving twice as fast as the other one.** The faster one is called the faster pointer and the other one is called the slow pointer.

- **<u>Method 2: Binary Search</u>**

  - 因为题目要找的是一个整数，并且==这个整数有明确的范围==，所以可以使用「二分查找」。

    - 重点理解：这个问题使用「二分查找」是在数组 [1, 2,.., n] 中查找一个整数，而并非在输入数组数组中查找一个整数。
    - **解题思路**：每一次猜一个数，然后 **遍历整个输入数组**，进而缩小搜索区间，最后确定重复的是哪个数。
    - 「二分查找」的思路是先猜一个数（搜索范围 [left..right] 里位于中间的数 mid），然后统计原始数组中小于等于 mid 的元素的个数 count：

      - 如果 count 严格大于 mid。根据抽屉原理 (Pigeonhole Principle: if n items are put into ${\displaystyle m}$  containers, with n > m, then at least one container must contain more than one item)，重复元素就在区间 `[left..mid]` 里；
      - 否则，重复元素可以在区间 `[mid + 1..right]`里找到
  - <font color=red>二分搜索法只对有序的数列有用 --> 这道题因为整数有明确的的范围一定有[1, n]，数列长度为n + 1，所以1-n是有序的</font>

  ```python
  def findDuplicate(self, nums: List[int]) -> int:
    n = len(nums)
    l, r = 1, n - 1    # 1~n是有序的，对这个范围进行二分查找，然后选取1-n中的mid去原数组中统计小于等于它的有多少个数
    									 # l, r也是数组的最大最小值
    
    while l <= r:
      m = (l + r)//2
      count = 0
      for num in nums:
        if num <= m:
          count += 1
      
      if count > m:
        r = m - 1
      else:
        l = m + 1
    return l				  # 退出以后 left 与 right 重合，返回 right 也可以。
  ```

  - Time Complexity: ==O(NlogN)== --> binary search: $O(logN)$, for loop: O(N)

    Space Complexity: O(1) --> only use two pointers

## 2. ==Iteration / Recursion 迭代/递归反转单链表==

<font color=red>**重要！！看这个链接和labuladong算法小抄结合**</font> 

https://leetcode.cn/problems/reverse-linked-list-ii/solution/yi-bu-yi-bu-jiao-ni-ru-he-yong-di-gui-si-lowt/

### 1. 206 Reverse Linked List (https://leetcode.com/problems/reverse-linked-list/description/)

|  Category  |  Difficulty   |                         Tags                          |
| :--------: | :-----------: | :---------------------------------------------------: |
| algorithms | Easy (70.37%) | [`linked-list`](https://leetcode.com/tag/linked-list) |

Given the `head` of a singly linked list, reverse the list, and return *the reversed list*.

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/02/19/rev1ex1.jpg)

```
Input: head = [1,2,3,4,5]
Output: [5,4,3,2,1]
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2021/02/19/rev1ex2.jpg)

```
Input: head = [1,2]
Output: [2,1]
```

**Example 3:**

```
Input: head = []
Output: [] 
```

- **Constraints:**

  - The number of nodes in the list is the range `[0, 5000]`.

  - `-5000 <= Node.val <= 5000`

- **Solution**

  - <u>Method 1: Iteration (two pointers)</u>

    <img src="https://camo.githubusercontent.com/36cf9298bccf54091dbcabb9ede884bf98d5b2f6f04bd89a36ac2904b26d0971/68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f30303865476d5a456c7931676e7266316f626f757067333067793063343471702e676966" alt="68747470733a2f2f747661312e73696e61696d672e636e2f6c617267652f30303865476d5a456c7931676e7266316f626f757067333067793063343471702e676966" style="zoom: 67%;" />

    ```python
    class Solution:
        def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
            dummyhead = ListNode(0)
            dummyhead.next = head 
    
            while (head != None and head.next != None):
                curr = dummyhead.next
                next = head.next
    
                dummyhead.next = next
                head.next = next.next
                next.next = curr
            return dummyhead.next
    ```

    Time Complexity: O(N)

    Space Complexity: O(1)

    ```python
    ## Optimal!!
    class Solution:
        def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
            prev, curr = None, head
            while curr:
                next = curr.next   # 提前先存储下这个节点，因为后面会改变curr.next
                curr.next = prev   # the reverse step
                prev = curr
                curr = next
            return prev
    ```

    Time Complexity: O(N)

    Space Complexity: O(1)

  - <u>Method 2: Recursion</u>

    ```haskell
                第一轮出栈，head为5，head.next为空，返回5
                第二轮出栈，head为4，head.next为5，执行head.next.next=head也就是5.next=4，
                          把当前节点的子节点的子节点指向当前节点
                          此时链表为1->2->3->4<->5，由于4与5互相指向，所以此处要断开4.next=null
                          此时链表为1->2->3->4<-5
                          返回节点5
                第三轮出栈，head为3，head.next为4，执行head.next.next=head也就是4.next=3，
                          此时链表为1->2->3<->4<-5，由于3与4互相指向，所以此处要断开3.next=null
                          此时链表为1->2->3<-4<-5
                          返回节点5
                第四轮出栈，head为2，head.next为3，执行head.next.next=head也就是3.next=2，
                          此时链表为1->2<->3<-4<-5，由于2与3互相指向，所以此处要断开2.next=null
                          此时链表为1->2<-3<-4<-5
                          返回节点5
                第五轮出栈，head为1，head.next为2，执行head.next.next=head也就是2.next=1，
                          此时链表为1<->2<-3<-4<-5，由于1与2互相指向，所以此处要断开1.next=null
                          此时链表为1<-2<-3<-4<-5
                          返回节点5
                出栈完成，最终头节点5->4->3->2->1
    ```

    递归解题首先要做的是明确递推公式的含义，在这里对于结点1来说，它只需要知道它之后的所有节点反转之后的结果就可以了，也就是说递推公式reverseList的含义是：把拿到的链表进行反转，然后返回新的头结点。

    <img src="https://labuladong.github.io/algo/images/%e5%8f%8d%e8%bd%ac%e9%93%be%e8%a1%a8/1.jpg" alt="img" style="zoom:25%;" />

    结点1之后的结点，经过递归公式reverseList处理之后的结果如下图：

    <img src="https://labuladong.github.io/algo/images/%e5%8f%8d%e8%bd%ac%e9%93%be%e8%a1%a8/2.jpg" alt="img" style="zoom:25%;" />

    ```python
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
      # 调用递推公式反转当前结点之后的所有节点, 返回的结果是反转后的链表的头结点
      newHead = self.reverseList(head.next)
    ```
    
    接着要做的就是反转结点1，也就是将head指向的结点作为其下一个结点的下一个结点，即`head.next.next=head`。
    
    <img src="https://labuladong.github.io/algo/images/%e5%8f%8d%e8%bd%ac%e9%93%be%e8%a1%a8/4.jpg" alt="img" style="zoom:25%;" />
    
    最后，将head指向的结点的下一个结点置为null，就完成了整个链表的反转。
    
    <img src="https://labuladong.github.io/algo/images/%e5%8f%8d%e8%bd%ac%e9%93%be%e8%a1%a8/5.jpg" alt="img" style="zoom:25%;" />
    
    ```python
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
      # 调用递推公式反转当前结点之后的所有节点, 返回的结果是反转后的链表的头结点
      newHead = self.reverseList(head.next)
      head.next.next = head
      head.next = None
      return head
    ```
    
    递归调用这一部分完成之后，还有重要的一步就是递归终止条件，递归反转链表什么时候停止呢？在head指向的结点为null或head指向的结点的下一个结点为null时停止，因为在这两种情况下，反转后的结果就是它自己。
    
    ```python
    class Solution:
        def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
            # 递归终止条件
            # head == None: 该链表为空；head.next == None：该链表只有一个节点
            if (head is None or head.next is None): 
                return head
            
            last = self.reverseList(head.next)
            head.next.next = head
            head.next = None      # 如果忽略这一步，链表中可能产生环
            return last
    ```
  
  - Time Complexity: O(N)
  
    Space Complexity: O(1)

### 2. 92 [Reverse Linked List II](https://leetcode.com/problems/reverse-linked-list-ii/description/)

|  Category  |   Difficulty    |                         Likes                         |
| :--------: | :-------------: | :---------------------------------------------------: |
| algorithms | Medium (43.45%) | [`linked-list`](https://leetcode.com/tag/linked-list) |

Given the `head` of a singly linked list and two integers `left` and `right` where `left <= right`, reverse the nodes of the list from position `left` to position `right`, and return *the reversed list*.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/02/19/rev2ex2.jpg)

```
Input: head = [1,2,3,4,5], left = 2, right = 4
Output: [1,4,3,2,5]
```

**Example 2:**

```
Input: head = [5], left = 1, right = 1
Output: [5]
```

- **Constraints:**

  - The number of nodes in the list is `n`.

  - `1 <= n <= 500`

  - `-500 <= Node.val <= 500`

  - `1 <= left <= right <= n`

- **Solution**

  - <u>Method 1: Iteration</u>

    整体思想是：在需要反转的区间里，每遍历到一个节点，让这个新节点来到反转部分的起始位置。下面的图展示了整个流程。

    <img src="https://pic.leetcode-cn.com/1615105242-ZHlvOn-image.png" alt="image.png" style="zoom:50%;" />
  
    - curr：指向待反转区域的第一个节点 left；
      next：永远指向 curr 的下一个节点，循环过程中，curr 变化以后 next 会变化；
      pre：永远指向待反转区域的第一个节点 left 的前一个节点，<font color=red>**在循环过程中不变**。</font>
  
    <img src="https://pic.leetcode-cn.com/1615105353-PsCmzb-image.png" alt="image.png" style="zoom: 25%;" />
  
    ```python
    def reverseBetween(self, head: Optional[ListNode], left: int, right: int) -> Optional[ListNode]:
            dummyhead = ListNode(-1)
            dummyhead.next = head
            prev = dummyhead
    
            # move the pointer to the previous node of the left node
            for _ in range(left - 1):
                prev = prev.next
            
            curr = prev.next
            for _ in range(right - left):
                next = curr.next
                curr.next = next.next
                next.next = prev.next    # 注意是prev.next不是curr! 循环过程中prev不会变化，而curr和next会变
                prev.next = next
            return dummyhead.next
    ```

    Time Complexity: O(N) --> the worst case is to iterate through the whole list
  
    Space Complexity: O(1)
  
  - <u>Method 2: Recursion</u>
  
    <img src="https://pic.leetcode-cn.com/1611457502-mRCiFs-image.png" alt="image.png" style="zoom: 50%;" />
  
    在这里head指向的结点1，不用关注其后的所有结点时如何将m=3与n=5之间的部分反转的，它只需要知道反转后的结果就可以。也就是说，在这里递推公式reverseBetween的含义是：<font color=blue>**将拿到的链表反转，然后返回反转后的链表的头结点**。</font>
    
    <img src="https://pic.leetcode-cn.com/1611457519-qDjWxe-image.png" alt="image.png" style="zoom: 50%;" />

```python
def reverseBetween(self, head: Optional[ListNode], left: int, right: int) -> Optional[ListNode]:
  between = self.reverseBetween(head.next, left - 1, right - 1) # 因为前移了一个元素，所以left和right减1
```

接着要做的就是，将递推公式reverseBetween返回的结果，挂在head之后，即`head.next=between`。

<img src="https://pic.leetcode-cn.com/1611457543-oQfolC-image.png" alt="image.png" style="zoom:50%;" />

```python
def reverseBetween(self, head: Optional[ListNode], left: int, right: int) -> Optional[ListNode]:
  between = self.reverseBetween(head.next, left - 1, right - 1) # 因为前移了一个元素，所以left和right减1
  head.next = between
  return head
```

递推公式的部分已经完成了，接着要明确的就是递归终止条件。在这里原问题是反转链表：1->2->3->4->5->6->NULL, m = 3, n = 5之间的部分。

更小的子问题是反转链表：2->3->4->5->6->NULL, m = 2, n = 4之间的部分。

在进一步是反转链表：3->4->5->6->NULL, m = 1, n = 3之间的部分。但是，这时这个子问题和上一个子问题还是可以用相同思路求解的同一个子问题吗？

<img src="https://pic.leetcode-cn.com/1611457589-DuqaZV-image.png" alt="image.png" style="zoom:50%;" />

不是。原因在于对于子问题：2->3->4->5->6->NULL, m = 2, n = 4来说，它只需要将其之后的所有结点反转之后的结点挂在自己之后就可以了。但是，对于问题3->4->5->6->NULL, m = 1, n = 3来说，结点3本身也是需要反转的。--> 递归条件：当`m = 1`的时候停止

```python
def reverseBetween(self, head: Optional[ListNode], left: int, right: int) -> Optional[ListNode]:
  if left == 1:
    return 待补充
  between = self.reverseBetween(head.next, left - 1, right - 1) # 因为前移了一个元素，所以left和right减1
  head.next = between
  return head
```

如何用递归思想反转3->4->5->6->NULL, m = 1, n = 3，即如何反转链表的前n个结点。

<img src="https://pic.leetcode-cn.com/1611457604-HPUsuX-image.png" alt="image.png" style="zoom:50%;" />

这里，递推公式`reverseTopN`的含义是反转链表的前n个结点并返回被反转的链表的头结点。因此，其需要的参数有两个，一是待反转的链表的头结点，二是反转前几个结点，即n。

<img src="https://pic.leetcode-cn.com/1611457645-LCTybw-image.png" alt="image.png" style="zoom:50%;" />

对于链表4->5->6->NULL, n = 2来说，经过递推公式reverseTopN处理之后，结果如下图所示：

<img src="https://pic.leetcode-cn.com/1611457658-Ocnveh-image.png" alt="image.png" style="zoom:50%;" />

至此，对于反转链表的前n个结点，可以初步写出如下的代码：

```python
def reverseTopN(head: Optional[ListNode], n: int):
  newHead = reverseTopN(head.next, n - 1)
```

经过前面分析，我们知道head指向的结点3也是需要反转的，即`head.next.next=head`。但是，我们发现在完成这一步操作后，结点6没法在和其它结点产生联系了。--> <font color=blue>**可以用topNSuccessor这个变量，指向第n个结点之后的结点**</font>

这时，在反转结点3，即`head.next.next=head`之后，我们可以将topNSuccessor指向的结点挂在head指向的结点之后，即`head.next=topNSuccessor`。

<img src="https://pic.leetcode-cn.com/1611457722-OqAvKM-image.png" alt="image.png" style="zoom:50%;" />

```python
def reverseTopN(head: Optional[ListNode], n: int):
  newHead = reverseTopN(head.next, n - 1)
  head.next.next = head
  head.next = topNSuccessor
```

topNSuccessor怎么确定的呢？

我们先看下反转链表前n个结点的递归终止条件是什么。当只需要反转链表的第一个结点时，返回原链表就可以了，即反转链表前n个结点的递归终止条件是n==1。

如下图，当n==1时，我们就可以确定topNSuccessor是head指向的结点的下一个结点。

```python
topNSuccessor = ListNode()
def reverseTopN(head: Optional[ListNode], n: int):
  if n == 1:
    topNSuccessor = head.next
    return head
  
  newHead = reverseTopN(head.next, n - 1)
  head.next.next = head
  head.next = topNSuccessor
```

最后，反转从位置 m 到 n 的链表的递归实现完整代码如下：

```python
topNSuccessor = ListNode()
def reverseTopN(self, head: Optional[ListNode], n: int):
  if n == 1:
    topNSuccessor = head.next
    return head
  
  newHead = self.reverseTopN(head.next, n - 1)
  head.next.next = head
  head.next = topNSuccessor
  
def reverseBetween(self, head: Optional[ListNode], left: int, right: int) -> Optional[ListNode]:
  if left == 1:					
    return reverseTopN(head, right)
  
  between = self.reverseBetween(head.next, left - 1, right - 1) # 因为前移了一个元素，所以left和right减1
  head.next = between
  return head
```

```python
class Solution:
    def __init__(self):
        self.successor = ListNode()
        
    # 反转以 head 为起点的 n 个节点，返回新的头结点
    def reverseTopN(self, head: Optional[ListNode], n: int):
        if n == 1:
          	# 记录第 n + 1 个节点
            self.successor = head.next
            return head
        
        newHead = self.reverseTopN(head.next, n - 1)
        head.next.next = head
        head.next = self.successor   # 让反转之后的 head 节点和后面的节点连起来
        return newHead
        
    def reverseBetween(self, head: Optional[ListNode], left: int, right: int) -> Optional[ListNode]:
        if left == 1: 			# base case
            return self.reverseTopN(head, right)
        between = self.reverseBetween(head.next, left - 1, right - 1)
        head.next = between
        return head
```

- Time Complexity: O(N) 

  Space Complexity: O(N) --> need stack memory for recursion

## 3. 143 [Reorder List](https://leetcode.com/problems/reorder-list/description/)

|  Category  |   Difficulty    |                         Tags                          |
| :--------: | :-------------: | :---------------------------------------------------: |
| algorithms | Medium (48.31%) | [`linked-list`](https://leetcode.com/tag/linked-list) |

You are given the head of a singly linked-list. The list can be represented as:

```
L0 → L1 → … → Ln - 1 → Ln
```

*Reorder the list to be on the following form:*

```
L0 → Ln → L1 → Ln - 1 → L2 → Ln - 2 → …
```

You may not modify the values in the list's nodes. Only nodes themselves may be changed.

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/03/04/reorder1linked-list.jpg)

```
Input: head = [1,2,3,4]
Output: [1,4,2,3]
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2021/03/09/reorder2-linked-list.jpg)

```
Input: head = [1,2,3,4,5]
Output: [1,5,2,4,3]
```

- **Constraints:**

  - The number of nodes in the list is in the range `[1, 5 * 104]`.

  - `1 <= Node.val <= 1000`

- **Thoughts**

  - For list `[1,2,3,4,5,6,7]` we need to return `[1,7,2,6,3,5,4]`. We can note, that it is actually two lists `[1,2,3,4]` and `[7,6,5]`, where elements are interchange. 
    - S1: Find the middle of the list --> use ==`slow/fast`== iterators trick, where `slow` moves with speed `1` and `fast` moves with speed `2`. Then when `fast` reches the end, `slow` will be in the middle, as we need. ***[LeetCode 876]***
    - S2: Reverse the second part of linked list. <font color=red>Do not forget to use `slow.next = None`, in opposite case you will have list with loop. </font> ***[LeetCode 206]***
    - S3: Merge two lists, given its heads. 

- **Solution**

  ```python
  def reorderList(self, head: Optional[ListNode]) -> None:
    """
    Do not return anything, modify head in-place instead.
    """
    
    # S1: find the middle node (LeetCode 876)
    slow = fast = head
    while fast and fast.next:
      slow = slow.next
      fast = fast.next.next
   
    # S2: reverse the second half of the linked list (LeetCode 206)
  	cur = slow.next
    prev = slow.next = None  # slow.next = None: separate the first and second half --> prevent loops 
    while cur:
      temp = cur.next
      cur.next = prev
      prev = cur
      cur = temp
    
    # S3: merge two linked lists one-by-one
    first, second = head, prev
    while first and second:
      temp1, temp2 = first.next, second.next    # prev is the head of the second half
      first.next = second
      second.next = temp1
      
      first = temp1
      second = temp2
  ```

  - Time Complexity: O(3N) = O(N) --> first do `O(n)` iterations to find middle, then we do `O(n)` iterations to reverse second half and finally we do `O(n)` iterations to merge lists

    Space Complexity: O(1) 

## ==3. 其余链表技巧==

### 1. 138 [Copy List with Random Pointer](https://leetcode.com/problems/copy-list-with-random-pointer/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (48.46%) | [`hash-table`](https://leetcode.com/tag/hash-table); [`linked-list`](https://leetcode.com/tag/linked-list) |

 A linked list of length `n` is given such that each node contains an additional random pointer, which could point to any node in the list, or `null`.

Construct a [**deep copy**](https://en.wikipedia.org/wiki/Object_copying#Deep_copy) of the list. The deep copy should consist of exactly `n` **brand new** nodes, where each new node has its value set to the value of its corresponding original node. Both the `next` and `random` pointer of the new nodes should point to new nodes in the copied list such that the pointers in the original list and copied list represent the same list state. **None of the pointers in the new list should point to nodes in the original list**.

For example, if there are two nodes `X` and `Y` in the original list, where `X.random --> Y`, then for the corresponding two nodes `x` and `y` in the copied list, `x.random --> y`.

Return *the head of the copied linked list*.

The linked list is represented in the input/output as a list of `n` nodes. Each node is represented as a pair of `[val, random_index]` where:

- `val`: an integer representing `Node.val`
- `random_index`: the index of the node (range from `0` to `n-1`) that the `random` pointer points to, or `null` if it does not point to any node.

Your code will **only** be given the `head` of the original linked list.

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2019/12/18/e1.png" alt="img" style="zoom: 33%;" />

```
Input: head = [[7,null],[13,0],[11,4],[10,2],[1,0]]
Output: [[7,null],[13,0],[11,4],[10,2],[1,0]]
```

**Example 2:**

<img src="https://assets.leetcode.com/uploads/2019/12/18/e2.png" alt="img" style="zoom:33%;" />

```
Input: head = [[1,1],[2,1]]
Output: [[1,1],[2,1]]
```

**Example 3:**

**<img src="https://assets.leetcode.com/uploads/2019/12/18/e3.png" alt="img" style="zoom:33%;" />**

```
Input: head = [[3,null],[3,0],[3,null]]
Output: [[3,null],[3,0],[3,null]]
```

- **Constraints:**

  - `0 <= n <= 1000`

  - `-104 <= Node.val <= 104`

  - `Node.random` is `null` or is pointing to some node in the linked list.

- **Thoughts**

  - 本题要求我们对一个特殊的链表进行深拷贝。如果是普通链表，我们可以直接按照遍历的顺序创建链表节点。而本题中因为==随机指针==的存在，<font color=blue>**当我们拷贝节点时，「当前节点的随机指针指向的节点」可能还没创建**</font>，因此我们需要变换思路。

- **Solution**

  - <u>Method 1: Hashmap</u>

    --> use extra space to keep **old node ---> new node** mapping to prevent creating multiples copies of same node

    --> 思路：先将所有节点单独存放在哈希表里，之后再进行指针的连接

    - S1: First pass: Create a deep copy of all nodes without linking them； Use a hash map to map old nodes to new nodes 
    - S2: Second pass: Connect all pointers in the hashmap

    首先遍历原链表，每遍历到一个节点，都新建一个相同val的节点，然后使用HashMap存放原链表到新链表的键值对。第二次遍历时通过HashMap，建立新链表节点之间的next和random关系。

    ```python
    class Node:
        def __init__(self, x: int, next: 'Node' = None, random: 'Node' = None):
            self.val = int(x)
            self.next = next
            self.random = random
    
    class Solution:
        def copyRandomList(self, head: 'Optional[Node]') -> 'Optional[Node]':
            if not head:
                return head
                
            hashmap = {None: None}   # 原节点的next和随机指针可能为空，令这些节点指向空
            # S1: add all nodes separately in the hashmap
            cur = head
            while cur:
                copy = Node(cur.val)
                hashmap[cur] = copy   # add a copy node to the hashmap
                cur = cur.next
            
            # S2: connect all pointers
            cur = head
            while cur:  # 遍历原链表的同时更新新链表
                copy = hashmap[cur]
                copy.next = hashmap[cur.next]
                copy.random = hashmap[cur.random] 
                cur = cur.next
            return hashmap[head]
    ```

    - Time Complexity: O(2N) = O(N)

      Space Complexity: O(N) --> create a map to store all new nodes 

  - <u>Method 2: Iteration</u>

    --> 由于使用哈希表储存复制的节点需要O(N)的空间复杂度，也可以更改原链表，实现O(1)的空间复杂度

    We can avoid using extra space for old node ---> new node mapping, by tweaking the original linked list. Simply interweave the nodes of the old and copied list. 

    For e.g.

    `Old List: A --> B --> C --> D
    InterWeaved List: A --> A' --> B --> B' --> C --> C' --> D --> D'`

    基本就是：==新节点接在老节点之后，连接新随机指针，分离链表。==

    <img src="https://pic.leetcode-cn.com/1626886532-UBAqDs-image.png" alt="image.png" style="zoom:50%;" />

    在每一个老节点之后，新建与老节点val相同的节点，插入在老节点之后，注意，随机指针不急赋值，因为随机在何处还不知，需要把链表全部新老节点建立连接完成。

    <img src="https://pic.leetcode-cn.com/1626886679-niBTZo-image.png" alt="image.png" style="zoom:50%;" />

    赋值新随机指针，等于为老随机指针的下一个。

    <img src="https://pic.leetcode-cn.com/1626889435-VypNsR-image.png" alt="image.png" style="zoom:50%;" />

    拆分链表。随机指针不受影响，老链表依然在，新链表复制完成

    <img src="https://pic.leetcode-cn.com/1626889471-eXelMq-image.png" alt="image.png" style="zoom:50%;" />

    <img src="https://pic.leetcode-cn.com/1626889485-tTgOqG-image.png" alt="image.png" style="zoom:50%;" />

    ```python
    class Node:
        def __init__(self, x: int, next: 'Node' = None, random: 'Node' = None):
            self.val = int(x)
            self.next = next
            self.random = random
    
    class Solution:
        def copyRandomList(self, head: 'Optional[Node]') -> 'Optional[Node]':
          if not head:
            return head
          
          # S1: insert a copied node after each node
          cur = head
          while cur: # traverse through all nodes in the original list
            copy = Node(cur.val)
            copy.next = cur.next
            cur.next = copy
            cur = cur.next.next
         
          # S2: connect all random pointers of copied nodes
          cur = head
          while cur:
            copy = cur.next
            copy.random = cur.random.next if cur.random else None
            cur = cur.next.next
          
          # S3: separate two linked lists
          cur = head
          newHead = head.next
          while cur:
            new = cur.next
            cur.next = cur.next.next
            new.next = new.next.next if new.next else None
            cur = cur.next
          return newHead
    ```

    - Time Complexity: O(3N) = O(N)

      Space Complexity: O(1) --> no extra space

### 2. 146 [LRU Cache](https://leetcode.com/problems/lru-cache/description/)

|  Category  |   Difficulty    |                    Tags                     |
| :--------: | :-------------: | :-----------------------------------------: |
| algorithms | Medium (40.01%) | [`design`](https://leetcode.com/tag/design) |

Design a data structure that follows the constraints of a **[Least Recently Used (LRU) cache](https://en.wikipedia.org/wiki/Cache_replacement_policies#LRU)**.

Implement the `LRUCache` class:

- `LRUCache(int capacity)` Initialize the LRU cache with **positive** size `capacity`.
- `int get(int key)` Return the value of the `key` if the key exists, otherwise return `-1`.
- `void put(int key, int value)` Update the value of the `key` if the `key` exists. Otherwise, add the `key-value` pair to the cache. If the number of keys exceeds the `capacity` from this operation, ==**evict** the least recently used key==.

The functions `get` and `put` ==must each run in `O(1)` average time complexity==.

**Example 1:**

```java
Input
["LRUCache", "put", "put", "get", "put", "get", "put", "get", "get", "get"]
[[2], [1, 1], [2, 2], [1], [3, 3], [2], [4, 4], [1], [3], [4]]
Output
[null, null, null, 1, null, -1, null, -1, 3, 4]

Explanation
LRUCache lRUCache = new LRUCache(2);
lRUCache.put(1, 1); // cache is {1=1}
lRUCache.put(2, 2); // cache is {1=1, 2=2}
lRUCache.get(1);    // return 1
lRUCache.put(3, 3); // LRU key was 2, evicts key 2, cache is {1=1, 3=3}
lRUCache.get(2);    // returns -1 (not found)
lRUCache.put(4, 4); // LRU key was 1, evicts key 1, cache is {4=4, 3=3}
lRUCache.get(1);    // return -1 (not found)
lRUCache.get(3);    // return 3
lRUCache.get(4);    // return 4 
```

- **Constraints:**

  - `1 <= capacity <= 3000`

  - `0 <= key <= 104`

  - `0 <= value <= 105`

  - At most 2` * 105` calls will be made to `get` and `put`.

- **Thoughts**

  - 题目是要求设计数据结构：

    首先要接收一个 `capacity` 参数作为缓存的最大容量，然后实现两个 API，一个是 `put(key, val)` 方法存入键值对，另一个是 `get(key)` 方法获取 `key` 对应的 `val`，如果 `key` 不存在则返回 -1。

    注意哦，`get` 和 `put` 方法必须都是 `O(1)` 的时间复杂度

    <font color=red>`put`函数的难点在于要除去the least recently used key --> 元素必须有序</font>

  - 要让 `put` 和 `get` 方法的时间复杂度为 O(1)，我们可以总结出 `cache` 这个数据结构必要的条件：

    1、显然 `cache` 中的元素必须有时序，以区分最近使用的和久未使用的数据，当容量满了之后要删除最久未使用的那个元素腾位置。

    2、我们要在 `cache` 中快速找某个 `key` 是否已存在并得到对应的 `val`；

    3、每次访问 `cache` 中的某个 `key`，需要将这个元素变为最近使用的，也就是说 `cache` 要支持在任意位置快速插入和删除元素。

    那么，什么数据结构同时符合上述条件呢？==哈希表查找快，但是数据无固定顺序；链表有顺序之分，插入删除快，但是查找慢。所以结合一下，形成一种新的数据结构：哈希链表 `LinkedHashMap`==。

    LRU 缓存算法的核心数据结构就是哈希链表，双向链表和哈希表的结合体。

    <img src="https://labuladong.github.io/algo/images/LRU%e7%ae%97%e6%b3%95/4.jpg" alt="img" style="zoom:50%;" />

    - 双向链表按照被使用的顺序存储了这些键值对，靠近头部的键值对是最近使用的，而靠近尾部的键值对是最久未使用的。

    - 哈希表即为普通的哈希映射（HashMap），通过缓存数据的键映射到其在双向链表中的位置。

    ![image-20220629174519603](/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220629174519603.png)

    <img src="https://labuladong.github.io/algo/images/LRU%e7%ae%97%e6%b3%95/put.jpg" alt="img" style="zoom: 50%;" />

- **Solution**

  ```python
  # S1: build a doubly linked list class for each node
  class Node: 
      def __init__(self, key, val):
          self.key = key
          self.val = val
          self.prev = self.next = None
  
  class LRUCache:
      def __init__(self, capacity: int):
          self.capacity = capacity
          self.cache = {} # map key to node
  
          # Use two dummyhead nodes --> 插入时不用管相邻是否有节点
          self.left = Node(-1, -1)
          self.right = Node(-1, -1)  
          # connect the two dummynodes
          self.left.next = self.right
          self.right.prev = self.left  
  
      # remove a node in the doubly linked list
      # 删除一个节点不光要得到该节点本身的指针，也需要操作其前驱节点的指针，而双向链表才能支持直接查找前驱，保证操作的时间复杂度 O(1)
      def remove(self, node):
          pre, nxt = node.prev, node.next
          pre.next = nxt
          nxt.prev = pre
  
      # insert the node to the right
      # 只从尾部插入 --> 靠尾部的数据是最近使用的，靠头部的数据是最久未使用的
      def insert(self, node):  
          pre, nxt = self.right.prev, self.right
          pre.next, nxt.prev = node, node
          node.prev, node.next = pre, nxt
  
      def get(self, key: int) -> int:
          # Return the value of the key from cache
          # Remove the element and insert from the right 
          if key in self.cache:
              self.remove(self.cache[key])
              self.insert(self.cache[key])
              return self.cache[key].val  # self.cache[key] is a node
          return -1
          
      def put(self, key: int, value: int) -> None:
          if key in self.cache:
              self.remove(self.cache[key])
          self.cache[key] = Node(key, value)    # 创建一个新的节点添加进哈希表
          self.insert(self.cache[key])					# 将该节点添加至双向链表
  
          # remove from the list and delete the LRU from the hashmap
          if len(self.cache) > self.capacity:
              LRU = self.left.next
              self.remove(LRU)  								# 如果超出容量，删除双向链表的尾部节点
              self.cache.pop(LRU.key)  					# 删除哈希表中对应的项
              # or: del self.cache[LRU.key]
  ```

  - Time Complexity: O(1)

    Space Complexity: O(Capacity) --> 因为哈希表和双向链表最多存储 capacity+1 个元素

  - <font color =red>**注意点：**</font>

    - 使用双向链表而不是单向链表的原因是：在缓存满了需要删除的时候，双向链表可以通过`right`快速找到最后一个节点。如果使用单向链表想要找到最后一个节点，需要从头节点遍历过去 --> <u>删除一个节点不光要得到该节点本身的指针，也需要操作其前驱节点的指针，而双向链表才能支持直接查找前驱，保证操作的时间复杂度 O(1)</u>。

    - 为什么要在链表中同时存储 key 和 val，而不是只存储 val: 

      在`put`函数中，当缓存容量已满，我们不仅仅要删除最后一个 `Node` 节点，还要把 `cache` 中映射到该节点的 `key` 同时删除，而这个 `key` 只能由 `Node` 得到。如果 `Node` 结构中只存储 `val`，那么我们就无法得知 `key` 是什么，就无法删除 `map` 中的键，造成错误。

### 3. 23 [Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Hard (47.14%) | [`linked-list`](https://leetcode.com/tag/linked-list); [`divide-and-conquer`](https://leetcode.com/tag/divide-and-conquer); [`heap`](https://leetcode.com/tag/heap)c |

You are given an array of `k` linked-lists `lists`, each linked-list is sorted in ascending order.

*Merge all the linked-lists into one sorted linked-list and return it.*

**Example 1:**

```
Input: lists = [[1,4,5],[1,3,4],[2,6]]
Output: [1,1,2,3,4,4,5,6]
Explanation: The linked-lists are:
[
  1->4->5,
  1->3->4,
  2->6
]
merging them into one sorted list:
1->1->2->3->4->4->5->6
```

**Example 2:**

```
Input: lists = []
Output: []
```

**Example 3:**

```
Input: lists = [[]]
Output: []
```

- **Constraints:**

  - `k == lists.length`

  - `0 <= k <= 104`

  - `0 <= lists[i].length <= 500`

  - `-104 <= lists[i][j] <= 104`

  - `lists[i]` is sorted in **ascending order**.

  - The sum of `lists[i].length` will not exceed `104`.

- **Solution**

  - <u>Method 1: An updated version of "Merge Two Lists"</u>

    --> Key: merge two lists again and again

    - S1: check edge cases: whether `lists` is empty
    - S2: merge two lists along the linked list array

    ```python
    def mergeKLists(self, lists: List[Optional[ListNode]]) -> Optional[ListNode]:
      if not lists:
        return
      
      while len(lists) > 1:
        mergedList = []
        for i in range(0, len(lists), 2):
          l1 = lists[i]
          l2 = lists[i + 1] if i + 1 < len(lists) else None
          mergedList.append(self.mergeTwoLists(l1, l2))
        lists = mergedList     # reduce the length by half
      
      return lists[0]  				 # now 'lists' has only one element: lists[0]
    
    def mergeTwoLists(self, l1, l2):
        dummy = ListNode(-1)
        cur = dummy
        
        while l1 and l2:
          if l1.val <= l2.val:
            cur.next = l1
            l1 = l1.next
          else:
            cur.next = l2
            l2 = l2.next
          cur = cur.next
       
        if l1:
          cur.next = l1
        if l2:
          cur.next = l2
        return dummy.next
    ```

    Time Complexity: O(nlogk) --> 其中 `k` 是链表的条数，`N` 是这些链表的节点总数。

    Space Complexity: O(k) --> for `mergedList` memory

  - <u>Method 2: Heap</u>

    --> Use min heap to order --> min-heap can extract the smallest elements between arrays

    这道题是21. 合并两个有序链表的进阶题
    https://leetcode.cn/problems/merge-two-sorted-lists/solution/shuang-zhi-zhen-by-shan-gui-tju-bz4g/
    回忆21题的做法，是每次比较双指针所在节点的值的大小，那么在这里，其实就是要比较所有链表指针所在节点的值的大小，即找到这些节点的最小节点，这里引入一个最小堆来优化这个过程。用所有链表的头结点的值，初始化最小堆，然后每次，==【弹出最小值的节点，并把该节点指向的下一个节点，加入到最小堆里】==；重复【】内的过程。

    ```python
    import heapq
    def mergeKLists(self, lists: List[Optional[ListNode]]) -> Optional[ListNode]:
      if not lists:
        return
      
      minHeap = []
      heapq.heapify(minheap)
      
      # Initialize the heap: add the first k elements to the heap
      for i in len(lists):
        if lists[i]:
          heapq.heappush(minheap, (lists[i].val, i, lists[i]))    # i is the index
      
      dummy = ListNode(-1)
      cur = dummy.next
      while minHeap:
        minNode = heapq.heappop(minheap)[2]    # 易错！！注意[2]才是node
        cur.next = minNode
        if minNode.next:
          heapq.heappush(minHeap, (minNode.next.val, i, minNode.next))
        cur = cur.next
      
      return dummy.next
    ```

    - Time complexity: 优先队列 `minHeap` 中的元素个数最多是 `k`，所以一次 `heappush` 或者 `heappop` 方法的时间复杂度是 `O(logk)`；所有的链表节点都会被加入和弹出 `minHeap`，**所以算法整体的时间复杂度是 `O(Nlogk)`，其中 `k` 是链表的条数，`N` 是这些链表的节点总数**。

      Space complexity: O(k) --> memory for heap, heap contains less than k elements

    - <font color=red>**注意点：**</font>
      - `heapq.heappush(minheap, (lists[i].val, i, lists[i]))`: The problem while adding `ListNode` objects as tasks is that the Tuple comparison breaks for (priority, task) pairs if the priorities are equal and the tasks do not have a default comparison order. The solution is to store entries as 3-element list including the priority, an entry count, and the task.
        The entry count serves as a tie-breaker so that two tasks with the same priority are returned in the order they were added.
        And since no two entry counts are the same, the tuple comparison will never attempt to directly compare two tasks.