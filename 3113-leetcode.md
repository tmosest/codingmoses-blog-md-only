---
title: LeetCode 3113. Find the Number of Subarrays Where Boundary Elements Are Maximum - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap

**LeetCode 3113 – Find the Number of Subarrays Where Boundary Elements Are Maximum**  

Given an array `nums` of positive integers, count all subarrays `[i … j]` such that  

* `nums[i] == nums[j]`  
* `nums[i]` (and therefore `nums[j]`) is the **maximum** element in the subarray.

The array size is up to \(10^5\), so an \(O(n^2)\) enumeration is impossible.  

The challenge is to exploit the fact that the boundary elements are the same and also the maximum. This leads to a classic *monotonic stack* pattern.

---

## 2.  High‑Level Idea

Process the array from left to right and keep a **monotonic decreasing stack** that stores pairs  

```
(value, cnt)   // value = nums[k] at that stack level
                // cnt  = number of subarrays ending at the current position
                //        whose maximum equals this value and
                //        whose left boundary is at the corresponding position
```

For each element `x = nums[i]`:

1. **Pop** all stack entries with a value **smaller** than `x`.  
   Those entries can never be the maximum of a subarray that ends at `i`.

2. If the stack is empty **or** the top value is **greater** than `x`,  
   push a new entry `(x, 0)` – a fresh start for `x`.

3. Increase the counter of the top entry (`cnt++`).  
   This counts the new subarray that ends at `i` and starts at the position represented by that stack entry.

4. Add that counter to the global answer.

Because each element is pushed and popped at most once, the total complexity is **O(n)**.

---

## 3.  Correctness Proof  

We prove that the algorithm returns the exact number of valid subarrays.

### Lemma 1  
After processing position `i`, the stack contains a strictly decreasing sequence of values, each of which appears in `nums[0 … i]` and is **not smaller** than any element to its right in the stack.

**Proof.**  
When we encounter `x = nums[i]` we pop all entries with value `< x`.  
Thus every remaining entry is `≥ x`.  
If the top entry has value `> x`, we push `(x,0)`.  
Consequently the stack stays strictly decreasing from bottom to top. ∎



### Lemma 2  
For every stack entry `(v, cnt)` at position `i`, `cnt` equals the number of subarrays that

* end at `i`,
* start at the position represented by this entry, and
* have maximum `v` and boundary element `v`.

**Proof.**  
Consider the entry added at index `k` (`k ≤ i`).  
All subarrays that end at `i` and start at any index `t` with `k ≤ t ≤ i` will contain only values `≤ v` because all values larger than `v` were popped earlier (Lemma&nbsp;1).  
Thus the maximum of each of those subarrays is `v`.  
The entry’s counter is increased exactly once for each `t` (step 3), so after the last occurrence of `v` it equals the number of such subarrays. ∎



### Lemma 3  
At the end of the loop, the sum added to `ans` equals the total number of valid subarrays.

**Proof.**  
Every valid subarray `[l … r]` has a maximum `m` and boundaries equal to `m`.  
While processing `r`, the algorithm pushes or keeps an entry `(m, cnt)` that corresponds to the start position `l`.  
By Lemma 2, when `r` is processed, `cnt` already counts this subarray, and `cnt` is added to `ans`.  
Each valid subarray contributes exactly once, so the total added equals the number of valid subarrays. ∎



### Theorem  
The algorithm returns the correct answer for any input array `nums`.

**Proof.**  
By Lemma 3, the accumulated sum `ans` equals the number of valid subarrays.  
Thus the algorithm is correct. ∎



---

## 4.  Complexity Analysis

| Operation | Cost |
|-----------|------|
| Push / Pop on stack | `O(1)` each |
| Loop over array | `O(n)` |

Because each element is pushed at most once and popped at most once, the total time is **O(n)**.  
The stack stores at most `n` pairs, so the space consumption is **O(n)**.  
The answer can be up to `n * (n+1) / 2`, so we use 64‑bit integers (`long` / `long long`).

---

## 5.  Reference Implementations

### 5.1 Java

```java
import java.util.ArrayDeque;
import java.util.Deque;

public class Solution {
    public long numberOfSubarrays(int[] nums) {
        // Each stack element: int[2] = {value, count}
        Deque<int[]> stack = new ArrayDeque<>();
        long ans = 0;

        for (int x : nums) {
            // Remove smaller values
            while (!stack.isEmpty() && stack.peek()[0] < x) {
                stack.pop();
            }

            // If we need a new entry for x
            if (stack.isEmpty() || stack.peek()[0] > x) {
                stack.push(new int[]{x, 0});
            }

            // Increment counter of the top element
            stack.peek()[1] += 1;
            ans += stack.peek()[1];
        }
        return ans;
    }
}
```

### 5.2 Python

```python
from typing import List

class Solution:
    def numberOfSubarrays(self, nums: List[int]) -> int:
        stack = []          # each element: [value, count]
        ans = 0

        for x in nums:
            while stack and stack[-1][0] < x:
                stack.pop()

            if not stack or stack[-1][0] > x:
                stack.append([x, 0])

            stack[-1][1] += 1
            ans += stack[-1][1]

        return ans
```

### 5.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long numberOfSubarrays(vector<int>& nums) {
        // stack of pairs: {value, count}
        vector<pair<int, long long>> st;
        long long ans = 0;

        for (int x : nums) {
            // pop smaller values
            while (!st.empty() && st.back().first < x) st.pop_back();

            // push new entry if needed
            if (st.empty() || st.back().first > x) {
                st.emplace_back(x, 0);
            }

            // increase counter of the top entry
            st.back().second += 1;
            ans += st.back().second;
        }
        return ans;
    }
};
```

All three codes run in **O(n)** time and **O(n)** memory, perfectly suitable for LeetCode’s limits.

---

## 6.  Blog Post – “Monotonic Stack Mastery: Counting Max‑Bounded Subarrays”

### 6.1 Title & Meta (SEO)

```
Monotonic Stack Mastery: Counting Subarrays Where Boundary Elements Are Maximum – LeetCode 3113
```

**Meta Description:**  
Learn how to solve LeetCode 3113 with a linear‑time monotonic stack. Step‑by‑step Java, Python, C++ code, proof, and pitfalls.

**Target Keywords:**  
* LeetCode 3113  
* monotonic stack  
* counting subarrays  
* algorithm design  
* Java solution  
* Python solution  
* C++ solution  
* coding interview  

### 6.2 Blog Body

---

### 🚀 Why This Problem Is a *Monotonic Stack Goldmine*

LeetCode 3113 sounds intimidating because you’re asked to count subarrays where the **first and last** numbers are **both the maximum**.  
If you stare at it for a second, the key observation pops up:

> **If a larger element appears anywhere between two equal boundary numbers, it blocks all subarrays that start at the left side.**

That “blocking” intuition is the hallmark of a *monotonic stack*—think of it as a **filter** that discards “bad” candidates as you scan the array.

---

### 🧠 The Step‑by‑Step Algorithm

| Step | What It Does | Why It Works |
|------|--------------|--------------|
| 1️⃣ **Pop smaller elements** | Removes values that can’t be the maximum of a subarray ending here. | Keeps the stack decreasing (Lemma 1). |
| 2️⃣ **Push new value if needed** | Starts a fresh counter for the current number. | Ensures every distinct value on the stack has a counter. |
| 3️⃣ **Increment counter** | Counts one more subarray ending at the current index. | Accumulates the number of valid subarrays for that maximum. |
| 4️⃣ **Add to answer** | Collects the total. | Each valid subarray contributes exactly once (Lemma 3). |

Because each array element is pushed/popped only once, the whole algorithm runs in linear time.

---

### ⚙️ Code Walk‑Through

#### Java

```java
public long numberOfSubarrays(int[] nums) {
    Deque<int[]> stack = new ArrayDeque<>();
    long ans = 0;
    for (int x : nums) {
        while (!stack.isEmpty() && stack.peek()[0] < x) stack.pop();
        if (stack.isEmpty() || stack.peek()[0] > x) stack.push(new int[]{x, 0});
        stack.peek()[1] += 1;
        ans += stack.peek()[1];
    }
    return ans;
}
```

#### Python

```python
class Solution:
    def numberOfSubarrays(self, nums):
        stack = []
        ans = 0
        for x in nums:
            while stack and stack[-1][0] < x:
                stack.pop()
            if not stack or stack[-1][0] > x:
                stack.append([x, 0])
            stack[-1][1] += 1
            ans += stack[-1][1]
        return ans
```

#### C++

```cpp
class Solution {
public:
    long long numberOfSubarrays(vector<int>& nums) {
        vector<pair<int, long long>> st;
        long long ans = 0;
        for (int x : nums) {
            while (!st.empty() && st.back().first < x) st.pop_back();
            if (st.empty() || st.back().first > x) st.emplace_back(x, 0);
            st.back().second += 1;
            ans += st.back().second;
        }
        return ans;
    }
};
```

---

### 🔧 What Makes the Code *Good*?

| Feature | Why It Matters |
|---------|----------------|
| **Single pass** | Eliminates the quadratic blow‑up. |
| **Monotonic stack** | Guarantees linear amortized complexity. |
| **Long integer result** | Handles the maximum possible answer (`~5×10⁹`). |
| **Clear variable names** | `stack`, `cnt`, `ans` – easy to read. |
| **No extra libraries** | Works in any language environment. |

---

### ⚠️ Common Pitfalls (The “Bad” & “Ugly” Parts)

| Pitfall | Explanation | Fix |
|---------|-------------|-----|
| **Using `int` for the answer** | The result can exceed 2³¹‑1. | Use `long` (Java), `long long` (C++), or `int`‑sized Python `int` (which is unbounded). |
| **Ignoring the “greater‑than” check** | Pushing a new pair when the top equals the current value leads to duplicate counting. | Only push when the stack is empty **or** the top value is *greater* than `x`. |
| **Not initializing the counter to 0** | A new value should start with 0 so the first increment counts the single‑element subarray. | Push `{x, 0}`. |
| **Pop only `< x` but forget to pop `== x`** | That would leave stale entries that are no longer valid. | The algorithm intentionally **does not** pop equal values – they are the ones we want to reuse. |
| **Using a max‑heap instead of a decreasing stack** | The stack must stay decreasing; a heap would lose the linear‑time guarantee. | Stick with the monotonic stack pattern. |

---

### 🎯 Final Takeaway

The monotonic stack provides a clean, linear‑time solution for counting subarrays whose boundary elements are also the maximum.  
The key insights are:

1. **One side is fixed** – we enumerate the right end.  
2. **Large numbers mask smaller ones** – they must be removed from consideration.  
3. **The stack remembers “where” a particular maximum can start** – its counter directly gives the number of subarrays ending at the current index.

With the proofs, complexity analysis, and reference code above, you’re all set to ace LeetCode 3113 and impress interviewers with your mastery of monotonic stacks! 🚀

---

## 6.  SEO Checklist

| Item | Status |
|------|--------|
| Title contains “LeetCode 3113” | ✅ |
| Meta description with problem statement | ✅ |
| Keywords: monotonic stack, counting subarrays, Java, Python, C++ solutions | ✅ |
| Structured headings (`h2`, `h3`) for readability | ✅ |
| Code blocks with syntax highlighting | ✅ |
| Proof section for “good” algorithmic confidence | ✅ |
| Common pitfalls for “bad” parts | ✅ |
| Summary of tricks for “ugly” edge cases | ✅ |

Happy coding—and remember: *when two equal numbers meet and win the fight against everything between them, the monotonic stack is your best ally.*