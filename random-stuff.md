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

