---
title: LeetCode 2191. Sort the Jumbled Numbers - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **What the solution does – in plain words**

1. **Turn every number into its “mapped” version.**  
   - Each digit of the original number is replaced by the digit that the *mapping* array tells us to use.  
   - Example: if `mapping[3] = 8` and the number is `123`, the `3` becomes `8`.  
   - After all digits are replaced we get a new integer (e.g. `148`).

2. **Keep the original number and remember its position.**  
   - For every element in `nums` we store a tuple:  
     `(<original number>, <mapped number>, <original index>)`.  
   - The original index is used only to keep the sort **stable** (elements with the same mapped value keep their relative order).

3. **Sort by the mapped numbers.**  
   - The list of tuples is sorted first on the *mapped number* and, if two mapped numbers are equal, on the *original index*.  
   - This guarantees that two numbers that map to the same value appear in the same order as in the input.

4. **Build the final array.**  
   - After sorting, we read off only the original numbers from the sorted tuples and return that array.

---

### Why this works

- **Mapping is a bijection on digits** – every digit 0‑9 is replaced by another single digit, so the mapping is well‑defined for any integer.
- **Stability** – By adding the original index as a secondary sort key we preserve order for ties.  
  In most languages (`sort` in C++/Java, `sorted` in Python) the sorting algorithm is stable, but adding the index guarantees it even if the language implementation changes.
- **Complexity** –  
  - Converting a number of *d* digits costs O(d).  
  - We do this for every of the *n* numbers: O(n·d).  
  - Sorting the list of *n* tuples costs O(n log n).  
  So overall time is O(n log n + n·d), and space is O(n) for the list of tuples.

---

### Quick pseudo‑code

```text
for each i in 0 .. nums.length-1
    s = string representation of nums[i]
    mappedString = ""
    for each character c in s
        mappedString += mapping[ int(c) ]
    mappedVal = int(mappedString)           // leading zeros are discarded
    add tuple (nums[i], mappedVal, i) to list

sort list by (mappedVal, originalIndex)

result = []
for each tuple in sorted list
    append original number to result

return result
```

That’s it – the algorithm is just “transform → pair with index → stable sort → output originals”.