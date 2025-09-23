---
title: LeetCode 1829. Maximum XOR for Each Query - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. 3‑Language Solutions

Below are three **fully‑working, production‑ready** implementations that solve LeetCode 1829 – *Maximum XOR for Each Query* – in O(n) time and O(1) extra space.  
Each snippet includes the same logic, only expressed in the syntax of its language.

> **Why this works**  
> The maximum value that can be achieved by `xor ⊕ k` where `k < 2^maximumBit` is `2^maximumBit – 1` (all lower bits set to 1).  
> If we know the current XOR of the array (`curXor`), choosing  
> `k = curXor ^ mask` (where `mask = 2^maximumBit – 1`) makes  
> `curXor ⊕ k = mask`.  
> So for every query we simply output `curXor ^ mask` and then *remove* the last element by XORing it out of `curXor`.  

```java
// ───────────────────────────────────────────────────────────────────────
// Java 17 – LeetCode 1829 – Maximum XOR for Each Query
// ───────────────────────────────────────────────────────────────────────
public class Solution {
    public int[] getMaximumXor(int[] nums, int maximumBit) {
        int curXor = 0;
        for (int num : nums) curXor ^= num;          // XOR of the whole array

        int mask = (1 << maximumBit) - 1;            // 111...1 (maximumBit bits)
        int n = nums.length;
        int[] ans = new int[n];

        for (int i = 0; i < n; i++) {
            ans[i] = curXor ^ mask;                 // answer for this query
            curXor ^= nums[n - 1 - i];              // remove the last element
        }
        return ans;
    }
}
```

```python
# ───────────────────────────────────────────────────────────────────────
# Python 3 – LeetCode 1829 – Maximum XOR for Each Query
# ───────────────────────────────────────────────────────────────────────
def getMaximumXor(nums, maximumBit):
    cur_xor = 0
    for num in nums:          # XOR of all numbers
        cur_xor ^= num

    mask = (1 << maximumBit) - 1
    n = len(nums)
    ans = [0] * n

    for i in range(n):
        ans[i] = cur_xor ^ mask
        cur_xor ^= nums[n - 1 - i]   # remove last element
    return ans
```

```cpp
// ───────────────────────────────────────────────────────────────────────
// C++17 – LeetCode 1829 – Maximum XOR for Each Query
// ───────────────────────────────────────────────────────────────────────
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> getMaximumXor(vector<int>& nums, int maximumBit) {
        int curXor = 0;
        for (int num : nums) curXor ^= num;   // XOR of the whole array

        int mask = (1 << maximumBit) - 1;
        int n = nums.size();
        vector<int> ans(n);

        for (int i = 0; i < n; ++i) {
            ans[i] = curXor ^ mask;
            curXor ^= nums[n - 1 - i];         // remove last element
        }
        return ans;
    }
};
```

> **All three solutions run in O(n) time and use only O(1) extra space.**  
> They rely on the fact that the *maximum* achievable XOR for a given `maximumBit` is always the bitwise mask itself, which dramatically simplifies the problem.

---

## 2. Blog Post – “The Good, The Bad, and The Ugly of LeetCode 1829”

### Title  
**Maximum XOR for Each Query (LeetCode 1829) – The Good, The Bad, and The Ugly**

### Meta Description  
Learn how to crack LeetCode 1829 in O(n) time. Discover the bit‑wise trick, edge cases, and why this simple solution beats a naïve O(n²) approach. Code in Java, Python, and C++.

---

### Table of Contents
1. [Problem Overview](#problem-overview)  
2. [Intuition & Key Insight](#intuition)  
3. [The Elegant O(n) Solution](#solution)  
4. [Edge Cases & Pitfalls](#edge-cases)  
5. [Complexity Analysis](#complexity)  
6. [Why the Sorted Array Is a Red Herring](#sorted-array)  
7. [The “Bad” – A Common Mistake](#bad)  
8. [The “Ugly” – Over‑engineering the Problem](#ugly)  
9. [Code Snapshots (Java / Python / C++)](#code)  
10. [Takeaways & Interview Tips](#takeaways)  

---

<a name="problem-overview"></a>
## 1. Problem Overview

> **LeetCode 1829 – Maximum XOR for Each Query**  
> **Difficulty:** Medium  
> **Input:**  
> * `nums` – a sorted array of `n` non‑negative integers (0 ≤ nums[i] < 2^maximumBit).  
> * `maximumBit` – the number of bits that bound the value `k` (`0 ≤ k < 2^maximumBit`).  
> **Goal:** For each query, find the `k` that maximizes  
> `nums[0] XOR nums[1] XOR … XOR nums[nums.length-1] XOR k`.  
> After answering, remove the last element from `nums`. Return the list of answers.

**Example**  
```text
nums = [0,1,1,3], maximumBit = 2
Output → [0,3,2,3]
```

---

<a name="intuition"></a>
## 2. Intuition & Key Insight

The crux is that the *maximum* XOR obtainable when you are allowed to pick a `k` under a fixed bit‑width is always a string of all 1’s:

```text
mask = (1 << maximumBit) - 1   // e.g., maximumBit = 3 → mask = 0b111 = 7
```

Why?  
- The XOR of any number `x` with `mask` flips every bit: `x ⊕ mask = ~x & mask`.  
- The largest value you can reach with `k` is `mask` itself, because any XOR result is bounded by `mask`.  
- If you know the current XOR of the array, say `curXor`, the *only* way to guarantee that `curXor ⊕ k = mask` is to choose  

```text
k = curXor ⊕ mask
```

Thus the answer for each query is simply `curXor ⊕ mask`.  
After answering, the last element is removed, which in XOR terms is just `curXor ^= lastElement`.

**Key takeaway**:  
> *Maximum XOR for each query is `currentXOR ^ mask`. No heavy data structures needed.*

---

<a name="solution"></a>
## 3. The Elegant O(n) Solution

1. Compute the XOR of the entire array – call it `curXor`.  
2. Compute `mask = (1 << maximumBit) - 1`.  
3. For `i` from `0` to `n-1`  
   * `ans[i] = curXor ^ mask`  
   * `curXor ^= nums[n-1-i]`  — remove the last element.  

No loops inside loops, no auxiliary data structures.  
The algorithm works because XOR is *commutative* and *associative*: removing an element is the same as XORing it out.

---

<a name="edge-cases"></a>
## 4. Edge Cases & Pitfalls

| Case | Why it matters | What to watch out for |
|------|----------------|------------------------|
| `maximumBit = 1` | `mask = 1` – smallest non‑zero mask | Be careful with bit shifting; `(1 << 1)` is `2`. |
| `nums` contains `0` | XORing with `0` has no effect | Works naturally. |
| `n = 1` | Only one query | Still works – `curXor` is the single element. |
| Large `maximumBit` (up to 20) | `mask` fits in 32‑bit int | No overflow in Java/Python/C++. |
| **Sortedness** | Input guarantees sorted | Sorting is irrelevant – the algorithm doesn’t depend on order. |

---

<a name="complexity"></a>
## 5. Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| XOR all elements | **O(n)** | O(1) |
| Loop to answer queries | **O(n)** | O(1) |
| **Total** | **O(n)** | **O(1)** (apart from the output array/vector) |

---

<a name="sorted-array"></a>
## 6. Why the Sorted Array Is a Red Herring

Many interviewers include “`nums` is sorted” just to trick you into thinking you need a *two‑pointer* or *binary‑search* solution.  
In reality, the sortedness gives no advantage because XOR is independent of order.  
Don’t waste time sorting – it would cost O(n log n) and still give the same result.

---

<a name="bad"></a>
## 6. The “Bad” – A Common Mistake

**Naïve approach:**  
For each query, recompute the XOR of the *remaining* array and try every possible `k` from `0` to `2^maximumBit – 1`:

```text
for each query:
    curXor = XOR(nums[0…len-1])
    best = 0
    for k in 0 … mask:
        best = max(best, curXor ^ k)
```

**Why it’s bad**  
- **Time complexity:** O(n · 2^maximumBit).  
  With `maximumBit = 20`, `2^maximumBit = 1,048,576`.  
  For `n = 10^5`, that’s 10^11 operations – astronomically slow.  
- **Unnecessary loops** make it *tardy* for the online judge and useless in an interview setting.

If you spot this in an interview, be sure to ask whether you can avoid the inner loop. The “mask” trick eliminates the need to iterate over all possible `k`.

---

<a name="ugly"></a>
## 7. The “Ugly” – Over‑engineering the Problem

> **People sometimes try to build a Trie of the bits of the array** (like the classic “maximum XOR pair” problem).  
> While a bit‑wise Trie is powerful, it’s **over‑kill** here because:
> * Every query asks for a *different* `k` that depends only on the *current* XOR of the entire array.  
> * Removing an element is trivial in XOR – no need to adjust a Trie.  
> * Building and querying a Trie would add O(n · maximumBit) overhead.

**Bottom line:**  
> Keep your solution as *stateless* as possible. Over‑engineering the data structure will only distract from the simple mask trick.

---

<a name="code"></a>
## 6. Code Snapshots (Java / Python / C++)

*(See the “3‑Language Solutions” section above.)*  
Each snippet follows the exact steps:

```text
mask = (1 << maximumBit) - 1
ans[i] = curXor ^ mask
curXor ^= nums[n-1-i]        // remove the last element
```

Feel free to copy‑paste into your favourite IDE or online compiler.

---

<a name="takeaways"></a>
## 7. Takeaways & Interview Tips

1. **Spot the bound first**  
   *When the answer is bounded by a bit‑mask, it’s often the mask itself.*  
2. **Use XOR properties**  
   *XOR is associative, commutative, and a number XORed with itself is 0.*  
3. **Ask clarifying questions**  
   *Does the sortedness matter?* – If not, you can skip sorting and save time.  
4. **Explain the mask trick**  
   *Talk through why `curXor ^ mask` yields the maximum XOR.  Interviewers love to hear the reasoning.*  
5. **Show your 3‑Language chops**  
   *Having the same algorithm in Java, Python, and C++ demonstrates language flexibility – a plus for full‑stack or system‑design roles.*

---

### Final Verdict

> **Good:** O(n) solution with a clean bit‑wise trick.  
> **Bad:** Forgetting that the mask is the maximum XOR leads to unnecessary loops.  
> **Ugly:** Building a complex Trie or sorting the array again is pure over‑engineering.  

If you can explain the mask trick and deliver any of the three code snippets above, you’ll be ready to ace LeetCode 1829 and impress interviewers alike. Happy coding! 🚀