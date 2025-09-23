---
title: LeetCode 2053. Kth Distinct String in an Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 2053 – Kth Distinct String in an Array  
**Java | Python | C++ – 3 clean, interview‑ready solutions**  

> **Why this problem matters**  
> *LeetCode 2053* is a “quick‑hit” interview question that tests three core skills:  
> 1. **Hash‑map / dictionary** usage for frequency counting.  
> 2. **Two‑pass logic** – first count, then find the target.  
> 3. **Ordering** – preserving the first‑appearance order.  
> Mastering it shows you can solve real‑world problems in O(n) time and O(n) space, a must‑have for any software‑engineering interview.  

---

## 1. Problem Statement (LeetCode 2053)

Given an array of lowercase strings `arr` and an integer `k`, return the *k*‑th distinct string in the array.  
A *distinct* string appears **exactly once** in `arr`.  
If fewer than `k` distinct strings exist, return the empty string `""`.

**Constraints**

| | Value |
|---|---|
| 1 ≤ k ≤ arr.length ≤ 1000 |  
| 1 ≤ arr[i].length ≤ 5 |  
| arr[i] consists of lowercase English letters |  

---

## 2. Intuition

1. **Count occurrences** – A hash map (`string → int`) tells us whether a string is unique.  
2. **Iterate again** – Scan the original array in order, keep a counter of distinct strings found.  
3. When the counter equals `k`, we have found the answer.  
4. If the loop ends before reaching `k`, return `""`.

---

## 3. Solution Overview

```
1. Build a frequency map:  for each s in arr → freq[s]++.
2. Traverse arr again:
      if freq[s] == 1:
          distinctSeen++
          if distinctSeen == k: return s
3. return ""
```

**Complexities**

| | Time | Space |
|---|---|---|
| Overall | **O(n)** | **O(n)** (for the frequency map) |

*`n` = arr.length.*

---

## 4. Code (3 Languages)

> All solutions are LeetCode‑style `Solution` classes/functions.  
> They compile with Java 17, Python 3.10+, and C++17.

### 4.1 Java

```java
import java.util.HashMap;

class Solution {
    public String kthDistinct(String[] arr, int k) {
        // 1️⃣ Build frequency map
        HashMap<String, Integer> freq = new HashMap<>();
        for (String s : arr) freq.put(s, freq.getOrDefault(s, 0) + 1);

        // 2️⃣ Scan again for the k‑th distinct
        int distinctSeen = 0;
        for (String s : arr) {
            if (freq.get(s) == 1) {
                distinctSeen++;
                if (distinctSeen == k) return s;
            }
        }
        return "";
    }
}
```

### 4.2 Python 3

```python
from typing import List
from collections import Counter

class Solution:
    def kthDistinct(self, arr: List[str], k: int) -> str:
        # 1️⃣ Count frequencies
        freq = Counter(arr)

        # 2️⃣ Find the k‑th distinct in order
        for s in arr:
            if freq[s] == 1:
                k -= 1
                if k == 0:
                    return s
        return ""
```

### 4.3 C++

```cpp
#include <vector>
#include <string>
#include <unordered_map>

class Solution {
public:
    std::string kthDistinct(std::vector<std::string>& arr, int k) {
        std::unordered_map<std::string, int> freq;
        for (const auto& s : arr) ++freq[s];

        for (const auto& s : arr) {
            if (freq[s] == 1) {
                if (--k == 0) return s;
            }
        }
        return "";
    }
};
```

---

## 5. The Good, The Bad, The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Clarity** | One‑pass frequency + second pass → easy to read | Some may skip the second pass and try to store all distinct strings in a vector (slower) | Misusing `HashMap<String, Boolean>` (true/false) instead of an integer count can lead to bugs when a string appears more than twice. |
| **Performance** | O(n) time, O(n) space | Using a sorted map or list to maintain order adds unnecessary overhead | Forgetting to use `unordered_map` in C++ can cause O(n²) with string keys. |
| **Ordering** | Natural order preserved because we re‑scan `arr` | Relying on `HashMap` iteration order (unordered) will break the requirement | Mixing order and uniqueness checks in one pass can miscount. |
| **Edge Cases** | Handles empty string array, `k` larger than distinct count | None | Returning `""` for no result may be misinterpreted as a valid string in some contexts. |

**Takeaway:** Stick to the two‑pass approach with a frequency map. It’s fast, simple, and bulletproof.

---

## 6. Testing & Validation

```python
# Python test harness
def test():
    sol = Solution()
    assert sol.kthDistinct(["d","b","c","b","c","a"], 2) == "a"
    assert sol.kthDistinct(["aaa","aa","a"], 1) == "aaa"
    assert sol.kthDistinct(["a","b","a"], 3) == ""
    assert sol.kthDistinct(["x","x","y","z"], 2) == "z"
    print("All tests passed.")
test()
```

Run similar unit tests in Java (JUnit) and C++ (GoogleTest or simple `assert`).

---

## 7. Variations & Extensions

| Variation | How to adapt |
|-----------|--------------|
| *Find the i‑th non‑duplicate* | Same algorithm, just change the target `k`. |
| *Case‑insensitive distinct* | Normalize all strings to lower/upper case before counting. |
| *Strings with digits or special chars* | No change – the map keys work for any string. |
| *Large input (n > 10⁶)* | Use streaming: count first, then streaming second pass; memory still O(n) but may need external storage. |

---

## 8. Interview Tips

1. **Explain your algorithm before coding** – interviewer loves a clear plan.  
2. **Mention edge cases**: “What if `k` is bigger than the number of distinct strings?”  
3. **Ask clarifying questions**: “Do we need to preserve the original order?”  
4. **Choose the right data structure**: `unordered_map`/`HashMap` is O(1) average.  
5. **Space optimisation**: In C++ use `unordered_map<string, int>`. In Java use `HashMap`.  
6. **Time complexity**: Emphasise you’re doing two linear scans – O(n).  
7. **Practice**: Write the solution on a whiteboard; keep it clean.

---

## 9. Conclusion – Why Mastering This Problem Helps You Land a Job

LeetCode 2053 is the quintessential “speed‑vs‑clarity” interview problem.  
By solving it in **Java, Python, and C++**, you show:

* You can leverage **hash‑maps** for frequency counting – a core data‑structure skill.  
* You understand **stable ordering** and how to preserve it.  
* You can reason about **time/space trade‑offs** and articulate them clearly.  

These are exactly the kinds of conversations hiring managers look for during a coding interview.  

> **Next step** – pair the code with a well‑written blog post (the one below). Share it on LinkedIn, Medium, or a personal portfolio site. Use the same keywords in your résumé: *“Solved LeetCode 2053 in Java/Python/C++ (O(n)) – interview‑ready frequency‑count solution.”*  

---

# 🎯  Blog Post: “Kth Distinct String in an Array – 3 Interview‑Ready Solutions”

> **Target Audience** – Aspiring software engineers, recruiters, and anyone who wants a SEO‑friendly tech blog.  
> **SEO Keywords**: LeetCode 2053, Kth Distinct String, interview question, coding interview, Java solution, Python solution, C++ solution, job interview tips, data structures, algorithm design.  

---

### 🔎 Title  
**LeetCode 2053 – Kth Distinct String: Java, Python, and C++ Solutions + Interview Tips**

### 📝 Meta Description  
> Learn how to crack LeetCode 2053 – “Kth Distinct String in an Array.” 3 clean solutions in Java, Python, and C++ with time‑space analysis, edge‑case handling, and interview advice. Perfect for coding interview prep.

---

## 1. Introduction

When recruiters scour LinkedIn or GitHub for proof of algorithmic chops, one problem often pops up: *“Show me how you solve LeetCode 2053.”*  
That question tests your grasp of hash maps, linear scans, and order preservation – skills that translate directly to production code.  

Below you’ll find:

* The full problem statement  
* A clear algorithmic strategy  
* Three production‑grade implementations (Java, Python, C++)  
* A “Good‑Bad‑Ugly” analysis that highlights pitfalls you should avoid  
* Testing scripts and interview‑ready talking points  

Let’s dive in.

---

## 2. Problem Statement (Re‑printed for SEO)

> **Kth Distinct String in an Array** (LeetCode 2053)  
> *Input*: `string[] arr`, `int k`  
> *Output*: `string` – the `k`‑th distinct string or `""` if none  
> *Distinct*: Appears **once** in `arr`  
> *Order*: First‑appearance order matters

---

## 3. Algorithmic Intuition

1. **Count frequencies** with a hash map.  
2. **Scan the array in original order** and count the distinct strings.  
3. Return the string when the count equals `k`.  

Two linear passes, `O(n)` time, `O(n)` space.

---

## 4. Three Clean Implementations

```java
/* Java (LeetCode style) */
class Solution {
    public String kthDistinct(String[] arr, int k) {
        Map<String, Integer> freq = new HashMap<>();
        for (String s : arr) freq.put(s, freq.getOrDefault(s, 0) + 1);

        int seen = 0;
        for (String s : arr) {
            if (freq.get(s) == 1) {
                if (++seen == k) return s;
            }
        }
        return "";
    }
}
```

```python
# Python 3.10
from collections import Counter
class Solution:
    def kthDistinct(self, arr: List[str], k: int) -> str:
        freq = Counter(arr)
        for s in arr:
            if freq[s] == 1:
                if k == 1: return s
                k -= 1
        return ""
```

```cpp
// C++17 (LeetCode style)
#include <unordered_map>
#include <vector>
#include <string>

class Solution {
public:
    std::string kthDistinct(std::vector<std::string>& arr, int k) {
        std::unordered_map<std::string, int> freq;
        for (const auto& s : arr) ++freq[s];
        for (const auto& s : arr) {
            if (freq[s] == 1 && --k == 0) return s;
        }
        return "";
    }
};
```

---

## 5. Edge‑Case Checklist

| Edge case | Why it matters | Mitigation |
|-----------|----------------|------------|
| `k` > distinct count | Must return `""` | Return early after loop | Avoid returning a default string that could be valid input. |
| Duplicate strings > 2 occurrences | Frequency map handles any count | None | Boolean map can mis‑label “duplicate” vs “non‑duplicate.” |
| Empty `arr` | Must not crash | None | Ensure loops handle zero length gracefully. |

---

## 6. Testing

```python
def run_tests():
    sol = Solution()
    tests = [
        (["d","b","c","b","c","a"], 2, "a"),
        (["aaa","aa","a"], 1, "aaa"),
        (["a","b","a"], 3, ""),
        (["x","x","y","z"], 2, "z"),
    ]
    for arr, k, expected in tests:
        assert sol.kthDistinct(arr, k) == expected
    print("✅ All tests passed.")
run_tests()
```

Run the equivalent Java/JUnit and C++/GoogleTest suites to validate.

---

## 7. Variations to Discuss

1. **Case‑insensitive distinct** – `freq[s.lower()]` in Python, `std::transform` in C++.  
2. **Longest distinct string** – Replace the `k` counter with a length comparison.  
3. **Streaming input** – Process chunks and write intermediate counts to disk if `n` > memory.

---

## 8. Interview‑Ready Talking Points

* **Explain the two‑pass algorithm** – Emphasize O(n) time and preserving order.  
* **Why a frequency map?** – Because we need O(1) look‑ups for each string.  
* **Edge‑case handling** – Return `""` when no result.  
* **Possible pitfalls** – Boolean map vs count, unordered iteration, forgetting to decrement `k`.  

---

## 9. How This Solves Your Job Search

1. **Showcasing Data‑Structure Mastery** – Recruiters love clear, scalable solutions.  
2. **Rapid Interview Time** – The two‑pass pattern is fast to code under a 1‑minute constraint.  
3. **Portfolio Value** – Add the snippet to your GitHub repo; tag it `#LeetCode2053`.  
4. **Resume Bullet** – “Implemented O(n) solution for LeetCode 2053 – Kth Distinct String in an Array, using frequency maps and ordered scans.”  

---

## 10. Final Takeaway

- **Keep it simple** – Frequency map + second pass.  
- **Avoid over‑engineering** – No need for sorted maps or extra vectors.  
- **Test thoroughly** – Edge cases are the real interview examiners.  

With these three implementations and a polished interview narrative, you’re ready to ace LeetCode 2053 and impress hiring managers in the next round. Happy coding! 🚀

---