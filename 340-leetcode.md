---
title: LeetCode 340. Longest Substring with At Most K Distinct Characters - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 340 – Longest Substring with At Most K Distinct Characters  
> **Keywords**: LeetCode 340, Sliding Window, HashMap, O(n) solution, Java, Python, C++, Interview Preparation, Algorithm, Data Structures  

---

### 📌 Problem Overview  

- **Title**: Longest Substring with At Most K Distinct Characters  
- **Difficulty**: Medium  
- **Input**:  
  - `s`: a string of length `1 ≤ |s| ≤ 5·10⁴`  
  - `k`: an integer `0 ≤ k ≤ 50`  
- **Output**: Length of the longest contiguous substring that contains **no more than** `k` distinct characters.  

> **Example**  
> `s = "eceba", k = 2  ➜  3`  *(substring = "ece")*  

---

### 🧠 Why This Problem Matters  

- **Interview Gold‑Mine**: This question tests your ability to think in *O(n)* time, use two‑pointer techniques, and manage frequency counts efficiently.  
- **Common Pitfall**: Brute‑force O(n²) or O(n³) solutions get TLE on large inputs.  
- **Transferable Skills**: Sliding window, hash map usage, pointer manipulation – all staple in system design and real‑world coding.

---

## 🛠️ The “Good” – The Classic Sliding‑Window Approach  

### 1️⃣ Idea  

Maintain a window `[left, right]` such that the substring `s[left..right]` contains at most `k` distinct characters.  
- Expand `right` one step at a time.  
- When adding a new character causes the number of distinct characters to exceed `k`, shrink the window from the left until the condition is restored.  

### 2️⃣ Data Structure  

A frequency map (`HashMap` / `unordered_map` / `collections.Counter`) tracks how many times each character appears in the current window.

### 3️⃣ Steps  

| Step | Action | Complexity |
|------|--------|------------|
| 1 | Initialize `left = 0`, `maxLen = 0`, empty map. | O(1) |
| 2 | Iterate `right` from 0 to n‑1. | O(n) |
| 3 | Increment freq of `s[right]`. | O(1) |
| 4 | While map size > k: decrement freq of `s[left]`, remove if zero, `left++`. | O(1) amortized |
| 5 | Update `maxLen = max(maxLen, right - left + 1)`. | O(1) |
| 6 | Return `maxLen`. | O(1) |

Total time `O(n)`, space `O(k)` (worst‑case when all characters distinct and `k = 50`).

---

## 💥 The “Bad” – Naïve Brute Force  

- Enumerate all substrings: `O(n²)` time, `O(1)` space for counting with a `Set`.  
- Or use a nested loop and maintain a frequency array for each substring: still `O(n²)`.  

**Result**: Fails on 5·10⁴ length strings – 2.5 billion substrings → TLE.

---

## 😈 The “Ugly” – Edge‑Case Mess  

| Edge Case | What can go wrong? | Fix |
|-----------|--------------------|-----|
| `k == 0` | No substring allowed → answer 0. | Handle early. |
| Empty string | Should return 0. | Early return. |
| All characters identical | Window grows to full string. | Works fine, but watch map size never > k. |
| `k >= number of unique chars in s` | Whole string is valid. | Works automatically, but still good to check for early return if wanted. |

---

## 🖥️ Code Implementations  

Below are clean, production‑ready solutions in **Java**, **Python**, and **C++**.

---

### Java

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    public int lengthOfLongestSubstringKDistinct(String s, int k) {
        if (k == 0 || s == null || s.isEmpty()) return 0;

        int left = 0, maxLen = 0;
        Map<Character, Integer> freq = new HashMap<>();

        for (int right = 0; right < s.length(); right++) {
            char r = s.charAt(right);
            freq.put(r, freq.getOrDefault(r, 0) + 1);

            while (freq.size() > k) {
                char l = s.charAt(left++);
                int count = freq.get(l) - 1;
                if (count == 0) freq.remove(l);
                else freq.put(l, count);
            }

            maxLen = Math.max(maxLen, right - left + 1);
        }
        return maxLen;
    }
}
```

---

### Python 3

```python
from collections import defaultdict

class Solution:
    def lengthOfLongestSubstringKDistinct(self, s: str, k: int) -> int:
        if k == 0 or not s:
            return 0

        left = 0
        max_len = 0
        freq = defaultdict(int)

        for right, char in enumerate(s):
            freq[char] += 1

            while len(freq) > k:
                freq[s[left]] -= 1
                if freq[s[left]] == 0:
                    del freq[s[left]]
                left += 1

            max_len = max(max_len, right - left + 1)

        return max_len
```

---

### C++

```cpp
#include <unordered_map>
#include <string>
#include <algorithm>

class Solution {
public:
    int lengthOfLongestSubstringKDistinct(const std::string& s, int k) {
        if (k == 0 || s.empty()) return 0;

        std::unordered_map<char, int> freq;
        int left = 0, maxLen = 0;

        for (int right = 0; right < static_cast<int>(s.size()); ++right) {
            freq[s[right]]++;

            while (freq.size() > static_cast<size_t>(k)) {
                if (--freq[s[left]] == 0)
                    freq.erase(s[left]);
                ++left;
            }

            maxLen = std::max(maxLen, right - left + 1);
        }
        return maxLen;
    }
};
```

---

## 🧩 How the Code Works (Step‑by‑Step)  

1. **Initialization** – `left` points to the start of the current window, `maxLen` records the best length found, `freq` stores character counts.  
2. **Expand** – For each `right`, add the character to `freq`.  
3. **Contract** – While we have too many distinct characters, shrink the window from the left: decrement the count, remove if zero, and move `left`.  
4. **Update** – After each expansion, the window is valid. Update `maxLen` if the current window is larger.  
5. **Return** – After the loop, `maxLen` holds the length of the longest substring.

---

## 📊 Complexity Analysis  

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| Time   | **O(n)** | **O(n)** | **O(n)** |
| Space  | **O(k)** | **O(k)** | **O(k)** |
| *n* = | length of `s` | length of `s` | length of `s` |

*Why O(k) space?*  
The frequency map never holds more than `k+1` keys (once it exceeds `k`, we shrink).  

---

## 🚨 Common Mistakes & How to Avoid Them  

| Mistake | Consequence | Fix |
|---------|-------------|-----|
| Forget to handle `k == 0` | Returns wrong non‑zero length | Add early guard |
| Using `HashSet` instead of frequency map | Cannot shrink correctly when duplicate chars appear | Use a map to track counts |
| Updating `maxLen` before contracting | Counts invalid window | Update after contraction loop |
| Off‑by‑one errors in window size | Incorrect length | Use `right - left + 1` for inclusive window |

---

## 🔧 Further Optimizations  

| Technique | When to Use | Effect |
|-----------|-------------|--------|
| **Two‑Pointer (already used)** | Always | Minimal overhead |
| **Pre‑allocate Array for ASCII** | Strings are ASCII | Faster constant‑time access than HashMap |
| **Avoid `HashMap.getOrDefault` in Java 8+** | Performance critical | Use `compute` or `merge` for concise code |

> *Tip*: For interviewers who value **clarity over micro‑optimizations**, the HashMap/Counter approach is ideal. For production systems, an array (size 256) can shave a few microseconds.

---

## ✅ Test Suite (Python)  

```python
import unittest

class TestLongestSubstring(unittest.TestCase):
    def setUp(self):
        self.sol = Solution()

    def test_examples(self):
        self.assertEqual(self.sol.lengthOfLongestSubstringKDistinct("eceba", 2), 3)
        self.assertEqual(self.sol.lengthOfLongestSubstringKDistinct("aa", 1), 2)

    def test_edge_cases(self):
        self.assertEqual(self.sol.lengthOfLongestSubstringKDistinct("", 0), 0)
        self.assertEqual(self.sol.lengthOfLongestSubstringKDistinct("abc", 0), 0)
        self.assertEqual(self.sol.lengthOfLongestSubstringKDistinct("abc", 5), 3)
        self.assertEqual(self.sol.lengthOfLongestSubstringKDistinct("a", 1), 1)

    def test_large(self):
        s = "a" * 50000
        self.assertEqual(self.sol.lengthOfLongestSubstringKDistinct(s, 1), 50000)

if __name__ == "__main__":
    unittest.main()
```

Run `python3 test.py` to ensure 100% coverage.

---

## 🎯 Takeaway – What Recruiters Look For  

1. **Correctness** – Handles all edge cases.  
2. **Time & Space Efficiency** – O(n) time, O(k) space.  
3. **Clean Code** – Clear variable names, early guards, minimal boilerplate.  
4. **Problem‑Solving Mindset** – Recognize sliding window as the natural fit.  

> *Job‑Ready Tip*: Practice explaining your solution aloud. Interviewers love concise, well‑structured explanations that highlight the algorithmic insight and trade‑offs.

---

### 📣 Final Words  

LeetCode 340 is a classic sliding‑window problem that can be solved in linear time. By mastering this pattern, you’re not only prepared for this specific question but also equipped for a host of interview problems involving substrings, subarrays, and frequency constraints.  

Happy coding, and may your next interview call the `Solution` class you’ve just written! 🚀