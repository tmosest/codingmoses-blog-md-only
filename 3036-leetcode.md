---
title: LeetCode 3036. Number of Subarrays That Match a Pattern II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 “Number of Subarrays That Match a Pattern II” – LeetCode 3036  
## The Good, the Bad, and the Ugly – A Full‑Stack Solution (Java / Python / C++)  

> **Why this post?**  
> If you’re hunting for a software‑engineering job, this article shows you how to solve a real‑world interview problem *in three languages* while also giving you a deeper understanding of pattern matching (KMP + rolling hash). Use the code snippets, add them to your portfolio, and impress hiring managers.

---

## 1. Problem Recap

> **LeetCode 3036** – *Number of Subarrays That Match a Pattern II*  
> Given an integer array `nums` (size ≥ 2) and a pattern array `pattern` (values ∈ {-1, 0, 1}), count all sub‑arrays of length `m+1` that match the pattern.  
> **Pattern rule**: for each `k` in `[0, m-1]`  
> * `pattern[k] == 1` → `nums[i+k+1] > nums[i+k]`  
> * `pattern[k] == 0` → `nums[i+k+1] == nums[i+k]`  
> * `pattern[k] == -1` → `nums[i+k+1] < nums[i+k]`

**Example**

```
nums     = [1, 2, 1, 3, 4]
pattern  = [1, -1, 1]     // m = 3
// hills array (comparisons between neighbours) : [1, -1, 1, 1]
           // (1>2? 1) (1==2? 0) (3>1? 1) (4>3? 1)
```

**Answer**: `1` (only `[1,2,1,3]` matches).

---

## 2. The Big Insight

The pattern only cares about *comparisons between adjacent elements*.  
If we compress `nums` into a new array of “hills” (`-1, 0, 1` for `<, =, >`), the problem becomes a classic **string pattern matching** problem:

```
hills[0..n-2] = sign(nums[i+1] - nums[i])   // sign ∈ {-1,0,1}
```

Now we just need to count how many times `pattern` appears inside `hills`.  
Two fast algorithms are perfect here:

1. **Knuth–Morris–Pratt (KMP)** – `O(n+m)` time, `O(m)` space.  
2. **Rolling‑Hash** (Rabin‑Karp) – `O(n)` time, constant space, collision‑safe.

Both are presented below in **Java, Python, and C++**.

---

## 2. The Good – KMP Solution

| Language | File | Complexity | Notes |
|----------|------|------------|-------|
| **Java** | `KMPSolution.java` | `O(n+m)` time, `O(m)` extra space | Clean, OOP‑friendly, ready for interview |
| **Python** | `kmp_solution.py` | `O(n+m)` time, `O(m)` space | Uses `list` slicing, great for CPython |
| **C++** | `KMPSolution.cpp` | `O(n+m)` time, `O(m)` space | STL‑friendly, works with g++17 |

> **Why KMP?**  
> KMP guarantees that each element of the “hills” array is examined at most twice (once when building the LPS table, once when scanning). This is optimal for the constraints (`n` ≤ 10⁵).  

---

### 2.1 Java – `KMPSolution.java`

```java
import java.util.*;

public class KMPSolution {

    /** Build the longest prefix‑suffix (lps) array for the pattern. */
    private static void buildLPS(int[] pat, int[] lps) {
        int m = pat.length, len = 0;
        lps[0] = 0;                         // lps[0] is always 0

        for (int i = 1; i < m; i++) {
            while (len > 0 && pat[i] != pat[len]) {
                len = lps[len - 1];
            }
            if (pat[i] == pat[len]) len++;
            lps[i] = len;
        }
    }

    /** Convert nums into “hills” (sign of consecutive pairs). */
    private static int[] buildHills(int[] nums) {
        int n = nums.length;
        int[] hills = new int[n - 1];
        for (int i = 0; i < n - 1; i++) {
            if (nums[i + 1] > nums[i]) hills[i] = 1;
            else if (nums[i + 1] < nums[i]) hills[i] = -1;
            else hills[i] = 0;
        }
        return hills;
    }

    /** Main method – counts pattern matches. */
    public static int countMatchingSubarrays(int[] nums, int[] pattern) {
        int n = nums.length, m = pattern.length;          // sub‑array length = m+1
        int[] hills = buildHills(nums);                    // length n-1

        // Edge case: if pattern is longer than hills array → 0 matches
        if (m > hills.length) return 0;

        // Build LPS for pattern
        int[] lps = new int[m];
        buildLPS(pattern, lps);

        int i = 0, j = 0, ans = 0;                       // i → hills index, j → pattern index
        while (i < hills.length) {
            if (hills[i] == pattern[j]) {                // chars match
                i++; j++;
            } else if (j > 0) {                          // mismatch after some matches
                j = lps[j - 1];
            } else {                                     // mismatch at start
                i++;
            }

            // When j reaches m → a full pattern match found
            if (j == m) {
                ans++;
                j = lps[j - 1];                          // allow overlapping matches
            }
        }
        return ans;
    }

    /* -------------------  Demo ------------------- */
    public static void main(String[] args) {
        int[] nums = {1, 2, 1, 3, 4};
        int[] pattern = {1, -1, 1};
        System.out.println("Matches: " + countMatchingSubarrays(nums, pattern));
        // → 1
    }
}
```

> **Key Take‑aways**  
> * `buildLPS` is *O(m)* – the heart of KMP.  
> * `buildHills` is *O(n)* – linear pre‑processing.  
> * Final scan is *O(n)*, so the whole algorithm is linear.

---

### 2.2 C++ – `KMPSolution.cpp`

```cpp
#include <bits/stdc++.h>
using namespace std;

class KMPSolution {
public:
    // Build longest prefix‑suffix table
    static void buildLPS(const vector<int>& pat, vector<int>& lps) {
        int m = pat.size(), len = 0;
        lps[0] = 0;
        for (int i = 1; i < m; ++i) {
            while (len > 0 && pat[i] != pat[len]) len = lps[len - 1];
            if (pat[i] == pat[len]) ++len;
            lps[i] = len;
        }
    }

    // Convert nums into hills (comparisons between neighbours)
    static vector<int> buildHills(const vector<int>& nums) {
        int n = nums.size();
        vector<int> hills(n - 1);
        for (int i = 0; i < n - 1; ++i) {
            if (nums[i + 1] > nums[i]) hills[i] = 1;
            else if (nums[i + 1] < nums[i]) hills[i] = -1;
            else hills[i] = 0;
        }
        return hills;
    }

    static int countMatchingSubarrays(const vector<int>& nums, const vector<int>& pattern) {
        int n = nums.size();
        int m = pattern.size();           // pattern length
        if (m == 0) return 0;             // pattern empty → no sub‑array

        vector<int> hills = buildHills(nums);   // length n-1
        if (m > (int)hills.size()) return 0;    // pattern longer than possible hills

        vector<int> lps(m);
        buildLPS(pattern, lps);

        int i = 0, j = 0, ans = 0;
        while (i < (int)hills.size()) {
            if (hills[i] == pattern[j]) {
                ++i; ++j;
            }
            if (j == m) {          // full match
                ++ans;
                j = lps[j - 1];    // allow overlapping
            } else if (i < (int)hills.size() && hills[i] != pattern[j]) {
                if (j > 0) j = lps[j - 1];
                else ++i;
            }
        }
        return ans;
    }
};

int main() {
    vector<int> nums = {1, 2, 1, 3, 4};
    vector<int> pattern = {1, -1, 1};
    cout << "Matches: " << KMPSolution::countMatchingSubarrays(nums, pattern) << endl;  // 1
    return 0;
}
```

> **C++ Tips**  
> * Use `std::vector<int>` – fast and STL‑friendly.  
> * The `buildHills` function is a one‑liner, but clarity is key for interviewers.  

---

### 2.3 Python – `kmp_solution.py`

```python
from typing import List

class KMPSolution:
    @staticmethod
    def build_lps(pattern: List[int]) -> List[int]:
        """Build longest prefix‑suffix (lps) table – O(m)."""
        m = len(pattern)
        lps = [0] * m
        length = 0
        i = 1
        while i < m:
            if pattern[i] == pattern[length]:
                length += 1
                lps[i] = length
                i += 1
            else:
                if length != 0:
                    length = lps[length - 1]
                else:
                    lps[i] = 0
                    i += 1
        return lps

    @staticmethod
    def build_hills(nums: List[int]) -> List[int]:
        """Sign of consecutive differences – O(n)."""
        return [1 if nums[i+1] > nums[i] else (-1 if nums[i+1] < nums[i] else 0)
                for i in range(len(nums) - 1)]

    @classmethod
    def count_matching_subarrays(cls, nums: List[int], pattern: List[int]) -> int:
        n, m = len(nums), len(pattern)
        if m == 0:                      # Empty pattern → no match
            return 0
        hills = cls.build_hills(nums)
        if m > len(hills):
            return 0

        lps = cls.build_lps(pattern)

        i = j = ans = 0
        while i < len(hills):
            if hills[i] == pattern[j]:
                i += 1
                j += 1
            if j == m:                 # full match
                ans += 1
                j = lps[j - 1]          # overlapping allowed
            elif i < len(hills) and hills[i] != pattern[j]:
                if j != 0:
                    j = lps[j - 1]
                else:
                    i += 1
        return ans

# ---- Demo ----
if __name__ == "__main__":
    nums = [1, 2, 1, 3, 4]
    pattern = [1, -1, 1]
    print("Matches:", KMPSolution().count_matching_subarrays(nums, pattern))
    # → 1
```

> **Python Specifics**  
> * Avoiding `while True` loops keeps the logic easy to trace.  
> * List comprehension for `build_hills` – Pythonic and fast.  

---

## 3. The Good‑Plus – Rolling‑Hash (Rabin‑Karp) Solution

The rolling‑hash algorithm is a *single pass* solution, no LPS table needed.  
We use **base = 3** (since our alphabet is `{-1, 0, 1}`) and a large prime modulus (`10⁹+7`) to avoid collisions.

### 3.1 Java – `RollingHashSolution.java`

```java
public class RollingHashSolution {
    private static final long MOD = 1_000_000_007L;
    private static final long BASE = 3L;   // > alphabet size

    public static int countMatchingSubarrays(int[] nums, int[] pattern) {
        int n = nums.length, m = pattern.length;
        if (m == 0) return 0;

        // Build hills
        int[] hills = new int[n - 1];
        for (int i = 0; i < n - 1; i++) {
            if (nums[i + 1] > nums[i]) hills[i] = 1;
            else if (nums[i + 1] < nums[i]) hills[i] = -1;
            else hills[i] = 0;
        }

        // Pre‑compute powers of BASE
        long[] pow = new long[hills.length + 1];
        pow[0] = 1;
        for (int i = 1; i <= hills.length; i++) pow[i] = (pow[i-1] * BASE) % MOD;

        // Compute pattern hash
        long patHash = 0;
        for (int v : pattern) {
            patHash = (patHash * BASE + (v + 1)) % MOD;   // shift by +1 to make 0→1, 1→2, -1→0
        }

        // Compute rolling hash over hills
        long curHash = 0;
        int ans = 0;
        for (int i = 0; i < hills.length; i++) {
            curHash = (curHash * BASE + (hills[i] + 1)) % MOD;

            if (i >= m - 1) {
                if (curHash == patHash) {
                    // verify to avoid accidental collisions
                    boolean match = true;
                    for (int j = 0; j < m; j++) {
                        if (hills[i - m + 1 + j] != pattern[j]) {
                            match = false; break;
                        }
                    }
                    if (match) ++ans;
                }
                // Slide window: remove highest power
                curHash = (curHash - ((hills[i - m + 1] + 1) * pow[m]) % MOD + MOD) % MOD;
            }
        }
        return ans;
    }

    /* Demo */
    public static void main(String[] args) {
        int[] nums = {1,2,1,3,4};
        int[] pattern = {1,-1,1};
        System.out.println(countMatchingSubarrays(nums, pattern));  // 1
    }
}
```

> **Rolling‑Hash Notes**  
> * `+1` shift maps `-1→0`, `0→1`, `1→2`.  
> * `pow[m]` is pre‑computed for fast window sliding.  
> * Final verification loop (inside window) guarantees correctness even under hash collision.

---

### 3.2 C++ – `RollingHashSolution.cpp`

```cpp
#include <bits/stdc++.h>
using namespace std;

class RollingHashSolution {
public:
    static const long long MOD = 1000000007LL;
    static const long long BASE = 3LL;     // > alphabet size

    static vector<int> buildHills(const vector<int>& nums) {
        int n = nums.size();
        vector<int> hills(n - 1);
        for (int i = 0; i < n - 1; ++i) {
            if (nums[i + 1] > nums[i]) hills[i] = 1;
            else if (nums[i + 1] < nums[i]) hills[i] = -1;
            else hills[i] = 0;
        }
        return hills;
    }

    static int countMatchingSubarrays(const vector<int>& nums, const vector<int>& pattern) {
        vector<int> hills = buildHills(nums);
        int m = pattern.size();
        if (m == 0 || m > (int)hills.size()) return 0;

        // Pre‑compute powers
        vector<long long> pow(m + 1, 1);
        for (int i = 1; i <= m; ++i) pow[i] = (pow[i-1] * BASE) % MOD;

        // Pattern hash
        long long patHash = 0;
        for (int v : pattern) patHash = (patHash * BASE + (v + 1)) % MOD;

        long long curHash = 0;
        int ans = 0;
        for (int i = 0; i < (int)hills.size(); ++i) {
            curHash = (curHash * BASE + (hills[i] + 1)) % MOD;
            if (i >= m - 1) {
                if (curHash == patHash) {
                    // verify to avoid collision
                    bool ok = true;
                    for (int j = 0; j < m; ++j)
                        if (hills[i - m + 1 + j] != pattern[j]) { ok = false; break; }
                    if (ok) ++ans;
                }
                // Remove oldest element from window
                curHash = (curHash - ((hills[i - m + 1] + 1) * pow[m]) % MOD + MOD) % MOD;
            }
        }
        return ans;
    }
};

int main() {
    vector<int> nums = {1, 2, 1, 3, 4};
    vector<int> pattern = {1, -1, 1};
    cout << "Matches: " << RollingHashSolution::countMatchingSubarrays(nums, pattern) << endl; // 1
}
```

---

### 2.4 Python – `rolling_hash.py`

```python
MOD = 10**9 + 7
BASE = 3          # > alphabet size

def build_hills(nums):
    return [1 if nums[i+1] > nums[i] else (-1 if nums[i+1] < nums[i] else 0) for i in range(len(nums)-1)]

def count_matches(nums, pattern):
    n, m = len(nums), len(pattern)
    hills = build_hills(nums)
    if m == 0 or m > len(hills): return 0

    # Pre‑compute powers of BASE
    pow_base = [1] * (m+1)
    for i in range(1, m+1):
        pow_base[i] = (pow_base[i-1] * BASE) % MOD

    # Hash of pattern
    pat_hash = 0
    for v in pattern:
        pat_hash = (pat_hash * BASE + (v + 1)) % MOD

    cur = 0
    ans = 0
    for i, val in enumerate(hills):
        cur = (cur * BASE + (val + 1)) % MOD
        if i >= m-1:
            if cur == pat_hash:                          # possible match
                # Verify – ensures collision safety
                if all(hills[i-m+1+j] == pattern[j] for j in range(m)):
                    ans += 1
            # Slide window: remove highest power
            cur = (cur - ((hills[i-m+1] + 1) * pow_base[m]) % MOD + MOD) % MOD
    return ans

# Demo
print(count_matches([1,2,1,3,4], [1,-1,1]))   # 1
```

---

## 4. The Big Picture

| Approach | Complexity | Space | Notes |
|----------|------------|-------|-------|
| **KMP** | `O(n + m)` | `O(m)` | Classic pattern‑matching, no hash needed |
| **Rolling Hash** | `O(n)` | `O(m)` | Single pass; verify window to avoid collisions |
| **Optimized** | `O(n)` | `O(m)` | Same as rolling hash but with base 3 and large prime |

All three solutions run comfortably within the given constraints:

- **Time**: `O(n)` with small constant factors
- **Memory**: `O(n)` for the input arrays; `O(1)` extra for the algorithmic core.

---

## 5. How to Use These Files

1. **Clone / Download** the repository containing the files.
2. **Compile** the Java solutions using `javac` or run the Java demos directly.
3. **Compile** the C++ solutions using `g++ -std=c++17`.
4. **Run** the Python scripts with `python3`.

All demo sections included in the files produce the same result:  
`1` for the example `nums = [1, 2, 1, 3, 4]` and `pattern = [1, -1, 1]`.

---

### Final Thoughts

- The **KMP** algorithm gives a clear, textbook approach with no chance of collision.
- The **Rolling‑Hash** variant achieves the same goal in a single pass and may be simpler to implement in languages lacking convenient mutable integer arrays.
- Both are well‑suited for large inputs; choose the one that best fits your language environment and coding style.

Feel free to use or adapt any of these solutions for your own projects or interview preparation!