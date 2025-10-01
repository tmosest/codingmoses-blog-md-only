---
title: LeetCode 3621. Number of Integers With Popcount-Depth Equal to K I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🧩 Problem Overview – 3621. Number of Integers With Popcount‑Depth Equal to *k*

**Definition**  
For a positive integer `x`

```
p0 = x
pi+1 = popcount(pi)          // popcount(y) = number of 1‑bits in y
```

The sequence stops when we first reach `1`.  
The **popcount‑depth** of `x` is the minimum `d` such that `pd = 1`.

**Task**  
Given `n (1 ≤ n ≤ 10^15)` and `k (0 ≤ k ≤ 5)`, count how many integers `x` in `[1, n]` have popcount‑depth exactly `k`.

> *Why is `k ≤ 5`?*  
> The binary length of any `x ≤ 10^15` is ≤ 50, so the popcount of `x` is ≤ 50.  
> Re‑applying popcount reduces the value to at most 6 (because 2^6 = 64).  
> Consequently, the depth never exceeds 5.

---

## 🎯 High‑Level Insight

The depth of a number depends **only** on the number of 1‑bits in its binary representation.  
Therefore we can:

1. **Pre‑compute** which popcount values (`c`) have a depth of `k‑1`.  
   (We need `k‑1` because the first popcount turns the original number into `c`.)

2. Count all numbers `x ≤ n` whose number of 1‑bits (`c`) belongs to that set.

Counting numbers with a specific number of 1‑bits up to `n` is a classic *Digit DP* on the binary representation of `n`.

---

## 📊 DP State

| Index | Meaning                               | Size |
|-------|---------------------------------------|------|
| `pos` | Current bit position (0 … len-1)      | ≤ 50 |
| `cnt` | Number of 1‑bits chosen so far        | ≤ 50 |
| `tight` | `1` if prefix equals `n`’s prefix | 2 |

Memoization table: `dp[pos][cnt][tight]`.

**Transition**  
At bit `pos` we can set it to `0` or `1` (respecting the `tight` constraint).  
If we set it to `1`, increment `cnt`.  
Move to `pos+1`.

At the end (`pos == len`), we accept the number iff `cnt` is in the pre‑computed set.

---

## 🔍 Edge Cases

| `k` | Result |
|-----|--------|
| 0   | Only `1` has depth `0`. If `n ≥ 1` answer is `1`; otherwise `0`. |
| 1   | We must exclude `1` itself because its popcount is `1` (depth 0). |
| `k > 1` | Normal DP. |

---

## ⚙️ Complexity

```
Time   :  O( log₂ n * 64 )   <= 3·10⁴ operations
Memory :  O( log₂ n * 64 )   <= 3·10⁴ integers
```

Both are trivial for the given limits.

---

## 🛠️ Reference Implementations

Below you’ll find a clean, commented solution in **Java**, **Python**, and **C++**.  
All use the same DP idea described above.

---

### Java 17

```java
import java.util.*;

public class Solution {
    private long[][][] memo;   // memo[pos][cnt][tight]
    private boolean[][][] vis;
    private String binary;
    private Set<Integer> target;   // popcount values with depth k-1

    public long popcountDepth(long n, int k) {
        if (k == 0) return n >= 1 ? 1 : 0;   // only 1 has depth 0

        // Step 1: find all popcount values whose depth is k-1
        target = new HashSet<>();
        for (int c = 1; c < 64; ++c) {
            if (depth(c) == k - 1) target.add(c);
        }
        if (target.isEmpty()) return 0;

        binary = Long.toBinaryString(n);
        int len = binary.length();

        memo = new long[len + 1][len + 1][2];
        vis  = new boolean[len + 1][len + 1][2];

        long ans = dfs(0, 0, 1);            // start from MSB, tight=1
        if (k == 1) ans -= 1;               // exclude number 1 itself
        return ans;
    }

    // depth of a number (number of times we need to popcount until 1)
    private int depth(int x) {
        int d = 0;
        while (x > 1) {
            x = Integer.bitCount(x);
            d++;
        }
        return d;
    }

    private long dfs(int pos, int cnt, int tight) {
        if (pos == binary.length()) {
            return target.contains(cnt) ? 1 : 0;
        }
        if (vis[pos][cnt][tight]) return memo[pos][cnt][tight];
        vis[pos][cnt][tight] = true;

        int limit = tight == 1 ? binary.charAt(pos) - '0' : 1;
        long res = 0;
        for (int bit = 0; bit <= limit; ++bit) {
            int nextTight = (tight == 1 && bit == limit) ? 1 : 0;
            res += dfs(pos + 1, cnt + bit, nextTight);
        }
        return memo[pos][cnt][tight] = res;
    }
}
```

---

### Python 3

```python
from functools import lru_cache
from math import comb

class Solution:
    def popcountDepth(self, n: int, k: int) -> int:
        if k == 0:                       # only number 1
            return 1 if n >= 1 else 0

        # Pre‑compute all popcount values whose depth is k-1
        target = {c for c in range(1, 64) if self._depth(c) == k - 1}
        if not target:
            return 0

        bin_n = bin(n)[2:]
        L = len(bin_n)

        @lru_cache(None)
        def dfs(pos: int, cnt: int, tight: int) -> int:
            if pos == L:
                return 1 if cnt in target else 0
            limit = int(bin_n[pos]) if tight else 1
            total = 0
            for bit in (0, 1):
                if bit > limit: break
                total += dfs(pos + 1, cnt + bit,
                              tight and bit == limit)
            return total

        ans = dfs(0, 0, 1)
        if k == 1:                       # exclude number 1 itself
            ans -= 1
        return ans

    @staticmethod
    def _depth(x: int) -> int:
        d = 0
        while x > 1:
            x = bin(x).count('1')
            d += 1
        return d
```

---

### C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
    string binary;                 // binary representation of n
    unordered_set<int> target;     // popcount values with depth k-1
    vector<vector<vector<long long>>> memo;
    vector<vector<vector<bool>>> vis;

    // popcount depth of an integer
    int depth(int x) {
        int d = 0;
        while (x > 1) {
            x = __builtin_popcount(x);
            ++d;
        }
        return d;
    }

    long long dfs(int pos, int cnt, int tight) {
        if (pos == (int)binary.size()) {
            return target.count(cnt) ? 1LL : 0LL;
        }
        if (vis[pos][cnt][tight]) return memo[pos][cnt][tight];
        vis[pos][cnt][tight] = true;

        int limit = tight ? binary[pos] - '0' : 1;
        long long res = 0;
        for (int bit = 0; bit <= limit; ++bit) {
            int nextTight = (tight && bit == limit) ? 1 : 0;
            res += dfs(pos + 1, cnt + bit, nextTight);
        }
        return memo[pos][cnt][tight] = res;
    }

public:
    long long popcountDepth(long long n, int k) {
        if (k == 0) return n >= 1 ? 1 : 0;

        target.clear();
        for (int c = 1; c < 64; ++c)
            if (depth(c) == k - 1) target.insert(c);

        if (target.empty()) return 0;

        binary = to_string(n);
        // Convert to binary string
        string bin_n = "";
        long long tmp = n;
        while (tmp) { bin_n.push_back((tmp & 1) + '0'); tmp >>= 1; }
        reverse(bin_n.begin(), bin_n.end());
        binary = bin_n;

        int L = binary.size();
        memo.assign(L + 1, vector<vector<long long>>(L + 1, vector<long long>(2, 0)));
        vis.assign(L + 1, vector<vector<bool>>(L + 1, vector<bool>(2, false)));

        long long ans = dfs(0, 0, 1);
        if (k == 1) ans -= 1;           // exclude 1 itself
        return ans;
    }
};
```

---

## 📚 The Blog: Good, Bad & Ugly – SEO Edition

> **Title (SEO‑Friendly)**  
> *Mastering LeetCode 3621: Popcount‑Depth DP – Good, Bad, & Ugly Tricks*  

---

### 1️⃣ Why This Problem Is a “Gold‑Mine” for DP

- **Pure combinatorics** – The depth depends solely on bit‑count.  
- **Tiny state space** – With `n ≤ 10^15`, we only need 50 bits.  
- **Clear teaching moment** – Demonstrates how to transform a seemingly numeric problem into a *Digit DP* by spotting the underlying invariant (bit‑count).

---

### 2️⃣ The “Good” – What Makes It Elegant

| Good | Explanation |
|------|-------------|
| **Linear pre‑computation** | Only 63 popcount values to check; trivial depth recursion. |
| **Binary DP** | Re‑uses a single DP for all `k`; no extra loops. |
| **Modular code** | `depth()` helper keeps the logic clean; DP is isolated. |
| **Language‑agnostic pattern** | The same 3‑dim DP works in Java, Python, C++ – a great interview showcase. |

---

### 3️⃣ The “Bad” – Pitfalls & What to Avoid

| Bad | Why it’s a problem |
|-----|--------------------|
| **Wrong target set** | Forgetting that depth of the *original* number is `k-1` will miscount. |
| **Off‑by‑one with tight** | Mixing up strictness (tight==1 means prefix equal, not strictly smaller). |
| **Neglecting 1** | Including `1` in the count for `k=1` is a subtle bug many novices make. |
| **No memoization** | A naive recursion would revisit `O(2^50)` states → impossible. |

---

### 4️⃣ The “Ugly” – What Might Look Confusing at First

- The recursive `dfs` function has three parameters (`pos`, `cnt`, `tight`).  
  Some solutions pack `tight` into the third dimension of the memo array; others use a boolean or integer flag.  
  Either way, the idea is the same: *if the prefix is already smaller, you can freely choose `0` or `1`.*

- Pre‑computing depths with a memoised `depth()` function is optional.  
  A straight‑forward loop with `bit_count` is perfectly fine because the number of iterations is tiny.

- The final `ans -= 1` for `k==1` is the only *special* correction.  
  Some codebases even *hard‑code* the exclusion of `1`, but doing it once after the DP keeps the logic clear.

---

## 📈 SEO Highlights

- **Keyword focus**: *LeetCode 3621*, *popcount depth*, *digit DP*, *binary DP*, *C++ solution*, *Python solution*, *Java solution*.
- **Meta‑description** (≈150 chars):

> “Solve LeetCode 3621 (popcount‑depth) in Java, Python & C++. Learn the DP trick, edge‑case handling, and a full‑code reference. Great for interview prep!”

- **Header structure**: H1 for the title, H2 for sections, H3 for language code blocks – all search‑engine friendly.

---

## 📌 Take‑away Checklist

1. **Pre‑compute** target popcount values (`c`) with depth `k-1`.
2. **Binary Digit DP** to count numbers `≤ n` having that exact number of 1‑bits.
3. Handle `k = 0, 1` specially.
4. Complexity stays tiny (< 3·10⁴ ops) – fits easily in LeetCode time limits.

Happy coding! 🚀