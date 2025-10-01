---
title: LeetCode 2306. Naming a Company - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🏢  Naming a Company – LeetCode 2306  
### The Good, The Bad, and The Ugly

---

### 1️⃣ Problem Recap (LeetCode 2306)

> **Given** an array of distinct strings `ideas`  
> **Goal**:  
> 1. Pick two distinct names `ideaA` & `ideaB`.  
> 2. Swap their first letters.  
> 3. If **both** swapped words do **not** exist in the original list,  
>    the concatenated string `"ideaA ideaB"` (with a space) is a *valid* company name.  
> 4. Count all **distinct** valid company names.

**Constraints**

| Param | Min | Max | Notes |
|-------|-----|-----|-------|
| `ideas.length` | 2 | 5 · 10⁴ | up to 50 k names |
| `ideas[i].length` | 1 | 10 | short strings |
| All strings are lowercase and unique |

The output must be a 64‑bit integer (`long` in Java / `long long` in C++ / `int` in Python).

---

### 2️⃣ Key Insight – “Swap‑and‑Count” = “Suffix‑Pairs”

When we swap the first letters, the *suffix* (everything after the first character) stays the same.  
For two names `aX` and `bY` (`a,b` are first letters, `X,Y` suffixes):

```
swap → aY , bX
```

The swap is **invalid** **iff**  
- `aY` already exists in the original list, **or**  
- `bX` already exists.

Thus, for a pair of first‑letter groups (`a`‑group, `b`‑group) we need:

- The number of suffixes that appear **only** in group `a` (call it `cntA`).
- The number of suffixes that appear **only** in group `b` (call it `cntB`).

Every pair (`suffixA` from group `a`, `suffixB` from group `b`) gives **two** valid company names:  
`(a suffixA) (b suffixB)` and `(b suffixB) (a suffixA)`.

So the answer is

```
ans = Σ over all (a<b)   2 × cntA × cntB
```

This reduces the problem to:

1. Group suffixes by first letter.  
2. For each pair of groups, count suffixes that do **not** appear in the other group.

---

### 3️⃣ The Algorithm (O(n))

| Step | What | Why |
|------|------|-----|
| **1** | Build an array `suffixes[26]` of sets. For every word `w` store `w[1:]` in `suffixes[w[0]-'a']`. | O(n) time, O(n) space. |
| **2** | For every pair of groups `(i,j)` (`i<j`):<br>  *`onlyI`* = number of suffixes in `suffixes[i]` not in `suffixes[j]`. <br>  *`onlyJ`* = number of suffixes in `suffixes[j]` not in `suffixes[i]`. <br>  `ans += 2 * onlyI * onlyJ`. | Each pair touches only the suffixes of those two groups. Total comparisons ≈ 26² = 676, negligible. |
| **3** | Return `ans`. | 64‑bit result. |

**Why is it correct?**

*If a suffix is present in both groups, swapping creates a name that already exists in the original list, so it is invalid.*  
*Only suffixes unique to a group produce valid swaps.*  
*Each unique pair yields two distinct names because we can order the two original words in two ways.*

---

### 4️⃣ Edge Cases & Common Pitfalls

| Case | Why it matters | Fix |
|------|----------------|-----|
| Empty suffix (`"a"` only one character) | `w[1:]` becomes `""`. Still a valid suffix and must be counted. | Use `w.substring(1)` or `w[1:]`. |
| Duplicate suffixes within a group | Sets automatically discard duplicates, so `cntI` counts unique suffixes. | Use a hash‑set per group. |
| Large number of groups but few suffixes | Still O(26²) comparisons – negligible. | No special handling needed. |
| Overflow in intermediate multiplication | Result fits in 64‑bit (`long`). | Use `long`/`long long` for counters. |

---

### 5️⃣ The Code (Java, Python, C++)

> **Tip:** All three implementations follow the exact same logic – only syntax changes.

---

#### 🟦 Java

```java
import java.util.*;

public class Solution {
    public long distinctNames(String[] ideas) {
        // 1. Group suffixes by first letter
        List<Set<String>> groups = new ArrayList<>(26);
        for (int i = 0; i < 26; i++) groups.add(new HashSet<>());

        for (String word : ideas) {
            int idx = word.charAt(0) - 'a';
            groups.get(idx).add(word.substring(1));
        }

        long ans = 0L;

        // 2. Count cross‑group unique suffix pairs
        for (int i = 0; i < 25; i++) {
            Set<String> setI = groups.get(i);
            for (int j = i + 1; j < 26; j++) {
                Set<String> setJ = groups.get(j);

                long onlyI = 0;
                for (String suf : setI) if (!setJ.contains(suf)) onlyI++;

                long onlyJ = 0;
                for (String suf : setJ) if (!setI.contains(suf)) onlyJ++;

                ans += 2L * onlyI * onlyJ;
            }
        }
        return ans;
    }

    // For quick manual testing
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.distinctNames(new String[]{"coffee","donut","time","apple"})); // 2
    }
}
```

**Complexity**:  
- Time: **O(n + 26²)**  
- Space: **O(n)**

---

#### 🟩 Python 3

```python
from collections import defaultdict
from typing import List

class Solution:
    def distinctNames(self, ideas: List[str]) -> int:
        # 1. Group suffixes
        groups = defaultdict(set)          # key: first letter, value: set of suffixes

        for w in ideas:
            groups[w[0]].add(w[1:])

        ans = 0
        keys = sorted(groups.keys())
        for i in range(len(keys)):
            for j in range(i + 1, len(keys)):
                a, b = keys[i], keys[j]
                set_a, set_b = groups[a], groups[b]

                only_a = sum(1 for s in set_a if s not in set_b)
                only_b = sum(1 for s in set_b if s not in set_a)

                ans += 2 * only_a * only_b
        return ans
```

---

#### 🟧 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long distinctNames(vector<string>& ideas) {
        // 1. Group suffixes by first letter
        vector<unordered_set<string>> groups(26);
        for (const string& w : ideas) {
            int idx = w[0] - 'a';
            groups[idx].insert(w.substr(1));
        }

        long long ans = 0;
        // 2. Count cross‑group unique suffix pairs
        for (int i = 0; i < 25; ++i) {
            for (int j = i + 1; j < 26; ++j) {
                long long onlyI = 0, onlyJ = 0;
                for (const auto& suf : groups[i])
                    if (groups[j].find(suf) == groups[j].end()) ++onlyI;
                for (const auto& suf : groups[j])
                    if (groups[i].find(suf) == groups[i].end()) ++onlyJ;

                ans += 2LL * onlyI * onlyJ;
            }
        }
        return ans;
    }
};
```

---

### 6️⃣ Testing & Validation

| Test | Expected | Why |
|------|----------|-----|
| `["coffee","donut","time","apple"]` | 2 | Only `"coffee donut"` and `"donut coffee"` are valid. |
| `["a","b"]` | 0 | Swapping produces `"a"` and `"b"` which already exist. |
| `["ab","ac","ba","bc"]` | 8 | All swaps are valid – 4 pairs × 2 = 8. |
| All names start with the same letter | 0 | No pairs of different first letters. |

Run the above tests in all three languages to verify correctness.

---

### 7️⃣ SEO‑Ready Blog Post

> **Keywords**: LeetCode 2306, Naming a Company, algorithm, solution, interview question, coding interview, Java solution, Python solution, C++ solution, 2024 interview prep

---

#### 📄 Blog Post Draft

```markdown
# Naming a Company – LeetCode 2306 – A Complete Solution in Java, Python & C++

**Published: 2024-04‑27 | Tags: #LeetCode #2306 #NamingACompany #Algorithm #Java #Python #C++ #InterviewPrep**

---

## 🚀 Problem Overview

You’re given a list of *distinct* company names (`ideas`).  
Pick any two different names, swap their first letters, and if the swapped names don’t already exist in the list, the concatenated pair (with a space) becomes a *valid* new company name.  
Count how many distinct valid names you can form.

> **Input**: `ideas = ["coffee","donut","time","apple"]`  
> **Output**: `2`

---

## 🔍 Core Idea: “First‑Letter + Unique Suffixes”

- Swapping only changes the **first character**; the **suffix** remains unchanged.  
- A swap is *invalid* if **either** of the new words already exists.  
- Therefore, for two letter‑groups we only need to know suffixes that are **unique** to each group.

**Formula**  
```
ans = Σ_{i<j} 2 × onlyInGroup(i) × onlyInGroup(j)
```

---

## 🧠 Step‑by‑Step Algorithm

1. **Group suffixes by first letter** – 26 hash‑sets.  
2. **For every pair of groups** (`i < j`):  
   - Count suffixes unique to group `i` → `cntI`.  
   - Count suffixes unique to group `j` → `cntJ`.  
   - `ans += 2 * cntI * cntJ`.
3. Return `ans` (64‑bit).

> **Time**: O(n)  
> **Space**: O(n)

---

## 📄 Code

### Java

```java
import java.util.*;

public class Solution {
    public long distinctNames(String[] ideas) {
        List<Set<String>> groups = new ArrayList<>(26);
        for (int i = 0; i < 26; i++) groups.add(new HashSet<>());

        for (String word : ideas) {
            int idx = word.charAt(0) - 'a';
            groups.get(idx).add(word.substring(1));
        }

        long ans = 0;
        for (int i = 0; i < 25; i++) {
            Set<String> setI = groups.get(i);
            for (int j = i + 1; j < 26; j++) {
                Set<String> setJ = groups.get(j);
                long onlyI = 0, onlyJ = 0;
                for (String suf : setI) if (!setJ.contains(suf)) onlyI++;
                for (String suf : setJ) if (!setI.contains(suf)) onlyJ++;
                ans += 2L * onlyI * onlyJ;
            }
        }
        return ans;
    }
}
```

### Python 3

```python
from collections import defaultdict
from typing import List

class Solution:
    def distinctNames(self, ideas: List[str]) -> int:
        groups = defaultdict(set)          # key: first letter
        for w in ideas:
            groups[w[0]].add(w[1:])        # suffix

        ans = 0
        keys = sorted(groups.keys())
        for i in range(len(keys)):
            for j in range(i + 1, len(keys)):
                a, b = keys[i], keys[j]
                setA, setB = groups[a], groups[b]
                onlyA = sum(1 for s in setA if s not in setB)
                onlyB = sum(1 for s in setB if s not in setA)
                ans += 2 * onlyA * onlyB
        return ans
```

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long distinctNames(vector<string>& ideas) {
        vector<unordered_set<string>> groups(26);
        for (const string& w : ideas)
            groups[w[0] - 'a'].insert(w.substr(1));

        long long ans = 0;
        for (int i = 0; i < 25; ++i) {
            for (int j = i + 1; j < 26; ++j) {
                long long onlyI = 0, onlyJ = 0;
                for (const string& suf : groups[i])
                    if (!groups[j].count(suf)) ++onlyI;
                for (const string& suf : groups[j])
                    if (!groups[i].count(suf)) ++onlyJ;
                ans += 2LL * onlyI * onlyJ;
            }
        }
        return ans;
    }
};
```

---

## 🔑 Why This Solution Rocks

| ✅ Feature | Benefit |
|-----------|---------|
| **O(n) Time** | Handles 50 k names comfortably. |
| **Only 26² Comparisons** | 676 pair checks – constant‑time overhead. |
| **Uses Hash‑Sets** | No duplicate suffixes; fast `O(1)` lookups. |
| **Clear Logic** | Group → unique suffix count → pair product. Easy to audit in an interview. |

---

## ❌ Common Mistakes to Avoid

| Mistake | Fix |
|---------|-----|
| **Naïve pair generation** (`O(m²)`) | Will TLE on large inputs. |
| **Using lists instead of sets** | Duplicate suffixes inflate counts. |
| **Forgetting the `*2` multiplier** | Under‑counting valid pairs. |
| **Wrong key for groups** | Using full first name instead of letter → wrong partitions. |

---

## 💡 Take‑away

For LeetCode 2306, the key is to recognize that *only the first letter matters*.  
By grouping names by that letter and focusing on suffix uniqueness, the problem collapses to a simple combinatorial product.  
This makes the solution both **fast** and **interview‑friendly**.

Happy coding! 🚀
```

---

**End of Post**

---

### 📌 Final Thoughts

- Use the draft above to create a polished article on a blog platform or Markdown‑supporting site.  
- Add a short introduction about the “unique suffix trick” and a concluding remark about practice.  
- Include the code snippets for all three languages with comments.  
- Link to your GitHub repo if you want to share full test harnesses.

Happy teaching and interviewing! 🎉
```

> The above markdown is a ready‑to‑publish article that will rank well for the targeted keywords and help developers prepare for LeetCode 2306.  

---

Feel free to copy, tweak, and publish!