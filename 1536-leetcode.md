---
title: LeetCode 1536. Minimum Swaps to Arrange a Binary Grid - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1️⃣  Code – 3 Languages

Below you’ll find clean, production‑ready solutions for **LeetCode 1536 – “Minimum Swaps to Arrange a Binary Grid”** in **Java, Python, and C++**.  
All three implementations use the same greedy strategy:  

1. Count the trailing‑zero run for each row.  
2. From the top row downwards, pick the closest row that has enough trailing zeros and “bubble” it to the current position – the number of swaps is simply the distance moved.  
3. If at any step no suitable row exists, return `‑1`.

| Language | Complexity | Notes |
|----------|------------|-------|
| Java | `O(n²)` time, `O(n)` space | Uses an `int[]` for the trailing‑zero counts. |
| Python | `O(n²)` time, `O(n)` space | Uses a plain list. |
| C++ | `O(n²)` time, `O(n)` space | Uses `vector<int>`. |

---

### 💻 Java

```java
/**
 * 1536. Minimum Swaps to Arrange a Binary Grid
 * Java solution – O(n²) time, O(n) space
 */
class Solution {
    public int minSwaps(int[][] grid) {
        int n = grid.length;
        int[] trailingZeros = new int[n];

        // 1. Compute trailing‑zero count for each row
        for (int i = 0; i < n; ++i) {
            int cnt = 0;
            for (int j = n - 1; j >= 0; --j) {
                if (grid[i][j] == 1) break;
                cnt++;
            }
            trailingZeros[i] = cnt;
        }

        int swaps = 0;

        // 2. Greedy placement from top row to bottom
        for (int required = n - 1; required >= 0; --required) {
            int idx = -1;
            // Find the first row (from the top) that has at least `required` zeros
            for (int i = 0; i < n; ++i) {
                if (trailingZeros[i] >= required) {
                    idx = i;
                    break;
                }
            }
            if (idx == -1) return -1;           // impossible

            // Move that row up to the current position
            for (int i = idx; i > required; --i) {
                trailingZeros[i] = trailingZeros[i - 1];
            }
            trailingZeros[required] = required; // the moved row now sits at `required`

            swaps += idx - required;
        }
        return swaps;
    }
}
```

---

### 🐍 Python

```python
# 1536. Minimum Swaps to Arrange a Binary Grid
# Python solution – O(n²) time, O(n) space

class Solution:
    def minSwaps(self, grid: List[List[int]]) -> int:
        n = len(grid)
        trailing = [0] * n

        # Count trailing zeros for each row
        for i in range(n):
            cnt = 0
            for j in range(n - 1, -1, -1):
                if grid[i][j] == 1:
                    break
                cnt += 1
            trailing[i] = cnt

        swaps = 0

        # Greedy: place rows from top to bottom
        for need in range(n - 1, -1, -1):
            idx = -1
            for i in range(n):
                if trailing[i] >= need:
                    idx = i
                    break
            if idx == -1:
                return -1

            # Move the chosen row up
            for i in range(idx, need, -1):
                trailing[i] = trailing[i - 1]
            trailing[need] = need

            swaps += idx - need

        return swaps
```

---

### ⚙️ C++

```cpp
// 1536. Minimum Swaps to Arrange a Binary Grid
// C++17 solution – O(n²) time, O(n) space

class Solution {
public:
    int minSwaps(vector<vector<int>>& grid) {
        int n = grid.size();
        vector<int> trailing(n, 0);

        // 1. Count trailing zeros per row
        for (int i = 0; i < n; ++i) {
            int cnt = 0;
            for (int j = n - 1; j >= 0; --j) {
                if (grid[i][j] == 1) break;
                ++cnt;
            }
            trailing[i] = cnt;
        }

        int swaps = 0;

        // 2. Greedy placement
        for (int need = n - 1; need >= 0; --need) {
            int idx = -1;
            for (int i = 0; i < n; ++i) {
                if (trailing[i] >= need) {
                    idx = i;
                    break;
                }
            }
            if (idx == -1) return -1;          // impossible

            // Shift rows up to bring idx to position `need`
            for (int i = idx; i > need; --i) {
                trailing[i] = trailing[i - 1];
            }
            trailing[need] = need;

            swaps += idx - need;
        }

        return swaps;
    }
};
```

---

## 2️⃣  📄 Blog Article – “The Good, The Bad, and The Ugly of LeetCode 1536”

---

### Title  
**The Good, The Bad & The Ugly of LeetCode 1536 – Mastering Minimum Swaps in a Binary Grid (Java/Python/C++)**  

---

### Meta Description  
Struggling with LeetCode 1536 “Minimum Swaps to Arrange a Binary Grid”? Read our SEO‑friendly guide covering the problem, greedy solution, performance trade‑offs, interview insights, and how to ace this challenge in Java, Python, and C++.

---

### Introduction  
If you’ve ever stared at LeetCode **1536** – “Minimum Swaps to Arrange a Binary Grid” – and felt your stomach drop, you’re not alone.  
It’s a classic **greedy + array‑manipulation** puzzle that can make or break your interview rhythm. In this post, we’ll dissect the **“good”** approach that lets you solve it cleanly, the **“bad”** pitfalls interviewers love to throw at you, and the **“ugly”** edge cases that test your robustness.  
Alongside, we’ll show you **ready‑to‑paste Java, Python, and C++ implementations**, interview‑ready explanations, and practical tips that’ll make your résumé shine.

---

### 📌 Problem Statement (for readers who haven’t seen it yet)

> **Input:**  
> `grid` – an `n × n` binary matrix (`0` or `1`).  
> **Operation:**  
> You may swap any two *adjacent* rows of the matrix.  
> **Goal:**  
> Rearrange the rows so that the top row contains at least `n‑1` trailing zeros, the second row at least `n‑2`, …, and the bottom row at least `0`.  
> **Output:**  
> Minimum number of swaps required, or `‑1` if impossible.

> **Example**  
> ```
> grid = [[0,1,1],
>         [1,1,0],
>         [1,0,0]]
> ```
> Answer: `1` (swap row 0 with row 2).

---

### ❌ 1. Naïve Approaches & Why They Fail

| Approach | What it does | Why it’s inefficient | Typical interview misstep |
|----------|--------------|----------------------|---------------------------|
| **Brute‑force search of all permutations** | Enumerate all `n!` row orders | `O(n! * n²)` → impossible for `n > 6` | “I’ll just generate all orders.” |
| **Dynamic programming over row subsets** | DP on bitmask of chosen rows | `O(2ⁿ * n)` → still too big for `n = 30` | “Bitmask DP is always the answer.” |
| **Top‑down recursion + memo** | Recursively move rows, caching states | Exponential state space | “Memoization always fixes it.” |

> **Good news:** The problem is **polynomial‑time solvable**. The key is to exploit the *structure* of a binary matrix: only the **rightmost 1** in each row matters.

---

### ✨ 2. The Greedy “Good” – Counting Trailing Zeros

1. **Why trailing zeros?**  
   A row can be moved to position `i` *iff* it has at least `n‑i‑1` zeros at its end.  
   So we pre‑compute this value for every row – one linear pass per row.

2. **Why pick the *closest* suitable row?**  
   Moving a row farther costs more swaps. By always taking the nearest row that satisfies the current requirement, we guarantee minimal total swaps.  
   Think of it as a one‑dimensional bubble‑sort where each element is a row and the “key” is its trailing‑zero count.

3. **Proof of Optimality**  
   - The *required* number of trailing zeros is monotonically decreasing as we move down the grid.  
   - Suppose we had a solution that moved a farther row instead of the nearest one; swapping those two moves would reduce the total number of swaps without breaking feasibility.  
   - Hence the greedy choice is optimal (standard exchange argument).

4. **Implementation details**  
   - **Time:** `O(n²)` – two nested loops (counting zeros + greedy shift).  
   - **Space:** `O(n)` – we keep only the trailing‑zero counts.  
   - Works for **Java, Python, C++** as shown above.

> **Good take‑away:** A single integer array turns a 2‑D matrix problem into a simple 1‑D greedy algorithm.

---

### ⚠️ 3. The “Bad” – Edge Cases & Common Pitfalls

| Edge Case | What Happens | How to Spot It |
|-----------|--------------|----------------|
| **No row has enough trailing zeros** | Greedy fails early → return `‑1`. | After counting zeros, check `max(trailingZeros) < n‑1`. |
| **Multiple rows satisfy a requirement** | Any of them works, but you must pick the *closest* to avoid extra swaps. | Look at the first row (smallest index) with enough zeros. |
| **Swapping “above” the required position** | Wrongly shifting rows downwards increases swaps. | Always shift *upwards* towards the current `need`. |
| **Mis‑reading the requirement** | You might think the top row needs `n` zeros, but it only needs `n‑1`. | Use the formula `need = n‑1 - i` where `i` is the target row index. |
| **Large input size (`n = 30`)** | Even `O(n²)` is fine, but naïve recursion can blow the stack. | Stick to the iterative greedy loop. |

> **Key lesson:** *Never assume that a “more complex” algorithm (DP, BFS, etc.) will automatically perform better.* Sometimes a linear pass on a derived array is all you need.

---

### 🧠 4. “Ugly” – When the Problem Tricks You

1. **Misinterpreting “adjacent” swaps**  
   - It’s *not* a full permutation; you can only swap row `i` with row `i+1`.  
   - Hence moving a row from position `idx` to `need` costs exactly `idx‑need` swaps (not `1`).

2. **Forgetting that the moved row’s trailing‑zero count changes**  
   - After bubbling a row up, its trailing‑zero count becomes exactly the *required* value (`need`).  
   - Failing to update it breaks later iterations.

3. **Off‑by‑one errors with indices**  
   - The top row corresponds to `need = n‑1`.  
   - The bottom row needs `need = 0`.  
   - Off‑by‑one bugs are the most common cause of a wrong answer in this problem.

4. **Assuming the matrix is already “sorted”**  
   - The grid may already satisfy the requirement; your algorithm must still return `0` swaps.  
   - Don’t pre‑filter rows; let the greedy loop handle the “already correct” case naturally.

---

### 💡 5. Interview‑Ready Tips

| Tip | Why it matters in a coding interview |
|-----|-------------------------------------|
| **Explain the invariant** – “At step `k` the first `k` rows are already correctly positioned and have at least `n‑k‑1` trailing zeros.” | Shows you understand the problem’s structure, not just code. |
| **Show the O(n²) analysis** – `n ≤ 30`, so this is fine. | Interviewers love to hear your complexity discussion; it proves you’re not just coding blind. |
| **Discuss the impossibility early** – `if maxZeros < n-1 return -1`. | Early exits keep the algorithm efficient and demonstrate edge‑case awareness. |
| **Highlight the one‑dimensional transformation** – converting a 2‑D problem into a 1‑D array. | This is a recurring interview theme (“reduce the dimensionality”) and a good talking point. |
| **Mention language‑specific nuances** – e.g., Java’s `int[]`, Python’s `list`, C++’s `vector<int>`. | Helps recruiters understand your breadth of experience. |
| **Show test cases** – include the provided example and an impossible case. | Demonstrates robustness and confidence in your solution. |

---

### 📈 6. SEO & Job‑Search Hook

- **Keywords**: *LeetCode 1536, minimum swaps binary grid, Java solution, Python solution, C++ solution, interview preparation, coding interview, software engineering interview, algorithm interview, greedy algorithms, array manipulation.*  
- **Why this article helps you land a job**:  
  1. **Clear, multi‑language code** that recruiters can copy‑paste into their test environments.  
  2. A deep dive into *why* a greedy strategy works, making you ready to explain the reasoning on day‑one interviews.  
  3. Insight into common pitfalls – the “bad” and “ugly” – that interviewers often use as “gotchas.”  
  4. Bonus: A discussion on **time & space trade‑offs** that shows you can balance performance and readability.

---

### 📚 Final Take‑Away

> **Good** – The greedy solution is elegant, runs fast, and scales easily.  
> **Bad** – It’s easy to get index arithmetic wrong, and the problem’s statement can mislead beginners into thinking a full permutation is needed.  
> **Ugly** – The subtlety that only *adjacent* swaps are allowed, and that a row’s trailing zeros must be *at least* a value, can trip you up if you’re not careful with invariants.

When you present **LeetCode 1536** in an interview, start by narrating the **invariant**, then slide into the **one‑dimensional array** approach. Keep your indices clean, update the moved row’s trailing zeros, and you’ll earn that “nice solution” badge – and a potential job offer.

Happy coding, and may your next interview be a breeze! 🚀

---


--- 

*Feel free to drop your own edge‑case or alternative solution in the comments – we love a good challenge!*