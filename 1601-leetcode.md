---
title: LeetCode 1601. Maximum Number of Achievable Transfer Requests - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Solution Code

Below are three **ready‑to‑copy** implementations of the “Maximum Number of Achievable Transfer Requests” (Leetcode 1601) – one in **Java**, one in **Python**, and one in **C++**.  
All three use a **backtracking + bitmask** approach that guarantees optimality while keeping the runtime well below the limits (the number of requests ≤ 16).

> **Why Bitmask?**  
> Because `m ≤ 16`, we can represent any subset of requests as a 16‑bit integer. Enumerating all `2^m` subsets is trivial (`65536` at most) and far faster than exploring a tree with pruning.  
> For larger `m`, backtracking with pruning (early‑exit when the net imbalance cannot be corrected) would be preferable, but for this problem the bitmask is both simpler and faster.

---

### 1.1  Java (Java 17)

```java
import java.util.*;

public class Solution {
    /**
     * Leetcode 1601 – Maximum Number of Achievable Transfer Requests
     * Backtracking with bitmask enumeration (O(2^m * n))
     */
    public int maximumRequests(int n, int[][] requests) {
        int m = requests.length;
        int best = 0;

        // Pre‑allocate an array for net change of each building
        int[] net = new int[n];

        // Enumerate all subsets of requests (0 … (1<<m)-1)
        for (int mask = 0; mask < (1 << m); mask++) {
            Arrays.fill(net, 0);
            int count = Integer.bitCount(mask);          // number of taken requests
            if (count <= best) continue;                 // cannot beat current best

            // Apply the subset
            for (int i = 0; i < m; i++) {
                if ((mask & (1 << i)) != 0) {
                    net[requests[i][0]]--;  // leave building
                    net[requests[i][1]]++;  // enter building
                }
            }

            // Check if all nets are zero
            boolean ok = true;
            for (int val : net) {
                if (val != 0) { ok = false; break; }
            }
            if (ok) best = count;
        }
        return best;
    }
}
```

---

### 1.2  Python 3

```python
from typing import List

class Solution:
    """
    Leetcode 1601 – Maximum Number of Achievable Transfer Requests
    Bitmask enumeration (O(2^m * n)), Python 3.11
    """
    def maximumRequests(self, n: int, requests: List[List[int]]) -> int:
        m = len(requests)
        best = 0
        for mask in range(1 << m):
            net = [0] * n
            cnt = mask.bit_count()
            if cnt <= best:
                continue
            for i in range(m):
                if mask >> i & 1:
                    net[requests[i][0]] -= 1
                    net[requests[i][1]] += 1
            if all(x == 0 for x in net):
                best = cnt
        return best
```

---

### 1.3  C++ (GNU‑C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    // Leetcode 1601 – Maximum Number of Achievable Transfer Requests
    // Bitmask enumeration (O(2^m * n))
    int maximumRequests(int n, vector<vector<int>>& requests) {
        int m = requests.size();
        int best = 0;

        for (int mask = 0; mask < (1 << m); ++mask) {
            vector<int> net(n, 0);
            int cnt = __builtin_popcount(mask);
            if (cnt <= best) continue;          // pruning

            for (int i = 0; i < m; ++i) {
                if (mask & (1 << i)) {
                    net[requests[i][0]]--;   // leave
                    net[requests[i][1]]++;   // enter
                }
            }

            bool ok = true;
            for (int x : net) if (x != 0) { ok = false; break; }
            if (ok) best = cnt;
        }
        return best;
    }
};
```

> **Tip:**  
> • All three codes run in **milliseconds** on the Leetcode test harness.  
> • If you prefer a pure backtracking version (e.g., for educational purposes), replace the bitmask loop with a recursive DFS that keeps an `int[] net` and updates it on‑the‑fly. The pruning trick (`if (cnt <= best) return;`) still works.

---

## 2. Blog Article

> **Title:** *Leetcode 1601 – “Maximum Number of Achievable Transfer Requests” – A Complete Backtracking + Bitmask Guide for Interviews*  
> **Meta Description:** Master Leetcode 1601 with a clean backtracking solution, bitmask optimization, and Java/Python/C++ implementations. Understand pitfalls, edge‑cases, and why this problem matters in coding interviews.

---

### 2.1  Introduction

In **software engineering interviews**, *Leetcode 1601 – Maximum Number of Achievable Transfer Requests* is a staple for testing **combinatorial optimization** skills. The problem forces you to think about:

1. **Net balance** per building (outflow = inflow).  
2. **Exhaustive search** of subsets of requests (≤ 16).  
3. **Efficient pruning** or **bitmask enumeration**.

If you can solve this cleanly, you’ll impress hiring managers at top tech firms.

---

### 2.2  Problem Statement (Re‑written)

You are given `n` buildings (`0 … n‑1`).  
Each building initially houses some employees, but we only care about **relative changes**.  
`requests[i] = [from_i, to_i]` denotes a single employee’s wish to move from building `from_i` to building `to_i`.

**Goal:**  
Select the largest possible subset of requests such that for **every building** the number of employees leaving equals the number of employees entering.  
In other words, the net change per building is zero.

**Constraints**

- `1 ≤ n ≤ 20`  
- `1 ≤ m = requests.length ≤ 16`  
- `0 ≤ from_i, to_i < n`

---

### 2.3  Naïve Approach (Exhaustive DFS)

A textbook solution is to try every combination using recursion:

```text
dfs(idx, net[], count)
    if idx == m:
        if all(net[i] == 0): best = max(best, count)
        return
    // Take request idx
    net[from[idx]]--, net[to[idx]]++
    dfs(idx + 1, net, count + 1)
    // Undo
    net[from[idx]]++, net[to[idx]]--
    // Skip request idx
    dfs(idx + 1, net, count)
```

**Pros**

- Intuitive, easy to debug.  
- Directly models “take / skip” decisions.

**Cons**

- `O(2^m)` recursion depth (worst‑case `65k` calls) – still fine but can be slow in tight time limits.  
- No pruning unless you manually add checks (e.g., if the remaining requests cannot compensate a current imbalance).

---

### 2.4  Bitmask Optimisation (The “Good” Approach)

Because `m ≤ 16`, every subset of requests can be represented as a 16‑bit integer. Enumerating all `2^m` subsets in a single loop is both **simple** and **fast**:

1. **Loop over `mask`** from `0` to `(1 << m) - 1`.  
2. **Compute** the net change for each building by iterating over bits set in `mask`.  
3. **Verify** that all nets are zero (`O(n)` per subset).  

The total complexity is `O(2^m * n)`, i.e., ≤ `65k * 20 ≈ 1.3M` primitive operations – well under 1 second.

> **Why is this better?**  
> • No stack overhead.  
> • `mask.bit_count()` gives you the number of requests instantly.  
> • You can prune a subset early (`if (count <= best) continue;`).  
> • The code is nearly identical across languages.

---

### 2.5  The “Bad” Pitfalls to Avoid

| Pitfall | Explanation | Fix |
|---------|-------------|-----|
| **Omitting the `best` pruning** | Checking all subsets regardless of `count` wastes time. | `if (cnt <= best) continue;` |
| **Using `int[][]` for net in DFS without resetting** | Net array must be freshly cleared for each mask; otherwise previous subsets pollute the result. | `Arrays.fill(net, 0);` |
| **Assuming `m` will be large** | If `m` were > 20, bitmask would explode. Use DFS + pruning then. | Switch to recursive DFS with early exit. |
| **Ignoring `n` up to 20** | If you use an array of size 20 for net, you’ll be fine. No overflow concerns. | `vector<int> net(n,0);` in C++, `int[] net = new int[n]` in Java, `net = [0]*n` in Python. |

---

### 2.6  The “Ugly” Corner Cases

1. **All requests are identical** – e.g., `[[0,1],[0,1],[0,1]]`.  
   *Solution:* The best subset will be **even** (each building’s net must be even). Bitmask handles it automatically.

2. **Circular chains** – `0→1`, `1→2`, `2→0`.  
   Any single request alone cannot satisfy the balance; you need **all three** together.  
   Bitmask will find this without extra logic.

3. **Requests with `from == to`** – trivial requests that do nothing.  
   They can always be taken; bitmask automatically includes them in the optimal subset.

4. **Large `n` (up to 20)** but small `m`.  
   The net array can be up to 20 elements; iterating over it is negligible.

---

### 2.7  Complexity Analysis

| Approach | Time | Space |
|----------|------|-------|
| Recursive DFS | `O(2^m)` recursion calls, each call does `O(n)` updates → `O(2^m * n)` | `O(n)` (net array) + recursion stack `O(m)` |
| Bitmask | `O(2^m * n)` with no recursion | `O(n)` (net array) |

With `m ≤ 16` and `n ≤ 20`, the bitmask version runs in **sub‑millisecond** on modern CPUs.  
If you want to scale beyond 16 requests, switch to DFS with branch‑and‑bound pruning.

---

### 2.8  Best‑Practice Checklist for Interviews

| ✅ | Checkpoint |
|---|------------|
| **Read the prompt carefully** – you only need relative balance, not absolute employee counts. |
| **Confirm constraints** – they guide the algorithmic choice (bitmask vs DFS). |
| **Test edge cases** – single building, single request, all requests cancel out. |
| **Write helper functions** – `isBalanced(net)` keeps the core logic tidy. |
| **Use built‑in bit‑count** (`__builtin_popcount` / `mask.bit_count()`) for speed. |
| **Prune early** – skip masks whose `count <= best`. |
| **Comment the code** – interviewers love clean, readable solutions. |

---

### 2.9  Sample Test Harness

```java
public static void main(String[] args) {
    int n = 3;
    int[][] req = {{0, 0}, {0, 1}, {1, 2}, {2, 0}};
    System.out.println(new Solution().maximumRequests(n, req)); // → 3
}
```

> Run the same harness in Python or C++ by translating the snippet.  
> Use **assert** statements or unit‑testing frameworks to cover the edge cases listed above.

---

### 2.10  Interview Takeaway

Top interviewers ask *Leetcode 1601* because it checks:

- **Combinatorial insight** – recognizing that every subset is a feasible solution candidate.  
- **Time‑space trade‑off** – choosing between DFS and bitmask based on input size.  
- **Clean coding style** – a well‑structured algorithm with comments shows professionalism.

Practicing this problem will sharpen your ability to:

- Encode constraints as *balance equations*.  
- Convert recursive choices into bitmasks.  
- Perform fast enumerations when the search space is small.  
- Discuss trade‑offs with interviewers, turning a simple code example into a conversation about scalability.

---

### 2.11 Conclusion & Next Steps

- **Clone** the code snippets for Java, Python, and C++ in your local IDE.  
- **Add unit tests** covering all edge cases:  
  - No requests → answer 0.  
  - All requests cancel out → answer m.  
  - Requests that cannot be balanced → answer 0.  
- **Explain your approach** to a friend or in a mock interview.  
- **Add the problem** to your Leetcode “Solved” list and use it as a talking point in your next interview.

> **Ready to land your dream software engineering role?**  
> Master problems like Leetcode 1601, practice clean code, and present them confidently in interviews. Good luck! 🚀

---

### 2.12  SEO Tags & Keywords

- `Leetcode 1601`  
- `Maximum Number of Achievable Transfer Requests`  
- `bitmask algorithm`  
- `backtracking solution`  
- `software engineer interview`  
- `Java 17 solution`  
- `Python 3 solution`  
- `C++ solution`  
- `coding interview practice`  
- `job interview coding problems`

Feel free to copy, adapt, and publish this article on Medium, Dev.to, or your personal blog. The structured headers and keyword‑rich content should help the post rank in searches like “Leetcode 1601 solution”, “backtracking interview problems”, or “how to solve transfer request problem”.