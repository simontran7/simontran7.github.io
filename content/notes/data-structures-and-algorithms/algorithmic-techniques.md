# Algorithmic Techniques

## Two Pointers

### Two Pointers, Opposite Ends

#### Use Case

The list input is sorted, and you are looking for a pair (or pairs) that meet a specific condition.

#### Template

```python
def two_pointers_opposite_ends(array):
    left = 0
    right = len(array) - 1
    result = 0

    while left < right:
        # Some logic involving `array[left]` and/or `array[right]`

        if <condition>:
            left += 1
        else:
            right -= 1

    return result
```

### Two Pointers, Merge

#### Use Case

The input list is sorted, and you want to perform an in-place modification by filtering or deduplicating elements.

#### Template

```python
def two_pointers_merge(array1, array2):
    i = 0
    j = 0
    result = 0

    while i < len(array1) and j < len(array2):
        # Some logic involving `array1[i]` and `array2[j]`
        if <condition>:
            i += 1
        else:
            j += 1

    while i < len(array1):
        # Some logic involving `array1[i]`
        i += 1

    while j < len(array2):
        # Some logic involving `array2[j]`
        j += 1

    return result
```

## Sliding Window

### Variable Sliding Window

#### Use Case

You are looking for the longest/smallest subarray/substring that satisfies a certain constraint.

#### Usage

```python
def variable_sliding_window_max(array):
    left = 0
    current = 0
    result = 0

    for right in range(len(array)):
        current += array[right]

        while <window is invalid condition>:
            current -= array[left]
            left += 1

        result = max(result, right - left + 1)

    return result
```

```python
import math

def variable_sliding_window_min(array):
    left = 0
    current = 0
    result = math.inf

    for right in range(len(array)):
        current += array[right]

        while <window is valid condition>:
            result = min(result, right - left + 1)
            current -= array[left]
            left += 1

    return result if result != math.inf else -1

```

### Fixed Sliding Window

#### Use Case

You are looking for a subarray/substring of some length `k` that satisfies a certain constraint.

#### Usage

```python
def fixed_sliding_window(array, k):
    current_sum = 0
    result = 0
    left = 0

    for right, num in enumerate(array):
        current_sum += num

        if (right - left + 1) == k:
            result = max(result, current_sum)
            current_sum -= array[left]
            left += 1

    return result
```

> [!NOTE]
> The constraint must be preserved as the sliding window shrinks. If shrinking the window can break the constraint, consider using the *prefix state* technique instead.

> [!NOTE]
> The formula for the length of any window is `right - left + 1` .

## Prefix Sum

### Use Case

You need a subroutine which involves range-sum queries of a subarray in $O(1)$.

### Usage

```python
def prefix_sum(array):
    """
    To get the sum from [i, j]: `prefix[j] - prefix[i] + nums[i]`
    """
    prefix_sum = [0]

    for num in array:
        prefix_sum.append(prefix_sum[-1] + num)

    return prefix_sum
```

## Map and Set

### Map

#### Use Case

- Track elements seen so far for uniqueness (with any extra info stored as the value)
- Frequency counting
- Basic mapping

#### Usage

```python
# Create an empty map
my_map = dict()

# Create a map with initial values
my_map = {
    <key 1>: <value 1>,
    <key 2>: <value 2>
}

# Add new entry or update current entry
my_map[<immutable key>] = <value>

# Remove an entry
del my_map[<key>]

# Remove all entries
my_map.clear()

# Get number of entries
len(my_map)

# Check if my_map is empty
not my_map

# Get value
my_map[<key>]

# Get the value with a default value if key isn't found
# Note: I like thise over `defaultdict(<default value type>)` for counting since it can avoid accidentally creating phantom keys
my_map.get(<key>, <default value>)

# Note: pretty much a must for adjacency list creation
from collections import defaultdict
my_map = defaultdict(<default value's type>)

# Check if key exists
<key> in my_map

# Get all entries
my_map.items()

# Get all keys
my_map.keys()

# Get all values
my_map.values()
```

### Set

#### Use Case

- Track elements seen so far for uniqueness
- Store a chunk (or all) of the input for fast lookups

#### Usage

```python
# Create an empty set
my_set = set()

# Add an element
my_set.add(<element>)

# Check if a set contains an element
<element> in my_set

# Remove an element
my_set.remove(<element>)

# Get the set difference
my_set.difference(other_set)

# Get the set intersection
my_set.intersection(other_set)

# Get the set union
my_set.union(other_set)

# Remove all elements
my_set.clear()

# Get the number of elements
len(my_set)

# Check if the set is empty
not my_set
```

## Prefix State

### Use Case

- Find the longest/shortest subarray that satisfies some condition
- Count the number of subarrays that satisfies some condition, and the state of any subarray `array[i..j]` can be computed from two prefix states `state[0..j]` and `state[0..(i - 1)]`.

### Usage

```python
def prefix_state(array, target_state):
    state_index_map = {0: -1} # we'll want to store the state as the key, and the first index of its appearance if looking for the longest subarray, the last index of its appearance if looking for the longest subarray, or its frequency as the value.
    current_state = 0
    result = 0 # initialize to `math.inf` if looking for shortest subarray

    for i in range(len(array)):
        current_state = # update the running prefix state with the new element

        needed_state = current_state - target_state # or `current_state ^ target_state`
        if needed_state in state_map:
            subarray_len = i - state_map[needed_state]
            result = max(result, subarray_len) # for shortest, use `min()` instead
        else:
            state_map[current_state] = i # for shortest, remove the else branch and do this instead `state_map[current_state] = i`

        return result # for shortest subarray, do this instead `return result if result != math.inf else 0`
```

## Fast and Slow Pointers (Floyd's Cycle Detection)

### Use Case

- Detect cycles in linked list
- Find middle of a linked list

### Usage

```python
def fast_and_slow_pointers(head):
    fast = head
    slow = head
    result = <initial value>

    while fast and fast.next:
        # Some logic

        fast = fast.next.next
        slow = slow.next
```

## Reversing a Linked List

### Use Case

- The problem largely involves reversing pointers in a linked list
- You need a subroutine which involves classically reversing pointers in a linked list

### Usage

```python
def reverse_linked_list(head):
    prev_node = None
    curr_node = head

    while curr_node:
        next_node = curr_node.next
        curr_node.next = prev_node
        prev_node = curr_node
        curr_node = next_node
```

> [!NOTE]
> To reduce edge cases, whenever you need to return the original head of the linked list, instead of returning the head node directly, create a **dummy node**, set it as the head of the linked list `dummy = ListNode(0, head)`, and return `dummy.next`.

## Stack and Queue

### Stack

#### Use Case

Elements in the input interacting with each other, with a LIFO order.

#### Usage

```python
# Create an empty stack
stack = list()

# Push an element onto the top
stack.append(<element>)

# Pop the element at the top
stack.pop()

# Get the element at the top
stack[-1]

# Get the number of elements in the stack
len(stack)

# Check if the stack is empty
not stack
```

> [!NOTE]
> We often use the stack to store the result and convert it to a string using the \$O(n)\$ string builder Technique.
```python
def build_string(string):
    array = []

    for char in string:
        array.append(char)

    return "".join(array)
```

### Queue

#### Use Case

Processing elements in a FIFO order.

#### Usage

```python
from collections import deque

# Create a queue from an initial list
queue = deque(<initial list>)

# Enqueue an element at the back
queue.append(<element>)

# Dequeue the element at the front
queue.popleft()

# Get the element at the front
queue[0]

# Get the number of elements
len(queue)

# Check if queue is empty
not queue
```

## Monotonic Stack and Monotonic Deque

### Monotonic Stack

#### Use Case

- Looking for the next/previous greater/smaller element
- Spans, ranges, or boundaries

#### Usage

```python
def monotonic_decreasing_stack(array):
    mono_stack = []
    result = <initial value>

    for i in range(len(array)):
        while mono_stack and array[mono_stack[-1]] < array[i]:
            mono_stack.pop()
            # Utilize stack.pop() in result
            # e.g. result[stack.pop()] = <something involving i>
            # e.g. result += stack.pop()

        mono_stack.append(i)

    return result
```

### Monotonic Deque

#### Use Case

Maximum or minimum values in a sliding window or some ranges.

#### Usage

```python
from collections import deque

def monotonic_non_increasing_deque(array, k):
    result = []
    dq = deque()

    for i in range(len(array)):
        while dq and array[dq[-1]] < array[i]:
            dq.pop()

        dq.append(i)

        if dq[0] <= i - k:
            dq.popleft()

        if i >= k - 1:
            result.append(array[dq[0]])

    return result
```

> [!NOTE]
> A monotonic increasing/decreasing stack or queue implies that the elements are always increasing/decreasing, while a monotonic non-decreasing/non-increasing one may include repeated elements.

## Binary Tree Traversal

### Binary Tree Depth-First Search

#### Use Case

Most binary tree problems that don't involve processing nodes by their levels.

#### Usage

```python
def recursive_preorder_dfs(root):
    if not root:
        return <base case result>

    # Additional base cases

    # Some logic involving the current node and the current result

    recursive_preorder_dfs(root.left)
    recursive_preorder_dfs(root.right)

    return <current result and the two recursive calls above>
```

```python
def iterative_preorder_dfs(root):
    if not root:
        return <base case result>

    stack = [root]

    result = <initial value>

    while stack:
        node = stack.pop()

	    # Additional base cases

        # Some logic involving the popped node and the result

	    if node.right:
            stack.append(node.right)
        if node.left:
            stack.append(node.left)

    return result
```

> [!NOTE]
> In the iterative depth-first search, the flow is usually pre-order `pop node → process node → push right → push left` , while in the recursive depth-first search, pre-order `process node → recurse left → recurse right` is the most common, followed by post-order `recurse left → recurse right → process node` , then in-order `recurse left → process node → recurse right` .

> [!NOTE]
> In depth-first search, a **state** is all the data you need to remember at one point in the search. Each state is typically compromised of one or more variables, which we call a **state variable**. In a recursive implementation, the state consist of function arguments stored in a call stack frame, while in an iterative implementation, the state consist of variables stored in tuple that will be pushed and popped from an explicit stack you create outside the while loop.

> [!NOTE]
> In a recursive depth-first search, `result` is typically implicit since it's usually sufficient to implicitly be returned, but an explicit `result` is sometimes a good choice, where you create it within the scope of the provided function, then create and call an inner depth-first search function to do the actual work. In an iterative depth-first search, you typically create an explicit `result` variable outside the while loop.

> [!NOTE]
> BST problems typically use DFS traversal. Common techniques include:
> - Checking if the current node's value is within bounds (e.g., `low <= node.val <= high`)
> - Leveraging the BST property to prune subtrees — if `node.val < low` , skip the left subtree; if `node.val > high` , skip the right subtree (where `low` or `high` can also just be a target value)
> - Using inorder traversal to collect values in sorted order for problems requiring sorted data without explicit sorting.

### Binary Tree Breadth-First Search

#### Use Case

Binary tree problems that involve processing nodes by their levels.

#### Usage

```python
from collections import deque

def bfs(root):
    if not root:
        return

    queue = deque([root])
    result = 0

    while queue:
        level_width = len(queue)

        # Some logic involving the current level

        for _ in range(level_width):
            node = queue.popleft()

            # Some logic involving the current node

            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)

    return result
```

## Graph Traversal

### Depth-First Search on an Adjacency List Graph

#### Use Case

Most graph problems.

#### Usage

```python
def adjacency_list_recursive_dfs(graph):
    def dfs(vertex):
        visited.add(vertex)
        result = <initial value>
        for neighbour in graph[vertex]:
            if neighbour not in visited:
                result += dfs(neighbour)
        return result

    result = <initial value>
    visited = set()
    for vertex in graph:
        if vertex not in visited:
            dfs(vertex)

            # Some logic involving the result per connected component
```

```python
def adjacency_list_iterative_dfs(graph):
    def dfs(vertex):
	    result = <initial value>
        stack = [vertex]
        while stack:
            vertex = stack.pop()
            visited.add(vertex)
            for neighbour in graph[vertex]:
                if neighbour not in visited:
                    result += <calculation>
                    stack.append(neighbour)
		return result

    result = <initial value>
    visited = set()
    for vertex in graph.keys():
        if vertex not in visited:
            dfs(vertex)

            # Some logic involving the result per connected component
```

### Depth-First Search on a Matrix Graph (Flood Fill)

#### Use Case

Most graph problems where the input is a matrix.

#### Usage

```python
def matrix_recursive_dfs(matrix):
    ROWS = len(matrix)
    COLUMNS = len(matrix[0])
    # NOTE: each element is of the form (change in row, change in col)
    DIRECTIONS = [(1, 0), (-1, 0), (0, -1), (0, 1)]

    def valid_cell(row, col):
        return 0 <= row < ROWS and 0 <= col < COLUMNS and <another condition for a cell to be valid>

    def dfs(row, col):
        visited.add((row, col))
        result = <initial value>
        for dr, dc in DIRECTIONS:
            neighbour_row = row + dr
            neighbour_col = col + dc
            if valid_cell(neighbour_row, neighbour_col) and (neighbour_row, neighbour_col) not in visited:
                result += dfs(neighbour_row, neighbour_col)
        return result

    visited = set()
    result = <initial value>
    for row in range(ROWS):
        for col in range(COLUMNS):
            if (row, col) not in visited:
                dfs(row, col)

                # Some logic involving the result per connected component

    return result
```

```python
def matrix_iterative_dfs(matrix):
    ROWS = len(matrix)
    COLUMNS = len(matrix[0])
    # NOTE: each element is of the form (change in row, change in col)
    DIRECTIONS = [(1, 0), (-1, 0), (0, -1), (0, 1)]

    def valid_cell(row, col):
        return 0 <= row < ROWS and 0 <= col < COLUMNS and <another condition for a cell to be valid>

    def dfs(row, col):
        stack = [(row, col)]
        result = <initial value>
        while stack:
            row, col = stack.pop()
            visited.add((row, col))
            for dr, dc in DIRECTIONS:
                neighbour_row = row + dr
                neighbour_col = col + dc
                if valid_cell(neighbour_row, neighbour_col) and (neighbour_row, neighbour_col) not in visited:
                    result += <some calculation>
                    stack.append((neighbour_row, neighbour_col))
        return result

    visited = set()
    result = <initial value>
    for row in range(ROWS):
        for col in range(COLUMNS):
            if (row, col) not in visited:
                dfs(row, col)

                # Some logic involving the result per connected component

    return result
```

### Breadth-First Search on an Adjacency List Graph

#### Use Case

Distance in a graph.

#### Usage

```python
from collections import deque

def adjacency_list_bfs(graph):
    queue = deque([(<source vertex>,<additional state variable>, <initial distance>)])
    visited = set([<source vertex>])

    while queue:
        vertex, <additional state>, dist = queue.popleft()

        if vertex == <destination vertex>:
            return dist

        for neighbour in graph[vertex]:
            if neighbour not in visited:
                visited.add(neighbour)
                queue.append((neighbour, <additional state variable>, dist + 1))

            # Some logic involving the neighbour
```

### Breadth-First Search on a Matrix Graph

#### Use Case

Distance in a graph given as a matrix.

#### Usage

```python
def matrix_iterative_bfs(matrix):
    ROWS = len(matrix)
    COLUMNS = len(matrix[0])
    # NOTE: each element is of the form (change in row, change in col)
    DIRECTIONS = [(0, 1), (1, 0), (1, 1), (-1, -1), (-1, 1), (1, -1), (-1, 0), (0, -1)]

    def valid_cell(row, col):
        return 0 <= row < ROWS and 0 <= col < COLUMNS and <another condition for a cell to be valid>

    queue = deque([(<source vertex>,<additional state>, <initial distance>)])
    visited = set([(<source vertex>, <initial distance>)])

    while queue:
        row, col, dist = queue.popleft()

        if (row, col) == (<destination row>, <destination col>):
            return dist

        for dr, dc in DIRECTIONS:
            neighbour_row = row + dr
            neighbour_col = col + dc
            neighbour_dist = dist + 1
            if (neighbour_row, neighbour_col) not in visited and valid_cell(neighbour_row, neighbour_col):
                visited.add((neighbour_row, neighbour_col))
                queue.append((neighbour_row, neighbour_col, <additional state>, neighbour_dist))

            # Some logic involving the neighbour's row and col
```

> [!NOTE]
> Unlike linked lists and binary trees, which we are given `head` or `root` respectively, there are various graph inputs:
>
> 1. Matrix: A 2D list, where each element will represent a vertex, but are *not* numbered `0` to `n`, its neighbours are the adjacent squares, and the edges are determined by the problem description.
> 2. Edge list: A list of edges `edges`. It's useful to turn it into an adjacency list.
>
> ```python
> from collections import defaultdict
>
> def build_adjacency_list_graph(edges):
>    graph = defaultdict(list)
>    for u, v in edges:
>        graph[u].append(v)
>        graph[v].append(u) # comment out this line if the input is a directed graph
>
>    return graph
> ```
>
> 3. Integer Adjacency List: A 2D list of integers `graph`, where `n` nodes are numbered from `0` to `n - 1`, and `graph[i]` represents the neighbours of node `i`.
> 4. Integer Adjacency Matrix: A 2D list of integers, where `n` nodes are numbered from `0` to `n - 1`, thereby forming an `n x n` square matrix, and where when `graph[i][j] == 1`, there exist an edge between node `i` and node `j`, and when `graph[i][j] == 0`, there is no edge between node `i` and node `j`. It's also useful to pre-process it into an adjacency list.
>
> ```python
> from collections import defaultdict
>
> def build_adjacency_list_graph(adjacency_matrix):
>     graph = defaultdict(list)
>     n = len(adjacency_matrix)
>
>     for i in range(n):
>         for j in range(i + 1, n):
>             if adjacency_matrix[i][j]:
>                 graph[i].append(j)
>                 graph[j].append(i) # comment out this line if the input is a directed graph
>
>    return graph
> ```
>
> Although, even if the input is none of the above, it may still be an implicit graph problem, often where vertices aren't explicitly given, but can be generated on the fly through valid transitions or transformations. These problems typically involve:
>
> - A starting state and a goal/end state
> - A defined set of valid transitions or mutations
> - Optional constraints like invalid/intermediate states

> [!NOTE]
> `visited` is typically a HashSet, but you might achieve better runtime performance by using a boolean array when the node range is predetermined (which is typical since graph problems usually number nodes from `0` to `n - 1` )

> [!NOTE]
> Whenever the problem mentions prohibited vertices, then that's an indicator to add them straight away to the `visited` container.

> [!NOTE]
> When using BFS to find shortest paths:
>
> - If the problem asks for distance (number of moves/steps), initialize source vertices with `distance = 0`
> - If the problem asks for path length (number of cells in the path), initialize source vertices with `path_length = 1`

> [!NOTE]
> For a multi-source BFS, create a for loop that visits all source nodes and appends them to the queue for the BFS.

> [!NOTE]
> For graph problems, it's useful to rephrase the problem in terms of its inverse. For instance, take [LeetCode #1557](https://leetcode.com/problems/minimum-number-of-vertices-to-reach-all-nodes/). The original problem description asks us to find the smallest set of vertices from which all nodes in the graph are reachable. Instead, we can rephrase the problem description in terms of its inverse — find the smallest set of nodes that *cannot* be reached from other nodes, since if a node can be reached from another node, then we would rather just include the pointer rather than the pointee in our set. Another example is [LeetCode #542](https://leetcode.com/problems/01-matrix/description/). The brute force solution would be to perform BFS for each cell with a 1, but instead, we can perform a multi-source BFS by performing starting from all cells with a 0 (if we have a cell `x` with value 1 and its nearest cell y has value 0, then it doesn't make a difference if we traverse from `x -> y` or `y -> x` — both give the same distance).

## Priority Queue

### Use Case

- Repeatedly find the maximum or minimum element
- Get the "top" $k$ elements

```python
import heapq

def top_k(array, k):
    pq = []
    for num in array:
        # some logic to push add an element according to problem's criteria
        heapq.heappush(pq, (<criteria as key>, num)) # use a min heap to keep the largest `k` elements, or a max heap to keep the smallest `k`
        if len(pq) > k:
            heapq.heappop(pq)

    return [num for _, num in pq]
```

- Find a median

```python
import heapq

class MedianFinder:
    def __init__(self):
        self.min_pq = []
        self.max_pq = []

    def addNum(self, num: int) -> None:
        """
        second line maintains the invariant that all the elements in the min pq are >= to all the elements in the max pq
        third and fourth line maintains the invariant that max pq will store 1 more element than the min pq
        if there are an odd number of elements
        """
        heapq.heappush_max(self.max_pq, num)
        heapq.heappush(self.min_pq, heapq.heappop_max(self.max_pq))
        if len(self.min_pq) > len(self.max_pq):
            heapq.heappush_max(self.max_pq, heapq.heappop(self.min_pq))

    def findMedian(self) -> float:
        """
        - If there are even numbers, then len(max_pq) == len(min_pq) (i.e., the median will be the average of the top elements)
        - If there are odd numbers, then len(max_pq) == len(min_pq) + 1 (i.e., it will have the median)
        """
        if len(self.max_pq) == len(self.min_pq):
            return (self.max_pq[0] + self.min_pq[0]) / 2
        return self.max_pq[0]
```

### Usage

#### Min Priority Queue

```python
import heapq

# Create an empty priority queue
min_pq = []
heapq.heapify(min_pq)

# Add an element
heapq.heappush(min_pq, <element>)

# Remove the top element
heapq.heappop(min_pq)

# Peek at the top element
min_pq[0]

# Get the number of elements
len(min_pq)

# Check if the priority queue is empty
not min_pq
```

#### Max Priority Queue

```python
import heapq

# Create an empty priority queue
max_pq = []
heapq.heapify_max(max_pq)

# Add an element
heapq.heappush_max(max_pq, <element>)

# Remove the top element
heapq.heappop_max(max_pq)

# Peek at the top element
max_pq[0]

# Get the number of elements
len(max_pq)

# Check if the priority queue is empty
not max_pq
```

> [!NOTE]
> If the Python version is less than 3.14, than the `heapq` module only supports min heaps. To simulate a max heap, first build a new list `pq` from the elements of the input but negated using `-`, call `heapq.heapify(pq)`, and then negate each popped value to get back the original.

> [!NOTE]
> To assign priority based on a specific key, wrap each item in your list as a tuple: `(key, element)`. For max heaps, you must negate the key by `-`, and if the question involves tie-breakers, negate the element by `-`.

## Greedy

### Use Case

Typically finding the minimum or the maximum of a property of the input array.

### Usage

1. Determine if you should greedily pick the minimum or the maximum at each step.
2. Sort the input array (often you may need to sort based on the frequency of each element using `Counter(<list>)`).
3. Iterate over the sorted array and increment the result, making use of a priority queue as needed.

## Binary Search

### Exact Match On an Array

#### Use Case

For some input array, `array`, and a desired element `target`, `array` must be sorted, and you want to find the index of `target` if it is in `array`, or otherwise return `-1`.

#### Usage

```python
def binary_search(array, target):
    low = 0
    high = len(array) - 1
    while low <= high:
        mid = (low + high) // 2

        if <found condition>:
            return mid

        if <go right condition>:
            low = mid + 1
        else:
            high = mid - 1
    return -1
```

### Boundary Convergence On an Array

#### Use Case

You want the first index where a condition flips in a sorted array (e.g. find minimum in rotated sorted array, or more commonly, you want to find a neighbouring value of a target value)

#### Usage

```python
def lower_bound(array, target):
	"""
	Returns the index of the leftmost value >= `target`
	"""
    low = 0
    high = len(array)

    while low < high:
        mid = (low + high) // 2
        if array[mid] < target:
            low = mid + 1
        else:
            high = mid

    return low
```

```python
def upper_bound(array, target):
	"""
	Returns the index of the leftmost value > `target`
	"""
    low = 0
    high = len(array)

    while low < high:
        mid = (low + high) // 2
        if array[mid] <= target:
            low = mid + 1
        else:
            high = mid

    return low
```

> [!NOTE]
> If you want the neighbouring value to a target, then the template requires little to no changes, and then use it as follows accordingly. Otherwise, you likely need to tweak the if condition, and set `high = len(array) - 1` (in the pure `lower_bound()` and `upper_bound()`, we kept `high = len(array)` because an insertion point that goes beyond the last element of the array is valid).
```python
def find_lt(array, target):
    """
	Returns the rightmost value < `target`
	"""
    i = lower_bound(array, target)
    if i > 0:
        return array[i - 1]
    raise ValueError

def find_le(array, target):
    """
	Returns the rightmost value <= `target`
	"""
    i = upper_bound(array, target)
    if i > 0:
        return array[i - 1]
    raise ValueError

def find_gt(array, target):
    """
	Returns the leftmost value > `target`
	"""
    i = upper_bound(array, target)
    if i != len(array):
        return array[i]
    raise ValueError

def find_ge(array, target):
    """
	Returns the leftmost value >= `target`
	"""
    i = lower_bound(array, target)
    if i != len(array):
        return array[i]
    raise ValueError
```

> [!NOTE]
> `binary_search()` doesn't work if `array` contains duplicates, but `lower_bound()` and `upper_bound()` allows duplicates in `array`.

> [!NOTE]
> If the input array is sorted in descending order, simply invert the inequality in the if condition.

#### Illustrating exercises

- [Find peak element](https://leetcode.com/problems/find-peak-element/description/)

### Boundary Convergence On a Solution Space

#### Use Case

You're trying to find a **maximum** or **minimum** value, and:
- You can verify (usually with a greedy algorithm) in $O(n)$ time (or faster) whether a given candidate `x` is a valid solution
- The solution space has to be structured so that all valid answers are grouped together on one side. That is:
	- If `x` is a valid solution:
	    - For a maximum, all values $ \le x$ are also valid.
	    - For a minimum, all values $\ge x$ are also valid.
	- If `x` is not a valid solution:
	    - For a maximum, all values $\gt x$ are also invalid.
	    - For a minimum, all values $\lt x$ are also invalid.

#### Usage

```python
def binary_search_minimum(array):
    def is_valid(x):
        # Some O(n) algorithm (usually also a greedy algorithm)
        return <boolean>

    low = <minimum possible answer>
    high = <maximum possible answer>

    while low <= high:
        mid = (low + high) // 2
        if is_valid(mid):
            high = mid - 1
        else:
            low = mid + 1

    return low
```

```python
def binary_search_maximum(array):
    def is_valid(x):
        # Some O(n) algorithm (usually also a greedy algorithm)
        return <boolean>

    low = <minimum possible answer>
    high = <maximum possible answer>

    while low <= high:
        mid = (low + high) // 2
        if is_valid(mid):
            low = mid + 1
        else:
            high = mid - 1

    return high
```

## Backtracking (Complete Search)

### Use Case

Enumerating all possible solutions

### Usage

```python
def is_valid(candidate):
    # Some logic to check if candidate is valid or not

def backtrack(path, <other state variables>):
    if <base case>:
        # update global result (i.e., append the path to the global result or increment the global result)
        return

    local_result = <initial value>
    for <candidate> in <input>:
        if not is_valid(candidate):
            continue
        # modify `path`
        local_result += backtrack(path, <other state variables>)
        # undo the modification to `path`

    return local_result
```

> [!NOTE]
> Backtracking can be visualized as a tree, where each node represents the current state of the path during a recursive function call. The `backtrack()` calls explore different branches of this tree, building potential solutions along the way. The leaves of the tree correspond to base cases—often representing complete solutions, though not necessarily in every problem.

## Dynamic Programming

### Use Case

Problems suitable for dynamic programming typically involve making decisions where each choice influences subsequent decisions. Then, the goal is to:

- Optimization problems
- Combinatorial problems
- Feasability problems

### Usage

#### Step 1: Define a function `dp()`

Define a function `dp()` and determine its return value based on the problem description.

#### Step 2: Define the Subproblems

Decide on the relevant state variables (i.e. what parameters it should have). A good way to think about state variables is to imagine if the problem was a real-life scenario, and ask determine what information would you need to describe it in full.

**Common state variables**

- A primary index $i$ along an input string, input array, or an implicit range of numbers. This state variable represents the slice in the range $[0, i]$, thereby flowing backwards, or it may represent a slice in the range $[i, N)$, thereby flowing forwards.
- A secondary index $j$ along an input string or an input array, or an implicit range of numbers. This state variable is often used in conjunction with a primary state variable $i$ to represent the slice in the range $[i, j]$. It may also simply represent another index into the second input.
- An integer variable to track the remaining amount of moves when there is an imposed problem constraint $k$.
- A boolean variable to track a status.

> [!NOTE]
> Constants given by the problem should *never* be state variables!

> [!NOTE]
> The **dimensionality** of a dynamic programming problem is determined by the number of state variables required by a dynamic programming algorithm. We say a problem is a $1D$ dynamic programming problem when a dynamic programming algorithm only requires one state variable, and when the dynamic programming algorithm requires only two state variables, we call that problem a $2D$ dynamic programming problem.

#### Step 3: Determine the Recurrence Relation

1. Determine the recurrence rule which will involve calls to `dp()`, and typically the `min()` or `max()` function for optimization problems.
2. Determine the base case(s) derived from the problem description.

> [!NOTE]
> A state variable may either flow _forward_ or _backward_:
>
> - If a state variable flows forward, the recurrence depends on larger values of that variable, and the base case(s) appear at the maximal values.
> - If a state variable flows backward, the recurrence depends on smaller values of that variable, and the base case(s) appear at the minimal values.
>
> In multi-dimensional problems, each variable can have its own flow direction.

#### Step 4: Determine the Arguments to the Initial `dp()` Call

The initial `dp()` call will return the result to the overall problem.

The arguments depend on the direction of flow of the state variables:

- If a state variable flows forward, the initial argument will be the minimum value.
- If a state variable flows backwards, the initial argument will be the maximum value.

#### Step 5: Produce the Top-Down Solution (Memoization)

```python
memo = dict()

def dp(<state variables>):
    if <state variable> == <value>:
        return <base case value>

    if (<state variables>) in memo:
        return memo[(<state variables>)]

    result = <recurrence> # may be more involved

    memo[(<state variables>)] = result

    return result

return dp(<initial arguments for the state variables>)
```

#### Step 6: Produce the Bottom-Up Solution (Tabulation)

1. Initialize an array `dp` that is sized according to the subproblem variables largest values. In particular, whenever you have a base case of the form `if i >= n: return <base case value>`, and somewhere else in your algorithm you have `dp(i + x)`, then your array must have `n + x` rows.
2. For every base case `if <state variable> == <value>: return <base case value>`, explicitly set them in the lookup table `dp` i.e., `dp[<state variable>][...] = <base case value>` or implicitly through the initial values of the lookup table.
3. Write for-loop(s) that will iterate over your state variables, such that the outermost for loop iterates the first state variable in the order of the `dp()` parameters, and such that the `range()` should begin from the first *non-base-case* state variables problems, and end at the final result state variables. For boolean state variables, the range should be from $[0, 2)$. However, for certain matrix problems (e.g., Unique Paths, Minimum Path Sum) where the calculation for a cell `(row, col)` depends only on the results from cells that are above it `(row - 1, col)`, to its left `(row, col - 1)`, or both `(row - 1, col - 1)`, you can iterate through all the rows and columns from top-to-bottom, left-to-right. Within the loop, you use `continue` to skip the base case cells because their values have already been correctly initialized.
4. Under the inner-most for loop, copy-paste *only* the recurrence logic from your memoization function.
5. Change every `dp(<state variable 1>, <state variable 2>, <...>)` function calls and `result` to array accesses `dp[<state variable 1>][<state variable 2][<...>]`. For boolean state variables, represent `True` as `1` and `False` as `0`.

> [!NOTE]
> We can reduce the space complexity of a bottom-up dynamic programming algorithm whenever the recurrence is static (i.e., it doesn't change between inputs and it only cares about a static number of previous states). Simply replace the lookup table with variables to keep track of those previous states — one rolling variable per dp index you look back at. Additionally, add an aggregation `result` variable (initialized to the first valid base case value) if the answer is the maximum over all states rather than just the final state `dp[n - 1]`. This happens when the recurrence can produce values smaller than a previous state, meaning the peak may not be at the end.

## Difference Array

### Use Case

Problem involves intervals of events (i.e., each event happens over a continuous number line of time or position) represented as a 2D array where each element represents an event `[left, right, value]`.

### Examples

#### [LeetCode 1094. Car Pooling](https://leetcode.com/problems/car-pooling/description/)

```python
class Solution:
    def carPooling(self, trips: List[List[int]], capacity: int) -> bool:
        last_end = max(to for _, _, to in trips)
        diff_array = [0] * (last_end + 1)
        for passengers, start, end in trips:
            diff_array[start] += passengers
            diff_array[end] -= passengers

        passenger_count = 0
        for change in diff_array:
            passenger_count += change
            if passenger_count > capacity:
                return False

        return True
```

#### [LeetCode 2021. Brightest Position on Street](https://leetcode.com/problems/brightest-position-on-street/description/)

```python
class Solution:
    def brightestPosition(self, lights: List[List[int]]) -> int:
        diff_array = []
        for position, radius in lights:
            diff_array.append((position - radius, 1))
            diff_array.append((position + radius + 1, -1))

        diff_array.sort()
        result = 0
        curr_brightness = 0
        max_brightness = 0
        for position, change in diff_array:
            curr_brightness += change
            if curr_brightness > max_brightness:
                max_brightness = curr_brightness
                result = position

        return result
```

## Prefix Tree

### Use Case

Prefix matching

### Usage

```python
class TrieNode:
    def __init__(self):
        self.data = None
        self.children = {}

def from(words):
    root = TrieNode()
    for word in words:
        curr_node = root
        for c in word:
            if c not in curr_node.children:
                curr_node.children[c] = TrieNode()
            curr_node = curr_node.children[c]
        # Some logic (you have a full word at `curr_node`).

    return root
```

## Bit Manipulation

### OR Operator

#### Behaviour

If _any_ bit is `1`, then the result will be `1`. Otherwise, the result is `0`.

#### Usage

```python
a | b
```

### AND Operator

#### Behaviour

If _all_ bits are `1`, then the result will be `1`. Otherwise, the result is `0`.

#### Usage

```python
a & b
```

### XOR Operator

#### Behaviour

If the count of `1` is odd, then the result will be `1`. Otherwise, the result is `0`.

#### Usage

```python
a ^ b
```

### Left Shift Operator and Right Shift Operator

#### Behaviour

- Left Shift: Moves all bits in `n` over by `k` places to the left, and fills `0`s from the right. Corresponds to multiplying by `2`.
- Right Shift: Moves all bits in `n` over by `k` places to the right, and fills `0`s from the left. Corresponds to floor division by `2`.

#### Usage

```python
n << k
```

```python
n >> k
```

> [!NOTE]
>  Bitwise operators have low precedence, and therefore happens later in evaluation order, so make sure to use parentheses to clearly define your intended grouping.

### Bit Mask

#### Use Case

- Isolate bit(s) in a bit field.
- A memory efficient set data structure used backtracking or dynamic programming problems.

#### Usage

1. Select the bitwise operator based on your use case.
2. Construct a bit mask.
```python
# set
mask = (1 << k)

# k lower bits
mask = (1 << k) - 1

# range of bits
mask = (1 << 5) | (1 << 3)
```
3. Retrieve the bits via `n <operator> mask`.

## Merge Interval

### Use Case

Interval problems

### Usage

```python
def merge(intervals):
    if not intervals:
        return

    intervals.sort()

    result = [intervals[0]]

    for interval in intervals:
        start, end = interval
        prev_end = result[-1][1]
        if start <= prev_end:
            result[-1][1] = max(prev_end, end)
        else:
            result.append(interval)

    return result
```

## Modular Arithmetic

### Use Case

Problems that involve `x mod m`, (`m` is typically $10^9 + 7$), where:
- Building `x` is expensive (repeated work)
- `x` becomes too large

### Usage

```python
def mod_subdigits(word: str, m: int):
    remainder = 0
    result = []

    for digit_char in word:
        digit = int(digit_char)
        remainder = (remainder * 10 + digit) % m

        if remainder == 0:
            result.append(1)  # divisible
        else:
            result.append(0)

    return result

def mod_power(base: int, exp: int, mod: int) -> int:
    result = 1
    base = base % mod

    while exp > 0:
        if exp % 2 == 1:  # odd exponent
            result = (result * base) % mod
        exp = exp >> 1  # divide by 2
        base = (base * base) % mod

    return result

def mod_product(nums: list, mod: int) -> int:
    result = 1

    for num in nums:
        result = (result * num) % mod

    return result

def mod_sum(nums: list, mod: int) -> int:
    result = 0

    for num in nums:
        result = (result + num) % mod

    return result
```

## Dijkstra's Algorithm

### Use Case

Finding the single source shortest path on graphs with non-negative edge weights.

### Usage

```python
import math
import heapq
from collections import defaultdict

def dijkstras_algorithm(edges, source):
    graph = defaultdict(list)
    for u, v, w in edges:
        graph[u].append((v, w))

    distances = [math.inf] * n
    distances[source] = 0

    pq = [(0, source)]

    while pq:
        distance, node = heapq.heappop(pq)

        if distance > distances[node]:
            continue

        for neighbour, weight in graph[node]:
            new_distance = curr_distance + weight
            if new_distance < distances[neighbour]:
                distances[neighbour] = new_distance
                heapq.heappush(pq, (new_distnace, neighbour))
```

## Topological Sort

### Use Case

Problems involving prerequisites, dependencies, or scheduling.

### Usage (Kahn's Algorithm)

```python
from collections import defaultdict, deque

def kahns_algorithm(n, edges):
    indegrees = [0] * n
    graph = defaultdict(list)
    for u, v in edges:
        graph[u].append(v)
        indegrees[v] += 1

    queue = deque([vertex for vertex, indegree in enumerate(in_degrees) if indegree == 0])
    result = []

    while queue:
        vertex = queue.popleft()
        result.append(node)
        for neighbour in graph[vertex]:
            indegrees[neighbour] -= 1
            if indegrees[neighbour] == 0:
                queue.append(neighbour)

    return result if len(result) == n else [] # if len(result) != n, there's a cycle
```

## Disjoint Set

TO DO

```python
class DisjointSet:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n

    def find(self, x):
        while self.parent[x] != x:
            self.parent[x] = self.parent[self.parent[x]]
			x = self.parent[x]
        return x

    def union(self, x, y):
        x_rep = self.find(x)
        y_rep = self.find(y)

        if x_rep == y_rep:
            return False

        if self.rank[x_rep] < self.rank[y_rep]:
            self.parent[x_rep] = y_rep
        elif self.rank[x_rep] > self.rank[y_rep]:
            self.parent[y_rep] = x_rep
        else:
            self.parent[y_rep] = x_rep
            self.rank[x_rep] += 1

        return True

    def is_connected(self, x, y):
        return self.find(x) == self.find(y)
```

## Minimum Spanning Tree

TO DO

```python
def kruskal(n: int, edges: List[List[int]]) -> int:
    ds = DisjointSet(n)
    mst_weight = 0
    edge_count = 0

    edges.sort(key=lambda x: x[2])

    for u, v, w in edges:
        if not ds.union(u, v):
            continue
        mst_weight += w
        edge_count += 1
        if edge_count == n - 1:
            break

    return mst_weight if edge_count == n - 1 else -1
```

## Radix Sort

TO DO

## QuickSelect (Hoare's Selection Algorithm)

TO DO

```python
import random

def quickselect(array, k):
    def partition(left, right, pivot_idx):
        pivot_val = array[pivot_idx]
        array[pivot_idx], array[right] = array[right], array[pivot_idx]
        write_idx = left
        for i in range(left, right):
            if array[i] < pivot_val:
                array[write_idx], array[i] = array[i], array[write_idx]
                write_idx += 1
        array[right], array[write_idx] = array[write_idx], array[right]
        return store

    low = 0
    high = len(array) - 1

    while low <= high:
        pivot_idx = random.randint(left, right)
        pivot_idx = partition(left, right, pivot_idx)

        if pivot_idx < k:
            low = pivot_idx + 1
        elif pivot_idx > k:
            high = pivot_idx - 1
		else:
            return array[k]
```

## Fenwick Tree (TO DO)

## Segment Tree (TO DO)

## Suffix Tree (TO DO)

## Bellman-Ford or any other non-Dijkstra's shortest path algorithm (TO DO)

## Rolling hash/Rabin Karp/KMP (TO DO)

## Divide and Conquer (TO DO)
