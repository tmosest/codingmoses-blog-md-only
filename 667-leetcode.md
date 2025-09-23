---
title: LeetCode 667. Beautiful Arrangement II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 LeetCode 667 – Beautiful Arrangement II  
**Medium** – `constructArray(n, k)`  

> **Problem**  
> Given two integers `n` and `k` (1 ≤ k < n ≤ 10⁴), build a permutation of the first `n` natural numbers such that the array of absolute differences between consecutive elements contains **exactly `k` distinct values**.  
> Return any valid permutation.

Below you’ll find **three full, ready‑to‑run solutions** – one in **Java**, one in **Python**, and one in **C++** – plus a *blog‑style* walkthrough that explains the algorithm, its pros and cons, and why it’s a great interview‑ready pattern.

---

### 📐 The “Beautiful Arrangement” Greedy Idea

1. **We need `k` distinct differences.**  
   The largest possible difference in a permutation of 1…n is `n-1`.  
   If we want exactly `k` distinct differences, we can guarantee them by building a pattern that produces the differences `k, k-1, … , 1` – that’s `k` distinct values.

2. **Construction strategy**  
   * Start with the first `n-k-1` numbers in ascending order – they will produce differences of 1 only, no new distinct values.
   * For the remaining `k+1` positions we *zig‑zag* between the two ends of the remaining range:
     * low side = `n-k` (the first unused number),
     * high side = `n`,
     * then decrement the high side, increment the low side, etc.
   * This zig‑zag produces differences: `k, k-1, … , 1` in that order – exactly `k` distinct values.

3. **Why it works**  
   * The first part creates no new differences beyond `1`.  
   * The zig‑zag part yields a strictly decreasing sequence of differences, each new difference is smaller than the previous one.  
   * Total distinct differences = `k`.  
   * All numbers from 1…n are used exactly once – a valid permutation.

The algorithm runs in **O(n)** time and uses **O(1)** additional space (besides the output array).

---

## 🎯 Code Snippets

### Java

```java
import java.util.*;

class Solution {
    public int[] constructArray(int n, int k) {
        int[] ans = new int[n];
        int idx = 0;

        // 1. Prefix with 1 … n-k-1
        for (int v = 1; v < n - k; v++) {
            ans[idx++] = v;
        }

        // 2. Zig‑zag part producing k distinct differences
        for (int i = 0; i <= k; i++) {
            if (i % 2 == 0) { // even i → take from the low side
                ans[idx++] = n - k + i / 2;
            } else {          // odd i → take from the high side
                ans[idx++] = n - i / 2;
            }
        }
        return ans;
    }

    // ---- Simple test harness (optional) ----
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(Arrays.toString(s.constructArray(3, 1))); // [1, 2, 3]
        System.out.println(Arrays.toString(s.constructArray(3, 2))); // [1, 3, 2]
    }
}
```

### Python

```python
class Solution:
    def constructArray(self, n: int, k: int) -> list[int]:
        ans = []
        # Prefix: 1 … n-k-1
        ans.extend(range(1, n - k))

        # Zig‑zag suffix
        for i in range(k + 1):
            if i % 2 == 0:
                ans.append(n - k + i // 2)     # low side
            else:
                ans.append(n - i // 2)         # high side
        return ans

# ---- Test ----
if __name__ == "__main__":
    s = Solution()
    print(s.constructArray(3, 1))  # [1, 2, 3]
    print(s.constructArray(3, 2))  # [1, 3, 2]
```

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> constructArray(int n, int k) {
        vector<int> ans;
        ans.reserve(n);

        // Prefix: 1 … n-k-1
        for (int v = 1; v < n - k; ++v)
            ans.push_back(v);

        // Zig‑zag suffix
        for (int i = 0; i <= k; ++i) {
            if (i % 2 == 0)          // even -> low side
                ans.push_back(n - k + i / 2);
            else                     // odd  -> high side
                ans.push_back(n - i / 2);
        }
        return ans;
    }
};

int main() {
    Solution s;
    vector<int> a = s.constructArray(3, 1);
    for (int x : a) cout << x << ' ';   // 1 2 3
    cout << '\n';
    a = s.constructArray(3, 2);
    for (int x : a) cout << x << ' ';   // 1 3 2
}
```

---

## 📚 Blog Article – “The Good, The Bad, and The Ugly of Beautiful Arrangement II”

### 1. Title & Meta (SEO‑ready)

**Title**:  
*Beautiful Arrangement II – A Step‑by‑Step LeetCode 667 Solution (Java, Python, C++) | Interview Coding Guide*

**Meta description**:  
“Want to ace LeetCode 667? Learn the greedy ‘zig‑zag’ construction for Beautiful Arrangement II. Full Java, Python & C++ solutions, algorithm walkthrough, edge cases, and interview tips. Perfect for software engineering interviews.”

### 2. Introduction

The *Beautiful Arrangement II* problem is a favorite among algorithm interviewers because it blends a classic permutation trick with a fresh twist on difference‑counting. If you’re looking to strengthen your coding interview arsenal, this post gives you:

* A **clear, one‑liner** greedy algorithm (why it works).
* **Full source code** in Java, Python, and C++.
* A deep dive into **time/space complexity**.
* Common pitfalls, edge‑case handling, and real‑world interview tips.

Let’s dive!

### 3. Problem Recap

- Input: `n` (size of the permutation) and `k` (desired number of distinct differences).
- Output: Any permutation of `1 … n` where the array of absolute differences between adjacent elements contains **exactly `k` distinct values**.

### 4. The Greedy Insight – “Zig‑Zag”

> **Why zig‑zag?**  
> The largest difference we can create is `n-1`. By alternating between the smallest unused number and the largest unused number, we can force the difference to shrink by 1 each time:  
> `n-k`, `n`, `n-k+1`, `n-1`, …  

The pattern produces differences: `k, k-1, … , 1`. That’s `k` distinct numbers – precisely what the problem demands.

### 5. Step‑by‑Step Construction

1. **Prefix with ascending numbers**  
   Use the numbers `1` to `n-k-1`.  
   *Why?* They generate a single difference of `1`, not introducing new distinct differences.

2. **Zig‑zag the remaining `k+1` positions**  
   ```text
   low  = n - k
   high = n
   result = []
   for i = 0 … k:
       if i is even:  result.append(low + i/2)
       else:          result.append(high - i/2)
   ```
   *The `i/2` integer division handles the alternating “move inward” step.*

3. **Combine**  
   The final array is the concatenation of the prefix and the zig‑zag part.

### 6. Why the Algorithm is Correct

*All differences produced by the prefix are `1`.*  
*The zig‑zag part produces differences that strictly decrease from `k` to `1`.*  
Therefore the set of distinct differences is exactly `{1, 2, … , k}` – `k` distinct values.

The algorithm visits each integer from `1` to `n` exactly once, guaranteeing a valid permutation.

### 7. Complexity Analysis

| Step | Operations | Complexity |
|------|------------|------------|
| Prefix | Loop `n-k-1` | **O(n)** |
| Zig‑zag | Loop `k+1` | **O(k)** (≤ O(n)) |
| Total | – | **Time: O(n)** |
| Extra Space | Output array of size `n` | **Space: O(1)** auxiliary (ignoring output) |

The solution is optimal – we can’t beat linear time because we must touch each element at least once.

### 8. Edge Cases & Common Mistakes

| Edge | Issue | Fix |
|------|-------|-----|
| `k = n-1` (maximum distinct differences) | The prefix loop becomes empty (`n-k-1` = 0). | Code handles this gracefully; just start zig‑zag from the beginning. |
| `n = 2`, `k = 1` | The zig‑zag uses the pattern correctly. | Ensure the loop bounds are `<= k`. |
| Wrong integer division | Using float division instead of integer division leads to wrong indices. | Use `i/2` in Java, `i // 2` in Python, `i / 2` in C++ (since `int` division truncates). |
| Mis‑ordering of low/high picks | Picking both sides on the same parity can skip numbers. | Stick to the “even → low, odd → high” rule. |
| Out‑of‑range indices | Subtracting too much from `high` or adding too much to `low` may exceed `[1, n]`. | Verify the formulas; test with random `n`, `k`. |

### 9. Interview Tips

1. **Explain the intuition first** – interviewers love seeing *why* you pick an approach.
2. **Sketch the permutation** on paper. Write the prefix and zig‑zag side by side; show the resulting differences.
3. **Talk about complexity** – say “linear time, constant extra space”.
4. **Ask clarifying questions** if the interviewer is vague about `k` being `0` (though constraints forbid it) or about the order of differences.
5. **Show the code** in the language you’re most comfortable with, but be ready to translate to another language on the spot.

### 10. Final Thoughts

Beautiful Arrangement II showcases a powerful pattern: *use a controlled prefix to avoid extra work, then a deliberate zig‑zag to hit exactly the needed distinct values*. It’s a great example of blending greedy construction with combinatorial reasoning – perfect for algorithm interviews.

Happy coding, and good luck on your next interview! 🚀

---

### 9. Conclusion

You now have:

* Three production‑ready code solutions.  
* A fully documented algorithm that’s both **simple** and **optimal**.  
* A blog article that’s polished for **search engines** and **learning**.

Whether you’re preparing for a senior software engineer interview, sharpening your interview skills, or just looking to deepen your understanding of permutation tricks, the *Beautiful Arrangement II* pattern is a must‑know. Use it, explain it, and watch it impress interviewers!

--- 

**P.S.** Try random tests with `n` up to `10^5` and confirm the distinct difference count. Feel free to extend the test harness in any language – the algorithm is robust and fast enough for all constraints. 

Good luck, and happy coding!