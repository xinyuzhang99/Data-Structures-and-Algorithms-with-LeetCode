# Iterator 迭代器

--> a way of iterating over indexed or finite data structures Eg. `for item in items:`

- **Properties**

  - It only needs to know how to get the next item --> not need to store the entire data in memory if we don't need the entire data structure --> 

    ```python
    class LinkedListIterator:
        def __init__(self, head):
            self._node = head				
    
        def hasNext(self):
            return self._node is not None
    
        def next(self):
            result = self._node.value
            self._node = self._node.next    
            return result
    # store the head at the start, but as items are consumed, discard the current one and replace it with the item after
    ```

  - Iterators can represent sequences without even using a data structure

    ```python
    class RangeIterator:
        def __init__(self, min, max):
            self._max = max
            self._current = min
    
        def hasNext(self):
            return self._current < self._max
    
        def next(self):
            self._current += 1
            return self._current - 1
    ```

  - Can <u>handle an infinite sequence</u> --> increased the performance

    ```python
    class SquaresIterator:
        def __init__(self):
            self._n = 0
    
        def hasNext(self):
            # It continues forever,
            # so definitely has a next!
            return True
    
        def next(self):
            result = self._n
            self._current += 1
            return result ** 2
    ```

  - Provide a clean interface for the code --> With an `Iterator`, the external code <u>doesn't even know how the underlying data structure works</u>. For all it knows, the data could be coming from a Linked List, an Array, a Tree, a State Machine, a clever number generator, a file reader, a robot sensor, etc. --> <font color=blue>**[Iterator Pattern: separating the underlying data structure from the code that uses it]**</font>

- 常规的迭代器的「访问」只支持两种操作：

  - `hasNext()` 操作：如果存在下一元素，返回 True，否则返回 False。实现上，就是判断游标是否到达结尾位置；
  - `next()` 操作：返回下一元素（当不存在下一元素时，返回 null）。实现上，就是返回游标指向的元素，并让游标后移。--> 当使用了`next()`操作后，If you don't store or process the value you got from the `Iterator` then it's possibly gone forever!

  