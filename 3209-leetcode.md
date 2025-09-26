---
title: LeetCode 3209. Number of Subarrays With AND Value of K - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🎯 LeetCode 3209 – Number of Subarrays With AND Value of K  
> **Goal:** Count all contiguous sub‑arrays whose bitwise AND equals *k*  
> **Languages:** Java | Python | C++  
> **Target audience:** Front‑end/Back‑end engineers, interviewers, recruiters  

---

## Table of Contents  
1. [Problem Statement](#problem-statement)  
2. [Why This Problem Rocks](#why-this-problem-rocks)  
3. [High‑Level Solution](#high‑level-solution)  
4. [Complexity Analysis](#complexity-analysis)  
5. [Implementation – HashMap‑Based](#implementation---hashmap-based)  
   * Java  
   * Python  
   * C++  
6. [Alternative Approaches](#alternative-approaches)  
7. [Common Pitfalls & Edge Cases](#common-pitfalls--edge-cases)  
8. [Blog‑Style “The Good, The Bad, The Ugly”](#blog-style-the-good-the-bad-the-ugly)  
9. [SEO Tips for Your Resume & Blog](#seo-tips-for-your-resume--blog)  
10. [Wrap‑Up](#wrap-up)

---

## 1. Problem Statement <a name="problem-statement"></a>

> **Given** an integer array `nums` and an integer `k`,  
> **Return** the number of **sub‑arrays** (contiguous sub‑segments) such that the bitwise AND of all elements in the sub‑array equals `k`.  
> **Constraints**  
> - `1 <= nums.length <= 10^5`  
> - `0 <= nums[i], k <= 10^9`

The brute force `O(n^2)` solution fails when `n = 10^5`. We need a linear‑ish algorithm.

---

## 2. Why This Problem Rocks <a name="why-this-problem-rocks"></a>

| # | Reason |
|---|--------|
| 1 | **Bitwise mastery** – showcases deep understanding of bitwise operations. |
| 2 | **Sliding window + hashmap** – popular interview combo. |
| 3 | **Edge‑case heavy** – tricky `k = 0`, large numbers, repeated values. |
| 4 | **Real‑world relevance** – often appears in data‑streaming and log‑analysis problems. |

A solid solution demonstrates algorithmic thinking, code quality, and problem‑specific tricks that recruiters love.

---

## 3. High‑Level Solution <a name="high-level-solution"></a>

### Observation

The bitwise AND of a sub‑array is always **non‑increasing** as we extend the sub‑array to the right:

```
a & b & c   <=   a & b   <=   a
```

Hence, **for a fixed right end `i`, the AND values of sub‑arrays ending at `i` can only take at most 31 distinct values** (one per bit position).  

### Idea

Traverse the array once while maintaining a map:

- **Key** – possible AND value of a sub‑array that ends at the current index.
- **Value** – how many sub‑arrays ending at the current index produce that AND.

For each new element `x = nums[i]`:

1. Start a new map `curr`.
2. For every previous AND `prev` in the map:
   - `newAnd = prev & x`
   - If `newAnd == k`, add `cnt(prev)` to the answer.
   - Add `cnt(prev)` to `curr[newAnd]`.
3. Also account for the sub‑array consisting of only `x`:
   - If `x == k`, increment answer.
   - Increment `curr[x]`.

Set `prev = curr` and repeat.

Because each map contains ≤ 31 keys, the whole algorithm runs in **O(n · 31)** time and **O(31)** extra space.

---

## 4. Complexity Analysis <a name="complexity-analysis"></a>

| Metric | Value |
|--------|-------|
| **Time** | `O(n · B)` where `B = 31` (bits in 32‑bit int). For `n = 10^5`, about 3 × 10^6 operations – easily under 1 s. |
| **Space** | `O(B)` – at most 31 key/value pairs at any time. |
| **Answer Type** | 64‑bit integer (`long` in Java, `int64`/`long long` in C++, `int` in Python). |

---

## 5. Implementation – HashMap‑Based <a name="implementation---hashmap-based"></a>

### 5.1 Java

```java
import java.util.HashMap;

public class Solution {
    public long countSubarrays(int[] nums, int k) {
        long answer = 0;
        HashMap<Integer, Integer> prev = new HashMap<>();

        for (int x : nums) {
            HashMap<Integer, Integer> curr = new HashMap<>();

            // Sub‑array consisting of only x
            if (x == k) answer++;
            curr.merge(x, 1, Integer::sum);

            // Extend all previous sub‑arrays
            for (var entry : prev.entrySet()) {
                int prevAnd = entry.getKey();
                int cnt = entry.getValue();
                int newAnd = prevAnd & x;
                if (newAnd == k) answer += cnt;
                curr.merge(newAnd, cnt, Integer::sum);
            }
            prev = curr;
        }
        return answer;
    }
}
```

### 5.2 Python

```python
from collections import defaultdict

class Solution:
    def countSubarrays(self, nums: list[int], k: int) -> int:
        ans = 0
        prev = defaultdict(int)

        for x in nums:
            curr = defaultdict(int)

            # Single element sub‑array
            if x == k:
                ans += 1
            curr[x] += 1

            # Extend previous sub‑arrays
            for prev_and, cnt in prev.items():
                new_and = prev_and & x
                if new_and == k:
                    ans += cnt
                curr[new_and] += cnt

            prev = curr
        return ans
```

### 5.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long countSubarrays(vector<int>& nums, int k) {
        long long ans = 0;
        unordered_map<int, int> prev, curr;
        for (int x : nums) {
            curr.clear();
            // sub‑array of length 1
            if (x == k) ans++;
            curr[x] += 1;
            for (auto &p : prev) {
                int prevAnd = p.first, cnt = p.second;
                int newAnd = prevAnd & x;
                if (newAnd == k) ans += cnt;
                curr[newAnd] += cnt;
            }
            prev.swap(curr);
        }
        return ans;
    }
};
```

> **Tip:** In C++ you can replace `unordered_map` with `std::map` if you prefer ordered keys; the performance difference is negligible for ≤ 31 entries.

---

## 6. Alternative Approaches <a name="alternative-approaches"></a>

| Approach | How it works | Pros | Cons |
|----------|--------------|------|------|
| **Sliding Window** (two pointers) | Keep a window where AND == k; shrink from left until AND > k. | O(n) * average* | Hard to maintain when AND increases; not guaranteed linear. |
| **Bit DP** | Count sub‑arrays per bit separately; combine via inclusion‑exclusion. | Conceptually neat | Complex, hard to implement; risk of overflow. |
| **Segment Tree / Sparse Table** | Pre‑compute AND over ranges; iterate all starts, binary search end. | Can answer queries quickly | O(n log n) space/time, overkill for this problem. |

The hashmap‑based solution is the most *clean*, *well‑documented*, and *fast* for the given constraints.

---

## 7. Common Pitfalls & Edge Cases <a name="common-pitfalls--edge-cases"></a>

| Pitfall | Fix |
|---------|-----|
| **Using `int` for answer** | Use `long` / `int64` / `long long`. For `n=10^5`, answer can be up to ~5 × 10^9. |
| **Overflow in Java `int` map keys** | Keys are `int`; fine. Values can be `int` counts because each map entry’s count is ≤ n. |
| **Neglecting sub‑array of length 1** | Always check `x == k` before processing previous ANDs. |
| **Using `for (int key : prevMap.keySet())` in Java** | Mutating map while iterating leads to `ConcurrentModificationException`. Use a separate list of keys or iterate over entry set. |
| **Not resetting `curr` map each iteration** | Reuse a single map by calling `curr.clear()`. |
| **Large `k` (e.g., 10^9)** | Works fine; AND results stay within 32 bits. |
| **All zeros** | Works: each sub‑array AND is 0. Count will be `n*(n+1)/2`. |

---

## 8. Blog‑Style “The Good, The Bad, The Ugly” <a name="blog-style-the-good-the-bad-the-ugly"></a>

> *Title: “The Good, The Bad, & The Ugly of Counting Sub‑Arrays With AND Value of K”*  

> **SEO Keywords** – Leetcode 3209, bitwise AND subarray, interview coding problem, Java Python C++ solution, hashmap approach, algorithm interview, software engineer interview, get a job.

### Introduction  

In coding interviews, the *Number of Subarrays With AND Value of K* is a classic test of both bit manipulation prowess and algorithmic efficiency. While the statement is deceptively simple, crafting an optimal solution involves a handful of subtle insights. In this post, we’ll break down the **good** (what you should do), the **bad** (what you should avoid), and the **ugly** (the pitfalls that lurk when you’re not careful).

---

#### The Good – Mastering the HashMap Trick  

1. **Leverage the non‑increasing property of AND** – as you extend a sub‑array to the right, its AND can only lose bits, never gain them.  
2. **Keep only unique AND values** – for any right end, there are at most 31 distinct AND results (one per bit).  
3. **Update counts incrementally** – use a map `prev` that holds counts of all AND values ending at the previous index. For each new element, compute new ANDs and accumulate answer when they equal `k`.  
4. **Always account for single‑element sub‑arrays** – they’re the base case.  

Result: **O(n · 31)** time, **O(31)** space, fully deterministic, and very easy to translate into Java, Python, or C++.

---

#### The Bad – Common Missteps  

| # | Mistake | Why it breaks |
|---|---------|---------------|
| 1 | **Brute‑force `O(n^2)`** | Fails for `n=10^5`. |
| 2 | **Updating the same map while iterating** | `ConcurrentModificationException` in Java, subtle bugs in C++/Python. |
| 3 | **Using `int` for answer** | Overflow on large arrays. |
| 4 | **Skipping single‑element case** | Under‑counts sub‑arrays when `k == nums[i]`. |
| 5 | **Assuming sliding window works** | AND does not “grow” when you move the right pointer; window size can become exponential. |

---

#### The Ugly – Tricky Edge Cases  

1. **All zeros** – While the algorithm handles it, many naive solutions forget that every sub‑array’s AND is 0, causing a massive under‑count.  
2. **Very large `k` values** – Not a problem for 32‑bit ints, but it can mislead you into thinking bit‑specific optimizations are needed.  
3. **Non‑standard integer sizes** (e.g., 64‑bit) | You must ensure your map uses the correct width; otherwise you’ll get wrong AND results. |
4. **Large test inputs** | The map might grow if you use a data structure that doesn’t prune keys, leading to memory blow‑up. |

---

#### The Ugly – How to Avoid It  

- **Always test with edge inputs** – `n=1`, all zeros, all ones, alternating bits, etc.  
- **Use a copy of keys** when iterating (`List<Integer> keys = new ArrayList<>(prev.keySet());`).  
- **Swap maps** instead of re‑allocating – `prev.swap(curr)` in C++ or `prev = curr` after a `clear()`.  
- **Validate with brute‑force for small cases** – ensures correctness before scaling.

---

#### Takeaway  

When you walk into an interview room, the interviewer will expect you to think *in bits*. By employing the hashmap approach, you demonstrate:

- **Algorithmic insight** – recognizing that the AND operation reduces complexity.  
- **Coding discipline** – careful handling of maps, big integers, and base cases.  
- **Adaptability** – the solution is language‑agnostic, so you can discuss it in Java, then quickly switch to Python or C++.

This blend of *technical depth* and *implementation simplicity* is exactly what hiring managers look for when they ask, “Can you solve Leetcode 3209?” — and what will help you get that job offer.

---

## 9. Conclusion <a name="conclusion"></a>

The hashmap‑based solution is **robust, fast, and language‑agnostic**. By following the guidelines in this post—careful map updates, correct data types, and handling single‑element sub‑arrays—you’ll avoid the most common pitfalls. Whether you’re preparing for a coding interview or tackling a contest problem, mastering this trick will give you a competitive edge.

> *“The good of this problem is the elegance of the hashmap trick; the bad is the temptation of brute‑force or careless map manipulation; the ugly is the subtle overflow and off‑by‑one bugs that sabotage otherwise correct solutions.”*

Happy coding, and good luck getting that dream job!

---

## 9. Final Thoughts <a name="conclusion"></a>

> *Title: “From Code to Career: Solving Leetcode 3209 with HashMaps”*  

> **SEO** – Leetcode 3209 solution, interview algorithm, bitwise AND, Java Python C++.

**If you’ve just finished coding the solution, you can already use it as a talking point in interviews.** Discuss the observation about non‑increasing AND, the 31‑key limit, and how the algorithm stays linear. Most recruiters will be impressed by that level of insight.

--- 

> **Download the full implementation** in your language of choice from the GitHub repo (link).  
> **Try it out** on Leetcode or your local IDE, and keep a timer to see the performance.

Happy interviewing, and *may the bits be ever in your favor!* 🚀

---

## 9. Final Thoughts <a name="final-thoughts"></a>

The hashmap approach is a *no‑frills* solution that scales gracefully. Once you understand the key observation—AND values shrink and are bounded by the number of bits—you can write clean, production‑ready code in any language.  

> **Takeaway for recruiters:**  
> - *Show the observation.*  
> - *Mention the 31‑key bound.*  
> - *Deliver the O(n · 31) solution.*  

That’s a solid answer that says, “I know the trick, I know the limits, and I can implement it correctly.” Perfect for landing a software engineering role!