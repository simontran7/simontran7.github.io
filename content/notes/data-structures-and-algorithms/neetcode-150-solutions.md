# [NeetCode 150](https://neetcode.io/roadmap) Solutions

## Arrays and Hashing Problems

### [Contains Duplicate](https://leetcode.com/problems/contains-duplicate/description/)

#### Solution

```python
class Solution:
    def containsDuplicate(self, nums: List[int]) -> bool:
        seen = set()

        for num in nums:
            if num in seen:
                return True
            seen.add(num)

        return False
```

#### Complexity Analysis

Let $n$ be the length of `nums`. Then:
- Time Complexity: worst-case $O(n)$
- Space Complexity: worst-case $O(n)$

### [Valid Anagram](https://leetcode.com/problems/valid-anagram/description/)

#### Solution

```python
from collections import defaultdict

class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        if len(s) != len(t):
            return False

        s_chars = defaultdict(int)
        t_chars = defaultdict(int)

        for i in range(len(s)):
            s_chars[s[i]] += 1
            t_chars[t[i]] += 1

        return s_chars == t_chars
```

#### Complexity Analysis

Let $n$ be the length of `s` and `t` (they should be equal). Then:
- Time Complexity: worst-case $O(n)$
- Space Complexity: worst-case $O(1)$

### [Two Sum](https://leetcode.com/problems/two-sum/description/)

#### Solution

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        seen = dict()

        for i, n in enumerate(nums):
            complement = target - n
            if complement in seen:
                return [seen[complement], i]
            seen[n] = i

        return []
```

#### Complexity Analysis

Let $n$ be the length of `nums`. Then:
- Time Complexity: worst-case $O(n)$
- Space Complexity: worst-case $O(n)$

### Group Anagrams

#### Solution

```python
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        result = defaultdict(list)

        for s in strs:
            char_count = [0] * 26
            for c in s:
                char_count[ord(c) - ord('a')] += 1
            result[tuple(char_count)].append(s)

        return list(result.values())
```

#### Complexity Analysis

Let $n$ be the length of `strs`, and $k$ the length of the longest possible string in `strs`. Then:
- Time Complexity: worst-case $O(nk)$
- Space Complexity: worst-case $O(nk)$

### [Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/description/)

#### Solution

```python
from collections import Counter
import random

class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        def partition(left, right, pivot_idx):
            # save the frequency of the pivot
            pivot_freq = frequency[unique[pivot_idx]]

            # move the pivot to the end of the list
            unique[pivot_idx], unique[right] = unique[right], unique[pivot_idx]
            store_idx = left

            # partition the list
            for i in range(left, right):
                if frequency[unique[i]] < pivot_freq:
                    unique[store_idx], unique[i] = unique[i], unique[store_idx]
                    store_idx += 1

            # place the pivot at the insertion index `store_idx`
            unique[right], unique[store_idx] = unique[store_idx], unique[right]

            return store_idx

        def quickselect(left, right, k_smallest_idx):
            if left == right:
                return

            # pick an random pivot from [left, right)
            pivot_idx = random.randint(left, right)

            # partition the list such that:
            #   - any elements to the left of the pivot has a lower frequency
            #   - any elements to the right of the pivot has an equal or higher frequency
            pivot_idx = partition(left, right, pivot_idx)

            if pivot_idx == k_smallest_idx:
                return
            elif k_smallest_idx < pivot_idx:
                quickselect(left, pivot_idx - 1, k_smallest_idx)
            else:
                quickselect(pivot_idx + 1, right, k_smallest_idx)

        # build an unordered map where the key is an element, and the value is its frequency
        frequency = Counter(nums)
        unique = list(frequency.keys())

        # run quickselect on the list
        n = len(unique)
        quickselect(0, n - 1, n - k)

        # return a slice of the list starting at the kth most frequent element
        return unique[n - k:]
```

#### Complexity Analysis

Let $n$ be the length of `nums`. Then:
- Time Complexity: average-case $O(n)$, worst-case $O(n^2)$
- Space Complexity: worst-case $O(nk)$

### [Encode and Decode Strings](https://leetcode.com/problems/encode-and-decode-strings/description/)

#### Solution

```python
class Codec:
    def encode(self, strs: List[str]) -> str:
        """Encodes a list of strings to a single string.
        """
        result = []

        for s in strs:
            result.append(str(len(s)) + '#' + s)

        return ''.join(result)


    def decode(self, s: str) -> List[str]:
        """Decodes a single string to a list of strings.
        """
        result = []
        i = 0

        while i < len(s):
            delim_idx = s.find('#', i)
            str_len = int(s[i:delim_idx])
            str_start = delim_idx + 1
            result.append(s[str_start:str_start + str_len])
            i = str_start + str_len

        return result


# Your Codec object will be instantiated and called as such:
# codec = Codec()
# codec.decode(codec.encode(strs))
```

#### Complexity Analysis

Let $m$ be the total characters across all original strings, and $n$ be the length of `strs`. Then:
- Time Complexity: worst-case $O(m)$
- Space Complexity: worst-case $O(m + n)$

### [Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/)

#### Solution

```python
class Solution:
    def productExceptSelf(self, nums: List[int]) -> List[int]:
        n = len(nums)
        result = [1] * n

        prefix = 1
        for i in range(n):
            result[i] *= prefix
            prefix *= nums[i]

        postfix = 1
        for i in range(n - 1, -1, -1):
            result[i] *= postfix
            postfix *= nums[i]

        return result
```

#### Complexity Analysis

Let $n$ be the length of `nums`. Then:
- Time Complexity: worst-case $O(n)$
- Space Complexity: worst-case $O(1)$ (excluding the output list)

### [Valid Sudoku](https://leetcode.com/problems/valid-sudoku/description/)

#### Solution

```python
class Solution:
    def isValidSudoku(self, board: List[List[str]]) -> bool:
        rows = defaultdict(set)
        cols = defaultdict(set)
        boxes = defaultdict(set)

        for row in range(9):
            for col in range(9):
                if board[row][col] == '.':
                    continue
                if board[row][col] in rows[row] or board[row][col] in cols[col] or board[row][col] in boxes[(row // 3, col // 3)]:
                    return False

                rows[row].add(board[row][col])
                cols[col].add(board[row][col])
                boxes[(row // 3, col // 3)].add(board[row][col])

        return True
```

#### Complexity Analysis

Let $n$ be the length of `board`. Then:
- Time Complexity: worst-case $O(n^2)$
- Space Complexity: worst-case $O(n^2)$

### [Longest Consecutive Sequence](https://leetcode.com/problems/longest-consecutive-sequence/description/)

#### Solution

```python
class Solution:
    def longestConsecutive(self, nums: List[int]) -> int:
        unique_nums = set(nums)
        result = 0

        for num in unique_nums:
            if num - 1 in unique_nums:
                continue

            curr_len = 1
            while num + curr_len in unique_nums:
                curr_len += 1

            result = max(result, curr_len)

        return result
```

#### Complexity Analysis

Let $n$ be the length of `nums`. Then:
- Time Complexity: worst-case $O(n)$
- Space Complexity: worst-case $O(n)$

## Two Pointers Problems

### [Valid Palindrome](https://leetcode.com/problems/valid-palindrome/description/)

#### Solution

```python
class Solution:
    def isPalindrome(self, s: str) -> bool:
        left = 0
        right = len(s) - 1

        while left < right:
            while left < right and not s[left].isalnum():
                left += 1
            while left < right and not s[right].isalnum():
                right -= 1

            if s[left].lower() != s[right].lower():
                return False

            left += 1
            right -= 1

        return True
```

#### Complexity Analysis

Let $n$ be the length of `s`. Then:
- Time Complexity: worst-case $O(n)$
- Space Complexity: worst-case $O(1)$

### [Two Sum II - Input Array is Sorted](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)

#### Solution

```python
class Solution:
    def twoSum(self, numbers: List[int], target: int) -> List[int]:
        left = 0
        right = len(numbers) - 1

        while left < right:
            curr_sum = numbers[left] + numbers[right]
            if curr_sum < target:
                left += 1
            elif curr_sum > target:
                right -= 1
            else:
                return [left + 1, right + 1]

        return [-1, -1]
```

#### Complexity Analysis

Let $n$ be the length of `numbers`. Then:
- Time Complexity: worst-case $O(n)$
- Space Complexity: worst-case $O(1)$

### [3Sum](https://leetcode.com/problems/3sum/description/)

#### Solution

```python
class Solution:
    def threeSum(self, nums: List[int]) -> List[List[int]]:
        nums.sort()

        result = []

        for i in range(len(nums)):
            if nums[i] > 0:
                break
            if i != 0 and nums[i] == nums[i - 1]:
                continue

            left = i + 1
            right = len(nums) - 1
            while left < right:
                triplet_sum = nums[i] + nums[left] + nums[right]
                if triplet_sum < 0:
                    left += 1
                elif triplet_sum > 0:
                    right -= 1
                else:
                    result.append([nums[i], nums[left], nums[right]])
                    left += 1
                    right -= 1
                    while left < right and nums[left] == nums[left - 1]:
                        left += 1

        return result
```

#### Complexity Analysis

Let $n$ be the length of `nums`. Then:
- Time Complexity: worst-case $O(n^2)$
- Space Complexity: worst-case $O(n)$

### [Container with Most Water](https://leetcode.com/problems/container-with-most-water/)

#### Solution

```python
class Solution:
    def maxArea(self, height: List[int]) -> int:
        result = 0
        left = 0
        right = len(height) - 1

        while left < right:
            area = min(height[left], height[right]) * (right - left)
            result = max(result, area)
            if height[left] <= height[right]:
                left += 1
            else:
                right -= 1

        return result
```

#### Complexity Analysis

Let $n$ be the length of `height`. Then:
- Time Complexity: worst-case $O(n)$
- Space Complexity: worst-case $O(1)$

### [Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/description/)

#### Solution

```python
class Solution:
    def trap(self, height: List[int]) -> int:
        left = 0
        right = len(height) - 1
        left_max = 0
        right_max = 0
        result = 0

        while left < right:
            if height[left] < height[right]:
                left_max = max(left_max, height[left])
                result += left_max - height[left]
                left += 1
            else:
                right_max = max(right_max, height[right])
                result += right_max - height[right]
                right -= 1

        return result
```

#### Complexity Analysis

Let $n$ be the length of `height`. Then:
- Time Complexity: worst-case $O(n)$
- Space Complexity: worst-case $O(1)$

## Stack Problems

### [Valid Parentheses](https://leetcode.com/problems/valid-parentheses/description/)

#### Solution

```python
class Solution:
    def isValid(self, s: str) -> bool:
        bracket_pairs = {
            ')': '(',
            '}': '{',
            ']': '[',
        }
        stack = []

        for c in s:
            if c in bracket_pairs:
                if len(stack) == 0 or stack[-1] != bracket_pairs[c]:
                    return False
                stack.pop()
            else:
                stack.append(c)

        return len(stack) == 0
```

#### Complexity Analysis

Let $n$ be the length of `s`. Then:
- Time Complexity: worst-case $O(n)$
- Space Complexity: worst-case $O(n)$

### [Min Stack](https://leetcode.com/problems/min-stack/description/)

#### Solution

```python
class MinStack:

    def __init__(self):
        self.data = []

    def push(self, val: int) -> None:
        if len(self.data) == 0:
            self.data.append((val, val))
        else:
            self.data.append((val, min(val, self.data[-1][1])))

    def pop(self) -> None:
        self.data.pop()

    def top(self) -> int:
        return self.data[-1][0]

    def getMin(self) -> int:
        return self.data[-1][1]

# Your MinStack object will be instantiated and called as such:
# obj = MinStack()
# obj.push(val)
# obj.pop()
# param_3 = obj.top()
# param_4 = obj.getMin()
```

#### Complexity Analysis

Let $n$ be the number of operations:
- Time Complexity:
    - `push(...)`: worst-case $O(1)$
    - `pop(...)`: worst-case $O(1)$
    - `top(...)`: worst-case $O(1)$
    - `getMin(...)`: worst-case $O(1)$
- Space Complexity: worst-case $O(n)$

### [Evaluate Reverse Polish Notation](https://leetcode.com/problems/evaluate-reverse-polish-notation/)

#### Solution

```python
class Solution:
    def evalRPN(self, tokens: List[str]) -> int:
        stack = []

        for token in tokens:
            match token:
                case "+":
                    y = stack.pop()
                    x = stack.pop()
                    stack.append(x + y)
                case "-":
                    y = stack.pop()
                    x = stack.pop()
                    stack.append(x - y)
                case "*":
                    y = stack.pop()
                    x = stack.pop()
                    stack.append(x * y)
                case "/":
                    y = stack.pop()
                    x = stack.pop()
                    stack.append(math.trunc(x / y))
                case _:
                    stack.append(int(token))

        return stack.pop()
```

#### Complexity Analysis

Let $n$ be the length of `tokens`. Then:
- Time Complexity: worst-case $O(n)$
- Space Complexity: worst-case $O(n)$

### [Daily Temperatures](https://leetcode.com/problems/daily-temperatures/)

#### Solution

```python
class Solution:
    def dailyTemperatures(self, temperatures: List[int]) -> List[int]:
        n = len(temperatures)
        result = [0] * n
        mono_stack = []

        for i in range(n):
            while mono_stack and temperatures[mono_stack[-1]] < temperatures[i]:
                top = mono_stack.pop()
                result[top] = i - top
            mono_stack.append(i)

        return result
```

#### Complexity Analysis

Let $n$ be the length of `temperatures`. Then:
- Time Complexity: worst-case $O(n)$
- Space Complexity: worst-case $O(n)$

### [Car Fleet](https://leetcode.com/problems/car-fleet/description/)

#### Solution

```python
class Solution:
    def carFleet(self, target: int, position: List[int], speed: List[int]) -> int:
        cars = sorted(zip(position, speed), reverse=True)
        prev_time = 0
        result = 0

        for position, speed in cars:
            time = (target - position) / speed
            if time > prev_time:
                result += 1
                prev_time = time

        return result
```

#### Complexity Analysis

Let $n$ be the length of `cars`. Then:
- Time Complexity: worst-case $O(n \log n)$
- Space Complexity: worst-case $O(n)$

### [Largest Rectangle in Histogram](https://leetcode.com/problems/largest-rectangle-in-histogram/description/)

#### Solution

```python
class Solution:
    def largestRectangleArea(self, heights: List[int]) -> int:
        stack = []
        result = 0

        for i in range(len(heights)):
            while stack and heights[stack[-1]] >= heights[i]:
                current_height = heights[stack.pop()]
                left_bound = stack[-1] + 1 if stack else 0
                right_bound = i - 1
                current_width = right_bound - left_bound + 1
                result = max(result, current_height * current_width)
            stack.append(i)

        while stack:
            current_height = heights[stack.pop()]
            left_bound = stack[-1] + 1 if stack else 0
            right_bound = len(heights) - 1
            current_width = right_bound - left_bound + 1
            result = max(result, current_height * current_width)

        return result
```

#### Complexity Analysis

Let $n$ be the length of `heights`. Then:
- Time Complexity: worst-case $O(n)$
- Space Complexity: worst-case $O(n)$

## Binary Search Problems

### [Binary Search](https://leetcode.com/problems/binary-search/)

#### Solution

```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        low = 0
        high = len(nums) - 1

        while low <= high:
            mid = (low + high) // 2
            if nums[mid] < target:
                low = mid + 1
            elif nums[mid] > target:
                high = mid - 1
            else:
                return mid

        return -1
```

#### Complexity Analysis

Let $n$ be the length of `nums`. Then:
- Time Complexity: worst-case $O(\log n)$
- Space Complexity: worst-case $O(1)$

### [Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/)

#### Solution

```python
class Solution:
    def searchMatrix(self, matrix: List[List[int]], target: int) -> bool:
        ROWS = len(matrix)
        COLS = len(matrix[0])
        low = 0
        high = ROWS * COLS - 1

        while low <= high:
            mid = (low + high) // 2
            row = mid // COLS
            col = mid % COLS
            if matrix[row][col] < target:
                low = mid + 1
            elif matrix[row][col] > target:
                high = mid - 1
            else:
                return True

        return False
```

#### Complexity Analysis

Let $m$ be the length of `matrix`, and $n$ be the length of `matrix[i]` for $i \in 1, 2, \dots, m$. Then:
- Time Complexity: worst-case $O(\log (m \times n))$
- Space Complexity: worst-case $O(1)$

### [Koko Eating Bananas](https://leetcode.com/problems/koko-eating-bananas/description/)

#### Solution

```python
class Solution:
    def minEatingSpeed(self, piles: List[int], h: int) -> int:
        def is_valid(k):
            hours = 0
            for pile in piles:
                hours += math.ceil(pile / k)
            return hours <= h

        low = 1
        high = max(piles)

        while low <= high:
            mid = (low + high) // 2
            if is_valid(mid):
                high = mid - 1
            else:
                low = mid + 1

        return low
```

#### Complexity Analysis

Let $m$ be the largest possible $k$, and $n$ be the length of `piles`. Then:
- Time complexity: worst-case $O(n \log m)$
- Space complexity: worst-case $O(1)$

### [Find Minimum in Rotated Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/description/)

#### Solution

```python
class Solution:
    def findMin(self, nums: List[int]) -> int:
        low = 0
        high = len(nums) - 1

        while low < high:
            mid = (low + high) // 2
            if nums[mid] > nums[high]:
                low = mid + 1
            else:
                high = mid

        return nums[low]
```

#### Complexity Analysis

Let $n$ be the length of `nums`. Then:
- Time Complexity: worst-case $O(\log n)$
- Space Complexity: worst-case $O(1)$

### [Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/description/)

#### Solution

```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        low = 0
        high = len(nums) - 1

        while low <= high:
            mid = (low + high) // 2

            if nums[mid] == target:
                return mid

            if nums[low] <= nums[mid]:
                if nums[low] <= target and target < nums[mid]:
                    high = mid - 1
                else:
                    low = mid + 1
            else:
                if nums[mid] < target and target <= nums[high]:
                    low = mid + 1
                else:
                    high = mid - 1

        return -1
```

#### Complexity Analysis

Let $n$ be the length of `nums`. Then:
- Time Complexity: worst-case $O(\log n)$
- Space Complexity: worst-case $O(1)$

### [Time Based Key-Value Store](https://leetcode.com/problems/time-based-key-value-store/description/)

#### Solution

```python
from collections import defaultdict

class TimeMap:

    def __init__(self):
        self.data = defaultdict(list)

    def set(self, key: str, value: str, timestamp: int) -> None:
        self.data[key].append((value, timestamp))

    def get(self, key: str, timestamp: int) -> str:
        values = self.data[key]
        low = 0
        high = len(values)

        while low < high:
            mid = (low + high) // 2
            if values[mid][1] <= timestamp:
                low = mid + 1
            else:
                high = mid
        
        return values[low - 1][0] if low > 0 else ""


# Your TimeMap object will be instantiated and called as such:
# obj = TimeMap()
# obj.set(key,value,timestamp)
# param_2 = obj.get(key,timestamp)
```

#### Complexity Analysis

Let $m$ be the number of `set` function calls, $n$ the number of `get` function calls, and `l` be the average length of key and value strings. Then:
- Time Complexity:
    - `set(...)`: worst-case $O(m \cdot l)$
    - `get(...)`: worst-case $O(n \cdot (l \cdot \log m))$
- Space Complexity: worst-case $O(m \cdot l)$
