---
title: LeetCode 1234. Replace the Substring for Balanced String - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 LeetCode 1234 – Replace the Substring for Balanced String  
> *A full‑stack guide: Java, Python, C++ solutions + a SEO‑friendly blog post that shows your problem‑solving chops to recruiters.*

---

### 📌 Problem Recap  

You’re given a string `s` of length `n` (4 ≤ n ≤ 10⁵, n % 4 = 0).  
` s ` contains only the letters `Q, W, E, R`.  
A string is **balanced** if each letter appears exactly `n/4` times.

**Goal:**  
Return the length of the smallest contiguous substring that can be replaced by any other string of the same length so that `s` becomes balanced.  
If `s` is already balanced, return `0`.

---

## 🧠 Core Idea – Sliding Window on “Excess” Counts  

1. **Count** the occurrences of each of the four letters.  
2. For every letter compute how many **extra** occurrences we have:
   ```
   excess[letter] = max(0, count[letter] - n/4)
   ```
   If a letter is already within the target, `excess` becomes `0`.  
3. The task now is to find the *smallest* window that contains at least  
   `excess[Q]` Q’s, `excess[W]` W’s, `excess[E]` E’s, `excess[R]` R’s.  
   Replacing this window will remove all surplus letters and leave the string balanced.

4. **Two‑pointer (sliding window)**  
   * Expand the right pointer adding the current character to the running “remaining needed” counter.  
   * When all needed counts are satisfied, shrink from the left as far as possible, updating the answer.

The algorithm runs in **O(n)** time and **O(1)** space (four counters).

---

## 🏗️ Code in Three Languages  

> All three snippets implement the same sliding‑window strategy and are ready for copy‑paste into LeetCode or a local IDE.

### 1. Java

```java
/**
 * LeetCode 1234 – Replace the Substring for Balanced String
 * Sliding window solution, O(n) time, O(1) space.
 */
class Solution {
    // Map letters to indices 0..3
    private int idx(char c) {
        switch (c) {
            case 'Q': return 0;
            case 'W': return 1;
            case 'E': return 2;
            default : return 3;           // 'R'
        }
    }

    public int balancedString(String s) {
        int n = s.length();
        int target = n / 4;
        int[] cnt = new int[4];

        // 1. Count all letters
        for (int i = 0; i < n; i++) cnt[idx(s.charAt(i))]++;

        // 2. Compute excess needed to be removed
        int[] need = new int[4];
        boolean alreadyBalanced = true;
        for (int i = 0; i < 4; i++) {
            if (cnt[i] != target) alreadyBalanced = false;
            need[i] = Math.max(0, cnt[i] - target);
        }
        if (alreadyBalanced) return 0;

        // 3. Sliding window
        int left = 0, minLen = n;
        for (int right = 0; right < n; right++) {
            need[idx(s.charAt(right))]--;     // consume char from right

            // shrink while window satisfies all needs
            while (good(need)) {
                minLen = Math.min(minLen, right - left + 1);
                need[idx(s.charAt(left))]++;   // release char from left
                left++;
            }
        }
        return minLen;
    }

    // Returns true if every need[i] <= 0
    private boolean good(int[] need) {
        for (int v : need) if (v > 0) return false;
        return true;
    }
}
```

---

### 2. Python

```python
# LeetCode 1234 – Replace the Substring for Balanced String
# Sliding window solution, O(n) time, O(1) space

class Solution:
    def balancedString(self, s: str) -> int:
        n = len(s)
        target = n // 4
        # count letters
        from collections import Counter
        cnt = Counter(s)

        # how many of each letter are surplus
        need = {c: max(0, cnt[c] - target) for c in 'QWER'}

        if all(v == 0 for v in need.values()):
            return 0

        # sliding window
        left = 0
        min_len = n
        for right, ch in enumerate(s):
            need[ch] -= 1   # consume

            while all(v <= 0 for v in need.values()):
                min_len = min(min_len, right - left + 1)
                need[s[left]] += 1   # release
                left += 1

        return min_len
```

---

### 3. C++

```cpp
// LeetCode 1234 – Replace the Substring for Balanced String
// Sliding window solution, O(n) time, O(1) space

#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int balancedString(string s) {
        int n = s.size();
        int target = n / 4;
        int cnt[4] = {0};

        auto idx = [](char c) {
            if (c == 'Q') return 0;
            if (c == 'W') return 1;
            if (c == 'E') return 2;
            return 3;            // 'R'
        };

        for (char c : s) cnt[idx(c)]++;

        int need[4];
        bool already = true;
        for (int i = 0; i < 4; ++i) {
            if (cnt[i] != target) already = false;
            need[i] = max(0, cnt[i] - target);
        }
        if (already) return 0;

        int left = 0, ans = n;
        for (int right = 0; right < n; ++right) {
            need[idx(s[right])]--;

            // shrink while window satisfies all needs
            while (need[0] <= 0 && need[1] <= 0 && need[2] <= 0 && need[3] <= 0) {
                ans = min(ans, right - left + 1);
                need[idx(s[left])]++;   // release
                ++left;
            }
        }
        return ans;
    }
};
```

---

## 📖 Blog Article – The Good, The Bad, The Ugly  

> **Target Audience:** Software‑engineering interviewers, recruiters, and job seekers preparing for data‑structure & algorithm questions.

---

### 1️⃣ The Good: Why This Problem is a “Show‑case”  

- **Simple domain, complex nuance:** Only four characters, but the twist is to find the minimal replacement window.  
- **Direct mapping to a classic pattern:** Sliding window on *surplus counts*—the same trick you’ll see in “Minimum Window Substring” and “Longest Substring with At Most K Distinct”.  
- **Scalable to large inputs:** O(n) time, O(1) space. Perfect for a timed coding interview (≤ 2 min to explain).  

> *Recruiters love candidates who can quickly identify a known pattern and adapt it.*

---

### 2️⃣ The Bad: Common Pitfalls to Avoid  

| Pitfall | Why it happens | Fix |
|---------|----------------|-----|
| **Mis‑counting the target** | Forget that each character must appear `n/4` times. | Compute `int target = s.length() / 4;` once and reuse. |
| **Sliding window condition** | Using `> 0` instead of `<= 0` when checking the window’s validity. | Keep a helper `good()` that returns true only if all `need[i] <= 0`. |
| **Over‑shrinking** | Removing left‑most character too early, causing the window to miss a needed surplus. | Shrink *only* while the window still satisfies all needs. |
| **Extra space** | Creating a map for each character in every iteration. | Use fixed-size array `int[4]`. |

---

### 3️⃣ The Ugly: Edge Cases & Hidden Traps  

1. **Already Balanced**  
   - Many solutions skip this and still process the window, leading to an unnecessary O(n) pass.  
   - **Solution:** Check if all `cnt[i] == target` before starting the sliding window.

2. **String Length Not a Multiple of 4**  
   - LeetCode guarantees `n % 4 == 0`, but a real‑world system might not.  
   - Defensive code: early return `-1` or throw an exception if `n % 4 != 0`.

3. **Large Surplus in One Letter**  
   - Example: `"QQQQQQQQQQ"` (n=10) → target=2, surplus Q=8.  
   - The window will have to cover the entire string.  
   - **Check:** `if (need[letter] == 0)` for all letters, return `0`.

---

## 🎯 How to Use This in Your Resume / Portfolio  

- **Add the problem name** (`1234 – Replace the Substring for Balanced String`) to a “Coding Challenges” section.  
- **Mention the pattern**: *“Recognized and solved with a two‑pointer sliding window over surplus counts.”*  
- **Show the three‑language implementation**: Demonstrates full‑stack versatility.  
- **Link to the article** (or embed the article) so recruiters see a clean, explainable walk‑through.

> 👉 **Pro tip:** Pair the article with a short video (3‑minute) where you run through the Java solution in a live coding session. Recruiters that watch short, focused videos often remember the candidate better.

---

## 🔍 SEO Checklist  

| Keyword | Placement | Comment |
|---------|-----------|---------|
| `LeetCode 1234` | Title, first paragraph | Exact match search |
| `Replace the Substring for Balanced String` | Headings | Full problem name |
| `Java`, `Python`, `C++` | Solution sections | Language tags |
| `Sliding window algorithm` | Core Idea | Technical depth |
| `minimum window substring` | Pattern comparison | Shows pattern recognition |
| `interview algorithm` | Blog intro | Target recruiters |
| `data structure interview` | Blog conclusion | Job‑search relevance |

> **Meta description (LeetCode or LinkedIn):**  
> “Learn how to solve LeetCode 1234 – Replace the Substring for Balanced String in Java, Python, and C++ with a proven sliding window technique. Perfect for software‑engineering interviews!”

---

## 📣 Final Takeaway  

> *If you can present the Java/Python/C++ code above, explain the sliding window logic, and discuss the pitfalls in the blog article, you’ll not only nail the problem but also showcase a polished, interview‑ready mindset.*

Happy coding, and may you land that dream role! 🚀