---
title: LeetCode 1533. Find the Index of the Large Integer - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 1533 – Find the Index of the Large Integer  
**LeetCode** | **Medium** | **ArrayReader API**  

> **Problem:**  
> You are given an integer array `arr` in which *all* elements are equal **except** one element that is strictly larger.  
> The array is **not** exposed directly; you can only call  
> ```text
> reader.compareSub(l, r, x, y)  // compares sums of arr[l..r] and arr[x..y]
> reader.length()                 // returns the array size
> ```  
> Each call to `compareSub` is O(1) and you may only make **20 calls**.  
> Return the index of the single largest element.  

---

## Why This Problem Rocks (and Why It’s a Good Interview Question)

| ✅  | 🎯  |
|-----|-----|
| *Low‑overhead* – 20 calls is a hard but realistic limit that forces a **log‑N** solution. | *Real‑world vibe* – it simulates accessing data through a remote API, a scenario many engineers face. |
| *Classic binary search twist* – you need to decide whether to include the middle element or not. | *Flexibility* – can be solved in Java, Python, C++, etc. |
| *Clear constraints* – all numbers are equal except one, so you can rely on sum comparison. | *Test your understanding of off‑by‑one bugs* – the “odd vs. even length” pitfall is the sweet spot. |

---

## The Intuition

If we split the array into two halves and compare their sums:

* If the sums are equal → the special element is exactly at the middle index.
* If the left sum is larger → the special element must lie in the left half.
* If the right sum is larger → it must lie in the right half.

The only subtlety: **whether to include the middle element in the left or right half**.  
Because all other elements are identical, the element at the middle is the only one that can tip the scale when the subarray lengths are *even*.

---

## Algorithm (Binary Search with Odd/Even Handling)

```
l = 0
r = reader.length() – 1

while l < r:
    m = (l + r) // 2           // middle index

    // When the segment length (r - l + 1) is odd,
    // the middle element belongs to the left side
    // for the purpose of comparison.
    if (r - l + 1) % 2 == 1:
        // Compare [l..m] with [m+1..r]
        res = reader.compareSub(l, m, m + 1, r)
    else:
        // Compare [l..m] with [m..r]
        res = reader.compareSub(l, m, m, r)

    if res == 0:
        return m                // found the special index
    elif res == 1:              // left side is heavier
        r = m
    else:                       // right side is heavier
        l = m + 1

return l                        // l == r is the answer
```

* **Time complexity**: `O(log n)` – at most 20 calls for `n ≤ 5·10⁵`.  
* **Space complexity**: `O(1)` – only a handful of variables.

---

## Code Implementations

> *All solutions assume the existence of the `ArrayReader` interface as defined by LeetCode.*

### 1️⃣ Java

```java
public class Solution {
    public int getIndex(ArrayReader reader) {
        int l = 0;
        int r = reader.length() - 1;

        while (l < r) {
            int m = l + (r - l) / 2;

            int res;
            // (r - l + 1) is the length of the current segment
            if ((r - l + 1) % 2 == 1) {
                res = reader.compareSub(l, m, m + 1, r);
            } else {
                res = reader.compareSub(l, m, m, r);
            }

            if (res == 0) {
                return m;
            } else if (res == 1) {
                r = m;          // special element on the left
            } else {
                l = m + 1;      // special element on the right
            }
        }
        return l;   // l == r
    }
}
```

### 2️⃣ Python (3.x)

```python
class Solution:
    def getIndex(self, reader: 'ArrayReader') -> int:
        l, r = 0, reader.length() - 1

        while l < r:
            m = (l + r) // 2

            if (r - l + 1) % 2 == 1:
                # odd length -> left side includes mid
                res = reader.compareSub(l, m, m + 1, r)
            else:
                # even length -> right side includes mid
                res = reader.compareSub(l, m, m, r)

            if res == 0:
                return m
            elif res == 1:
                r = m
            else:
                l = m + 1

        return l
```

### 3️⃣ C++ (GNU++17)

```cpp
class Solution {
public:
    int getIndex(ArrayReader &reader) {
        int l = 0, r = reader.length() - 1;

        while (l < r) {
            int m = l + (r - l) / 2;

            int res;
            if ((r - l + 1) % 2 == 1) {      // odd segment
                res = reader.compareSub(l, m, m + 1, r);
            } else {                        // even segment
                res = reader.compareSub(l, m, m, r);
            }

            if (res == 0) return m;
            else if (res == 1) r = m;
            else l = m + 1;
        }
        return l;
    }
};
```

---

## The “Good, Bad, and Ugly”

| ✅  Good | ❌  Bad | 🤢  Ugly |
|----------|--------|---------|
| **O(log n)** solution meets the 20‑call limit. | Failing to handle the odd/even split leads to an **off‑by‑one error**. | **Brute‑force** comparison of every pair would exceed the call limit and time budget. |
| Clear, short code that easily passes tests. | Some contestants write a recursive solution that leaks the stack or mis‑counts calls. | Writing a custom `ArrayReader` mock for local testing can be cumbersome but is essential for offline debugging. |

---

## Variations & Extensions

1. **Two larger numbers** – A second binary search on the remaining segment after finding the first large number will locate the second.  
2. **One large and one small number** – Split the array into three parts and compare two halves; the difference tells you whether the special element is large or small.  
3. **No API, raw array** – The same logic reduces to a classic binary search where you compare element values directly.

---

## Take‑away for Your Job Hunt

* **Showcase binary search mastery** – Interviewers love seeing a clean O(log n) solution to a seemingly complex API problem.  
* **Explain your thought process** – Mention the odd/even split; talk about how you handle the API’s abstract nature.  
* **Highlight constraints** – Emphasize how your solution stays within the 20‑call limit.  
* **Mention testability** – Share how you mock `ArrayReader` for unit tests – a practical tip for coding interviews.

---

## SEO‑Friendly Blog Title

> **“LeetCode 1533 – Find the Index of the Large Integer: A Binary Search Masterclass (Java/Python/C++)”**

**Meta Description:**  
Learn the O(log n) solution to LeetCode 1533. Understand the odd/even split trick, read complete Java, Python, and C++ code, and get interview‑ready with this algorithm guide.

---

Happy coding – and may your next interview be as smooth as this binary search!