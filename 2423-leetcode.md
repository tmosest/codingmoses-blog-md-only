---
title: LeetCode 2423. Remove Letter To Equalize Frequency - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 2423 – “Remove Letter to Equalize Frequency”  
**Java | Python | C++** solutions + a complete **blog post** that will get you noticed by recruiters.  

---

### 1. The Problem (in one sentence)

> Given a lowercase string `word`, can you delete **exactly one** character so that every remaining character appears the same number of times?

---

### 2. Brute‑Force vs. Greedy – The “Good, the Bad, the Ugly”

| Stage | What you do | Why it matters |
|-------|-------------|----------------|
| **Good** | Count frequencies once, then *simulate* removing one letter at a time and check the remaining frequencies. | Runs in *O(26²) = O(1)* time (constant because the alphabet size is fixed). |
| **Bad** | Try all possible deletions (O(n²) time) and re‑count after each deletion. | Works but is unnecessary overhead; slower on longer strings. |
| **Ugly** | Forget that the string may contain **only one distinct letter** or **two** – edge cases that make your code crash or return wrong results. | Many interviewers ask about edge cases; missing them means you’ll lose points. |

---

### 3. The Greedy‑Frequency Validation Algorithm

1. **Frequency Count** – Build an array of 26 ints (`freq[26]`).
2. **Try Removing Each Character** – For each `i` such that `freq[i] > 0`:
   * Decrease `freq[i]` by 1 (simulate the deletion).
   * Check whether *all non‑zero frequencies are equal*:
     * Grab the first non‑zero value as the “target”.
     * If any other non‑zero value differs → this deletion **fails**.
   * Restore `freq[i]` before moving on.
3. **Return** `true` if any deletion succeeded; otherwise `false`.

Because the alphabet size is constant, the algorithm runs in **O(1)** time and uses **O(1)** extra memory.

---

### 4. Code – Three Languages

---

#### Java (LeetCode‑style)

```java
class Solution {
    public boolean equalFrequency(String word) {
        int[] freq = new int[26];
        for (int i = 0; i < word.length(); i++) {
            freq[word.charAt(i) - 'a']++;
        }

        for (int i = 0; i < 26; i++) {
            if (freq[i] == 0) continue;

            freq[i]--;                     // simulate removal
            int target = 0;
            boolean ok = true;

            for (int j = 0; j < 26; j++) {
                if (freq[j] == 0) continue;
                if (target == 0) target = freq[j];
                else if (freq[j] != target) { ok = false; break; }
            }

            freq[i]++;                     // restore
            if (ok) return true;
        }
        return false;
    }
}
```

> **Why it shines** – Simple, uses only a fixed‑size array, and follows the optimal O(1) reasoning.

---

#### Python

```python
class Solution:
    def equalFrequency(self, word: str) -> bool:
        freq = [0] * 26
        for ch in word:
            freq[ord(ch) - 97] += 1

        for i in range(26):
            if freq[i] == 0:
                continue

            freq[i] -= 1                      # simulate removal
            target = None
            ok = True

            for f in freq:
                if f == 0:
                    continue
                if target is None:
                    target = f
                elif f != target:
                    ok = False
                    break

            freq[i] += 1                      # restore
            if ok:
                return True

        return False
```

> **Pythonic touches** – list comprehension could be used, but readability is paramount here.

---

#### C++

```cpp
class Solution {
public:
    bool equalFrequency(string word) {
        vector<int> freq(26, 0);
        for (char c : word) freq[c - 'a']++;

        for (int i = 0; i < 26; ++i) {
            if (freq[i] == 0) continue;

            freq[i]--;                         // simulate removal
            int target = -1;
            bool ok = true;

            for (int f : freq) {
                if (f == 0) continue;
                if (target == -1) target = f;
                else if (f != target) { ok = false; break; }
            }

            freq[i]++;                         // restore
            if (ok) return true;
        }
        return false;
    }
};
```

> **C++ note** – Uses `vector<int>` for simplicity; the algorithm stays the same.

---

### 5. Complexity Recap

| Language | Time | Space |
|----------|------|-------|
| Java | O(1) (26² iterations) | O(1) |
| Python | O(1) | O(1) |
| C++ | O(1) | O(1) |

> *Why constant time?* The alphabet size is fixed (26 letters), so all loops are bounded by that constant.

---

### 6. SEO‑Optimized Blog Article

> **Title**  
> **“Remove Letter to Equalize Frequency – Master the LeetCode 2423 Problem (Java, Python, C++)”**

---

#### 1. Introduction

When recruiters search for *LeetCode* solutions, the “Remove Letter to Equalize Frequency” problem is a perfect showcase. It blends frequency counting, greedy validation, and edge‑case handling – all of which are staples in software‑engineering interviews. Below, we’ll walk through a concise, production‑ready solution in **Java**, **Python**, and **C++**, and then dive into the “Good, the Bad, and the Ugly” of solving this challenge.

---

#### 2. Problem Recap

*You’re given a lowercase string. Remove exactly one character so that every remaining character has the same frequency. Return `true` if possible, else `false`.*

---

#### 3. Core Insight

> **Frequency counts + one‑character removal = constant‑time check**

Because the alphabet is fixed, we can:
1. Count each letter once.
2. For each letter with a non‑zero count, pretend we removed one occurrence.
3. Verify whether all non‑zero frequencies are equal.

If any removal satisfies the equality condition, the answer is `true`.

---

#### 4. Why the Greedy Check Is Optimal

| Reason | Detail |
|--------|--------|
| **O(1) Time** | 26 letters → at most 26×26 iterations. |
| **O(1) Space** | Only a 26‑element array/vector. |
| **No Re‑Scanning** | We don’t rebuild the string; we just decrement and restore counts. |
| **Deterministic** | No randomness or backtracking. |

---

#### 5. Code Walkthrough (Java)

1. Build the `freq` array.  
2. Loop over each letter.  
3. `freq[i]--` → simulate deletion.  
4. Find the first non‑zero frequency (`target`).  
5. Compare the rest; if any differ → break.  
6. Restore `freq[i]++`.  
7. Return `true` if any pass, otherwise `false`.

(Full code snippet provided in section 4.)

---

#### 6. Edge Cases You Must Pass

| Case | Explanation | Why It Matters |
|------|-------------|----------------|
| **All letters identical** (e.g., `"aaaa"`) | Removing one leaves all equal. | Many solutions forget this trivial case. |
| **Two letters with different counts** (e.g., `"aabbc"`) | Must check if removing one of the high‑count letter fixes the imbalance. | Edge case that can trip up a simple loop. |
| **String length 2** | Always `true` because you can always remove one to leave a single letter. | Smallest input; easy to miss. |
| **Already equal frequencies** | Still need to remove one character, which may break equality. | Some solutions mistakenly return `true` immediately. |

---

#### 7. Common Pitfalls (The “Bad” & “Ugly”)

| Pitfall | Fix |
|---------|-----|
| **O(n²) approach** – deleting each character and re‑counting. | Use the greedy frequency validation; no need to rebuild the string. |
| **Failing to restore the count** after a trial deletion. | Always `freq[i]++` after the check. |
| **Ignoring zero frequencies** – treating them as a valid frequency. | Skip zeros when comparing. |
| **Misunderstanding “exactly one deletion”** – allowing “do nothing”. | Ensure the loop always decrements before the check. |

---

#### 8. Take‑away for Interviewers

> **“I can solve this in O(1) time and O(1) space, handling all edge cases, and I can explain the reasoning clearly.”**

Showcasing the solution in three languages demonstrates versatility and a solid grasp of data‑structures fundamentals.

---

#### 9. Final Thoughts

The “Remove Letter to Equalize Frequency” problem is deceptively simple but is a great litmus test for:

* Counting frequencies efficiently.
* Thinking in terms of *simulation* rather than *reconstruction*.
* Handling edge cases gracefully.

Mastering this problem not only boosts your LeetCode score but also sharpens the core skills recruiters look for in a software engineer.

---

#### 10. Keywords & SEO Tags

- LeetCode 2423  
- Remove Letter to Equalize Frequency  
- Frequency counting algorithm  
- Greedy algorithm interview question  
- Java interview solution  
- Python coding interview  
- C++ LeetCode solution  
- Software engineer interview prep  
- Job interview coding problem  
- Data structures & algorithms  

---

Happy coding, and good luck with your next interview! 🚀