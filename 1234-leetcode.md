---
title: LeetCode 1234. Replace the Substring for Balanced String - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem recap

> **Replace the Substring for Balanced String**  
> You are given a string `s` of length `n` that contains only the four characters `Q`, `W`, `E`, `R`.  
> A string is *balanced* if each character appears exactly `n / 4` times.  
> In one operation you may replace **any contiguous substring** of `s` with another string of the same length (the new string can be chosen arbitrarily).  
> Return the **minimum length** of the substring you need to replace in order to make `s` balanced.  
> If `s` is already balanced, return `0`.

> *Constraints*  
> * `4 ≤ n ≤ 10⁵`  
> * `n` is a multiple of `4`  
> * `s` only contains `Q`, `W`, `E`, `R`

The classical solution uses a **sliding window** (two‑pointer) technique.

---

## 2.  Algorithm – Sliding Window

1. **Count the frequency** of each character in the whole string.
2. Compute `required = n / 4`.  
   For every character `c` we only *need* `freq[c] - required` of them in the substring we will replace – if the value is ≤ 0 the character is already balanced or under‑represented and we do not need to “remove” any of it.
3. If every `freq[c] == required`, the string is already balanced → return `0`.
4. Initialise a window `[l, r)` (inclusive on the left, exclusive on the right).  
   Move the right end one step at a time, *decrementing* the “needed” counter for the character that enters the window.
5. While the window already contains *at least* the needed number of each over‑represented character, try to shrink it from the left (increment `l` and *increment* the counter for that character).  
   Update the answer with the current window length every time the window is “good”.
6. The smallest window found is the answer.

> **Why it works**  
> The window represents a candidate substring that we will replace.  
> When the window contains all the “excess” characters, the rest of the string already has the right count, so replacing the window can fix the whole string.  
> By sliding the window greedily and shrinking it whenever possible we guarantee that we test the smallest possible substrings.

### Complexity

| Operation | Time | Space |
|-----------|------|-------|
| Count frequencies | `O(n)` | `O(1)` |
| Sliding window | `O(n)` | `O(1)` |

Overall: **`O(n)` time, `O(1)` auxiliary space**.

---

## 3.  Reference Implementations

Below are clean, well‑commented implementations in **Java**, **Python**, and **C++**.  
Each contains a small `main`/`solve` helper that can be used for local testing.

---

### 3.1 Java

```java
import java.util.*;

public class Solution {
    // map character to index 0..3
    private int idx(char c) {
        return switch (c) {
            case 'Q' -> 0;
            case 'W' -> 1;
            case 'E' -> 2;
            default  -> 3; // 'R'
        };
    }

    public int balancedString(String s) {
        int n = s.length();
        int required = n / 4;

        // 1) total frequencies
        int[] freq = new int[4];
        for (int i = 0; i < n; i++) {
            freq[idx(s.charAt(i))]++;
        }

        // 2) compute how many of each char we need to remove
        int[] need = new int[4];
        boolean balanced = true;
        for (int i = 0; i < 4; i++) {
            if (freq[i] > required) {
                need[i] = freq[i] - required;
                balanced = false;
            }
        }
        if (balanced) return 0;               // already balanced

        // 3) sliding window
        int left = 0, best = n;
        for (int right = 0; right < n; right++) {
            int id = idx(s.charAt(right));
            need[id]--;                          // char enters window

            // shrink while window contains enough of each over‑represented char
            while (allNonPositive(need)) {
                best = Math.min(best, right - left + 1);
                need[idx(s.charAt(left))]++;      // char leaves window
                left++;
            }
        }
        return best;
    }

    private boolean allNonPositive(int[] a) {
        for (int v : a) if (v > 0) return false;
        return true;
    }

    // ----- for local testing -----
    public static void main(String[] args) {
        Solution sol = new Solution();
        System.out.println(sol.balancedString("QWER"));   // 0
        System.out.println(sol.balancedString("QQWE"));   // 1
        System.out.println(sol.balancedString("QQQW"));   // 2
    }
}
```

---

### 3.2 Python

```python
import sys

class Solution:
    def balancedString(self, s: str) -> int:
        n = len(s)
        req = n // 4

        # count frequencies
        freq = {c: 0 for c in 'QWER'}
        for ch in s:
            freq[ch] += 1

        # how many of each char must be removed from the window
        need = {c: max(0, freq[c] - req) for c in 'QWER'}
        if all(v == 0 for v in need.values()):
            return 0

        # sliding window
        left = 0
        best = n
        for right, ch in enumerate(s):
            need[ch] -= 1  # char enters window

            while all(v <= 0 for v in need.values()):
                best = min(best, right - left + 1)
                need[s[left]] += 1  # char leaves window
                left += 1

        return best


# ----- local testing -----
if __name__ == "__main__":
    sol = Solution()
    print(sol.balancedString("QWER"))   # 0
    print(sol.balancedString("QQWE"))   # 1
    print(sol.balancedString("QQQW"))   # 2
```

---

### 3.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int balancedString(string s) {
        int n = s.size();
        int req = n / 4;
        array<int, 4> cnt = {0, 0, 0, 0};
        auto id = [](char c) {
            switch (c) {
                case 'Q': return 0;
                case 'W': return 1;
                case 'E': return 2;
                default : return 3; // 'R'
            }
        };

        for (char c : s) cnt[id(c)]++;

        array<int, 4> need;
        bool balanced = true;
        for (int i = 0; i < 4; ++i) {
            if (cnt[i] > req) {
                need[i] = cnt[i] - req;
                balanced = false;
            } else need[i] = 0;
        }
        if (balanced) return 0;

        int left = 0, best = n;
        for (int right = 0; right < n; ++right) {
            need[id(s[right])]--;        // window expands

            while (all_of(need.begin(), need.end(),
                          [](int v){ return v <= 0; })) {
                best = min(best, right - left + 1);
                need[id(s[left])]++;      // shrink from left
                ++left;
            }
        }
        return best;
    }
};

// ----- local testing -----
int main() {
    Solution sol;
    cout << sol.balancedString("QWER") << endl;   // 0
    cout << sol.balancedString("QQWE") << endl;   // 1
    cout << sol.balancedString("QQQW") << endl;   // 2
    return 0;
}
```

---

## 4.  Blog Article – *The Good, the Bad, and the Ugly of “Replace the Substring for Balanced String”*

> *Published 2025‑09‑26 | Tags: #LeetCode #CodingInterview #Algorithms #SlidingWindow #JobSearch*

---

### 4.1 Why this problem matters for recruiters

Recruiters love LeetCode problems that test **data‑structure mastery + clever algorithm design**.  
This problem sits in the *medium* bucket, but its solution requires:

1. **Observing over‑representation** – a small logic trick that shows you can think *beyond* the obvious “count each character”.
2. **Sliding window** – a classic two‑pointer technique that appears in many interviews (palindrome substrings, two sum 2‑d array, etc.).
3. **Edge‑case handling** – the string can already be balanced, or the window may need to shrink multiple times. A clean implementation avoids subtle bugs.

If you can explain this problem in 5–10 minutes and hand‑write the algorithm in one go, you’re already halfway to *top‑tier candidate* status.

---

### 4.2 The **Good** – Simple yet powerful

- **O(n) time** – no nested loops, just linear scan.  
- **O(1) space** – only 4 counters, even for 100 000‑char strings.  
- **Reusable technique** – the sliding window skeleton can be adapted for *any* problem that asks for a contiguous segment with a given property.

You can showcase this in your portfolio or on a whiteboard:

```
Count > required -> excess
Window contains all excess -> replace window
```

It is a great example of **“reduce the problem”**: from “find a substring” to “find a substring that contains all the excess”.  
Recruits will ask: *“Why did we subtract `required`?”* and you can immediately say: *“Because we only need to delete the surplus, the rest of the string stays intact.”*

---

### 4.3 The **Bad** – Common pitfalls

| Pitfall | How to avoid it |
|---------|-----------------|
| **Incorrect need calculation** (subtracting when you should add) | Use a dedicated `need` array, and only set it to `freq[i] - req` if positive. |
| **Assuming the window must grow before shrink** | Keep the “goodness” check **after** expanding the window; only then attempt to shrink. |
| **Missing the already balanced case** | A quick `if all(freq[i] == req)` guard saves 0‑time edge cases. |
| **Using a map in Java/Python** (O(1) vs O(4)) | Prefer arrays (`int[4]`) for speed; maps give readability but a slight overhead. |

Highlighting these pitfalls during an interview shows that you *know how to debug* and can think defensively about edge cases – a highly prized skill.

---

### 4.4 The **Ugly** – Why people struggle

1. **Misreading “replace any substring”** – Some candidates think you must delete all excess characters, not realizing you can also *insert* new ones in the replacement.  
2. **Over‑optimistic window expansion** – They let the right pointer move all the way to the end before shrinking, causing `O(n²)` in practice because of repeated checks.  
3. **Complexity obsession** – Trying to avoid two‑pointer “all‑non‑positive” checks and instead using a priority queue or segment tree – a *horrendous over‑engineering* of a simple O(n) solution.

A senior candidate will recognize that the *over‑engineered* solution is just **ugly**. They’ll say, “No need for a heap; just a two‑pointer pass.”

---

### 4.5 Quick interview cheat‑sheet

| Step | Code snippet | Key takeaway |
|------|--------------|--------------|
| Count frequencies | `for ch in s: freq[ch] += 1` | One pass, O(n). |
| Excess array | `need[ch] = max(0, freq[ch] - req)` | Only remove what you actually need. |
| Window expand | `need[ch] -= 1` | Negative values mean “window already has enough of this char”. |
| Goodness test | `while all(v <= 0 for v in need)` | `O(1)` check per iteration. |
| Shrink | `need[left_char] += 1; left++` | Move left only when the window is good. |

Remember: the window length **shrinks** only when *every* over‑represented character is already satisfied.

---

### 4.6 How to talk about this in an interview

1. **State the problem in plain English** – make sure the interviewer knows you understand the replacement operation.
2. **Explain the observation** – “Why do we care about `freq[c] - req`?”  
   → This shows you noticed the string already has the right amount of *under‑represented* characters.
3. **Sketch the sliding window** – draw a simple diagram on the whiteboard.  
   *Right pointer adds, left pointer removes, shrink while good*.
4. **Edge‑case check** – point out the `0` answer for already balanced strings.
5. **Complexity** – mention `O(n)` time and constant memory, and optionally comment on why this beats a naive `O(n²)` substring search.

If you finish with a *clean, runnable code snippet*, you’ll have convinced the interviewer that you can write **correct, efficient, production‑ready code**.

---

### 4.7 Final words

The **“Replace the Substring for Balanced String”** problem is a *gateway* to more advanced topics (prefix sums, frequency maps, monotone queues).  

- **Good** – A classic sliding‑window exercise that demonstrates linear thinking.  
- **Bad** – Often tripped over by those who forget to subtract the required amount first.  
- **Ugly** – The “over‑engineered” solutions that blow up memory or time by adding needless data structures.

If you ace this problem, you’re proving that you can see patterns, design efficient algorithms, and write clean code – exactly what recruiters are looking for.  

Happy coding and good luck with your next interview! 🚀

--- 

### 5.  How to Use This Article to Boost Your Resume

- **Add a section** titled “LeetCode Problem Solved” and list this problem with a link to your solution.  
- **Use the bullet points** from the “Why this problem matters” paragraph as talking points in your interview prep.  
- **Share the code snippets** in your GitHub README (e.g., `BalancedString.java`) – recruiters often skim your repositories for clean code.

---

### 6.  Take‑away Checklist

- [ ] Understand the **excess–representation** trick.
- [ ] Master the **sliding window** template.
- [ ] Be able to write the solution in **Java/Python/C++** within 5 min.
- [ ] Prepare a 5‑minute oral explanation that covers the *why* and *how*.
- [ ] Add the implementation to your portfolio for quick recruiter review.

Happy interviewing! 🚀

--- 

### 7.  References

1. [LeetCode 1694 – Replace the Substring for Balanced String](https://leetcode.com/problems/replace-the-substring-for-balanced-string/)
2. *Two‑Pointer (Sliding Window) Pattern*, CS 101 textbook
3. *HackerRank – “Frequency Counter” pattern*  

--- 

### 8.  SEO & Job‑Search Hooks

- Keywords: `balanced string`, `substring replacement`, `LeetCode medium`, `sliding window interview`, `coding interview prep`, `job interview algorithms`.  
- Meta description: *Learn the fastest O(n) solution to LeetCode 1694 – Replace the Substring for Balanced String, with reference code in Java, Python, and C++. Master the sliding window technique to impress recruiters.*

---

**End of article.**