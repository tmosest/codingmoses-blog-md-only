---
title: LeetCode 2262. Total Appeal of A String - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 Problem Recap – LeetCode 2262: *Total Appeal of a String*  

| | |
|---|---|
| **Difficulty** | Hard |
| **Type** | String, Math, Dynamic Programming |
| **Runtime Constraints** | `1 ≤ s.length ≤ 10⁵` |
| **Language Support** | Java, Python, C++ |

**Definition**

*The **appeal** of a string* = the number of **distinct** characters it contains.  
Given a string `s`, return the **sum of appeals** of **every** possible contiguous substring of `s`.

---

## 1️⃣ Naïve Brute‑Force (and why it’s a *big no‑no*)

1. Enumerate all `O(n²)` substrings.  
2. For each substring, build a `Set` of its characters → `O(length)` work.  
3. Sum the sizes.

**Time** – `O(n³)` in the worst case (e.g., all characters are the same).  
**Space** – `O(1)` (besides temporary set).  

> **Bad** – This blows up quickly. Even `n = 10⁴` would already be infeasible.

---

## 2️⃣ Insight: Contribution of a Character

Think of each character `c` independently.  
How many substrings contain that particular occurrence of `c`?  
If we can sum these counts over all occurrences, we get the answer.

### Observation

For an occurrence at index `i` (0‑based):

- Let `last[c]` be the index of the **previous** occurrence of the same character.  
  (If `c` hasn’t appeared yet, `last[c] = -1`.)  
- Any substring that ends at index `i` *and* starts **after** `last[c]` will contain this `c`.  

Number of such substrings  
```
(i - last[c])      // possible start positions
```

So the contribution of this occurrence to the final sum is  
```
(i - last[c]) * (n - i)
```
because the substring can end anywhere from `i` to `n‑1`.

### Total Complexity

We traverse the string once, updating `last[26]` and adding the contribution for each character.  
Both **time** and **space** are linear/constant.

> **Good** – `O(n)` time, `O(1)` space.  

---

## 3️⃣ Why a 26‑loop per character is *Ugly*

A simpler DP solution keeps a running total of the last positions of each letter (`last[26]`) and adds them via an inner `for (int k=0;k<26;k++) total += i+1-last[k]`.  
That is **`O(26 · n)`** – still linear, but the factor 26 can dominate for very large `n`.  
Moreover, the code is harder to read and reason about.

---

## 4️⃣ Final Elegant O(n) Solution (Prev‑Occurrence trick)

A slightly different viewpoint is to maintain the **current appeal for all substrings that end at index `i`** (`cur`) and add that to the global result.

```
cur += (i + 1) - last[s[i]]         // new substrings that bring this char into the appeal
last[s[i]] = i + 1                  // +1 so we can start counting at 1 instead of 0
res += cur
```

Both variants produce the same result; we’ll present both for clarity.

---

## 4️⃣ Code – Three Languages

> **Tip** – All codes are ready‑to‑run on LeetCode, CodeChef, HackerRank, etc.

### 4.1. Java 17

```java
// 2262. Total Appeal of a String – Java
class Solution {
    public long appealSum(String s) {
        int n = s.length();
        int[] last = new int[26];          // last occurrence (+1). 0 means “never seen”.
        long res = 0, total = 0;

        for (int i = 0; i < n; i++) {
            int idx = s.charAt(i) - 'a';
            total += (i + 1) - last[idx];  // substrings that newly include this char
            last[idx] = i + 1;             // update to current position (+1)
            res += total;                  // all substrings ending at i
        }
        return res;
    }
}
```

### 4.2. Python 3.10+

```python
# 2262. Total Appeal of a String – Python 3
from collections import defaultdict

class Solution:
    def appealSum(self, s: str) -> int:
        n = len(s)
        last = defaultdict(lambda: -1)
        res = 0

        for i, ch in enumerate(s):
            # (i - last[ch]) * (n - i)   – contribution of this occurrence
            res += (i - last[ch]) * (n - i)
            last[ch] = i
        return res
```

### 4.3. C++17

```cpp
// 2262. Total Appeal of a String – C++17
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long appealSum(string s) {
        int n = s.size();
        vector<int> last(26, -1);
        long long res = 0;
        for (int i = 0; i < n; ++i) {
            int idx = s[i] - 'a';
            res += 1LL * (i - last[idx]) * (n - i);
            last[idx] = i;
        }
        return res;
    }
};
```

All three codes run in **O(n)** time, **O(1)** auxiliary memory.

---

## 4️⃣ Edge‑Case Checklist

| Case | Expected Result | Why it matters |
|------|-----------------|----------------|
| All same letters, e.g. `"aaaa"` | `4*5/2 = 10` substrings, each appeal `1` → sum `10` | ensures we treat duplicates correctly |
| All distinct, e.g. `"abcd"` | Appeal of each substring equals its length | tests the *max* appeal scenario |
| Empty string – not allowed by constraints | N/A | |
| Single character, e.g. `"z"` | Appeal `1` | trivial base case |
| Max length `10⁵` random string | Should finish < 0.5 s on LeetCode | stress‑test |

---

## 5️⃣ Quick Test Harness (Python)

```python
def brute(s):
    n = len(s)
    ans = 0
    for i in range(n):
        seen = set()
        for j in range(i, n):
            seen.add(s[j])
            ans += len(seen)
    return ans

def test():
    import random, string
    for n in range(1, 50):
        for _ in range(100):
            s = ''.join(random.choice('abc') for _ in range(n))
            if brute(s) != Solution().appealSum(s):
                print('Mismatch', s, brute(s), Solution().appealSum(s))
                return
    print('All tests passed!')

if __name__ == "__main__":
    test()
```

---

## 6️⃣ Conclusion – What Interviewers Want

- **Time‑efficient**: `O(n)` solves the problem in sub‑second time even for `n=10⁵`.  
- **Space‑light**: only a 26‑element array or dictionary.  
- **Mathematical elegance**: character‑by‑character contribution avoids messy DP or loops.  

> **Good** – Simple, linear, easy to reason about.  
> **Bad** – Naïve set‑based or DP with a 26‑loop per character – both are unnecessary.  
> **Ugly** – Over‑engineering the solution or forgetting the +1 trick can lead to off‑by‑ones.

---

## 📄 SEO‑Optimized Blog Article

### Title
> **Master LeetCode 2262 – Total Appeal of a String | Java, Python, C++ O(n) Solution**

### Meta Description
> Solve LeetCode 2262 *Total Appeal of a String* in linear time. Learn the character‑contribution trick with clear Java, Python, and C++ code. Perfect for interview prep!

---

## 🚀 Blog Post

### Introduction
Interviews love problems that disguise a simple pattern behind a daunting “Hard” label. *Total Appeal of a String* is one such classic. Despite its `Hard` tag, the most efficient solution is a single‑pass `O(n)` algorithm with `O(1)` memory. This post walks you through:

- Problem restatement
- Naïve pitfalls
- The **good** linear solution
- The **bad** quadratic/​cubic brute force
- The **ugly** DP version with a 26‑loop
- Clean code snippets (Java, Python, C++)
- Complexity analysis
- Edge‑case discussion
- Quick test harness

---

### 1️⃣ Problem Statement

> **Appeal** = number of distinct characters in a string.  
> For every contiguous substring of `s`, compute its appeal and sum them all.

Example: `s = "aba"`  
Substrings → `"a"`, `"ab"`, `"aba"`, `"b"`, `"ba"`, `"a"`  
Appeals → 1, 2, 2, 1, 2, 1 → **Total = 9**.

---

### 2️⃣ Naïve Approach – The Big No‑No

```text
for every i in [0,n):
  for every j in [i,n):
    build set of s[i..j]
    ans += size(set)
```

- **Time** `O(n³)` → impossible for `n=10⁵`.  
- **Space** `O(1)` (ignoring the temporary set).  

This is the classic *exponential blow‑up* you must avoid in interviews.

---

### 3️⃣ The “Good” Insight: Character Contribution

Consider each occurrence of a character separately.

1. `last[26]` holds the index of the last occurrence of each letter (`-1` if none).  
2. For the occurrence at index `i` of character `c`:
   - Substrings that **start after** `last[c]` and **end at or after** `i` contain this `c`.  
   - Number of starts: `i - last[c]`.  
   - Number of ends: `n - i`.  
3. Contribution: `(i - last[c]) * (n - i)`.

Add this for every index.  

**Result:** Linear scan, constant auxiliary space.

> **Why it’s *Good*** – No nested loops, no expensive sets, just integer arithmetic.

---

### 4️⃣ The “Ugly” DP with a 26‑loop

A common DP pattern:

```text
total = 0
for each character:
    total += i - last[c]
    last[c] = i
```

Then, in every iteration, sum all `last` values via an inner `for (k=0;k<26;k++) total += last[k];`  
This is **`O(26 · n)`**, still linear but the constant factor hurts readability and performance.

> **Ugly** – Adds unnecessary loops and makes the logic harder to maintain.

---

### 5️⃣ Full Code Snippets

| Language | Code |
|----------|------|
| **Java** | <details><summary>Click to view</summary><pre>class Solution {<br>    public long appealSum(String s) {<br>        int n = s.length();<br>        int[] last = new int[26];<br>        long res = 0;<br>        for (int i = 0; i < n; ++i) {<br>            int idx = s.charAt(i) - 'a';<br>            res += 1L * (i - last[idx]) * (n - i);<br>            last[idx] = i;<br>        }<br>        return res;<br>    }<br>}<br></pre></details> |
| **Python** | <details><summary>Click to view</summary><pre>class Solution:<br>    def appealSum(self, s: str) -> int:<br>        n = len(s)<br>        last = defaultdict(lambda: -1)<br>        res = 0<br>        for i, ch in enumerate(s):<br>            res += (i - last[ch]) * (n - i)<br>            last[ch] = i<br>        return res</pre></details> |
| **C++** | <details><summary>Click to view</summary><pre>class Solution {<br>public:<br>    long long appealSum(string s) {<br>        int n = s.size();<br>        vector<int> last(26, -1);<br>        long long res = 0;<br>        for (int i = 0; i < n; ++i) {<br>            int idx = s[i] - 'a';<br>            res += 1LL * (i - last[idx]) * (n - i);<br>            last[idx] = i;<br>        }<br>        return res;<br>    }<br>};</pre></details> |

All codes compile on LeetCode, CodeChef, and other online judges.

---

### 6️⃣ Complexity Recap

- **Time**: `O(n)` (single pass).  
- **Space**: `O(1)` (26‑element array or dictionary).  
- **Numerical operations**: `1 × 10⁶` multiplications and adds for `n=10⁵` – trivial on modern CPUs.

---

### 7️⃣ Testing – Quick Python Script

```python
def brute(s):
    # O(n^3) brute force for small strings
    ...

def test():
    import random, string
    for n in range(1, 100):
        for _ in range(200):
            s = ''.join(random.choice('abcd') for _ in range(n))
            if brute(s) != Solution().appealSum(s):
                print('Fail', s, brute(s), Solution().appealSum(s))
                return
    print('All good!')

test()
```

Run this script locally to validate your implementation before submitting.

---

### 8️⃣ Final Takeaway

*Total Appeal of a String* teaches you that many “Hard” problems boil down to a hidden linear trick. Master the character‑contribution pattern, and you’ll answer confidently in Java, Python, or C++ during coding interviews. Remember: always look for a single‑pass arithmetic solution before resorting to sets or nested loops.

---

### 📚 Further Reading

- “Cracking the Coding Interview” – Character counting tricks
- “LeetCode Hard Problems” – Pattern‑recognition chapter
- “Coding Interview Prep” – O(n) tricks collection

Happy coding! 🚀

---

## 📣 Final Thought

Whether you’re tackling coding challenges or acing technical interviews, the lesson from *Total Appeal of a String* is clear: look for the **simple arithmetic pattern** behind a complex problem, keep your loops tight, and always aim for linear time. Happy interviewing!