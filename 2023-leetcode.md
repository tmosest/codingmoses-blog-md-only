---
title: LeetCode 2023. Number of Pairs of Strings With Concatenation Equal to Target - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🧩 LeetCode 2023 – “Number of Pairs of Strings With Concatenation Equal to Target”

| Language | Solution | Complexity |
|----------|----------|------------|
| **Java** | ✅ 1‑pass hash map | O(n · L) time, O(n) space |
| **Python** | ✅ 1‑pass hash map | O(n · L) time, O(n) space |
| **C++** | ✅ 1‑pass hash map | O(n · L) time, O(n) space |

> *“If you want to land a tech job, you need to master problems that showcase your ability to think algorithmically and write clean code.”* – **Your future interviewers**

Below you’ll find the complete, production‑ready code for each language, followed by a detailed blog‑style write‑up that explains the **good, the bad, and the ugly** of solving this problem. The article is SEO‑optimized to help you rank higher on search engines and get noticed by recruiters.

---

## 📦 The Problem (LeetCode 2023)

> **Given** an array of digit strings `nums` and a digit string `target`, **return** the number of pairs of indices `(i, j)` (where `i != j`) such that `nums[i] + nums[j] == target`.

**Constraints**

| # | Constraint | Detail |
|---|------------|--------|
| 1 | `2 <= nums.length <= 100` | Small array size |
| 2 | `1 <= nums[i].length <= 100` | Individual strings can be long |
| 3 | `2 <= target.length <= 100` | Target string can be long |
| 4 | All strings consist of digits and **no leading zeros** |

---

## 🏁 Quick Overview

1. **Naïve Approach** – O(n²) nested loops. Works for the given constraints but is not elegant or scalable.
2. **Hash‑Map (Optimal)** – O(n) time, O(n) space. Count the occurrences of every string and then look up the complementary part of the target.
3. **Why Hash‑Map?** – It eliminates redundant comparisons, gives constant‑time look‑ups, and keeps the code succinct.

---

## 🔧 Code Solutions

> All solutions share the same core idea: for each string `s` in `nums`, if `target` starts with `s`, the remaining suffix `t` must exist in `nums` (except when `s == t`, we must avoid counting the same index twice).

### 1️⃣ Java

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    /**
     * Counts the number of ordered pairs (i, j) such that nums[i] + nums[j] == target.
     * Time:  O(n * L)
     * Space: O(n)
     */
    public int numOfPairs(String[] nums, String target) {
        Map<String, Integer> freq = new HashMap<>();
        for (String s : nums) {
            freq.put(s, freq.getOrDefault(s, 0) + 1);
        }

        int count = 0;
        for (String s : nums) {
            if (target.startsWith(s)) {
                String suffix = target.substring(s.length());
                int add = freq.getOrDefault(suffix, 0);
                if (suffix.equals(s)) add--;   // exclude i == j
                count += add;
            }
        }
        return count;
    }
}
```

### 2️⃣ Python

```python
from collections import Counter
from typing import List

def num_of_pairs(nums: List[str], target: str) -> int:
    """
    Returns the number of ordered pairs (i, j) where nums[i] + nums[j] == target.
    Time:  O(n * L)
    Space: O(n)
    """
    freq = Counter(nums)
    count = 0

    for s in nums:
        if target.startswith(s):
            suffix = target[len(s):]
            count += freq.get(suffix, 0)
            if suffix == s:
                count -= 1   # avoid i == j

    return count
```

### 3️⃣ C++

```cpp
#include <vector>
#include <string>
#include <unordered_map>

int numOfPairs(const std::vector<std::string>& nums, const std::string& target) {
    std::unordered_map<std::string, int> freq;
    for (const auto& s : nums) {
        ++freq[s];
    }

    int count = 0;
    for (const auto& s : nums) {
        if (target.rfind(s, 0) == 0) {          // target.startsWith(s)
            std::string suffix = target.substr(s.size());
            auto it = freq.find(suffix);
            if (it != freq.end()) {
                count += it->second;
                if (suffix == s) count--;       // avoid i == j
            }
        }
    }
    return count;
}
```

> All three snippets compile on the latest JDK 17 / Python 3.10+ / C++17 compilers and run in linear time.

---

## 📖 Blog Article – “The Good, the Bad, and the Ugly of Pair Concatenation”

> **Title**: *LeetCode 2023 – Mastering the “Number of Pairs of Strings with Concatenation” Problem (Java, Python, C++)*

### 1. Introduction

The “Number of Pairs of Strings with Concatenation Equal to Target” problem appears deceptively simple. But it teaches a lesson that shows up in real‑world coding interviews: **optimizing data structures and avoiding unnecessary work** can turn a naïve O(n²) solution into an elegant O(n) one.

Below we walk through the problem, discuss the naive approach, explain the optimal hash‑map solution, and reflect on the *good, bad, and ugly* aspects of each.

> **Why read this?**  
> *If you’re preparing for technical interviews, mastering this problem will not only boost your LeetCode score but also give you a talking point that recruiters love.*

---

### 2. Problem Recap

- **Input**: `nums` – array of digit strings, `target` – digit string.
- **Output**: Count of ordered pairs `(i, j)` (`i ≠ j`) such that `nums[i] + nums[j] == target`.

*Examples*  
1. `nums = ["777","7","77","77"], target = "7777"` → 4 pairs  
2. `nums = ["123","4","12","34"], target = "1234"` → 2 pairs  
3. `nums = ["1","1","1"], target = "11"` → 6 pairs  

The key nuance: **indices must differ** – the same string value can appear multiple times, and each occurrence counts as a separate index.

---

### 3. The Naïve (Bad) Approach

```python
count = 0
for i in range(n):
    for j in range(n):
        if i != j and nums[i] + nums[j] == target:
            count += 1
```

**Pros**  
- *Simple to write and understand.*  
- Works under the problem’s small constraints (n ≤ 100).

**Cons**  
- **O(n²)** time complexity.  
- Redundant string concatenations (`nums[i] + nums[j]`) for every pair.  
- Poor scalability – would time out on larger inputs.

In interview terms, this is *good enough for a quick demo*, but recruiters look for *efficient* solutions. The naïve method also hides a subtle bug: if `nums[i] == target`, you still need to ensure the suffix exists in the array. The hash‑map solution handles this cleanly.

---

### 4. The Optimal (Good) Approach – One‑Pass Hash Map

**Idea**  
For each string `s` in `nums`, if `target` starts with `s`, the remaining suffix `t` must appear somewhere in `nums`. Counting occurrences of each string lets us find `t` in **O(1)**.

**Algorithm Steps**

1. **Build Frequency Map**  
   `freq[s] = number of times s appears in nums`.

2. **Iterate over each string s**  
   - If `target` starts with `s`, let `t = target.substring(len(s))`.  
   - Add `freq[t]` to the answer.  
   - If `t == s`, subtract 1 to avoid pairing a string with itself (since indices must differ).

**Why It Works**  
- We never compare every pair.  
- Constant‑time look‑up for the complementary part.  
- Linear in `n` (plus the string length `L` which is unavoidable because we must inspect `target`).

**Complexities**  
- *Time*: `O(n · L)` – `n` loop iterations, each doing a prefix check and a map lookup.  
- *Space*: `O(n)` – for the frequency map.

**Proof of Correctness**  
Every ordered pair that satisfies the condition will be counted exactly once:

- Suppose `(i, j)` is a valid pair. Let `s = nums[i]`.  
- In the loop for `s`, we see that `target` starts with `s`, compute `t = nums[j]`.  
- We add `freq[t]`, which counts *all* indices `k` such that `nums[k] == t`.  
- If `k == j`, the addition is correct; if `k == i`, we subtract 1 when `s == t`.  

All pairs are captured, and no extraneous pairs are counted.

**Edge Cases Handled**

- Multiple identical strings (`["1","1","1"]`).  
- Empty suffix (only when `s` equals `target`, but the problem guarantees `target.length >= 2`).  
- Very long strings (concatenation would be expensive, but substring extraction is cheap).

---

### 5. Implementation Snippets (One‑Pass Hash Map)

(See the code section above.)

All three languages share the same structure:

- Build a `Counter`/`HashMap`/`unordered_map`.  
- Use `startsWith`/`target.rfind`/`target.rfind(s, 0)`.  
- `suffix = target[len(s):]` (or `substr`).  
- Add frequency, adjust for self‑pairing.

---

### 5. The Ugly – Potential Pitfalls & Common Mistakes

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| **Index‑value collision** – using a map keyed by *value* but forgetting that identical values can appear at different indices. | Assuming each value is unique. | Store *frequency*, not a boolean flag. |
| **Off‑by‑one in substring** – forgetting to use `len(s)` instead of `s.size()`. | Mismatch between language indexing conventions. | Test with a unit‑test that covers single‑character strings. |
| **Self‑pairing** – counting `(i, i)` when `s == t`. | Many candidates overlook the `i != j` rule. | Subtract 1 when the suffix equals the prefix string. |
| **Large L** – string concatenation inside nested loops can become expensive. | Even if `n` is small, concatenating 100‑character strings 10 000 times is wasteful. | Avoid concatenation by using `startsWith`/`substring`. |
| **Case sensitivity** – treating `"1"` and `"01"` differently. | Problem guarantees no leading zeros, but some solutions mishandle empty suffixes. | Ensure `target` length ≥ 2 and check `target.startsWith(s)` first. |

---

### 6. Why Recruiters Love the Hash‑Map Solution

- **Scalability**: The code remains fast even if `n` grows to 10⁵ (the time complexity doesn’t change).  
- **Readability**: One loop, one map lookup, minimal branching.  
- **Idiomatic use of language features**: Java’s `Map.getOrDefault`, Python’s `Counter`, C++’s `unordered_map`.  
- **Test‑ability**: Easy to unit‑test each component (frequency map, suffix extraction, self‑pair adjustment).

---

### 7. Take‑aways for Your Next Interview

| Tip | How It Helps |
|-----|--------------|
| **Start with a clean frequency map** | It instantly tells you how many times a substring appears. |
| **Avoid double counting** | Remember that the problem asks for *ordered* pairs, so `(i, j)` and `(j, i)` are distinct. |
| **Edge‑case first** | Write tests for `"1" * 3` & `"11"`, `"777" * 2` & `"7777"`, etc. |
| **Time complexity matters** | Even if the constraints are small, always aim for linear or log‑linear solutions. |
| **Leverage built‑in helpers** | `String.startsWith()`, `Counter`, `unordered_map` – they’re optimized under the hood. |

---

### 8. SEO Boost – Keywords & Meta Data

- **Primary Keywords**: LeetCode 2023, number of pairs concatenation, string concatenation problem, optimal solution, hash map interview question.  
- **Secondary Keywords**: Java solution LeetCode, Python solution LeetCode, C++ solution LeetCode, interview coding problems, efficient algorithm.  

> *Add this article to your portfolio site or LinkedIn blog and watch recruiters reach out!*

---

### 9. Closing Thoughts

- The **naïve solution** is easy but fails on the *optimization* criterion.  
- The **hash‑map solution** is concise, scalable, and demonstrates a deep understanding of the problem’s structure.  
- **Interviewers** often probe *why* you chose a particular data structure – be ready to explain the `O(1)` lookup advantage.  

Practice the three implementations, run your own test harnesses, and try extending the problem (e.g., allow non‑digit strings or larger arrays). The learning you gain will be invaluable for your next coding interview.

---

### 🎉 Bonus: Quick Test Harness (Python)

```python
def test():
    assert num_of_pairs(["777","7","77","77"], "7777") == 4
    assert num_of_pairs(["123","4","12","34"], "1234") == 2
    assert num_of_pairs(["1","1","1"], "11") == 6
    print("All tests passed!")

test()
```

---

Happy coding, and may your algorithmic mind be as sharp as your résumé! 🚀