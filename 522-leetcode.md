---
title: LeetCode 522. Longest Uncommon Subsequence II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 522. Longest Uncommon Subsequence II – Three‑Way Solution (Java | Python | C++)  
*Solve it in 30 seconds, explain the intuition, and get the interviewers excited!*

---

### 1️⃣ Problem Recap

> **Given an array of strings `strs`, find the length of the longest string that is a subsequence of **exactly one** element of `strs`.  
> If no such string exists, return `-1`.**

> *Subsequence* – a string that can be derived by deleting zero or more characters without changing the order.

**Constraints**

| | | |
|---|---|---|
| `2 ≤ strs.length ≤ 50` | `1 ≤ strs[i].length ≤ 10` | all lowercase letters |

> Example  
> `strs = ["aba","cdc","eae"]` → **3** (the string `"aba"` itself)  
> `strs = ["aaa","aaa","aa"]` → **-1** (no string is unique)

---

## 2️⃣ The “Aha!” Insight

At first glance, the phrase “subsequence” might hint at dynamic programming or backtracking.  
But a very simple observation turns the problem into a one‑liner:

> **If a string appears only once in the array, it is *automatically* an uncommon subsequence.**  
> Because every string is a subsequence of itself, but no other string contains it as a subsequence (otherwise the other string would be longer or equal, contradicting the uniqueness).

Hence the **longest uncommon subsequence** is simply:

1. **Find the longest string that occurs exactly once.**  
2. If none exist, answer is `-1`.

No DP, no pairwise comparison – just a frequency map.

> *Why is this correct?*  
> Suppose the longest uncommon subsequence `S` has length `L`.  
> If `S` appears more than once, then it is a subsequence of at least two strings, violating the definition.  
> So `S` must be unique.  
> And because any other string `T` that contains `S` as a subsequence would have length ≥ `L`, `S` must be the longest unique string.

---

## 3️⃣ Implementation in Three Languages

> **All three solutions are O(n log n) due to sorting or map construction, but with `n ≤ 50` the runtime is negligible.**

---

### 3.1 Java

```java
import java.util.*;

class Solution {
    public int findLUSlength(String[] strs) {
        // Count frequencies
        Map<String, Integer> freq = new HashMap<>();
        for (String s : strs) freq.put(s, freq.getOrDefault(s, 0) + 1);

        int maxLen = -1;
        for (String s : strs) {
            if (freq.get(s) == 1)          // unique string
                maxLen = Math.max(maxLen, s.length());
        }
        return maxLen;                      // -1 if none unique
    }
}
```

> **Complexity**  
> Time: O(n)  
> Space: O(n)

---

### 3.2 Python

```python
class Solution:
    def findLUSlength(self, strs: List[str]) -> int:
        from collections import Counter
        freq = Counter(strs)
        max_len = max((len(s) for s in strs if freq[s] == 1), default=-1)
        return max_len
```

> **Complexity**  
> Time: O(n)  
> Space: O(n)

---

### 3.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int findLUSlength(vector<string>& strs) {
        unordered_map<string,int> freq;
        for (const string &s : strs) freq[s]++;

        int ans = -1;
        for (const string &s : strs)
            if (freq[s] == 1)
                ans = max(ans, (int)s.size());
        return ans;
    }
};
```

> **Complexity**  
> Time: O(n)  
> Space: O(n)

---

## 4️⃣ Good, Bad, and Ugly

| Aspect | ✅ Good | ❌ Bad | ⚠️ Ugly |
|--------|---------|--------|---------|
| **Complexity** | O(n) with constant factor – super fast | N/A – no heavy algorithmic burden | Be careful of integer overflow with huge inputs (not an issue here) |
| **Readability** | Extremely short, clear | Too short may look “magic” to some interviewers | Avoid relying on language‑specific tricks that are hard to understand |
| **Edge Cases** | Handles duplicates, all unique, all identical | None | Empty string array (not in constraints), extremely long strings (not in constraints) |
| **Proof** | Can give a one‑sentence proof in 10 seconds | None needed if you can explain | Over‑engineering (DP, LCS) can confuse |

---

## 5️⃣ When to Use This Approach

- **Interview setting**: Show that you can spot a pattern and reduce the problem to counting.  
- **Coding challenge**: Time limit is generous, but this is the most maintainable solution.  
- **Production code**: If you ever need a “longest uncommon subsequence” in a log or analytics pipeline, this is perfect.

---

## 6️⃣ SEO‑Optimized Takeaway

> **Keywords**: Leetcode 522, Longest Uncommon Subsequence II, Java solution, Python solution, C++ solution, string subsequence, interview question, coding interview, algorithm, time complexity, job interview.

> **Why this matters**:  
> - Recruiters love concise, efficient code.  
> - The blog’s headline *“Longest Uncommon Subsequence II – The 30‑Second Java/Python/C++ Solution”* targets the exact search terms candidates use when preparing for interviews.  
> - The “good, bad, ugly” section shows you can analyze problems from multiple angles—an interview staple.

---

## 7️⃣ Final Thoughts

The Longest Uncommon Subsequence II problem teaches a powerful lesson: **never underestimate the power of frequency counting**.  
What looks like a classic subsequence problem turns into a “find the unique string” puzzle.  
Keep this pattern in your toolbox; it will pop up in other interview questions about uniqueness, duplicates, and subsequences.

> **Want to shine in your next interview?**  
> Practice spotting such simplifications, write clean code in your language of choice, and explain the intuition clearly.  
> You’ll be ready to tackle even the trickiest LeetCode problems and impress any hiring manager.

Happy coding! 🚀