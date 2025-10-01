---
title: LeetCode 3036. Number of Subarrays That Match a Pattern II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem 3036 – *Number of Subarrays That Match a Pattern II*

**Problem URL** – <https://leetcode.com/problems/number-of-subarrays-that-match-a-pattern-ii/>

**What we need to do**

> Given an array `nums` (size *n*) and a pattern array `pattern` (size *m*, each element is `-1, 0, 1`), count how many sub‑arrays `nums[i..i+m]` satisfy the pattern:  
> • `pattern[k] = 1` → `nums[i+k+1] > nums[i+k]`  
> • `pattern[k] = 0` → `nums[i+k+1] = nums[i+k]`  
> • `pattern[k] = -1`→ `nums[i+k+1] < nums[i+k]`

The sub‑array length is always `m+1`, so we slide a window of length `m` over the *difference* sequence of `nums`.

-------------------------------------------------------------------

## 2.  Why This Problem Is a Gold‑mine for Interviews

| Why you should care | How it shows your skill |
|---------------------|------------------------|
| **Array + Pattern Matching** – a classic interview topic | Demonstrates *array manipulation*, *string/array pattern matching*, and *algorithmic thinking* |
| **LeetCode “hard” but solvable in *O(n)* time** | You’ll impress recruiters with an efficient solution |
| **Typical “gotcha”** – many candidates fall into an *O(n·m)* TLE trap | Avoid that trap and land the interview |
| **Applicable to real‑world problems** – e.g. trend detection in time series | Shows you can solve production‑grade problems |

-------------------------------------------------------------------

## 2.  High‑Level Idea – KMP on the “hill” array

1. **Convert `nums` to a “hill” array**  
   `hill[i] = sign(nums[i+1] - nums[i])`  
   where `sign(x)` = `1` if `x > 0`, `0` if `x == 0`, `-1` if `x < 0`.

2. **The pattern itself is already a sequence of `-1,0,1`**, so after step 1 we only have to find how many times the `pattern` appears in `hill`.  
   This is a classic **string‑matching** problem → we can use the **Knuth‑Morris‑Pratt (KMP)** algorithm.

3. The KMP algorithm gives **`O(n)` time** and **`O(m)` auxiliary space**.  
   (The “hill” array has length `n‑1`; we only keep a rolling window of length `m` while running KMP.)

-------------------------------------------------------------------

## 3.  The *Good* – KMP Implementation (All 3 Languages)

| Language | File | Time | Space |
|----------|------|------|-------|
| C++ | `kmp.cpp` | `O(n)` | `O(m)` |
| Java | `kmp.java` | `O(n)` | `O(m)` |
| Python | `kmp.py` | `O(n)` | `O(m)` |

### 3.1  C++ – KMP

```cpp
// kmp.cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
private:
    // Build longest-prefix‑suffix (LPS) table
    vector<int> buildLPS(const vector<int>& pat) {
        int m = pat.size();
        vector<int> lps(m, 0);
        for (int i = 1, len = 0; i < m; ) {
            if (pat[i] == pat[len]) {
                lps[i++] = ++len;
            } else if (len) {
                len = lps[len-1];
            } else {
                lps[i++] = 0;
            }
        }
        return lps;
    }

    // Convert nums into the hill array: [-1,0,1] → [-1,0,1]
    vector<int> buildHill(const vector<int>& nums) {
        int n = nums.size();
        vector<int> hill(n-1);
        for (int i = 0; i < n-1; ++i) {
            if (nums[i+1] > nums[i]) hill[i] =  1;
            else if (nums[i+1] < nums[i]) hill[i] = -1;
            else hill[i] = 0;
        }
        return hill;
    }

public:
    int countMatchingSubarrays(vector<int>& nums, vector<int>& pattern) {
        if (pattern.empty() || nums.size() <= pattern.size()) return 0;

        vector<int> hill = buildHill(nums);
        vector<int> lps = buildLPS(pattern);

        int ans = 0, i = 0, j = 0, h = hill.size(), m = pattern.size();

        while (i < h) {
            if (hill[i] == pattern[j]) {
                ++i; ++j;
            }
            if (j == m) {          // pattern found
                ++ans;
                j = lps[j-1];
            } else if (i < h && hill[i] != pattern[j]) {
                if (j) j = lps[j-1];
                else ++i;
            }
        }
        return ans;
    }
};
```

> **Why it works** – The hill array is the “signature” of adjacent comparisons.  
> The KMP algorithm guarantees that we never re‑inspect a character that we already know is a match or mismatch, giving us linear time.

-------------------------------------------------------------------

### 3.2  Java – KMP

```java
// kmp.java
import java.util.*;

public class Solution {
    /* Build longest‑prefix‑suffix (LPS) array */
    private void buildLPS(int[] pat, int[] lps) {
        int m = pat.length;
        lps[0] = 0;
        for (int i = 1, len = 0; i < m; ) {
            if (pat[i] == pat[len]) {
                lps[i++] = ++len;
            } else if (len != 0) {
                len = lps[len-1];
            } else {
                lps[i++] = 0;
            }
        }
    }

    /* Convert nums to hill array (-1,0,1) */
    private int[] buildHill(int[] nums) {
        int n = nums.length;
        int[] hill = new int[n-1];
        for (int i = 0; i < n-1; i++) {
            if (nums[i+1] > nums[i]) hill[i] = 1;
            else if (nums[i+1] < nums[i]) hill[i] = -1;
            else hill[i] = 0;
        }
        return hill;
    }

    public int countMatchingSubarrays(int[] nums, int[] pattern) {
        if (pattern.length == 0 || nums.length <= pattern.length) return 0;

        int[] hill = buildHill(nums);
        int m = pattern.length;
        int[] lps = new int[m];
        buildLPS(pattern, lps);

        int i = 0, j = 0, ans = 0;
        while (i < hill.length) {
            if (hill[i] == pattern[j]) {
                i++; j++;
            }
            if (j == m) {               // pattern matched
                ans++;
                j = lps[j-1];
            } else if (i < hill.length && hill[i] != pattern[j]) {
                if (j != 0) j = lps[j-1];
                else i++;
            }
        }
        return ans;
    }
}
```

-------------------------------------------------------------------

### 3.3  Python – KMP

```python
# kmp.py
class Solution:
    def countMatchingSubarrays(self, nums, pattern):
        if not pattern or len(nums) <= len(pattern):
            return 0

        # 1. Build hill array: -1,0,1 based on adjacent comparisons
        hill = [1 if nums[i+1] > nums[i] else -1 if nums[i+1] < nums[i] else 0
                for i in range(len(nums)-1)]

        # 2. Build LPS table for pattern
        m = len(pattern)
        lps = [0] * m
        len_lps = 0
        i = 1
        while i < m:
            if pattern[i] == pattern[len_lps]:
                len_lps += 1
                lps[i] = len_lps
                i += 1
            else:
                if len_lps:
                    len_lps = lps[len_lps-1]
                else:
                    lps[i] = 0
                    i += 1

        # 3. Run KMP over hill
        ans = 0
        i = 0  # index in hill
        j = 0  # index in pattern
        while i < len(hill):
            if hill[i] == pattern[j]:
                i += 1
                j += 1
            if j == m:
                ans += 1
                j = lps[j-1]
            elif i < len(hill) and hill[i] != pattern[j]:
                if j:
                    j = lps[j-1]
                else:
                    i += 1
        return ans
```

-------------------------------------------------------------------

## 2.  The *Good* – What You’ll Show in a Real Interview

|  Feature  |  Why it’s Good |
|-----------|----------------|
| **Linear time (`O(n)`)** | Interviewers love solutions that “don’t do the obvious nested loops.” |
| **No heavy data‑structures** | Keeps the memory footprint small (`O(m)` for the LPS table). |
| **Deterministic** | No hash‑collision risk. |
| **Reusable** | The same KMP skeleton works for any pattern‑matching problem on integer arrays. |

### Complexity Summary

| Implementation | Time | Space |
|----------------|------|-------|
| Brute‑force | `O(n · m)` | `O(1)` |
| KMP (above) | `O(n)` | `O(m)` |
| Rolling‑hash (alternative) | `O(n)` | `O(m)` (hash table) |
| **KMP** (our favourite) | **`O(n)`** | **`O(m)`** |

-------------------------------------------------------------------

## 3.  The *Bad* – Common Pitfalls

| Pitfall | Why it hurts |
|---------|--------------|
| Forgetting that the “hill” array length is `n‑1` | Mis‑aligning indices → off‑by‑one errors |
| Using `O(n·m)` nested loops | TLE on the test set |
| Using a rolling hash without handling collisions | May produce wrong answers on adversarial input |
| Not handling empty pattern or `nums.size() == pattern.size()` | Edge‑case bugs (often uncovered in tests). |

### Edge‑Case Checklist

1. `pattern` empty → answer is `0`.
2. `nums.size()` ≤ `pattern.size()` → answer is `0`.
3. `nums` of length 1 → no hill array → answer is `0`.

-------------------------------------------------------------------

## 3.  The *Alternative* – Rolling‑Hash (Optional)

If you’re comfortable with probabilistic solutions, you can replace KMP with a **rolling hash** (Rabin‑Karp) on the hill array. It’s also `O(n)` on average, but you must pick a large prime modulus and base, and be careful of overflow. For interview‑ready code, stick to KMP.

-------------------------------------------------------------------

## 3.  The *Tricky* – Rolling‑Hash Implementation (Optional)

| Language | File |
|----------|------|
| C++ | `hash.cpp` |
| Java | `hash.java` |
| Python | `hash.py` |

> **Caution** – Even though `O(n)` is promised, you might still get wrong answers if the chosen base or modulus leads to a collision. KMP is the safest choice.

-------------------------------------------------------------------

## 3.  The *Bad* – Brute‑Force (Illustrate the TLE)

|  Implementation |  Time |
|-----------------|-------|
| Brute‑force | `O(n · m)` |

```python
def brute(nums, pattern):
    hill = [1 if nums[i+1] > nums[i] else -1 if nums[i+1] < nums[i] else 0
            for i in range(len(nums)-1)]
    m = len(pattern)
    ans = 0
    for i in range(len(hill)-m+1):
        if hill[i:i+m] == pattern:
            ans += 1
    return ans
```

> **Why it’s bad** – `n` can be up to 10⁵ and `m` up to 10⁴ (in many test sets).  
> The nested loop will hit the “10⁹ operations” barrier and receive **Time‑Limit Exceeded**.

-------------------------------------------------------------------

## 3.  The *Nice* – Rolling‑Hash Alternative (for completeness)

Below is a simple **Rabin‑Karp** solution.  
It runs in linear time on average but has a **tiny probability of collision** (negligible for interview purposes if you choose a 64‑bit modulus).

### C++ – Rabin‑Karp

```cpp
// rk.cpp
class Solution {
public:
    int countMatchingSubarrays(vector<int>& nums, vector<int>& pattern) {
        if (!pattern.size() || nums.size() <= pattern.size()) return 0;
        const long long MOD = 1e9 + 7;
        const long long BASE = 91138233;  // random odd number

        // Hill array
        vector<int> hill(nums.size()-1);
        for (int i=0; i<hill.size(); ++i) {
            hill[i] = (nums[i+1] > nums[i]) - (nums[i+1] < nums[i]); // -1,0,1
        }

        // Prefix hash for hill
        vector<long long> pref(hill.size()+1, 0), pow(BASE, hill.size()+1, 1);
        for (int i=1; i<=hill.size(); ++i) pow[i] = pow[i-1]*BASE % MOD;
        for (int i=0; i<hill.size(); ++i)
            pref[i+1] = (pref[i]*BASE + (hill[i]+2)) % MOD; // shift to [1,3]

        // Hash of pattern
        long long hpat = 0;
        for (int x: pattern) hpat = (hpat*BASE + (x+2)) % MOD;

        int ans = 0;
        for (int i=0; i<=hill.size()-pattern.size(); ++i) {
            long long hsub = (pref[i+pattern.size()] - pref[i]*pow[pattern.size()]) % MOD;
            if (hsub < 0) hsub += MOD;
            if (hsub == hpat) ++ans;
        }
        return ans;
    }
};
```

> **Shift (`x+2`)** maps `{-1,0,1}` → `{1,2,3}` so we avoid negative modulo operations.

-------------------------------------------------------------------

## 3.  Final Take‑Away

1. **Use the KMP algorithm on the hill array** – that is the canonical, deterministic, and linear‑time solution.  
2. Keep your code **readable** and **compact** – the skeleton above is production‑ready.  
3. Always test the edge cases first: empty pattern, short `nums`, etc.

You’ve now got a fully‑functional, interview‑ready solution in **C++**, **Java**, and **Python**. Good luck, and happy coding!