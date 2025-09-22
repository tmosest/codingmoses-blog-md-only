---
title: LeetCode 2399. Check Distances Between Same Letters - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 2399 – Check Distances Between Same Letters  
> **“The Good, the Bad, and the Ugly” – A practical guide for interview‑ready candidates**

---

### TL;DR  

- **Problem**: Every lowercase letter appears **exactly twice** in a string `s`.  
  For each letter `i` (`'a' → 0`, …, `'z' → 25`) the array `distance[i]` tells how many **other** letters should lie between its two occurrences.  
  Return `true` if `s` satisfies all given distances.

- **Solution**:  
  1. **Track‑First** – Store the first index of each letter, then validate on the second appearance.  
  2. **Check‑Second** – When you see a letter, immediately look ahead `distance + 1` steps and compare; set the entry to `-1` to avoid double‑checking.

- **Complexity**:  
  - Time: `O(|s|)` (≤ 52)  
  - Space: `O(26)` (constant)

- **Why it matters**:  
  The pattern is common in string‑and‑array interview questions. Mastering it shows you can handle *two‑pass* logic, map usage, and edge‑case safety.

---

## 1️⃣ Problem Recap

```text
Input
------
s        – string (length 2‑52, all lowercase)
distance – int[26] (0‑based for 'a'…'z')

Output
------
boolean – true iff s is a “well‑spaced” string
```

*Example*  

```
s = "abaccb"
distance = [1,3,0,5,0,…]   // only a,b,c matter
Result: true
```

---

## 2️⃣ Two Winning Approaches

| Approach | Idea | Pros | Cons |
|----------|------|------|------|
| **Track‑First** | Remember first index; on second index compute `i - firstIndex - 1`. | Simple, clear. | Requires a “first‑seen” flag; uses an array of 26 ints. |
| **Check‑Second** | On first occurrence, directly peek `i + distance + 1`. Mark as processed (`-1`). | One‑pass; no need for separate flag array. | Slightly trickier to understand; uses `distance` array mutably. |

Below you’ll find **Java, Python, and C++** implementations for *both* methods so you can pick the one that fits your style.

---

## 3️⃣ Code Snippets

> **Tip:** All solutions assume the constraints guarantee each letter appears *exactly twice*, so no extra validation is required beyond bounds checks.

### 3.1 Java

```java
// ------------------------------------------------------------
// Java – Track‑First (HashMap based)
// ------------------------------------------------------------
public class Solution {
    public boolean checkDistances(String s, int[] distance) {
        Map<Character, Integer> firstPos = new HashMap<>();
        for (int i = 0; i < s.length(); i++) {
            char ch = s.charAt(i);
            if (firstPos.containsKey(ch)) {
                int diff = i - firstPos.get(ch) - 1;
                if (diff != distance[ch - 'a']) return false;
            } else {
                firstPos.put(ch, i);
            }
        }
        return true;
    }
}

// ------------------------------------------------------------
// Java – Check‑Second (in‑place)
// ------------------------------------------------------------
public class Solution {
    public boolean checkDistances(String s, int[] distance) {
        for (int i = 0; i < s.length(); i++) {
            int d = distance[s.charAt(i) - 'a'];
            // if second occurrence or out of bounds → fail
            if (i + d + 1 >= s.length() || s.charAt(i + d + 1) != s.charAt(i))
                return false;
            distance[s.charAt(i) - 'a'] = -1;   // mark processed
        }
        return true;
    }
}
```

### 3.2 Python

```python
# ------------------------------------------------------------
# Python – Track‑First
# ------------------------------------------------------------
def checkDistances(s: str, distance: list[int]) -> bool:
    first = {}
    for i, ch in enumerate(s):
        if ch in first:
            if i - first[ch] - 1 != distance[ord(ch) - 97]:
                return False
        else:
            first[ch] = i
    return True

# ------------------------------------------------------------
# Python – Check‑Second
# ------------------------------------------------------------
def checkDistances(s: str, distance: list[int]) -> bool:
    for i, ch in enumerate(s):
        d = distance[ord(ch) - 97]
        if i + d + 1 >= len(s) or s[i + d + 1] != ch:
            return False
        distance[ord(ch) - 97] = -1   # mark processed
    return True
```

### 3.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

// ------------------------------------------------------------
// C++ – Track‑First
// ------------------------------------------------------------
bool checkDistances(string s, vector<int>& distance) {
    vector<int> first(26, -1);
    for (int i = 0; i < (int)s.size(); ++i) {
        int idx = s[i] - 'a';
        if (first[idx] != -1) {
            if (i - first[idx] - 1 != distance[idx]) return false;
        } else {
            first[idx] = i;
        }
    }
    return true;
}

// ------------------------------------------------------------
// C++ – Check‑Second
// ------------------------------------------------------------
bool checkDistances(string s, vector<int>& distance) {
    for (int i = 0; i < (int)s.size(); ++i) {
        int d = distance[s[i] - 'a'];
        if (i + d + 1 >= (int)s.size() || s[i + d + 1] != s[i]) return false;
        distance[s[i] - 'a'] = -1;   // processed
    }
    return true;
}
```

> **Choose the style that feels most natural to you.**  
> *Track‑First* is ideal for interviews that value **explicit state tracking**.  
> *Check‑Second* demonstrates **optimised one‑pass logic** and a deeper understanding of array indices.

---

## 4️⃣ Complexity Analysis

| Operation | Track‑First | Check‑Second |
|-----------|-------------|--------------|
| Time | `O(n)` – one scan, constant‑time map look‑ups | `O(n)` – one scan, constant‑time array access |
| Space | `O(26)` (map of first indices) | `O(1)` – reuses input array |

Both meet the LeetCode constraints comfortably (max `n = 52`).  

---

## 5️⃣ Common Pitfalls (The Ugly)

| Mistake | Why it fails | Fix |
|---------|--------------|-----|
| **Using `i - firstIndex` instead of `i - firstIndex - 1`** | Mis‑counts the letters *between* occurrences. | Subtract 1 to exclude the endpoints. |
| **Checking bounds incorrectly (`i + d < n` instead of `i + d + 1 < n`)** | Off‑by‑one error when peeking ahead. | Remember the `+1` for the letter itself. |
| **Assuming all 26 letters appear** | Ignoring the “ignore if absent” rule can lead to false positives/negatives. | Use a map or mark processed entries; ignore distances when the letter is never seen. |
| **Mutating `distance` in Check‑Second without a copy** | Side‑effects may leak into calling code or tests. | Copy the array first (`int[] copy = distance.clone()`) or explicitly document that the method mutates it. |

---

## 6️⃣ Test‑Driven Verification

```text
Test 1
-------
s = "abaccb"
distance = [1,3,0,5,0,…]
Result: true

Test 2
-------
s = "aa"
distance = [1,0,0,…]
Result: false

Test 3 (edge)
-------------
s = "zzxyyzxxzz"  // 10 letters, each twice
distance = [0]*26; distance[25] = 8; distance[23] = 2; distance[24] = 1
Result: true
```

Running both implementations on the same test suite guarantees you catch subtle off‑by‑one bugs.

---

## 7️⃣ SEO‑Optimized Blog Wrap‑Up

**Title**  
> *Check Distances Between Same Letters – Java, Python, C++ Solutions & Interview Tips*

**Meta‑Description**  
> Master LeetCode 2399 with clear Java, Python, and C++ implementations. Learn the Track‑First vs. Check‑Second approaches, complexity, and common pitfalls. Boost your coding interview game today!

**Header Keywords**  
- LeetCode 2399  
- Check Distances Between Same Letters  
- String Algorithms  
- Two‑Pass String Matching  
- Interview Preparation  

**Closing Call‑to‑Action**  
> “If you’re prepping for your next software engineering interview, practice this problem until the solution feels second nature. The concepts—hash maps, two‑pointer checks, and off‑by‑one safety—are reusable across countless coding challenges.”

---

### Final Thought

*The good* – A straightforward, time‑efficient algorithm that turns a potentially confusing “distance” rule into a clean, linear pass.  
*The bad* – The temptation to hard‑code indices or ignore the “absent letter” rule can trip up even experienced coders.  
*The ugly* – Off‑by‑one mistakes that subtly break solutions; avoid them by double‑checking bounds and using the `-1` trick.

Now that you own all three language implementations and understand the *why* behind every line, you’re ready to hit LeetCode 2399, impress interviewers, and land that job offer. 🚀

Happy coding!  

--- 

*— Your friendly coding mentor*  
<https://www.codemaster.io> | @codeMaster  
--- 

**Happy solving, and may your interview code always be off‑by‑zero correct!**