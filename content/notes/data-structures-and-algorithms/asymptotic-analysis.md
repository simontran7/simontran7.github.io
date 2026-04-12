# Asymptotic Analysis

## Bachmann-Landau Notation

### Definition (big O)

Let $f(n)$ and $g(n)$ be functions mapping positive integers to positive real numbers. We say that $f(n)$ is $O(g(n))$ if there is a real constant $c \gt 0$ and an integer constant $n_0 \ge 1$ such that:
$$
f(n) \leq c \cdot g(n), \text{ for } n \geq n_0
$$

### Simplification Rules

1. Drop constant factors

$$
O(c \cdot f(n)) \rightarrow O(f(n))
$$

3. Drop lower-order terms

$$
O(f(n) + g(n)) \rightarrow O(max(f, g))
$$

### Law of Addition

$$
O(f(n)) + O(g(n)) \rightarrow O(f(n) + g(n))
$$

### Law of Multiplication

$$
O(f(n)) \cdot O(g(n)) \rightarrow O(f(n) \cdot g(n))
$$

### Common Orders of Growth

| Growth type  | Function       |
| ------------ | ------------   |
| Constant     | $1$        |
| Logarithmic  | $\log n$   |
| Linear       | $n$        |
| Linearithmic | $n \log n$ |
| Quadratic    | $n^2$      |
| Cubic        | $n^3$      |
| Exponential  | $2^n$      |
| Factorial    | $n!$       |

## Worst Case Time Complexity

### General steps

1. Identify the input parameters that influence running time and assign variables (commonly $n$, $m$, $k$, etc.).
2. Break the algorithm into significant parts
3. Assign a cost to each part according to the rules below
4. Combine the costs of sequential parts using the law of addition, and nested parts using the law of multiplication
5. Simplify using the simplification rules

### Primitive Operations

The following primitive operations are worst-case $O(1)$:
- Declaring a variable
- Assigning a value to a variable
- Following an object reference
- Performing an arithmetic operation
- Comparing two numbers
- Calling/Returning from a function/method

### Loops

The time complexity of a single loop is expressed as the sum of the complexities of each iteration of the loop.

The time complexity of nested loops is found by expression the innermost loop as a summation, and proceeding outwards, which will give nested summations.

The following are useful properties when analyzing loop time complexities.

#### Property (big O and summation)

$$
\sum_{i \in I} O(f(i)) = O\left(\sum_{i \in I} f(i)\right)
$$

#### Property (summation of a constant)

$$
\sum_{i = m}^{n} ca_i = c \sum_{i = m}^{n} a_i
$$

#### Property (addition and subtraction of summations)

$$
\sum_{i = m}^{n} (a_i \pm b_i) = \sum_{i = m}^{n} a_i \pm \sum_{i = m}^{n} b_i
$$

#### Formula (summation of a $1$)

$$
\sum_{i = m}^{n} 1 = n - m + 1
$$

#### Formula (summation of arithmetic series)

$$
\sum_{i = 1}^{n} i = \frac{n(n + 1)}{2}
$$

#### Formula (summation of quadratic series)

$$
\sum_{i = 1}^{n} i^2 = \frac{n(n + 1)(2n + 1)}{6}
$$

#### Formula (summation of cubic series)

$$
\sum_{i = 1}^{n} i^3 = \left(\frac{n(n + 1)}{2}\right)^2
$$

#### Formula (summation of $i^k$ series)

$$
\sum_{i = 1}^{n} i^k \approx \frac{1}{k + 1}n^{k + 1}
$$

#### Formula (summation of geometric series)

$$
\sum_{i = 1}^{n}ar^{i - 1} = a\left(\frac{r^n - 1}{r - 1}\right) = a\left(\frac{1 - r^n}{1 - r}\right), \text{ where } r > 0 \text{ and } r \neq 1
$$

#### Formula (summation of harmonic series)

$$
\sum_{i = 1}^{n} \frac{1}{i} \approx \ln n + \gamma, \text{ where } \gamma \approx 0.5772 \dots
$$

#### Formula (summation of $\log_{2}$ series)

$$
\sum_{i = 1}^{n} \log_2 i = O\left(\sum_{i = 1}^{n} \log_2 n\right) = O(n\log_2 n)
$$

### Binary Search Algorithms

$O(\log⁡ n)$, where $n$ is the size of your initial search space.

### Binary Tree Algorithms

$$
O(n \cdot C_{node})
$$

- $C_{node}$: cost of processing a single node

### Graph Traversal Algorithms

$$
O(S \cdot C_{state} + T \cdot C_{transition})
$$

- $S$: number of reachable states (product of the ranges of each state variable)
- $C_{state}$: cost of processing a single state
- $T$: number of transitions (the transitions per state times S)
- $C_{transition}$: cost of processing a single transition

### Backtracking Algorithms

$$
O(S \cdot C)
$$

- $S$: number of possible states explored (typically $b^d$, $\frac{n!}{(n - k)!}$, or $n \choose k$)
- $C$: cost of processing a single state

### Dynamic Programming Algorithms

$$
O(P \cdot C)
$$

- $P$: number of subproblems (product of the ranges of each subproblem variable)
- $C$: cost of processing a single subproblem

### Shortest Path Algorithms

##### Dijkstra's Algorithm

$$
O((V + E) \log V)
$$

### Kahn's Algorithm (for Topological Sort)

$$
O(V + E)
$$

## Worst-case Space Complexity

### Common Data Structures

- Variable: $O(1)$
- Array: $O(n)$
- $m \times n$ Matrix: $O(m \cdot n)$
- Node-Pointer based container: $O(n)$
- Adjacency List : $O(V + E)$

### Recursive Function

$$
O(D \cdot F)
$$
- $D$: maximum recursion depth
- $F$: memory call per stack frame

> [!NOTE]
> The algorithm's input is typically excluded from the total space complexity cost.

## Input size and Time Complexity

| Range of $n$         | Likely Time Complexity                                           | Likely Approach                                                                                                     |
|--------------------|------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| $n \le 10$         | Factorial (e.g., $O(n^2 \cdot n!)$) or exponential base $> 2$ (e.g., $O(4^n)$) | Backtracking or recursive brute-force                                                                                 |
| $10 < n \le 20$    | $O(2^n)$                                                         | Backtracking or recursive brute-force (specifically "take it or leave it")                                                         |
| $20 < n \le 10^2$  | $O(n^3)$                                                         | Iterative brute-force with nested loops; possibly optimize using hash maps or heap/priority queues                     |
| $10^2 < n \le 10^3$| $O(n^2)$, assuming constant factors are moderate                 | Iterative brute-force with nested loops                                                                               |
| $10^3 < n < 10^5$  | Slowest $O(n \log n)$, ideally $O(n)$ with constant factor $\le 40$ | HashMap, two pointers, monotonic stack, binary search, or HeapPriorityQueue                                        |
| $10^5 < n < 10^6$  | Slowest $O(n \log n)$ (small constant), ideally $O(n)$          | Usually heavy use of HashMaps                                                                                       |
| $n > 10^6$         | $O(\log n)$, $O(\sqrt n)$ (for advanced problems), or $O(1)$     | Aggressively reduce search space (e.g., binary search) or clever use of HashMaps or math tricks |
