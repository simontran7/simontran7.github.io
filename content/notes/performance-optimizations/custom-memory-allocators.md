# Custom Memory Allocators

## Background

A custom memory allocator is a memory allocator tuned for our program's specific constraints, which allows us to achieve better performance. A custom allocator tries to keep the number of memory allocations low by first allocating large chunks of memory, and then managing it internally to provide smaller allocations. It also usually involves abstract data types and data structures (e.g., linked lists, or binary search trees, stacks, etc.) to keep track of allocated and/or free portions of memory.

## Linear/Arena/Bump Allocators

A **linear allocator (a.k.a arena allocator, or bump allocator)** is

## Stack Allocators

## Pool Allocators

## Free List Allocators

### Explicit Free List

### Blocks sorted by size

### Segregated Free List

## Buddy Allocators

https://cs341.cs.illinois.edu/coursebook/Malloc

https://www.reddit.com/r/ProgrammingLanguages/comments/1dfo6ez/comment/l8lvmlt/

https://github.com/mtrebi/memory-allocators#linear-allocator

https://www.gingerbill.org/article/2019/02/01/memory-allocation-strategies-001/

For reference, a typical modern malloc implementation should allocate small blocks (~< MB) in around 100 cycles (20 ns at 5GHz)

https://www.reddit.com/r/ProgrammingLanguages/comments/1hz0bu8/comment/m6quq7e/