---
title: LeetCode 555. Split Concatenated Strings - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🧩 LeetCode 555 – Split Concatenated Strings  
**The Good | The Bad | The Ugly – A Deep Dive (Java / Python / C++)**

> *“If the question doesn’t get you thinking, it’s not worth the interview.”* – Anonymous

---

## 🚀 TL;DR

- **Goal**:  
  Concatenate an array of strings in order, optionally reverse each one, then cut the resulting circular string at any point.  
  Return the **lexicographically largest** string possible.

- **Insight**:  
  The optimal string is simply the lexicographically largest *rotation* of the **combined string** (after possibly reversing individual words).  
  The reversal choice that maximises the result is the *larger* of the word itself and its reverse.

- **Complexity**:  
  `O(total_length)` time, `O(total_length)` auxiliary space.

- **Code**:  
  Java, Python, C++ – all using the same elegant algorithm.

---

## 📚 Problem Statement (From LeetCode)

> You are given an array of strings `strs`. You could concatenate these strings together into a loop, where for each string you can choose to reverse it or not.  
> Among all possible loops, cut the loop at any position to create a linear string.  
> Return the **lexicographically largest** such linear string.

*Constraints*:  
`1 <= strs.length <= 1000`  
`1 <= strs[i].length <= 1000`  
`sum(strs[i].length) <= 1000`  
`strs[i]` contains only lowercase English letters.

---

## 🔍 Why It Looks Hard

- You have *two* degrees of freedom:
  1. **Reversal** of each word.
  2. **Cut position** on the circular string.

- A naive brute‑force would try `2^n` reversal combinations × `total_length` cuts → impossible.

- The challenge is to avoid the exponential blow‑up while still considering every feasible outcome.

---

## 💡 The Key Observation

When we **fix** a particular reversal configuration, the set of all linear strings we can obtain is exactly the set of **rotations** of the resulting circular string.  
For any string `S`, its rotations are all strings `S[i:] + S[:i]` for `i` from `0` to `len(S)-1`.

**Thus**:  
- The best rotation of `S` is the lexicographically largest among all rotations.  
- If we can choose the reversal of each word, we can simply pick the *larger* version of the word (original vs reversed).  
- The *overall* best string is the lexicographically largest rotation of the concatenation of the *maximal* versions of all words.

> **Why the maximal version?**  
> Because any rotation of a string that contains a smaller word can be improved by replacing that word with its larger variant, and rotations are invariant under such replacements.  
> Formally, if `w` < `rev(w)`, then for any `S = ... w ...`, replace `w` by `rev(w)` gives a string `S'` where every rotation of `S` is <= corresponding rotation of `S'`. So the global maximum comes from using all maximal words.

So we just need two sub‑problems:

1. **Build the best possible circular string** – concatenate `max(word, reverse(word))` for all words.
2. **Find the lexicographically largest rotation** of that string.

---

## 🏎️ Efficient Rotation Finding (Booth’s Algorithm)

Booth’s algorithm returns the index of the smallest lexicographic rotation in linear time.  
For the *largest* rotation we simply apply it to the *negated* string, or we can adapt it slightly.

**Pseudo‑code** (largest rotation):

```
S2 = S + S          // double the string
i = 0, j = 1, k = 0 // i: candidate start, j: competitor, k: offset
while i < n and j < n and k < n:
    diff = S2[i+k] - S2[j+k]
    if diff == 0:
        k += 1
    else if diff < 0:    // i rotation < j rotation, discard i
        i = i + k + 1
        if i <= j: i = j + 1
        k = 0
    else:                // i rotation > j rotation, discard j
        j = j + k + 1
        if j <= i: j = i + 1
        k = 0
return min(i, j)
```

The slice `S2[start:start+n]` is the desired rotation.

The algorithm is `O(n)` and uses only constant extra space.

---

## 🎯 Final Algorithm

1. **Pre‑process words**  
   For each `w` in `strs`, compute `rev = reverse(w)` and keep `max(w, rev)`.  
   Concatenate all maxima → `bestCircular`.

2. **Find largest rotation**  
   Use Booth’s algorithm to get `pos`.  
   Return `bestCircular[pos:] + bestCircular[:pos]`.

---

## ⏱️ Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| Pre‑processing (reverse & max) | `O(total_length)` | `O(total_length)` for result string |
| Booth’s algorithm | `O(total_length)` | `O(1)` |
| Total | **`O(total_length)`** | **`O(total_length)`** |

`total_length` ≤ 1000, so the solution easily meets the limits.

---

## 📦 Code Implementations

Below are clean, self‑contained implementations in **Java**, **Python**, and **C++**.  
All use the same algorithmic idea; feel free to copy/paste into your editor or LeetCode submission box.

### Java (Java 17)

```java
import java.util.*;

public class Solution {
    // Reverse a string
    private String rev(String s) {
        return new StringBuilder(s).reverse().toString();
    }

    // Booth's algorithm for lexicographically largest rotation
    private int largestRotation(String s) {
        int n = s.length();
        String ss = s + s;
        int i = 0, j = 1, k = 0;

        while (i < n && j < n && k < n) {
            char a = ss.charAt(i + k);
            char b = ss.charAt(j + k);
            if (a == b) {
                k++;
                continue;
            }
            if (a < b) { // rotation at i < rotation at j
                i = i + k + 1;
                if (i <= j) i = j + 1;
            } else {     // rotation at i > rotation at j
                j = j + k + 1;
                if (j <= i) j = i + 1;
            }
            k = 0;
        }
        return Math.min(i, j);
    }

    public String splitLoopedString(String[] strs) {
        StringBuilder sb = new StringBuilder();
        for (String w : strs) {
            String rev = rev(w);
            sb.append(w.compareTo(rev) >= 0 ? w : rev);
        }
        String bestCircular = sb.toString();
        int pos = largestRotation(bestCircular);
        return bestCircular.substring(pos) + bestCircular.substring(0, pos);
    }
}
```

### Python 3

```python
class Solution:
    def splitLoopedString(self, strs: List[str]) -> str:
        # Build the best circular string
        best = []
        for w in strs:
            rev = w[::-1]
            best.append(w if w >= rev else rev)
        circ = "".join(best)

        # Booth's algorithm for largest rotation
        def largest_rotation(s: str) -> int:
            n = len(s)
            ss = s + s
            i, j, k = 0, 1, 0
            while i < n and j < n and k < n:
                a, b = ss[i + k], ss[j + k]
                if a == b:
                    k += 1
                    continue
                if a < b:          # i rotation < j rotation
                    i = i + k + 1
                    if i <= j:
                        i = j + 1
                else:              # i rotation > j rotation
                    j = j + k + 1
                    if j <= i:
                        j = i + 1
                k = 0
            return min(i, j)

        pos = largest_rotation(circ)
        return circ[pos:] + circ[:pos]
```

### C++17

```cpp
class Solution {
    string rev(const string &s) {
        string r = s;
        reverse(r.begin(), r.end());
        return r;
    }

    // Largest lexicographic rotation using Booth's algorithm
    int largestRotation(const string &s) {
        int n = s.size();
        string ss = s + s;
        int i = 0, j = 1, k = 0;
        while (i < n && j < n && k < n) {
            char a = ss[i + k];
            char b = ss[j + k];
            if (a == b) {
                ++k;
                continue;
            }
            if (a < b) {            // rotation i < rotation j
                i = i + k + 1;
                if (i <= j) i = j + 1;
            } else {                // rotation i > rotation j
                j = j + k + 1;
                if (j <= i) j = i + 1;
            }
            k = 0;
        }
        return min(i, j);
    }

public:
    string splitLoopedString(vector<string>& strs) {
        string circ;
        for (const string &w : strs) {
            string revW = rev(w);
            circ += (w >= revW ? w : revW);
        }
        int pos = largestRotation(circ);
        return circ.substr(pos) + circ.substr(0, pos);
    }
};
```

---

## 🧩 Edge Cases & Debugging Tips

| Issue | Cause | Fix |
|-------|-------|-----|
| **Empty strings** | Problem constraints forbid empty strings, but if present, treat them as `" "` or skip. | Guard against `w.isEmpty()` before reversing. |
| **All words same after reverse** | Booth may return index `0`. | Works fine – the rotation is the string itself. |
| **Large input size** | O(total_length²) naive solution may TLE. | Use Booth’s linear algorithm. |
| **Tie-breaking** | When `w == rev(w)` the algorithm still picks the word. | `w.compareTo(rev)` handles equality gracefully. |
| **Off‑by‑one in substring** | Using `substring(0, pos)` may miss the last char. | Use `circ.size()` for full length; ensure indices are < `n`. |

---

## 📈 How to Use This in an Interview

1. **Clarify** the problem – confirm that the cut position can be *any* character, not just between words.
2. **Explain** the two degrees of freedom and why a brute‑force would explode.
3. **Introduce** the key insight: choose the maximal word variant first, then reduce to a rotation problem.
4. **Derive** Booth’s algorithm or simply state you’ll use a linear rotation finder.
5. **Code** succinctly, comment your helper functions.
6. **Discuss** time/space complexity.

---

## 🎯 Final Takeaways

- **Good**: The solution is *linear* and *simple once you see the pattern*.  
- **Bad**: Without the insight, the problem seems NP‑hard.  
- **Ugly**: Some people try to generate all 2^n reversal combinations or all rotations naively, leading to TLE.

By mastering this pattern – **pick the optimal local variant first, then solve the global ordering** – you’ll crack many seemingly combinatorial LeetCode puzzles.

---

## 🚀 Ready for the next challenge?

- **Practice**: Try similar problems like *Maximum Circular Subarray*, *Maximum Subarray Rotation*, or *Largest Number* (LeetCode 179).
- **Showcase**: Add this solution to your GitHub portfolio with clear comments.
- **Interview**: Bring up the *reversal + rotation* technique to impress recruiters.

Happy coding! 💻✨

--- 

### SEO Meta Description

> Solve LeetCode 555 – Split Concatenated Strings. Learn the linear‑time algorithm using maximal reversals and Booth’s rotation finder. Java, Python, C++ solutions + interview prep guide. 🚀

--- 

**Keywords**: LeetCode 555, Split Concatenated Strings, lexicographically largest string, Booth’s algorithm, interview question, Java solution, Python solution, C++ solution, rotation of string, algorithm interview, job interview prep, coding challenge.