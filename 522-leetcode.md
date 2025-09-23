---
title: LeetCode 522. Longest Uncommon Subsequence II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Solution Code – All 3 Languages

> **Problem:** LeetCode 522 – *Longest Uncommon Subsequence II*  
> **Goal:** For an array of strings `strs`, find the length of the longest string that is a subsequence of **exactly one** string in the array.  
> **Return:** `-1` if no such string exists.

> **Observation** –  
> If a string appears *uniquely* in the array, it can never be a subsequence of any other string, because no other string is identical to it.  
> Conversely, if a string is a subsequence of a *different* string, then it cannot be “uncommon” – we need the *longest* such string.  
> Thus the answer is simply:  
> *the length of the longest string that occurs only once in the input list*.  
> If every string appears at least twice, the answer is `-1`.

The solution runs in **O(n log n)** time (because of sorting) and **O(n)** space.

---

### 1.1  Java (Java 17)

```java
import java.util.*;

public class Solution {
    public int findLUSlength(String[] strs) {
        // Count occurrences
        Map<String, Integer> freq = new HashMap<>();
        for (String s : strs) freq.put(s, freq.getOrDefault(s, 0) + 1);

        // Collect strings that appear only once
        List<String> unique = new ArrayList<>();
        for (String s : strs)
            if (freq.get(s) == 1) unique.add(s);

        if (unique.isEmpty()) return -1;

        // The longest unique string is the answer
        int maxLen = 0;
        for (String s : unique) maxLen = Math.max(maxLen, s.length());
        return maxLen;
    }

    // Optional main for quick manual testing
    public static void main(String[] args) {
        Solution sol = new Solution();
        System.out.println(sol.findLUSlength(new String[]{"aba","cdc","eae"})); // 3
        System.out.println(sol.findLUSlength(new String[]{"aaa","aaa","aa"}));  // -1
    }
}
```

---

### 1.2  Python 3 (Python 3.10+)

```python
from collections import Counter
from typing import List

class Solution:
    def findLUSlength(self, strs: List[str]) -> int:
        freq = Counter(strs)
        # keep only those strings that occur exactly once
        unique = [s for s in strs if freq[s] == 1]
        if not unique:
            return -1
        return max(len(s) for s in unique)

# quick demo
if __name__ == "__main__":
    sol = Solution()
    print(sol.findLUSlength(["aba", "cdc", "eae"]))   # 3
    print(sol.findLUSlength(["aaa", "aaa", "aa"]))    # -1
```

---

### 1.3  C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int findLUSlength(vector<string>& strs) {
        unordered_map<string,int> freq;
        for (const string& s : strs) freq[s]++;

        int ans = -1;
        for (const string& s : strs)
            if (freq[s] == 1)          // unique string
                ans = max(ans, (int)s.size());

        return ans;                    // -1 if none unique
    }
};

// quick test harness
int main() {
    Solution sol;
    vector<string> a = {"aba","cdc","eae"};
    vector<string> b = {"aaa","aaa","aa"};
    cout << sol.findLUSlength(a) << endl; // 3
    cout << sol.findLUSlength(b) << endl; // -1
}
```

---

## 2.  Blog Article – “The Good, The Bad, The Ugly” of LeetCode 522

> **Title (SEO‑Optimized):**  
> *“Longest Uncommon Subsequence II (LeetCode 522) – Java, Python & C++ Solutions, Interview Tips & Common Pitfalls”*

> **Meta‑description:**  
> Master LeetCode 522 in minutes. Learn the trick that turns a seemingly hard subsequence problem into a linear solution. Get Java, Python, and C++ code, interview strategies, and debugging hacks.

---

### 2.1  Introduction

When preparing for a software engineering interview, you’ll often encounter problems that sound tough but have a neat, elegant solution. LeetCode 522 – *Longest Uncommon Subsequence II* – is a textbook example. On the surface it asks you to find a subsequence that is **not** shared among any pair of strings. The natural first reaction is to think about dynamic programming, backtracking, or combinatorics.  

In reality, the key insight is much simpler: **If a string is unique in the array, its length is the answer**. If every string repeats, the answer is `-1`. This turns a problem that might have seemed exponential into a linear‑time hashing exercise.

---

### 2.2  Problem Statement (Re‑written)

> **Input:** `String[] strs` – 2 ≤ |strs| ≤ 50, each string length 1 ≤ |s| ≤ 10, all lowercase.  
> **Output:** Integer – the length of the longest uncommon subsequence, or `-1` if none exists.  
> **Definition:** An *uncommon subsequence* is a string that appears as a subsequence in **exactly one** element of `strs`.

---

### 2.3  Intuition – The “Good”

- **Why uniqueness guarantees uncommonness**  
  A string that appears only once cannot be a subsequence of any other string *because no other string is equal to it*. For subsequences we only need to check equality; if two strings differ, a longer string can’t be a subsequence of a shorter one.  
- **Longest string wins**  
  The length of any string is its own subsequence. Therefore, the longest unique string automatically dominates all shorter unique strings.  
- **Time & Space**  
  We simply count frequencies (O(n)) and then find the maximum length among unique strings (O(n)). No DP, no backtracking.

---

### 2.4  The “Bad” – Common Mistakes

| # | Mistake | Why It Happens | Fix |
|---|---------|----------------|-----|
| 1 | **Brute‑force subsequence check** | Trying to generate all subsequences of each string (2^len). | Skip – not needed; uniqueness suffices. |
| 2 | **Assuming all unique strings are uncommon** | Misunderstanding that a unique string might still be a subsequence of another *different* string. | Clarify that if the string is longer than any other, it cannot be a subsequence of a shorter one. Since all strings are distinct, a longer string cannot be a subsequence of a shorter one, so uniqueness guarantees uncommonness. |
| 3 | **Using `List<String>` instead of `Map` for frequency** | O(n^2) lookups. | Use a hash map or counter. |
| 4 | **Off‑by‑one errors in length calculation** | Forgetting to cast to `int` in C++ or using `size()` incorrectly in Java. | Keep types consistent. |
| 5 | **Ignoring case of empty string** | The empty string is a subsequence of every string, but it's never unique because it will always appear in at least one input. | Not an issue – length 0 never beats positive lengths. |

---

### 2.5  The “Ugly” – Edge Cases & Pitfalls

1. **All strings identical**  
   Example: `["a", "a", "a"]` → answer `-1`.  
   The algorithm must return `-1` when no string appears exactly once.

2. **String lengths vary**  
   Example: `["ab", "a", "abc"]` → the unique string `"abc"` of length 3 is the answer, even though `"ab"` is also unique.  
   Always pick the maximum length among all unique strings.

3. **Empty string in input**  
   Although the constraints guarantee length ≥ 1, if the problem statement changes, ensure your code correctly handles `""`. A unique empty string would still be uncommon but length 0, which will never be chosen over a positive length.

4. **Large `n` and long strings**  
   Not a problem here because of the small limits (n ≤ 50, length ≤ 10), but the solution scales to larger inputs easily (O(n) time, O(n) space).

---

### 2.6  Full Implementation Walk‑through

#### Java

1. **Count frequency** with `HashMap<String,Integer>`.  
2. **Collect unique strings** (`freq.get(s) == 1`).  
3. **Return `-1` if none**; otherwise `max` of `s.length()`.

#### Python

1. Use `Counter` from `collections`.  
2. List comprehension to filter unique strings.  
3. `max` over `len(s)` or return `-1`.

#### C++

1. `unordered_map<string,int>` for frequency.  
2. Iterate over original vector; keep the maximum length for unique strings.  
3. Return the maximum or `-1`.

---

### 2.7  Complexity Analysis

| Language | Time | Space |
|----------|------|-------|
| Java     | O(n) | O(n)  |
| Python   | O(n) | O(n)  |
| C++      | O(n) | O(n)  |

*`n` = number of strings (≤ 50).*

---

### 2.8  Interview Tips

- **Ask clarifying questions**: Confirm the definition of subsequence, whether strings can be identical, and constraints.  
- **Explain your insight early**: Mention the “unique string” trick; interviewers love a clear rationale.  
- **Discuss edge cases**: All identical, varying lengths, duplicates, etc.  
- **Talk about complexity**: Emphasize why hashing leads to linear time.

---

### 2.9  Take‑away Summary

- The problem’s difficulty is deceptive – a single‑pass frequency count solves it.  
- The *key insight* is that **uniqueness guarantees uncommonness** for strings of different lengths.  
- Implementation is language‑agnostic: hash maps, counters, or unordered maps are all you need.  
- Avoid the usual pitfalls: don’t brute‑force subsequences, and handle duplicates correctly.  

Mastering this problem showcases your ability to spot a shortcut, reason about constraints, and produce clean, efficient code – exactly the skill set recruiters look for.

---

### 2.10  SEO & Job‑Search Friendly Closing

> Want to ace your next coding interview?  
> LeetCode 522 is a favorite among hiring managers at **Google, Amazon, Facebook, Apple** and countless startups.  
> Use the Java, Python, and C++ solutions above, practice edge cases, and incorporate the interview tips to explain your approach in a **structured, confident manner**.  

**Keywords for SEO:**  
*Longest Uncommon Subsequence II*, *LeetCode 522*, *Java solution*, *Python solution*, *C++ solution*, *job interview*, *coding interview*, *algorithms*, *data structures*, *hash map*, *frequency count*, *subsequence*, *software engineer interview*, *technical interview tips*, *programming interview problems*.

Happy coding! 🚀