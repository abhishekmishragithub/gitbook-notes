---
description: Bunch of random stuff here
---

# 🦖 Random Stuff

## Why hashing is faster?

Hashing is a fast way to look up or retrieve data because it allows us to access a value directly instead of iterating over an entire data structure.

Here's a simplified explanation:

1. When you hash an object, you get a hash value (an integer). This hash value is used as an index to store the object in an array (the hash table).
2. When you want to look up the object, you hash it again to get the same hash value. You then use this hash value as an index to directly access the object in the array.
3. Because <mark style="color:yellow;">accessing an element in an array by its index is a constant time operation (O(1))</mark>, this makes hash-based lookups very fast.

## Calculating Big O

Calculating the Big O notation for any given program involves understanding how the runtime or space requirements of an algorithm increase as the size of the input data increases. Big O notation provides a high-level approximation, focusing on the dominant factors that affect growth rather than precise measurements. There isn't a single formula or rule that applies universally to all algorithms, but there are general steps and principles you can follow to determine Big O notation:

#### 1. Identify the Basic Operations

Look for the basic operations that contribute significantly to the total running time. These could be key statements in a loop, recursive calls, comparisons in a sorting algorithm, etc.

#### 2. Understand Input Size Relation

Consider how the algorithm's performance scales with the size of the input. The "size" might mean different things (e.g., the number of elements in a list, the number of nodes in a graph).

#### 3. Count Operation Occurrences

For simple algorithms, count how many times the basic operations are executed in terms of the input size, (n). This might involve analyzing loops, recursive calls, and other structures.

#### 4. Simplify Using Big O Notation Rules

Once you've identified how the count of operations grows with (n), you can simplify it to Big O notation by keeping only the highest order term and dropping constants or lower order terms. For example, an algorithm that takes time proportional to (3n^2 + 2n + 1) can be simplified to (O(n^2)).

#### General Rules for Common Patterns

* **Constant Time (O(1))**: The running time does not increase with the size of the input data set.
* **Linear Time (O(n))**: The running time increases linearly with the size of the input data set.
* **Quadratic Time (O(n^2))**: The running time increases quadratically with the size of the input data set. This is common for simple algorithms with nested loops.
* **Logarithmic Time (O(\log n))**: The running time increases logarithmically with the size of the input data set. This is common in algorithms that break the problem in half every iteration, such as binary search.
* **Linearithmic Time (O(n\log n))**: The running time increases linearly and logarithmically with the size of the input data set. This is common in efficient sorting algorithms like mergesort and heapsort.

#### Example

For a simple for-loop iterating over an array of size (n):

```python
for i in range(n):  # Runs n times
    print(i)        # Basic operation, executed n times
```

The running time is (O(n)) because the print statement, which is the basic operation here, is executed (n) times, directly proportional to the size of the input.

#### Special Considerations

* **Amortized Time**: Sometimes, an operation is expensive, but when averaged over many operations, the cost is still low (e.g., dynamic array resizing).
* **Best, Worst, and Average Case**: Big O often refers to the worst-case scenario, but algorithms can also be analyzed for their best and average-case performances.
* **Space Complexity**: Similar principles apply to space complexity, which measures how the amount of memory required grows with input size.

Ultimately, determining Big O notation is as much about understanding the algorithm's structure and behavior as it is about applying mathematical analysis. Practice with different algorithms is key to getting better at accurately determining their Big O complexity.
