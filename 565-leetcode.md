---
title: LeetCode 565. Array Nesting - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 565. Array Nesting – Java / Python / C++ Solutions + In‑Depth Blog

> **Goal** – Walk you through a clean, O(n) solution to LeetCode 565 “Array Nesting”, and show you a **blog article** that is **SEO‑friendly** so you can land a tech interview.

---

## 1️⃣ Problem Recap (LeetCode 565)

You’re given a permutation `nums` of length `n` (`0 … n‑1`).  
For each index `k`, build a set

```
s[k] = { nums[k], nums[nums[k]], nums[nums[nums[k]]], … }
```

stop when a value would repeat.  
Return the **maximum length** of all such sets.

---

## 2️⃣ Key Insight

Because `nums` is a permutation, following the indices from any starting point will form a **cycle**.  
Thus the problem reduces to: *find the longest cycle in a directed graph where every node has exactly one outgoing edge.*

You can detect cycles **in‑place** by marking visited nodes (e.g. setting them to a sentinel value).  
This gives **O(n)** time and **O(1)** extra space.

---

## 3️⃣ Reference Code (Java / Python / C++)

Below are self‑contained, ready‑to‑paste implementations.  
All three follow the *in‑place marking* strategy.

---

### 3.1 Java

```java
public class Solution {
    public int arrayNesting(int[] nums) {
        int maxLen = 0;
        final int MARK = Integer.MAX_VALUE;   // sentinel for visited

        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == MARK) continue;     // already visited

            int start = i;
            int currLen = 0;

            // walk the cycle, marking visited indices
            while (nums[start] != MARK) {
                int next = nums[start];
                nums[start] = MARK;   // mark visited
                start = next;
                currLen++;
            }
            maxLen = Math.max(maxLen, currLen);
        }
        return maxLen;
    }
}
```

> **Why `int start = i`?**  
> The outer loop starts at each index; `start` follows the chain.  
> Marking with `MARK` guarantees we never revisit a node.

---

### 3.2 Python

```python
class Solution:
    def arrayNesting(self, nums: List[int]) -> int:
        max_len = 0
        MARK = float('inf')   # sentinel for visited

        for i in range(len(nums)):
            if nums[i] == MARK:
                continue

            curr = i
            cnt = 0
            while nums[curr] != MARK:
                nxt = nums[curr]
                nums[curr] = MARK          # mark visited
                curr = nxt
                cnt += 1

            max_len = max(max_len, cnt)

        return max_len
```

> **Tip** – Using `float('inf')` works because all array values are `< n`.

---

### 3.3 C++

```cpp
class Solution {
public:
    int arrayNesting(vector<int>& nums) {
        const int MARK = INT_MAX;   // sentinel
        int maxLen = 0;

        for (int i = 0; i < nums.size(); ++i) {
            if (nums[i] == MARK) continue;   // already visited

            int curr = i;
            int cnt = 0;
            while (nums[curr] != MARK) {
                int nxt = nums[curr];
                nums[curr] = MARK;   // mark visited
                curr = nxt;
                ++cnt;
            }
            maxLen = max(maxLen, cnt);
        }
        return maxLen;
    }
};
```

> **Why `INT_MAX`?**  
> All elements are in `[0, n-1]`, so `INT_MAX` is a safe marker.

---

## 4️⃣ Blog Article – “Array Nesting: The Good, The Bad, and The Ugly”

> **SEO Target Keywords**  
> *LeetCode 565 Array Nesting*, *array nesting solution*, *Java Python C++*, *LeetCode interview questions*, *array nesting explanation*, *graph cycle detection*, *optimal LeetCode solutions*

---

### 4.1 Introduction

> *LeetCode 565 – Array Nesting* is a seemingly simple puzzle that actually teaches you a lot about **graph traversal**, **cycle detection**, and **in‑place algorithms**. In this article, I’ll walk through the problem, the cleanest solution, pitfalls (“bad”), and the less‑obvious edge cases (“ugly”). By the end, you’ll be ready to ace this question in an interview.

---

### 4.2 Problem Statement (Re‑written)

> You’re given an integer array `nums` of length `n` where every value is a unique integer in `[0, n-1]`.  
> For any starting index `k`, define a sequence: `nums[k]`, `nums[nums[k]]`, `nums[nums[nums[k]]]`, …  
> Stop **before** you would repeat a value.  
> Return the maximum length among all such sequences.

> **Examples**

| nums | Output | Explanation |
|------|--------|-------------|
| [5,4,0,3,1,6,2] | 4 | One longest set: `{5,6,2,0}` |
| [0,1,2] | 1 | Every element forms a self‑loop. |

---

### 4.3 The “Good”: In‑Place Cycle Counting

1. **Observation** – The array is a permutation → every node has out‑degree 1 → the graph is a collection of disjoint cycles.
2. **Goal** – Find the longest cycle.
3. **Method** – Walk from every unvisited node, marking visited nodes with a sentinel (`INT_MAX` / `float('inf')`).
4. **Complexity** –  
   *Time*: `O(n)` – each element visited at most once.  
   *Space*: `O(1)` – only a few integer variables (no extra container).

> This approach is *the gold standard* for interviews: it uses minimal extra memory and demonstrates deep understanding of graph cycles.

---

### 4.4 The “Bad”: Naïve HashSet or Recursion

| Approach | Why it’s Bad |
|----------|--------------|
| **HashSet** – For each `k` use a `HashSet` to record visited values. | Extra `O(n)` space per iteration; total `O(n^2)` worst‑case if implemented incorrectly. |
| **Recursive DFS** – Recursively follow `nums` until a repeat. | Stack depth may reach `n`; risk of stack overflow for large inputs (`n ≤ 10⁵`). |

> Even if these pass on LeetCode, they’re *unidiomatic* for interviewers looking for efficient solutions.

---

### 4.5 The “Ugly”: Edge Cases & Subtleties

| Issue | Explanation | Fix |
|-------|-------------|-----|
| **Sentinel collision** – Using a value that could appear in `nums`. | In this problem all values `< n`, so using `INT_MAX` is safe. |
| **Large `n`** – Overflow in recursion depth or marking. | Stick to iterative loops. |
| **Non‑permutation input** – LeetCode guarantees a permutation, but if you were to generalize, you’d need to handle repeated values or missing numbers. | Use a `visited` array or hashset to guard. |

> Handling these subtleties shows maturity and attention to detail.

---

### 4.6 Code Walk‑Through (Java Example)

```java
for (int i = 0; i < nums.length; i++) {
    if (nums[i] == MARK) continue;   // already visited

    int curr = i;
    int cnt = 0;
    while (nums[curr] != MARK) {
        int next = nums[curr];
        nums[curr] = MARK;          // mark as visited
        curr = next;
        cnt++;
    }
    maxLen = Math.max(maxLen, cnt);
}
```

*Each `while` loop follows a distinct cycle, never revisiting any node because we immediately overwrite it with the sentinel.*

---

### 4.7 Optimization Tips

| Tip | Benefit |
|-----|---------|
| **Use `int` sentinel** – `INT_MAX` or `-1` | Avoids allocating a large auxiliary array. |
| **Iterate in place** – Don’t create new lists | Keeps memory footprint minimal. |
| **Profile** – For very large arrays, check that the sentinel update is inlined by the JVM/C++ compiler. | Eliminates hidden overhead. |

---

### 4.8 Frequently Asked Questions (FAQ)

| Question | Answer |
|----------|--------|
| *Can I use a HashSet for marking visited nodes?* | Yes, but it costs `O(n)` extra space. Prefer the in‑place sentinel. |
| *Will the sentinel alter the input for subsequent calls?* | In LeetCode, each test case is isolated. For real projects, copy the array first. |
| *Why not use Floyd’s Tortoise‑Hare?* | It finds a cycle starting at a specific node, but you still need to explore all nodes. In‑place marking is simpler. |

---

### 4.9 Conclusion

> The LeetCode 565 “Array Nesting” problem is a classic illustration of **cycle detection** in a functional graph.  
> By marking visited nodes *in place*, we achieve **O(n) time** and **O(1) space**, which is the most interview‑friendly solution.  
> Avoid the naive HashSet or recursive approaches; they’re “bad” for performance and stack safety.  
> Finally, remember the edge cases (sentinel collisions, large inputs) – handling them will impress hiring managers.

> **Next Steps**  
> 1. Practice the same pattern on problems like “Number of Islands” or “Linked List Cycle”.  
> 2. Share your solution on GitHub and write a short blog (like this one) to showcase your thought process.  
> 3. Prepare to explain the approach in a 5‑minute interview pitch.  

Happy coding!

---

## 5️⃣ Quick Reference – Search‑Engine Friendly Meta

| Field | Content |
|-------|---------|
| **Title** | Array Nesting – LeetCode 565 Solution in Java, Python, C++ |
| **Description** | Learn the optimal in‑place algorithm for LeetCode 565 “Array Nesting”. Full Java, Python, and C++ implementations + interview‑ready blog post. |
| **Keywords** | LeetCode 565, array nesting, graph cycle detection, Java solution, Python solution, C++ solution, interview coding, O(n) algorithm, optimal LeetCode problems |
| **URL Slug** | array-nesting-leetcode-565-solution |

> Including these meta tags will help recruiters searching for “Array Nesting solution” find your content.

--- 

> **Good luck on your coding interviews!**