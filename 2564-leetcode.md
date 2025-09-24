---
title: LeetCode 2564. Substring XOR Queries - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## Substring XOR Queries – LeetCode 2564  
*Master the bit‑wise trick, write clean code in Java, Python and C++, and get the blog‑ready write‑up that’s SEO‑friendly for recruiters.*

---

### 1. Problem Recap

| What | Why |
|------|-----|
| **Input** | Binary string `s` (≤ 10⁴) and `queries[i] = [firsti, secondi]` (≤ 10⁵) |
| **Task** | For each query find the *shortest* substring of `s` whose decimal value `val` satisfies `val ^ firsti == secondi`. |
| **Output** | `[lefti, righti]` (0‑based) or `[-1,-1]` if no substring exists. If multiple answers, pick the one with the smallest `lefti`. |

> **Why is it “hard” for recruiters?**  
> It tests bit‑wise algebra, prefix tricks, and a good understanding of *hash‑map* space‑time trade‑offs.  

---

### 2. The Core Insight

1. `val ^ first == second  →  val = first ^ second`  
   So every query boils down to *“find a substring with decimal value `target = first ^ second`.”*

2. The binary string has at most **30** bits that can be represented by a 32‑bit integer.  
   → For each starting position we only need to examine at most the next 30 characters.  
   This keeps the preprocessing O(n · 30).

3. We only need the *first* (left‑most) occurrence of every value.  
   → Store a single pair `[start, end]` per value in a hash‑map.  

4. **Leading zeros** – a substring that starts with `0` and is longer than 1 has a value of 0.  
   We treat it as a single special case: we store `[i,i]` for value 0 once, then skip any longer “0…0” substrings.

---

### 3. Algorithm

```
preprocess(s):
    map = {}
    for l in 0 … n-1:
        if s[l] == '0':
            map.setdefault(0, (l,l))
            continue
        val = 0
        for r in l … min(n-1, l+29):
            val = (val << 1) | (s[r] - '0')
            map.setdefault(val, (l,r))

answer(queries):
    res = []
    for first, second in queries:
        target = first ^ second
        if target in map:
            res.append(map[target])
        else:
            res.append((-1,-1))
    return res
```

*Time* = O(n · 30 + q) | *Space* = O(n · 30)  (worst‑case ≈ 3 × 10⁵ entries).

---

### 4. Reference Implementations  

#### 4.1 Java (LeetCode style)

```java
import java.util.*;

class Solution {
    public int[][] substringXorQueries(String s, int[][] queries) {
        Map<Integer, int[]> mp = new HashMap<>();
        int n = s.length();

        for (int l = 0; l < n; l++) {
            if (s.charAt(l) == '0') {           // only value 0 is valid
                mp.putIfAbsent(0, new int[]{l, l});
                continue;
            }
            int val = 0;
            for (int r = l; r < n && r - l < 30; r++) {
                val = (val << 1) | (s.charAt(r) - '0');
                mp.putIfAbsent(val, new int[]{l, r});
            }
        }

        int[][] ans = new int[queries.length][2];
        for (int i = 0; i < queries.length; i++) {
            int target = queries[i][0] ^ queries[i][1];
            int[] pair = mp.getOrDefault(target, new int[]{-1, -1});
            ans[i][0] = pair[0];
            ans[i][1] = pair[1];
        }
        return ans;
    }
}
```

#### 4.2 Python 3

```python
class Solution:
    def substringXorQueries(self, s: str, queries: List[List[int]]) -> List[List[int]]:
        mp = {}
        n = len(s)

        for l in range(n):
            if s[l] == '0':
                mp.setdefault(0, (l, l))
                continue
            val = 0
            for r in range(l, min(n, l + 30)):
                val = (val << 1) | int(s[r])
                mp.setdefault(val, (l, r))

        res = []
        for first, second in queries:
            target = first ^ second
            res.append(list(mp.get(target, (-1, -1))))
        return res
```

#### 4.3 C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<vector<int>> substringXorQueries(string s, vector<vector<int>>& queries) {
        unordered_map<int, pair<int,int>> mp;
        int n = s.size();

        for (int l = 0; l < n; ++l) {
            if (s[l] == '0') {                   // only value 0 is allowed
                mp.emplace(0, make_pair(l, l));
                continue;
            }
            int val = 0;
            for (int r = l; r < n && r - l < 30; ++r) {
                val = (val << 1) | (s[r] - '0');
                mp.emplace(val, make_pair(l, r));
            }
        }

        vector<vector<int>> ans;
        ans.reserve(queries.size());
        for (auto &q : queries) {
            int target = q[0] ^ q[1];
            auto it = mp.find(target);
            if (it != mp.end())
                ans.push_back({it->second.first, it->second.second});
            else
                ans.push_back({-1, -1});
        }
        return ans;
    }
};
```

> **Note:** `emplace` with `operator[]` behaves like `putIfAbsent` – it keeps the *left‑most* pair.

---

### 5. Blog‑Ready Write‑Up

> **Meta description (≈155 chars)**  
> “Solve LeetCode 2564 Substring XOR Queries in Java, Python & C++. 30‑bit hash‑map solution, interview tips, bitwise trick & SEO‑friendly guide for recruiters.”

---

## Substring XOR Queries – The Interview‑Ready Guide  
*(SEO keywords: **Substring XOR Queries**, **LeetCode 2564**, **binary string XOR**, **hashmap solution**, **bitwise interview problem**)*

### Table of Contents
1. What the Problem is About  
2. The Winning Insight  
3. Why This Is a Recruiter’s Favorite  
4. Detailed Algorithm & Complexity  
5. Reference Code (Java / Python / C++)  
6. “Good, Bad & Ugly” – A Critical Take  
7. How to Frame This Problem in a Resume/Interview  
8. FAQs & Edge Cases  

---

#### 1. What the Problem is About

LeetCode 2564 asks you to **look for a decimal value inside a binary string** that satisfies a simple XOR equation.  
It’s a classic *bit‑wise algebra* + *hash‑map* problem that many interviewers love to throw at candidates.

> **Key take‑away:**  
> Convert every query to a *single integer target* (`first ^ second`) and then ask: “Does `s` contain a substring with this value?”

---

#### 2. The Winning Insight

| Insight | Practical Impact |
|---------|------------------|
| `val = first ^ second` | Every query is a **lookup** – no need to examine the string at query time. |
| Binary string value fits in **30 bits** | We only examine at most 30 chars per start → **O(n · 30)** preprocessing. |
| Only the **first** (left‑most) occurrence matters | Store a single `[start, end]` per value → small, clean map. |
| **Leading zeros** produce value 0 | Handle as a special case → avoid exponential duplicate entries. |

---

#### 3. Why Recruiters Love This Problem

| Skill Tested | Why it Matters |
|--------------|----------------|
| Bit‑wise algebra | Many low‑level roles (embedded, systems) require a solid grasp of XOR, AND, OR. |
| Prefix/rolling‑hash ideas | Demonstrates ability to turn a naive O(n²) scan into O(n). |
| Hash‑map space‑time trade‑off | Shows you can think in *O(1) amortized* lookups, a core CS concept. |
| Clean Java/Python/C++ implementation | Proves you can code in multiple languages – a plus for full‑stack positions. |

---

#### 4. The “Good” – What Makes This Solution Stand Out

1. **Linear + Constant** – `O(n · 30 + q)` is essentially linear in the size of the input, which is the best you can hope for on this scale.  
2. **Simplicity** – The core idea is a single pass that builds a map; the query phase is just a dictionary lookup.  
3. **Deterministic answer** – The map stores the left‑most occurrence automatically, satisfying the “smallest left” requirement.  
4. **Language‑agnostic** – Works exactly the same in Java, Python, C++ – ideal for showcasing versatility.

---

#### 5. The “Bad” – Where the Solution Can Strain

| Issue | Impact | Mitigation |
|-------|--------|------------|
| **Map size** – Up to ~3 × 10⁵ entries | Uses ~12 MB in Java, 8 MB in Python, 8 MB in C++ (≈ 30 % of memory limit on LeetCode) | Acceptable for the constraints; if you hit memory limits, you can drop the map for values > 10⁶ (rare). |
| **30‑char limit** – Hard‑coded for 32‑bit ints | If the platform changes to 64‑bit, you’d need to extend to 60 characters | Keep the limit as a constant (`MAX_LEN = 30`). |
| **Leading zero handling** | Requires a tiny but essential branch | Document it clearly to avoid confusion during a code‑review. |

---

#### 6. The “Ugly” – Things That Feel Tricky

1. **Value 0 vs. “0…0” substrings** – It feels counter‑intuitive that any longer string of zeros still equals 0.  
2. **Duplicate values** – Two different substrings can produce the same integer value; you must guarantee the *left‑most* one wins.  
3. **Off‑by‑one in the 30‑char window** – The loop uses `r - l < 30`, which is subtle but crucial to stay within 32‑bit bounds.  
4. **Hash‑map collision** – In C++ `unordered_map` can be hit‑rate‑sensitive; reserve a large bucket count (`reserve(n*30)`) to keep amortized O(1).

---

#### 7. How to Present This in a Resume / Interview

| Section | What to Say |
|---------|-------------|
| **Problem Statement** | “Solved LeetCode 2564 – Substring XOR Queries – binary string + 100k queries.” |
| **Approach** | “Reduced each query to a single integer lookup via `target = first ^ second`. Pre‑computed the shortest substring for every possible value using a hash‑map and only examined the next 30 characters per start.” |
| **Complexity** | “O(n·30 + q) time, O(n·30) space – scales to 10⁴ string length and 10⁵ queries.” |
| **Language Proficiency** | “Implemented clean solutions in Java, Python 3 and C++17.” |
| **Result** | “Accepted on LeetCode in < 0.2 s for all test cases; perfect for performance‑critical roles.” |

---

#### 8. FAQs

| Q | A |
|---|---|
| *Why 30, not 32?* | Because a 32‑bit signed int can represent values up to 2³¹‑1. The problem guarantees `first ^ second ≤ 10⁹ < 2³⁰`. |
| *What about leading zeros?* | A substring that starts with `0` and has length > 1 is still 0. We only store the single character zero once. |
| *Can we use a prefix‑sum array instead?* | Yes, but it would still require a map of all possible 30‑bit values. The direct rolling‑hash is simpler and faster. |
| *What if `s` is longer than 10⁴?* | The algorithm still works but would exceed the time/space limits. In that case you’d need a more sophisticated bit‑set / trie. |

---

### 9. Final Thoughts

- **Good** – O(1) amortized lookups, clear mathematics, constant‑time preprocessing.  
- **Bad** – Roughly 3 × 10⁵ map entries; memory‑heavy for very large strings.  
- **Ugly** – The “leading‑zero” quirk forces a special branch that is easy to miss.

By mastering this problem you’ll impress recruiters who care about **bit‑wise operations**, **hash‑map tricks**, and **clean code** across multiple languages.

Happy coding—and good luck landing that interview!