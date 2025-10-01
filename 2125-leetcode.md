---
title: LeetCode 2125. Number of Laser Beams in a Bank - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 2125. Number of Laser Beams in a Bank – 3‑Way Solution + SEO‑Optimized Blog

> **Problem ID**: 2125  
> **Difficulty**: Medium  
> **Languages**: Java | Python | C++  
> **Keywords**: LeetCode 2125, Laser Beams, Security Grid, Interview Algorithm, Time Complexity, Space Complexity, Coding Interview

---

## 🎯 Problem Recap

You’re given an `m × n` binary grid (`bank`) where each cell is either `0` (empty) or `1` (security device).  
A **laser beam** can be drawn between two devices that satisfy:

1. They are in **different rows** (`r1 < r2`).  
2. **All rows** between `r1` and `r2` contain **no devices**.

Each pair of devices that satisfies the above forms one beam.  
Return the **total number of laser beams**.

---

## 🧠 Intuition

If two rows `A` and `B` each contain devices, then **every device in `A` can connect to every device in `B`** provided no other row in between has a device.  
Hence, the number of beams between those two rows is simply:

```
count(A) × count(B)
```

So we only need to:

1. Count the number of devices in every **non‑empty** row.  
2. Multiply the counts of **consecutive** non‑empty rows and sum all products.

---

## 🏗️ Algorithm (Linear Scan)

```text
deviceCounts = []

for each row in bank:
    ones = count of '1' in row
    if ones > 0:
        deviceCounts.append(ones)

total = 0
for i in 0 .. deviceCounts.size() - 2:
    total += deviceCounts[i] * deviceCounts[i+1]

return total
```

**Why it works**

- Only rows with devices can participate in beams.  
- Beams can’t “skip” over a row that has devices; thus we only consider *adjacent* rows in `deviceCounts`.  
- The product formula covers all pairings between two rows.

---

## ⏱️ Complexity Analysis

| Metric          | Value                     |
|-----------------|---------------------------|
| Time            | `O(m × n)` (scan every cell once) |
| Extra Space     | `O(r)` where `r` = number of rows containing at least one device |

The algorithm is optimal: you must look at every cell at least once to know if a row is empty or not.

---

## ⚠️ Edge Cases & Gotchas

| Case | What to Watch For | Language‑Specific Note |
|------|-------------------|------------------------|
| All rows empty | `deviceCounts` stays empty → return `0` | None |
| Exactly one non‑empty row | No pair → `0` | None |
| Overflow (Java) | Result can be up to `500 × 500 = 250,000` per pair, but sum can exceed `int` → use `long` | **Use `long`** |
| Python big integers | No overflow | None |
| C++ int overflow | Same as Java → use `long long` | None |

---

## 💻 Code

> All solutions run in **O(m × n)** time and **O(r)** auxiliary space.  
> Each implementation follows the same logic but respects idiomatic practices of the language.

### 1. Java

```java
// 2125. Number of Laser Beams in a Bank
// LeetCode
public class Solution {
    public long numberOfBeams(String[] bank) {
        List<Integer> counts = new ArrayList<>();
        for (String row : bank) {
            int ones = 0;
            for (char c : row.toCharArray()) if (c == '1') ones++;
            if (ones > 0) counts.add(ones);
        }

        long total = 0;
        for (int i = 0; i < counts.size() - 1; i++) {
            total += (long) counts.get(i) * counts.get(i + 1);
        }
        return total;
    }
}
```

#### Notes

- `long` is used to avoid integer overflow.
- The code counts ones manually to avoid `String.count` (which doesn’t exist in Java).

---

### 2. Python

```python
# 2125. Number of Laser Beams in a Bank
# LeetCode
from typing import List

class Solution:
    def numberOfBeams(self, bank: List[str]) -> int:
        # Count ones per row, discard empty rows
        counts = [row.count('1') for row in bank if '1' in row]
        total = 0
        for i in range(len(counts) - 1):
            total += counts[i] * counts[i + 1]
        return total
```

#### Notes

- Python integers have arbitrary precision – no overflow worries.
- List comprehension keeps the code concise.

---

### 3. C++

```cpp
// 2125. Number of Laser Beams in a Bank
// LeetCode
#include <vector>
#include <string>

class Solution {
public:
    long long numberOfBeams(std::vector<std::string>& bank) {
        std::vector<int> counts;
        for (const std::string& row : bank) {
            int ones = std::count(row.begin(), row.end(), '1');
            if (ones > 0) counts.push_back(ones);
        }

        long long total = 0;
        for (size_t i = 0; i + 1 < counts.size(); ++i) {
            total += static_cast<long long>(counts[i]) * counts[i + 1];
        }
        return total;
    }
};
```

#### Notes

- `long long` ensures no overflow.  
- `std::count` is a handy STL function for counting characters.

---

## 📄 Blog Article – “The Good, The Bad, and The Ugly of Solving LeetCode 2125”

> **Title:** *“Laser Beams & Job Interviews: The Good, The Bad, and The Ugly of LeetCode 2125”*  
> **Meta Description:** Master LeetCode 2125 with a clear 3‑language solution. Learn the intuition, pitfalls, and interview‑ready insights to ace your coding interview.

---

### 1️⃣ The Good: Why This Problem Is a Sweet Spot

- **Medium Difficulty, Simple Insight** – A quick mental model (“product of counts between adjacent rows”) reduces the problem to a single pass.
- **Excellent Interview Practice** – Showcases ability to:
  - Translate a real‑world rule (no devices in between) into a clean algorithm.
  - Recognize that only *non‑empty* rows matter.
- **Reusable Pattern** – “Count in rows → multiply adjacent” appears in many grid‑based interview questions.

### 2️⃣ The Bad: Common Pitfalls That Trip Coders

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| **Ignoring empty rows** | Some think every row contributes a beam. | Filter rows with zero `'1'`. |
| **O(n²) mis‑approach** | Trying to pair every device pair (nested loops). | Use the product trick; no need for O(n²). |
| **Integer overflow (Java/C++)** | The maximum result can exceed `int`. | Use `long`/`long long`. |
| **Misreading the “between” rule** | Allowing beams between rows that have a device in between. | Only multiply *consecutive* non‑empty rows. |
| **Assuming grid is always square** | Problem allows rectangular grids. | Code should work for any `m × n`. |

### 3️⃣ The Ugly: Edge Cases That Sneak Into Your Brain

- **All rows empty** → `deviceCounts` empty → return `0`.  
- **Exactly one non‑empty row** → still `0` beams.  
- **Large grid (500×500)** → naive O(n²) will TLE; our O(m × n) is safe.  
- **Mixed string types** (e.g., input contains other characters) – LeetCode guarantees `'0'` or `'1'`, but defensive coding is good.

---

### 📌 Takeaway Checklist for Interviewers

1. **Understand the rule**: "Between two rows with devices and no devices in between."  
2. **Count only the relevant rows**: filter out rows with zero devices.  
3. **Multiply adjacent counts**: `count[i] * count[i+1]`.  
4. **Sum up**.  
5. **Use long types** in statically typed languages.  
6. **Explain** your logic clearly: talk through the intuition, not just code.

---

## 🎓 How This Helps You Land a Job

- **Showcase Clean Code**: The 3‑language solutions demonstrate idiomatic coding in Java, Python, and C++ – a must‑have for many tech stacks.  
- **Problem‑Solving Narrative**: By explaining the intuition, you prove you can *conceptualize* before coding.  
- **Interview Confidence**: Practice with this problem and similar grid/counting patterns; many interviewers love to see how you handle “row‑based” constraints.  
- **SEO Boost**: Keywords like *“LeetCode 2125”, “Number of Laser Beams”, “Java solution”, “Python solution”* will drive traffic to your blog, positioning you as a knowledgeable candidate.

---

### 📢 Final Thought

LeetCode 2125 is more than just counting beams—it’s a lesson in **abstraction**. Strip a problem down to its essence (non‑empty rows → adjacent multiplication), and you’ll not only ace this question but also strengthen the habit of looking for the underlying pattern in every interview problem.

Happy coding, and may the beams (and the job offers) shine bright! ✨