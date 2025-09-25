---
title: LeetCode 3662. Filter Characters by Frequency - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 LeetCode 3662 – **Filter Characters by Frequency**  
*(Easy – 1 ≤ s.length ≤ 100)*  

| Language | Time | Space |
|----------|------|-------|
| **Java** | O(n) | O(n) |
| **Python** | O(n) | O(n) |
| **C++** | O(n) | O(n) |

> **Why this problem rocks for a job interview**  
> 1️⃣ It tests basic string manipulation + frequency counting.  
> 2️⃣ You can discuss *two‑pass* vs *one‑pass* solutions, and the trade‑offs.  
> 3️⃣ It opens doors to talk about *time/space complexity*, *corner cases*, *testing*, and *code readability*—all interview‑must‑know topics.  

---

## 📖 Problem Restatement

Given a string `s` of lowercase English letters and an integer `k`, build a new string that contains **every** character from `s` whose total frequency in `s` is **strictly less** than `k`.  
The characters in the result must keep their original order.  
If no character qualifies, return an empty string.

> **Example**  
> `s = "aadbbcccca", k = 3`  
> Frequencies → a:3, d:1, b:2, c:4 → only `d` and `b` are < 3  
> Result → `"dbb"`

---

## 💡 Core Idea – Two‑Pass Frequency Counting

1. **First pass** – Count each letter’s frequency (array of size 26).  
2. **Second pass** – Iterate the original string again and append only those
   letters whose frequency `< k`.

Because the alphabet is fixed and small, the frequency array is *O(1)* space, while
the string itself is `O(n)`.

---

## ⚙️ Code Solutions

### 1️⃣ Java (O(n) Time, O(1) Extra Space)

```java
import java.util.*;

public class Solution {
    public String filterCharacters(String s, int k) {
        // Frequency table for 26 lowercase letters
        int[] freq = new int[26];
        for (char c : s.toCharArray()) {
            freq[c - 'a']++;
        }

        StringBuilder sb = new StringBuilder();
        for (char c : s.toCharArray()) {
            if (freq[c - 'a'] < k) {
                sb.append(c);
            }
        }
        return sb.toString();
    }
}
```

### 2️⃣ Python (Pythonic Counter + List Comprehension)

```python
from collections import Counter
from typing import *

class Solution:
    def filterCharacters(self, s: str, k: int) -> str:
        freq = Counter(s)                     # O(n) counting
        return ''.join(c for c in s if freq[c] < k)  # O(n) building
```

> **Note:** Using `Counter` keeps the code concise and clearly expresses the intent.

### 3️⃣ C++ (Fast & Explicit)

```cpp
#include <string>
#include <vector>
using namespace std;

class Solution {
public:
    string filterCharacters(string s, int k) {
        vector<int> freq(26, 0);
        for (char c : s) freq[c - 'a']++;    // first pass

        string res;
        for (char c : s)                    // second pass
            if (freq[c - 'a'] < k) res += c;

        return res;
    }
};
```

---

## 🧠 Edge Cases & Common Pitfalls

| Edge case | What to watch for | Fix |
|-----------|-------------------|-----|
| `k = 1` | Every character qualifies → return `s` unchanged. | The `< k` check naturally handles this. |
| All characters appear `>= k` times | Should return empty string. | Return `""` after the loop. |
| Empty string (`s.length == 0`) | Problem constraints forbid, but defensive code returns `""`. | Add guard or rely on the constraints. |
| Upper‑case letters | Problem says lowercase only; otherwise adjust array size. | Either validate or convert to lowercase. |

---

## 🔍 “Good, Bad, Ugly” of This Problem

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Clarity** | Very clear description & constraints. | None. | N/A |
| **Difficulty** | Easy – good for first‑round screening. | None. | N/A |
| **Time Complexity** | O(n) – optimal. | None. | N/A |
| **Space Complexity** | O(1) (fixed 26‑int array). | Using hash map would increase space. | Over‑engineering with regex or heavy libraries is overkill. |
| **Potential Interview Questions** | • Could we do it in one pass? <br>• What if the alphabet is bigger? | • Ask about edge case handling. | • Ask to explain memory footprint if `s` were huge and `k` dynamic. |

---

## 📈 How to Talk About It in an Interview

1. **Explain the two‑pass approach** – show that you understand why we need the frequency first.  
2. **Mention O(1) space** – highlight the 26‑int array advantage.  
3. **Discuss edge cases** – give at least two examples (e.g., all chars >= k, k=1).  
4. **Show alternative solutions** – e.g., single‑pass with `HashMap` + queue, or using `Counter` in Python.  
5. **Talk about test cases** – write a few to demonstrate understanding.  
6. **Explain why we preserve order** – emphasise that we iterate the original string again.  

---

## 📚 Sample Test Suite

| Test | Input | Expected Output |
|------|-------|-----------------|
| 1 | `"aadbbcccca", 3` | `"dbb"` |
| 2 | `"xyz", 2` | `"xyz"` |
| 3 | `"aaaa", 5` | `""` |
| 4 | `"abacadae", 2` | `"bde"` |
| 5 | `"abc", 1` | `""` |

---

## 🎯 SEO‑Optimized Blog Closing

If you're preparing for technical interviews, **Filter Characters by Frequency** is a must‑know LeetCode problem.  
- **Java** and **C++** solutions are ultra‑fast, while **Python** keeps it tidy with `collections.Counter`.  
- Understanding the **two‑pass frequency counting** trick demonstrates solid algorithmic thinking.  
- You can confidently discuss **time/space trade‑offs**, **edge case handling**, and **alternative approaches** – exactly what interviewers look for.

Drop the code above into your favorite IDE, run the test cases, and you’ll be ready to ace this problem.  

**Good luck, and happy coding!** 🚀  

--- 

**Keywords:** LeetCode 3662, Filter Characters by Frequency, Java solution, Python solution, C++ solution, interview coding problem, string manipulation, frequency counting, job interview, algorithmic thinking.