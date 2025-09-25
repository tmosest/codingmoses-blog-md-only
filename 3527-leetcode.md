---
title: LeetCode 3527. Find the Most Common Response - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 3527. Find the Most Common Response  
## 🚀 Problem Recap

Given a 2‑D list `responses` where each sub‑list represents the survey answers collected in one day, we need to determine the **most common response** across all days **after removing duplicate answers from each day**.  

If two or more answers share the same maximum frequency, return the **lexicographically smallest** one.

**Constraints**

| # | Value |
|---|-------|
| 1 | `1 <= responses.length <= 1000` |
| 2 | `1 <= responses[i].length <= 1000` |
| 3 | `1 <= responses[i][j].length <= 10` |
| 4 | Each answer contains only lowercase English letters |

---

## 📌 Core Idea

* **Deduplicate per day** – We need to count each distinct answer once per day.  
* **Hash map** – Keep a global frequency counter (`Map<String, Integer>`).  
* **Track best answer on the fly** – While updating the counter we can check if the new frequency beats the current best or ties with it but is lexicographically smaller.

The overall time complexity is `O(total_answers)` and the space complexity is `O(unique_answers)`.

---

## 🏗️ Reference Implementation

Below are clean, idiomatic solutions in **Java, Python, and C++**. Each follows the same logic: iterate over days, use a `HashSet`/`unordered_set` to deduplicate within that day, update the global count, and keep a running best answer.

> **Tip** – Avoid nested loops over the same day more than once.  
> **Edge‑case** – When the answer list is empty, the function will return an empty string (you can change this behavior if needed).

### 1️⃣ Java (LeetCode‑style)

```java
import java.util.*;

public class Solution {
    public String findCommonResponse(List<List<String>> responses) {
        Map<String, Integer> freq = new HashMap<>();
        String best = "";
        int bestCount = 0;

        for (List<String> day : responses) {
            Set<String> seen = new HashSet<>();
            for (String ans : day) {
                if (seen.add(ans)) {          // only first occurrence per day
                    int cnt = freq.merge(ans, 1, Integer::sum);
                    // update best if necessary
                    if (cnt > bestCount || (cnt == bestCount && ans.compareTo(best) < 0)) {
                        bestCount = cnt;
                        best = ans;
                    }
                }
            }
        }
        return best;
    }
}
```

### 2️⃣ Python 3

```python
from typing import List
from collections import defaultdict

class Solution:
    def findCommonResponse(self, responses: List[List[str]]) -> str:
        freq = defaultdict(int)
        best = ""
        best_cnt = 0

        for day in responses:
            seen = set()
            for ans in day:
                if ans not in seen:
                    seen.add(ans)
                    cnt = freq[ans] + 1
                    freq[ans] = cnt

                    if cnt > best_cnt or (cnt == best_cnt and ans < best):
                        best_cnt = cnt
                        best = ans
        return best
```

### 3️⃣ C++ (C++17)

```cpp
#include <vector>
#include <string>
#include <unordered_map>
#include <unordered_set>
#include <algorithm>

class Solution {
public:
    std::string findCommonResponse(const std::vector<std::vector<std::string>>& responses) {
        std::unordered_map<std::string, int> freq;
        std::string best = "";
        int bestCnt = 0;

        for (const auto& day : responses) {
            std::unordered_set<std::string> seen;
            for (const auto& ans : day) {
                if (seen.insert(ans).second) {   // only first occurrence per day
                    int cnt = ++freq[ans];
                    if (cnt > bestCnt || (cnt == bestCnt && ans < best)) {
                        bestCnt = cnt;
                        best = ans;
                    }
                }
            }
        }
        return best;
    }
};
```

---

## 📈 Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| Deduplication per day | `O(answers_in_day)` | `O(unique_in_day)` (HashSet) |
| Global count update | `O(total_answers)` | `O(unique_answers)` (HashMap) |
| **Overall** | **`O(total_answers)`** | **`O(unique_answers)`** |

For the worst case (`1000 days × 1000 answers/day = 10⁶ answers`), the algorithm comfortably fits within time limits on modern judges.

---

## 📝 Blog Article: “The Good, the Bad, and the Ugly of Finding the Most Common Response”

> **Target audience**: Software engineers preparing for LeetCode or coding interviews, especially those eyeing data‑intensive roles (Data Analyst, Backend Engineer, etc.).  
> **SEO keywords**: `leetcode 3527`, `most common response`, `hash map interview question`, `Java Python C++ solution`, `algorithm interview`, `frequency counter`, `duplicate removal`, `lexicographically smallest`.

---

### Introduction

When you step into a data‑driven interview, you’re often asked to process large volumes of strings or numbers. One deceptively simple question is **“Find the most common response”** – a variant of the classic frequency counter problem. Although it looks trivial, subtle pitfalls (duplicates, lexicographic tie‑breaking) can trip you up. In this post, we walk through the **good** (clear logic, optimal), the **bad** (common mistakes), and the **ugly** (edge‑case nightmares). We’ll finish with a polished, interview‑ready solution in Java, Python, and C++.

---

### The Good: Simplicity & Efficiency

* **Deduplication per day** – A single `HashSet`/`unordered_set` per day eliminates duplicates in *O(day_length)*.  
* **Single pass counting** – Updating a global hash map while iterating is O(1) amortized per element.  
* **On‑the‑fly best tracking** – No need for a separate pass to find the maximum.  
* **Clean tie‑breaking** – `String.compareTo` (or `<` in Python) handles lexicographic order gracefully.  

> *Result*: Linear time, constant auxiliary overhead per day – perfect for interview constraints.

---

### The Bad: Common Pitfalls

1. **Ignoring intra‑day duplicates**  
   *Example*: `["good","good"]` counted twice → wrong frequency.  
   *Fix*: Use a set per day before counting.

2. **Multiple passes**  
   *First pass* to deduplicate → *Second pass* to count → *Third pass* to find max.  
   *Cost*: Extra O(n) overhead and code complexity.

3. **Lexicographic mishaps**  
   *Using `>` or `>=` instead of `<` for tie‑breaking* → returns a lexicographically larger string.

4. **Mutable strings in Java**  
   Using `StringBuilder` or `StringBuffer` for keys can lead to unintended key collisions.

5. **Assuming non‑empty input**  
   Returning `""` when all lists are empty may not match the problem’s expectations; consider throwing an exception or returning `null`.

---

### The Ugly: Edge Cases & Overflow

| Edge Case | Why It Matters | Fix |
|-----------|----------------|-----|
| **Large number of unique strings** (≈10⁶) | Hash map could hit memory limits. | Use custom hash (e.g., `String.intern()`) or an external storage solution for production. |
| **Very long strings** | `compareTo` becomes expensive. | Short strings (≤10) per constraints, so fine. |
| **Negative or non‑ASCII input** | Problem statement guarantees lowercase letters. | Still guard against unexpected characters if reused elsewhere. |
| **Empty `responses`** | Returning `""` might be misleading. | Decide on a sentinel value or throw an exception. |

---

### A Clean, Interview‑Ready Solution

Below is the Java implementation that embodies all the “good” practices while sidestepping the pitfalls. It can be pasted directly into your LeetCode editor.

```java
import java.util.*;

class Solution {
    public String findCommonResponse(List<List<String>> responses) {
        Map<String, Integer> freq = new HashMap<>();
        String best = "";
        int bestCount = 0;

        for (List<String> day : responses) {
            Set<String> seen = new HashSet<>();
            for (String ans : day) {
                if (seen.add(ans)) { // deduplicate per day
                    int cnt = freq.merge(ans, 1, Integer::sum);
                    if (cnt > bestCount || (cnt == bestCount && ans.compareTo(best) < 0)) {
                        bestCount = cnt;
                        best = ans;
                    }
                }
            }
        }
        return best;
    }
}
```

*Time*: `O(total_answers)`  
*Space*: `O(unique_answers)`

The same logic in Python and C++ is only a few lines shorter – see the reference implementations above.

---

### How This Helps Your Job Hunt

* **Data‑structure mastery** – Hash maps and sets are interview staples.  
* **Problem‑solving clarity** – Demonstrates you can spot hidden constraints (duplicate removal, tie‑breaking).  
* **Language versatility** – Solved in three popular languages; shows adaptability.  
* **Optimized code** – Linear time and minimal extra memory – recruiters love efficiency.  

In a technical interview, you can confidently say, “I’ll deduplicate per day, update a global counter, and keep the best answer in one pass.” That’s a clean, interview‑ready narrative.

---

### Conclusion

Finding the most common response may sound trivial, but it tests your attention to detail, ability to handle edge cases, and understanding of hash‑based counting. By following the **good** approach, avoiding the **bad** pitfalls, and preparing for the **ugly** edge cases, you’ll land a clean, production‑ready solution in Java, Python, or C++. Good luck on your next interview – and happy coding! 🚀

---

### Bonus: SEO‑Friendly Meta‑Description

> Master LeetCode 3527 “Find the Most Common Response” with our Java, Python, and C++ solutions. Learn the optimal hash‑map approach, avoid common pitfalls, and ace your data‑structure interview. 🚀💡

---