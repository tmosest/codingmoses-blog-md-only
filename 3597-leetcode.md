---
title: LeetCode 3597. Partition String - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Mastering LeetCode 3597 – **Partition String**  
*Java • Python • C++ Solutions – The Good, the Bad, and the Ugly (SEO‑Optimized)*  

---

## Why This Problem Rocks Your Resume

- **Hard‑er than it looks** – a classic greedy trick disguised as a “unique substring” puzzle.  
- **LeetCode #3597** – appears in many mid‑level interview sets (Google, Amazon, Microsoft).  
- **Languages** – you’ll see the same logic implemented in Java, Python, and C++ – perfect to showcase cross‑platform mastery.  
- **Time & Space** – O(n) time & O(n) space – clean, scalable, and “production ready”.  

If you nail this, you’ll demonstrate:  

1. **Greedy strategy** – picking the shortest possible unique segment.  
2. **Data‑structure intuition** – hash‑sets vs. maps.  
3. **Clean code** – concise, readable, and bug‑free.

---

## Problem Restatement

> Given a string `s` of lowercase letters, partition `s` into **unique** substrings.  
>  
>  Starting from index `0`, keep extending the current substring until it becomes *new* (has never appeared as a whole before).  
>  Once the substring is unique, add it to the answer list, mark it as “seen”, and start building the next substring from the next character.  
>  
>  Return the list of substrings in the order they were created.

> **Constraints**  
> `1 <= s.length <= 10⁵`  
> `s` contains only lowercase English letters.

---

## Intuition – “The Greedy Shortest‑Unique” Principle

Think of the string as a conveyor belt of letters.  
When you’re building a segment, you **never need to overshoot** the first point where the segment becomes *new*.  
If you go beyond that point, you waste time and create a longer substring that would have been split earlier.

So the optimal algorithm is:

1. Start a segment at the current index.  
2. Keep appending characters until the segment isn’t in the `seen` set.  
3. Append that segment to the result, add it to `seen`, and start a new segment at the next index.

This runs in linear time because each character is processed exactly once.

---

## Edge‑Case “The Ugly”

| Case | Why It Breaks Naïve Approaches |
|------|--------------------------------|
| `aaaa…` (all same) | A naïve nested loop may repeatedly test the same prefix, causing O(n²). |
| `abcabcabc…` | The greedy rule ensures we never build `abcabc`; however, some implementations mistakenly allow overlapping repeats. |
| Very long input (10⁵) | Using a `StringBuilder` that copies substrings each time can blow up memory/time. |

The key is to use a *hash‑set* that checks substring existence in O(1) and to build the current segment incrementally.

---

## Code Solutions

### 1. Java

```java
import java.util.*;

class Solution {
    public List<String> partitionString(String s) {
        int n = s.length();
        List<String> ans = new ArrayList<>();
        Set<String> seen = new HashSet<>();

        StringBuilder cur = new StringBuilder();
        for (int i = 0; i < n; i++) {
            cur.append(s.charAt(i));
            if (!seen.contains(cur.toString())) {
                ans.add(cur.toString());
                seen.add(cur.toString());
                cur.setLength(0); // reset for next segment
            }
        }
        return ans;
    }
}
```

- **Time**: `O(n)` – each character appended once.  
- **Space**: `O(n)` – result + set.

---

### 2. Python 3

```python
from typing import List

class Solution:
    def partitionString(self, s: str) -> List[str]:
        seen = set()
        ans = []
        cur = []
        for ch in s:
            cur.append(ch)
            seg = ''.join(cur)
            if seg not in seen:
                ans.append(seg)
                seen.add(seg)
                cur.clear()
        return ans
```

Python’s `''.join()` on a growing list is `O(len(cur))` but overall still linear because each char is joined exactly once.

---

### 3. C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<string> partitionString(string s) {
        unordered_set<string> seen;
        vector<string> ans;
        string cur;

        for (char c : s) {
            cur.push_back(c);
            if (!seen.count(cur)) {
                ans.push_back(cur);
                seen.insert(cur);
                cur.clear();
            }
        }
        return ans;
    }
};
```

- **unordered_set** gives `O(1)` lookup.  
- `cur.clear()` keeps the string small – no unnecessary copies.

---

## Full Test Harness (Optional)

```python
if __name__ == "__main__":
    sol = Solution()
    tests = [
        ("abbccccd", ["a","b","bc","c","cc","d"]),
        ("abcabcabc", ["a","b","c","ab","ca","bc"]),
        ("aaaaaaa", ["a","aa","aaa","aaaa","aaaaa"]),
        ("xyz", ["x","y","z"])
    ]
    for inp, exp in tests:
        out = sol.partitionString(inp)
        assert out == exp, f"FAIL: {inp} → {out} (expected {exp})"
    print("All tests passed!")
```

---

## Good, Bad, Ugly – What Interviewers Look For

| **Good** | **Bad** | **Ugly** |
|----------|---------|----------|
| **Greedy proof** – no backtracking, simple logic. | **Hash‑set misuse** – using a `List` to track seen substrings → `O(n²)`. | **String copying** – building a new string for every character can cause TLE on 10⁵ length. |
| **Space‑efficient** – only store seen segments, not all prefixes. | **Excessive memory** – pre‑allocating huge arrays or `StringBuilder` copies. | **Recursive solution** – stack overflow for long inputs. |
| **Cross‑lang clarity** – identical algorithm in Java, Python, C++. | **Implicit assumptions** – e.g., that the input contains only lowercase letters. | **No error handling** – failing silently on empty strings (although constraints forbid it). |

---

## Interview Talking Points

1. **Greedy proof** – Show you can reason why the “shortest unique segment” is optimal.  
2. **Data‑structure choice** – Hash‑set vs. trie vs. map – discuss time trade‑offs.  
3. **Complexity** – Emphasise `O(n)` – important for 10⁵‑length strings.  
4. **Testing** – Mention boundary cases (all same letter, alternating patterns).  
5. **Potential pitfalls** – If the interviewer mentions “overlap allowed?”, clarify that repeats must be whole substrings.

---

## SEO Meta (for the blog platform)

- **Title**: “Java, Python, C++ – Partition String LeetCode 3597 – Interview‑Ready Solution”  
- **Description**: “Step‑by‑step guide to LeetCode 3597 – Partition String. Learn the greedy strategy, edge cases, and get clean Java, Python, and C++ code for interviews.”  
- **Keywords**: *LeetCode Partition String*, *Java solution*, *Python solution*, *C++ solution*, *Greedy algorithm*, *Hash‑set*, *Interview problem*, *Resume boost*, *Coding interview*.

---

## Final Thoughts

Partition String is a **tiny gem** in the LeetCode toolbox.  
With a single greedy pass and a hash‑set you can solve it in linear time, regardless of input size.  

**Good** – clean greedy logic, O(n) complexity.  
**Bad** – over‑engineering or nested loops leading to O(n²).  
**Ugly** – memory misuse or improper string construction causing TLE on large data.

Mastering this problem demonstrates that you can *translate a concise algorithm into multiple languages*, a trait every senior engineer’s recruiter looks for. Happy coding—and good luck on your next interview!