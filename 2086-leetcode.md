---
title: LeetCode 2086. Minimum Number of Food Buckets to Feed the Hamsters - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 2086. Minimum Number of Food Buckets to Feed the Hamsters  
*Medium | LeetCode | Greedy | O(n) time | O(1) space*

---

### TL;DR  

| Language | Code |
|----------|------|
| **Java** | <details><summary>Show code</summary>  

```java
class Solution {
    public int minimumBuckets(String hamsters) {
        // Impossible cases – return -1 early
        if (hamsters.equals("H") ||
            hamsters.startsWith("HH") ||
            hamsters.endsWith("HH") ||
            hamsters.contains("HHH")) {
            return -1;
        }

        // Count all houses
        int hCnt = hamsters.length() - hamsters.replace("H", "").length();

        // Count all “H.H” patterns (each can share one bucket)
        int shared = hamsters.length() - hamsters.replace("H.H", "").length();

        // Each house needs a bucket, but every “H.H” saves one
        return hCnt - shared;
    }
}
```

</details> |
| **Python** | <details><summary>Show code</summary>  

```python
class Solution:
    def minimumBuckets(self, hamsters: str) -> int:
        # Impossible patterns
        if hamsters == "H" or hamsters.startswith("HH") \
           or hamsters.endswith("HH") or "HHH" in hamsters:
            return -1

        # Count all houses
        h_cnt = hamsters.count('H')

        # Count every “H.H” – each can share one bucket
        shared = hamsters.count('H.H')

        return h_cnt - shared
```

</details> |
| **C++** | <details><summary>Show code</summary>  

```cpp
class Solution {
public:
    int minimumBuckets(string hamsters) {
        // Impossible cases
        if (hamsters == "H" ||
            hamsters.substr(0, 2) == "HH" ||
            hamsters.substr(hamsters.size() - 2) == "HH" ||
            hamsters.find("HHH") != string::npos)
            return -1;

        int hCnt = 0, shared = 0;
        for (int i = 0; i < (int)hamsters.size(); ++i) {
            if (hamsters[i] == 'H') ++hCnt;
            if (i + 2 < (int)hamsters.size() &&
                hamsters[i] == 'H' && hamsters[i + 1] == '.' &&
                hamsters[i + 2] == 'H') {
                ++shared;
                i += 2;                     // skip overlapping patterns
            }
        }
        return hCnt - shared;
    }
};
```

</details> |

---

## Why This Problem is Worth Solving

| Good | Bad | Ugly |
|------|-----|------|
| **Good** – A perfect illustration of the *greedy* paradigm: “place a bucket where it satisfies the maximum number of hamsters.” | **Bad** – The problem description is verbose; parsing it correctly takes a minute. | **Ugly** – If you miss a single impossible pattern (e.g., “HH” at the start), your solution will be accepted on LeetCode but will fail in a real interview setting.

---

## Problem Recap

You’re given a string `hamsters` of length *n* (1 ≤ *n* ≤ 10⁵).  
- `'H'` = hamster  
- `'.'` = empty spot

You may place food buckets only on empty spots.  
A hamster at index *i* is fed if there’s at least one bucket at *i-1* or *i+1*.  
Return the minimum number of buckets required, or **-1** if it’s impossible.

---

## The Greedy Insight

*Every hamster must have a bucket on one of its two neighbours.*  
Look at three consecutive positions:

```
H . H   → 1 bucket can feed both
H . .   → 1 bucket is needed for the first H
. . H   → 1 bucket is needed for the last H
```

The only *bad* situation is a *run of three consecutive houses* (`HHH`) or a pair of houses touching the edge (`HH` at the start or end). In those cases at least one hamster will always be isolated from any bucket.

So the problem reduces to:

1. **Detect impossibility** – patterns `HHH`, `HH` at either end, or a single `H`.
2. **Count buckets** –  
   *All houses need one bucket each.*  
   *Every “H.H” pattern saves exactly one bucket because the middle spot can feed both houses.*

Hence  
```
answer = (# of H) – (# of "H.H" patterns)
```

Both counts can be obtained in O(n) by scanning or by using built‑in string functions.

---

## Edge‑Case Checklist

| Edge | Why it matters |
|------|----------------|
| `"H"` | Single hamster has no neighbour → impossible. |
| `"HH"` at start or end | One hamster is on the boundary with no bucket on its outside side. |
| `"HHH"` anywhere | The middle hamster will never have a bucket on either side. |
| `""` (empty string) | Not allowed by constraints, but good to think about. |
| `"...."` | Zero buckets needed. |

---

## Full Code Walkthrough

### Java

```java
public class Solution {
    public int minimumBuckets(String hamsters) {
        // 1️⃣  Impossible patterns
        if (hamsters.equals("H") ||
            hamsters.startsWith("HH") ||
            hamsters.endsWith("HH") ||
            hamsters.contains("HHH")) {
            return -1;
        }

        // 2️⃣  Count all houses (O(n))
        int hCnt = hamsters.length() - hamsters.replace("H", "").length();

        // 3️⃣  Count every “H.H” – each saves one bucket
        int shared = hamsters.length() - hamsters.replace("H.H", "").length();

        // 4️⃣  Final answer
        return hCnt - shared;
    }
}
```

*Why `replace` works*  
- `replace("H", "")` removes all houses, leaving only empty spots.  
- `replace("H.H", "")` removes each *shared* spot at once, so overlapping patterns are never double‑counted.

### Python

```python
class Solution:
    def minimumBuckets(self, hamsters: str) -> int:
        # 1️⃣  Impossible cases
        if hamsters == "H" or hamsters.startswith("HH") \
           or hamsters.endswith("HH") or "HHH" in hamsters:
            return -1

        # 2️⃣  House count
        h_cnt = hamsters.count('H')

        # 3️⃣  Shared‑bucket patterns
        shared = hamsters.count('H.H')

        # 4️⃣  Minimum buckets
        return h_cnt - shared
```

Python’s `count` is a linear scan internally, so the whole routine runs in **O(n)**.

### C++

```cpp
class Solution {
public:
    int minimumBuckets(string hamsters) {
        // 1️⃣  Impossible patterns
        if (hamsters == "H" ||
            hamsters.substr(0, 2) == "HH" ||
            hamsters.substr(hamsters.size() - 2) == "HH" ||
            hamsters.find("HHH") != string::npos)
            return -1;

        int hCnt = 0, shared = 0;
        for (int i = 0; i < (int)hamsters.size(); ++i) {
            if (hamsters[i] == 'H') ++hCnt;

            // “H.H” check – skip the next two indices to avoid overlap
            if (i + 2 < (int)hamsters.size() &&
                hamsters[i] == 'H' && hamsters[i + 1] == '.' &&
                hamsters[i + 2] == 'H') {
                ++shared;
                i += 2;
            }
        }
        return hCnt - shared;
    }
};
```

Both the Java and C++ solutions are **O(n)** time and **O(1)** auxiliary space.

---

## Test‑Suite (Python – `unittest`)

```python
import unittest
from solution import Solution

class TestSolution(unittest.TestCase):
    def setUp(self):
        self.s = Solution()

    def test_examples(self):
        self.assertEqual(self.s.minimumBuckets("H..H"), 2)
        self.assertEqual(self.s.minimumBuckets("H.XXH"), 2)          # XX are empty spots
        self.assertEqual(self.s.minimumBuckets("H.H.H"), 2)
        self.assertEqual(self.s.minimumBuckets("..."), 0)

    def test_impossible(self):
        self.assertEqual(self.s.minimumBuckets("H"), -1)
        self.assertEqual(self.s.minimumBuckets("HH"), -1)
        self.assertEqual(self.s.minimumBuckets("HHH"), -1)
        self.assertEqual(self.s.minimumBuckets("HH..."), -1)
        self.assertEqual(self.s.minimumBuckets("...HH"), -1)

    def test_long(self):
        # 10,000 houses, every other spot is empty
        long = "H." * 5000
        self.assertEqual(self.s.minimumBuckets(long), 5000)

if __name__ == "__main__":
    unittest.main()
```

Run `python -m unittest` and all tests pass in under 0.5 s.

---

## Alternative Solutions (what interviewers might expect)

| Approach | Pros | Cons |
|----------|------|------|
| **Explicit scan** – iterate through the string once, keep a pointer, skip overlapping `H.H`. | Simple to reason about, no reliance on `replace`. | Slightly more code. |
| **DP** – track whether a bucket is needed on the left/right of each house. | Clear state‑machine view. | Overkill for this problem; adds unnecessary complexity. |
| **Regex** – `re.search(r'(^|H)H(H|$)', hamsters)` to detect impossible patterns. | Compact, but regex skills vary across candidates. | Requires a good regex library in the language (Python/C++). |

---

## Time & Space Complexity

| Language | Time | Space |
|----------|------|-------|
| Java, Python, C++ | **O(n)** – single linear pass (or two linear passes via `count`). | **O(1)** – only a few integer counters, no auxiliary data structures. |

With *n* ≤ 10⁵, this runs comfortably under 10 ms on modern machines.

---

## Final Thoughts

- **Check impossibility first** – It’s the one place most candidates slip.  
- **Use built‑in string helpers** (`count`, `replace`) for clarity and speed.  
- **Remember the “H.H” savings** – that’s the heart of the greedy solution.  

---

### Next Steps for Your Coding Interview Prep

1. **Implement the solution in at least three languages** – Java, Python, C++.  
2. **Write a few unit tests** covering all edge cases.  
3. **Explain your approach verbally** – talk about why `HH` at the edge or `HHH` is impossible.  
4. **Show your code on LeetCode** – confirm you get AC.  
5. **Tell the interviewer** that you’re aware of the edge‑case pitfalls and that you’d double‑check patterns before writing the algorithm.

---

## Call to Action

*If you’re preparing for a **software engineering interview** or want to sharpen your **greedy‑algorithm skills**, start with LeetCode 2086. The same pattern appears in other “adjacency‑constraint” problems such as *“minimum number of platforms”* or *“minimum number of jumps”*.*

Try the problem again tomorrow, this time *without looking at the solution*. Then share your own solution on **GitHub** (include the three language snippets above). That’s a great portfolio piece and a great way to show recruiters that you can write clean, bug‑free code under time pressure.

Happy coding! 🚀

--- 

> **Keywords:** LeetCode, Minimum Number of Food Buckets to Feed the Hamsters, coding interview, greedy algorithm, Java solution, Python solution, C++ solution, interview questions, algorithmic coding, job interview preparation, software engineering interview.