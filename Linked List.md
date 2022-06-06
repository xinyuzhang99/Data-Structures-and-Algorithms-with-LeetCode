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
          
          if list1 is None:
              curr.next = list2
          elif list2 is None:
              curr.next = list1
  
          return dummyhead.next
  ```

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
  
          slow = dummyhead
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

  - 由于直接得到单链表的长度 `n`，想一次遍历就得到中间节点，使用==「快慢指针」==的技巧：

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

    如果 `fast` 最终遇到空指针，说明链表中没有环；如果 `fast` 最终和 `slow` 相遇，那肯定是 `fast` 超过了 `slow` 一圈，说明链表中含有环

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


## 2. ==Iteration / Recursion 迭代/递归反转单链表==

<font color=red>**重要！！看这个链接和labuladong算法小抄结合**</font> 

https://leetcode.cn/problems/reverse-linked-list-ii/solution/yi-bu-yi-bu-jiao-ni-ru-he-yong-di-gui-si-lowt/

### 1. 206 [Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/description/)

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

    <img src="https://labuladong.github.io/algo/images/%e5%8f%8d%e8%bd%ac%e9%93%be%e8%a1%a8/1.jpg" alt="img" style="zoom:25%;" />

    <img src="https://labuladong.github.io/algo/images/%e5%8f%8d%e8%bd%ac%e9%93%be%e8%a1%a8/2.jpg" alt="img" style="zoom:25%;" />

    <img src="https://labuladong.github.io/algo/images/%e5%8f%8d%e8%bd%ac%e9%93%be%e8%a1%a8/4.jpg" alt="img" style="zoom:25%;" />

    <img src="https://labuladong.github.io/algo/images/%e5%8f%8d%e8%bd%ac%e9%93%be%e8%a1%a8/5.jpg" alt="img" style="zoom:25%;" />

    ```python
    class Solution:
        def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
            if (head is None or head.next is None):
                return head
            
            last = self.reverseList(head.next)
            head.next.next = head
            head.next = None      # 如果忽略这一步，链表中可能产生环
            return last
    ```

​			Time Complexity: O(N)

​			 Space Complexity: O(1)

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
                next.next = prev.next
                prev.next = next
            return dummyhead.next
    ```

    Time Complexity: O(N) --> the worst case is to iterate through the whole list

    Space Complexity: O(1)

  - <u>Method 2: Recursion</u>

    https://leetcode.cn/problems/reverse-linked-list-ii/solution/yi-bu-yi-bu-jiao-ni-ru-he-yong-di-gui-si-lowt/

    <font color=red>**空缺！**</font>